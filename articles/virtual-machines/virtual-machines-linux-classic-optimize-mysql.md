<properties
    pageTitle="Otimizar o desempenho do MySQL no Linux VMs | Microsoft Azure"
    description="Saiba como otimizar MySQL em execução numa máquina virtual Azure (VM) com o Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>Otimizar o desempenho do MySQL no Azure Linux VMs

Existem vários fatores que impacto no desempenho MySQL no Azure, ambos na configuração de seleção e software do virtual hardware. Este artigo foca-se no desempenho optimizar através de armazenamento, sistema e configurações de base de dados.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>Recorrendo RAID num computador virtual Azure
Armazenamento está fator de chave impactos desempenho de bases de dados nos ambientes na nuvem.  Em comparação com um único disco, RAID pode fornecer um acesso mais rápido através de simultaneidade do.  Consulte [Níveis RAID padrão](http://en.wikipedia.org/wiki/Standard_RAID_levels) para obter mais detalhes.   

Débito do disco e/s e o tempo de resposta e/s no Azure podem ser melhorados significativamente através de RAID. Os nossos testes laboratório mostram e/s do disco, pode ser duplicado débito e tempo de resposta e/s pode ser reduzido a metade em média quando o número de discos RAID é duplicado (de 2 a 4, 4 para 8, etc.). Consulte o artigo [anexo respostas](#AppendixA) para obter detalhes.  

Para além do disco e/s, o desempenho do MySQL melhora quando aumentar o nível RAID.  Para obter mais detalhes, consulte [B do anexo](#AppendixB) .  

Também poderá querer ter em consideração o tamanho do segmento. Em geral quando tem um tamanho de segmento maior, obterá inferior sobrecarga, especialmente para escritas grandes. No entanto, quando o tamanho do segmento for demasiado grande, poderá adicionar sobrecarga adicional e não é possível tira partido do RAID. O tamanho predefinido atual é 512KB prova seja ideal para ambientes de produção mais gerais. Consulte o artigo [C do anexo](#AppendixC) para obter detalhes.   

Tenha em atenção que existem limites quantos discos pode adicionar para tipos de máquina virtual diferente. Estes limites encontram detalhadas mais [Máquina Virtual e tamanhos de serviço de nuvem para Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Terá de 4 discos dados anexados a seguir o exemplo RAID neste artigo, embora pode escolher configurar o RAID com menos de discos.  

Este artigo assume já tiver criado uma máquina de virtual Linux e ter MYSQL instalado e configurado. Para obter mais informações acerca de como começar Consulte como instalar MySQL no Azure.  

###<a name="setting-up-raid-on-azure"></a>Configurar o RAID no Azure
Os seguintes passos mostram como criar RAID no Azure através do portal clássico Azure. Pode também configurar RAID utilização de scripts do Windows PowerShell.
Neste exemplo estamos irá configurar RAID 0 com 4 discos.  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>Passo 1: Adicionar um disco de dados para o seu computador Virtual  

Na página máquinas virtuais do portal clássica Azure, clique na máquina de virtual ao qual pretende adicionar um disco de dados. Neste exemplo, a máquina virtual é mysqlnode1.  

![][1]

Na página para a máquina virtual, clique em **Dashboard**.  

![][2]


Na barra de tarefas, clique em **anexar**.

![][3]

E, em seguida, clique em **anexar de disco vazio**.  

![][4]

Para discos de dados, a **Preferência de Cache de anfitrião** deve ser definidos **nenhum**.  

Isto irá adicionar um disco vazio para o seu máquina virtual. Repita este passo três vezes mais, para que tenha 4 discos de dados para RAID.  

Pode ver as unidades adicionadas na máquina virtual verificando o registo de mensagem kernel. Por exemplo, para ver isto no Ubuntu, utilize o seguinte comando:  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>Passo 2: Criar RAID com os discos adicionais
Siga este artigo para obter os passos de configuração do detalhadas RAID:  

[Configurar o software RAID em Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] Se estiver a utilizar o sistema de ficheiros XFS, siga os passos abaixo depois de ter criado RAID.

Para instalar XFS em Debian, Ubuntu ou Linux menta, utilize o seguinte comando:  

    apt-get -y install xfsprogs  

Para instalar o XFS no Fedora, CentOS ou RHEL, utilize o seguinte comando:  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>Passo 3: Configurar um novo caminho de armazenamento
Utilize o seguinte comando:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>Passo 4: Copiar os dados originais para o novo caminho de armazenamento
Utilize o seguinte comando:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Passo 5: Modificar permissões para que possam aceder MySQL (leitura e escrita) o disco de dados
Utilize o seguinte comando:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>Ajustar o algoritmo de agendamento do disco e/s
Linux implementa quatro tipos de e/s agendamento algoritmos:  

-   Algoritmo NOOP (sem operação)
-   Algoritmo de prazo (prazo)
-   Algoritmo de colocação completamente justo (CFQ)
-   Algoritmo de período de orçamento (Anticipatory)  

Pode selecionar diferentes e/s responsáveis pelo agendamento em cenários diferentes para otimizar o desempenho. Num ambiente do acesso completamente aleatório, não existe uma grande diferença entre os algoritmos CFQ e prazo para obter um desempenho. Recomenda-se geralmente para configurar o ambiente de base de dados do MySQL para prazo para estabilidade. Se existir muitas e/s sequenciais, CFQ pode reduzir o desempenho do disco e/s.   

Para SSD e outro equipamento, utilizar NOOP ou prazo possível alcançar melhor desempenho do que o agendador predefinido.   

Do núcleo 2,5, o algoritmo de agendamento de e/s predefinido é o prazo. Início do núcleo 2.6.18, CFQ ficou o algoritmo de agendamento de e/s predefinido.  Pode especificar esta definição durante o arranque Kernel ou modificar dinamicamente esta definição quando está a executar o sistema.  

O exemplo seguinte demonstra como verificar e definir o agendador predefinida para o algoritmo de NOOP.  

Para a família Debian distribuição:

###<a name="step-1view-the-current-io-scheduler"></a>Programador de vista a e/s atual do passo 1.
Utilize o seguinte comando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Irá ver as seguintes saída, que indica o agendador atual.  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Passo 2. Alterar o dispositivo atual (/ Dev Center/sda) de e/s algoritmo de agendamento
Utilize os comandos seguintes:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Esta definição para /dev/sda sozinho não é útil. Necessita de ser definidas em todos os discos de dados onde reside a base de dados.  

Deverá ver o resultado seguinte, que indica que grub.cfg tenha sido recompilado com êxito e que o agendador predefinida foi atualizado para NOOP.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Família de distribuição do sistema Redhat, apenas terá o seguinte comando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>Configurar definições de operações de ficheiro do sistema
É uma melhor prática desativar a funcionalidade de registo atime no sistema de ficheiros. Atime é a hora do último acesso de ficheiro. Quando um ficheiro é acedido, o sistema de ficheiros regista o carimbo de hora no registo de. No entanto, esta informação raramente é utilizada. Pode desativá-lo caso não precise da mesma, que irá reduzir o tempo de acesso de disco global.  

Para desativar o registo de atime, é necessário modificar fstab de /etc/. de ficheiro de configuração de sistema de ficheiros e adicione a opção **noatime** .  

Por exemplo, edite o ficheiro de /etc/fstab vim, adicionando o noatime conforme apresentado abaixo.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Remonte o sistema de ficheiros com o seguinte comando:  

    mount -o remount /RAID0

Teste o resultado modificado. Tenha em atenção que, quando modifica o ficheiro de teste, o tempo de acesso não é atualizado.  

Antes de exemplo:     

![][5]

Depois de exemplo:

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Aumentar o número máximo de alças de sistema de simultaneidade alta
MySQL é base de dados de simultaneidade alta. O número predefinido de alças em simultâneo é 1024 para Linux, que não é sempre suficiente. **Utilize os passos seguintes para aumentar as alças em simultâneo máximas do sistema para suportar alta simultaneidade do MySQL**.

###<a name="step-1-modify-the-limitsconf-file"></a>Passo 1: Modificar o ficheiro limits.conf
Adicione as seguintes quatro linhas no ficheiro /etc/security/limits.conf para aumentar as alças de em simultâneo máximas permitidas. Note que 65536 é o número máximo que pode suportam o sistema.   

    * Contornos nofile 65536
    * disco rígido nofile 65536
    * Contornos nproc 65536
    * disco rígido nproc 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>Passo 2: Atualizar o sistema para os novos limites
Execute os seguintes comandos:  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>Passo 3: Certifique-se de que os limites são actualizados durante o arranque
Coloque os seguintes comandos de arranque no ficheiro /etc/rc.local para que a mesma será aplicada cada durante o arranque.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>Otimização de base de dados do MySQL
Pode utilizar a mesma estratégia de sintonização de desempenho para configurar o MySQL no Azure como num computador no local.  

As regras de Otimização e/s principais são:   

-   Aumente o tamanho da cache.
-   Reduza o tempo de resposta e/s.  

Para otimizar o definições de servidor do MySQL, pode atualizar o ficheiro de my.cnf, que é o ficheiro de configuração de predefinido para computadores cliente e servidor.  

Os seguintes itens de configuração são os fatores principais que afetam o desempenho do MySQL:  

-   **innodb_buffer_pool_size**: O conjunto de memória intermédia contém dados na memória intermédia e o índice. Normalmente, isto é definido para 70% de memória física.
-   **innodb_log_file_size**: Este é o tamanho do registo Refazer. Utilize os registos de Refazer para assegurar que operações de escrita são mais rápido, fiável e recuperáveis após uma falha de sistema. Esta é definida para 512MB, irá dar-lhe muito espaço de para o registo de operações de escrita.
-   **max_connections**: por vezes, aplicações não fechar ligações corretamente. Um valor maior dar ao servidor mais tempo a Reciclagem idled ligações. O número máximo de ligações é 10000, mas o máximo recomendado é de 5000.
-   **Innodb_file_per_table**: esta definição ativar ou desativar a capacidade de InnoDB para armazenar tabelas em ficheiros separados. Ativar a opção irá garantir que várias operações de administração avançada podem ser aplicadas de forma eficaz. A partir do desempenho ponto de vista, pode acelerar a transmissão de espaço de tabela e otimizar o desempenho de gestão de lixo no disco. Por isso, a definição recomendada para que esta está Ativada.</br>
    A partir do MySQL 5.6, a predefinição está Ativada. Por conseguinte, não é necessária nenhuma ação. Para outras versões, que é anteriores ao 5.6, predefinições está DESATIVADA. É necessário ativar este Sucessivamente. E deve aplicá-lo antes de dados são carregados, uma vez que apenas as tabelas recentemente criado são afetadas.
-   **innodb_flush_log_at_trx_commit**: valor predefinido é 1, com o âmbito definido como 0 ~ 2. O valor predefinido é a opção mais adequada para autónomo MySQL DB. A definição de 2 permite a integridade dos dados a maior parte dos e é adequada para o modelo global de cluster de MySQL. A definição de 0 permite perda de dados, que pode afetar a fiabilidade, em alguns casos com um melhor desempenho e é adequado para secundária num cluster de MySQL.
-   **Innodb_log_buffer_size**: A memória intermédia de registo permite que as transações executar sem ser necessário, remova o registo para o disco antes das transações consolidar. No entanto, se existir objecto binário grande ou campo de texto, a cache será consumida muito rapidamente e será acionado e de disco frequente/s. É melhor aumentar o tamanho de memória intermédia se Innodb_log_waits Estado variável não é 0.
-   **query_cache_size**: A melhor opção é para desativá-lo desde o início. Definir query_cache_size como 0 (é agora a predefinição na MySQL 5.6) e utilizar outros métodos para acelerar as consultas.  

Consulte o artigo [Anexo D](#AppendixD) para comparar desempenho após a optimização.


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Ativar o registo de consulta lenta do MySQL para analisar a redução do desempenho
O registo de consulta lenta MySQL pode ajudar a identificar as consultas lentas para MySQL. Depois de activar o registo de consulta lenta MySQL, pode utilizar as ferramentas de MySQL como **mysqldumpslow** para identificar a redução do desempenho.  

Tenha em atenção que por predefinição esta não estiver ativada. Ativar o registo de consulta lenta pode consumir alguns recursos de CPU. Por conseguinte, recomenda-se que lhe permite isto temporariamente para resolver o problema congestionamentos de desempenho.

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>Passo 1: Modificar my.cnf ficheiro adicionando as linhas seguintes para o fim   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>Passo 2: Reiniciar o servidor do mysql
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>Passo 3: Verificar se a definição está a demorar efeito utilizando o comando "Mostrar"

![][7]   

![][8]

Neste exemplo, pode ver que a funcionalidade de consulta lenta foi ativada. Em seguida, pode utilizar a ferramenta de **mysqldumpslow** para determinar congestionamentos de desempenho e otimizar o desempenho, tal como adicionar índices.





##<a name="appendix"></a>Anexo

Seguem-se dados de teste de desempenho de exemplo produzidos no ambiente de teste alvo, fornecem gerais sobre a tendência de dados de desempenho com desempenho diferentes optimização abordagens, no entanto, os resultados podem variar em versões diferentes do ambiente ou produto.

<a name="AppendixA"></a>R: de anexo  
**Desempenho do disco (IOPS) com RAID diferentes níveis**


![][9]

**Comandos de teste:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE. Nota: A carga de trabalho deste teste utiliza 64 threads, está a tentar entrar o limite superior de RAID.

<a name="AppendixB"></a>Atalhos do teclado  
**Comparação de desempenho (débito) MySQL com RAID diferentes níveis**   
(Sistema de ficheiros XFS)


![][10]  
![][11]

**Comandos de teste:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparação de desempenho (OLTP) MySQL com RAID diferentes níveis**  
![][12]

**Comandos de teste:**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Anexo c:   
**Comparação de desempenho (IOPS) do disco para tamanhos de segmento diferente**  
(Sistema de ficheiros XFS)


![][13]

**Comandos de teste:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Tenha em atenção o tamanho de ficheiro utilizado para este teste é 30GB e 1GB, respetivamente, com RAID 0(4 disks) XFS campo sistema.


<a name="AppendixD"></a>Anexo d:  
**Comparação de desempenho (débito) MySQL antes e depois optimização**  
(Sistema de ficheiros XFS)


![][14]

**Comandos de teste:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**A definição de configuração para predefinido e optimização é da seguinte forma:**

|Parâmetros |Predefinido    |optimização
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |Nenhum   |7G
|**innodb_log_file_size**   |5M |512M
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8M |128M
|**query_cache_size**   |16M    |0


Parâmetros de configuração de otimização mais detalhados, consulte as instruções oficial do mysql.  

[http://Dev.MySQL.com/doc/refman/5.6/en/innodb-Configuration.HTML](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://Dev.MySQL.com/doc/refman/5.6/en/innodb-Parameters.HTML#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Ambiente de teste**  

|Hardware   |Detalhes
|-----------|-------
|CPU    |AMD Opteron (TM) processador 4171 HE / 4 núcleos
|Memória |14G
|disco   |10G/disco
|sistema operativo |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png
