<properties
    pageTitle="Executar um cluster de MariaDB (MySQL) no Azure"
    description="Criar um MariaDB + Galera MySQL cluster no máquinas virtuais do Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>Cluster MariaDB (MySQL) - Azure tutorial

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  Cluster MariaDB empresa está agora disponível no Azure Marketplace.  Nova oferta automaticamente irá implementar um cluster de MariaDB Galera no braço. Deve utilizar a nova oferta de https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 

Vamos está a criar um cluster de [Galera](http://galeracluster.com/products/) múltiplos principal de [MariaDBs](https://mariadb.org/en/about/), substituição queda livre robusta, e dimensionáveis para MySQL, para trabalhar num ambiente altamente disponível em máquinas virtuais do Azure.

## <a name="architecture-overview"></a>Descrição geral de arquitectura

Este tópico executa os seguintes passos:

1. Criar um cluster de 3-node
2. Separe os discos de dados do disco SO
3. Criar os discos de dados com a definição RAID-0/riscas para aumentar IOPS
4. Utilizar o Balanceador de carga Azure para equilibrar a carga para os nós de 3
5. Para minimizar o trabalho repetitivo, crie uma imagem VM que contém MariaDB + Galera e utilizá-la para criar o cluster VMs.

![Arquitetura](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  Este tópico utiliza as ferramentas do [Azure clip](../xplat-cli-install.md) , por isso, certifique-se para transferi-los e ligá-los à sua subscrição do Azure acordo com as instruções. Se precisar de uma referência para os comandos disponíveis no clip do Azure, consulte o artigo esta ligação para a [referência de comandos do clip Azure](../virtual-machines-command-line-tools.md). Também serão necessárias para [criar uma chave SSH para autenticação] e tome nota da **localização do ficheiro .pem**.


## <a name="creating-the-template"></a>Criar o modelo

### <a name="infrastructure"></a>Infraestrutura

1. Criar um grupo de afinidade para colocar em espera os recursos em conjunto

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Criar uma rede Virtual

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Crie uma conta de armazenamento para todos os nossos discos do anfitrião. Nota que que não deve ser colocar mais de 40 fortemente utilizado discos na mesma conta de armazenamento para evitar atingir o limite de conta de armazenamento IOPS 20.000. Neste caso, estamos afastadas a partir deste número para que recomendamos irá armazenar tudo a mesma conta para simplificar

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Localize o nome da imagem CentOS 7 Virtual Machine

        azure vm image list | findstr CentOS
Esta será de saída algo parecido com `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Utilize o nome no passo seguinte.

4. Criar o modelo VM substituir **/path/to/key.pem** com o caminho onde armazenou a chave SSH .pem gerado

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Anexar discos de dados de 4 x 500GB para a VM para utilização na configuração RAID

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. SSH para o modelo VM que criou na **mariadbhatemplate.cloudapp.net:22** e ligar utilizando a sua chave privada.

### <a name="software"></a>Software

1. Obter a raiz

        sudo su

2. Instale o suporte RAID:

     - Instalar mdadm

                yum install mdadm

     - Criar a configuração de RAID0/faixa com um sistema de ficheiros EXT4

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - Criar o directório de ponto montagem

                mkdir /mnt/data

     - Obter o UUID do dispositivo RAID recentemente criado

                blkid | grep /dev/md0

     - Editar /etc/fstab

                vi /etc/fstab

     - Adicionar o dispositivo na daí para ativar mouting automática no reinício substituir o UUID com o valor obtido a partir do comando **blkid** antes de

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Montagem a nova partição

                mount /mnt/data

3. Instale MariaDB:

     - Crie o ficheiro MariaDB.repo:

                vi /etc/yum.repos.d/MariaDB.repo

     - Preenchê-lo com a seguir conteúdo

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - Remover postfix existente e mariadb-efectuar para evitar conflitos

            yum remove postfix mariadb-libs-*

     - Instalar MariaDB com Galera

            yum install MariaDB-Galera-server MariaDB-client galera

4. Mover o diretório de dados do MySQL para o dispositivo de bloco RAID

     - Copie o diretório MySQL atual para a nova localização e remover o diretório antigo

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - Definir permissões no novo directório em conformidade

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Criar um symlink apontar para o diretório antigo para a nova localização na partição RAID

            ln -s /mnt/data/mysql /var/lib/mysql

5. Uma vez que [que selinux irá interferir com as operações de cluster](http://galeracluster.com/documentation-webpages/configuration.html#selinux), é necessário desativá-lo para a sessão actual (até ser apresentada uma versão compatível). Editar `/etc/selinux/config` para desativá-lo para ser reiniciado subsequentes:

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. Validar execuções do MySQL

    - Iniciar MySQL

            service mysql start

    - Proteger a instalação do MySQL, definir a palavra-passe de raiz, remover utilizadores anónimos, desativar o início de sessão de raiz remoto e remover a base de dados de teste

            mysql_secure_installation

    - Criar um utilizador da base de dados para operações de cluster e, opcionalmente, as suas aplicações

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Parar MySQL

            service mysql stop

7. Criar o marcador de posição de configuração

    - Edite a configuração do MySQL para criar um marcador de posição para que as definições de cluster. Não substituir o **`<Vairables>`** ou remova os comentários agora. Que irá acontecer depois criamos uma VM a partir deste modelo.

            vi /etc/my.cnf.d/server.cnf

    - Edite a secção **[galera]** e desmarque-a

    - Edite a secção **[mariadb]**

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Abra necessárias portas na firewall (utilizando FirewallD no CentOS 7)

    - MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA TSI:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Recarregar a firewall:`firewall-cmd --reload`

9.  Otimizar o sistema para o desempenho. Consulte este artigo sobre [estratégia optimização do desempenho](virtual-machines-linux-classic-optimize-mysql.md) para obter mais detalhes

    - Editar novamente o ficheiro de configuração do MySQL

            vi /etc/my.cnf.d/server.cnf

    - Edite a secção **[mariadb]** e acrescentar a abaixo

    > [AZURE.NOTE] Recomenda-se que **innodb\_memória intermédia\_pool_size** ser 70% de memória a VM. -Foi definida na 2.45GB aqui para a VM do Azure médio com 3,5 GB de RAM.

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. Parar MySQL, desativar o serviço de MySQL em execução no arranque para evitar a afetar o cluster ao adicionar um novo nó e deprovision computador.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. Capture VM através do portal. (Atualmente, o [problema #1268 no clip do Azure] ferramentas descreve o facto de que imagens capturadas através de ferramentas o clip do Azure não capturar discos dados anexados.)

    - Encerrar o computador através do portal
    - Clique em captura e especifique o nome da imagem como **imagem de galera mariadb** e forneça uma descrição e verificar "Tiver executado waagent".
    ![Capturar a Máquina Virtual](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![capturar a Máquina Virtual](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>Criar o cluster

Crie 3 VMs terminar o modelo que acabou de criar e, em seguida, configura e iniciar o cluster.

1. Criar a primeira VM de 7 CentOS da imagem de **imagem de galera mariadb** que criou, fornecendo tamanho **médio**prisma no serviço de nuvem de máquina de nome de rede virtual **mariadbvnet** e de sub-rede **mariadb**, atribua um nome para ser **mariadbha** (ou o nome que pretender ser acedido através de mariadbha.cloudapp.net), definição do nome do presente de máquina sejam **mariadb1** e o nome de utilizador para ser **azureuser**e ativar o acesso SSH e transmitir a SSH de certificados .pem ficheiro e substituir **/path/to/key.pem** com o caminho onde armazenados a chave SSH .pem gerado.

    > [AZURE.NOTE] Os comandos abaixo estão divididos em várias linhas para maior clareza, mas deverá introduzir cada um deles como uma linha.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. Crie 2 máquinas virtuais mais _ligando_ -los para atualmente criado **mariadbha** serviço na nuvem, alterar o **nome VM** bem como a **porta SSH** para uma porta exclusivo não em conflito com outros VMs no serviço de nuvem do mesmo.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
e para MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. Terá de obter o endereço IP interno de cada um dos VMs a 3 para o passo seguinte:

    ![Obter o endereço IP](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. SSH para as 3 VMs e e editar o ficheiro de configuração em cada

        sudo vi /etc/my.cnf.d/server.cnf

    uncommenting **`wsrep_cluster_name`** e **`wsrep_cluster_address`** removendo a **#** no início e de validação são facto o que pretende.
    Para além disso, substituir **`<ServerIP>`** no **`wsrep_node_address`** e **`<NodeName>`** no **`wsrep_node_name`** com a VM IP endereço e atribua um nome respetivamente e remova os comentários, assim essas linhas.

5. Iniciar o cluster no MariaDB1 e reproduza-a executar o comando no arranque

        sudo service mysql bootstrap
        chkconfig mysql on

6. Inicie o MySQL no MariaDB2 e MariaDB3 e reproduza-a executar o comando no arranque

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>Cluster de balanceamento de carga
Quando tiver criado os VMs agrupadas, adicionou-los para um Availablity definido denominado **clusteravset** para se certificar de que sejam colocados no falha diferentes e actualizar domínios e que Azure nunca não manutenção em todos os computadores em simultâneo. Esta configuração cumpra os requisitos de serão suportadas por esse contrato de nível do Azure serviço (SLA).

Agora pode utilizar o Balanceador de carga Azure para equilibrar pedidos entre os nossos 3 nós.

Executar a abaixo comandos no seu computador utilizando o clip do Azure.
A estrutura de parâmetros de comando é:`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Por fim, uma vez que o clip define o intervalo de sonda do Balanceador de carga para 15 segundos (que pode ser um pouco demasiado longos), alterá-la no portal em **pontos finais** para qualquer uma das VMs

![Editar o ponto final](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

em seguida, clique em definir Reconfigure The Load-Balanced e aceda seguinte

![Reconfigure carregar conjunto desequilibrado](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

em seguida, altere o intervalo de sonda para 5 segundos e guardar

![Intervalo de sonda de alteração](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>Validar o cluster

O trabalho de disco rígido é feito. Cluster deve ser agora acessível no `mariadbha.cloudapp.net:3306` qual será o Balanceador de carga de visitas e encaminhar pedidos entre as 3 VMs sem problemas e eficiente.

Utilize o cliente do MySQL favorito para ligar ou apenas ligar a partir de uma VMs para verificar que este cluster está a funcionar.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Em seguida, criar uma nova base de dados e preencha-a com alguns dados

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Irá resultar na tabela abaixo

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Neste artigo, que criou uma 3 nó MariaDB + Galera altamente-cluster disponíveis em máquinas virtuais do Azure a executar o CentOS 7. Os VMs são distribuído com Balanceador de carga o Azure.

Pretende veja [outra forma de cluster MySQL no Linux](virtual-machines-linux-classic-mysql-cluster.md) e formas de [otimizar e testar o desempenho do MySQL no Azure Linux VMs](virtual-machines-linux-classic-optimize-mysql.md).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[criar uma chave de SSH para autenticação]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[problema #1268 no clip do Azure]:https://github.com/Azure/azure-xplat-cli/issues/1268
