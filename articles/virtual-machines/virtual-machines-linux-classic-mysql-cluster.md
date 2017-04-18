<properties
    pageTitle="Clusterize MySQL com conjuntos de balanceamento de carga | Microsoft Azure"
    description="Configurar uma disponibilidade de balanceamento de carga, alta cluster de Linux MySQL criado com o modelo de implementação clássica no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Utilizar conjuntos de balanceamento de carga para clusterize MySQL em Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


O objetivo deste artigo é explorar e ilustrar as abordagens diferentes disponíveis para implementar os serviços com base em Linux altamente disponíveis no Microsoft Azure, explorar elevada disponibilidade de servidor do MySQL precisar como uma introdução. Um vídeo que ilustrem esta abordagem está disponível no [canal 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Vamos destacar uma nada partilhado dois nós mestre único MySQL elevada disponibilidade solução baseada em DRBD, Corosync e pacemakers. Apenas um nó está em execução MySQL cada vez. Leitura e escrita a partir do recurso DRBD também estão limitado a apenas um nó cada vez.

Não é necessário para obter uma solução VIP como LVS uma vez que utilizamos conjuntos de balanceamento de carga do Microsoft Azure para fornecer tanto a funcionalidade de round robin e a deteção de ponto final, remoção e recuperação sem problemas do VIP. O VIP é um endereço de IPv4 globalmente encaminhável atribuído pelo Microsoft Azure quando em primeiro lugar, criamos o serviço de nuvem.

Existem outras arquiteturas de possíveis para MySQL incluindo NBD Cluster, Percona e Galera, bem como várias soluções de software intermédio, incluindo, pelo menos, uma disponíveis como uma VM no [Depósito de VM](http://vmdepot.msopentech.com). Desde que estas soluções podem criar uma réplica unicast vs. multicast ou difusão e não dependem de armazenamento partilhado ou várias interfaces de rede, os cenários devem ser mais fácil implementar o Microsoft Azure.

Obviamente, estas arquitecturas clusters podem ser expandidas para outros produtos, como PostgreSQL e OpenLDAP no são de forma semelhante. Por exemplo, este balanceamento de carga procedimento com nada partilhado com êxito foi submetido, com várias modelo global de OpenLDAP e que o pode ver no nosso blogue de 9 de canal.

## <a name="getting-ready"></a>Preparar-se

Terá de uma conta do Microsoft Azure com uma subscrição válida conseguir criar, pelo menos, dois (2) VMs (x foi utilizado neste exemplo), definem uma rede e uma sub-rede, um grupo de afinidade e uma disponibilidade, assim como a capacidade para criar novos VHDs na mesma região como o serviço de nuvem e para anexe-os para o VMs Linux.

### <a name="tested-environment"></a>Ambiente testado

- Ubuntu 13.10
  - DRBD
  - Servidor do MySQL
  - Corosync e pacemakers

### <a name="affinity-group"></a>Grupo afinidade

Um grupo de afinidade para a solução é criado pelo registo para o Azure clássica portal deslocar para baixo até definições e criar um novo grupo de afinidade. Os recursos atribuídos criados mais tarde serão atribuídos a este grupo afinidade.

### <a name="networks"></a>Redes

É criada uma nova rede e é criada uma sub-rede dentro da rede. Optamos por uma rede 10.10.10.0/24 com apenas uma /24 sub-rede dentro.

### <a name="virtual-machines"></a>Máquinas virtuais

A primeira 13.10 VM da Ubuntu é criada com uma imagem da Galeria de Ubuntu Endorsed e denominada `hadb01`. É criado um novo serviço de nuvem do processo, denominado hadb. Chamamos-lo desta forma para ilustrar a natureza partilhada, balanceamento de carga que o serviço terá quando Recomendamos adicionar mais recursos. A criação de `hadb01` está a decorrer e concluídas através do portal. Um ponto final para SSH é criado automaticamente e os nossos criado rede está selecionada. Recomendamos também optar por criar uma nova disponibilidade definido para os VMs.

Assim que a primeira VM é criada (tecnicamente, quando é criado o serviço de nuvem) prosseguir para criar a segunda VM, `hadb02`. Para a segunda VM será também utilizamos Ubuntu 13.10 VM a partir da Galeria através do portal mas recomendamos irá optar por utilizar um serviço de nuvem existente, `hadb.cloudapp.net`, em vez de criar um novo. O conjunto de rede e disponibilidade deverá ser automaticamente selecionado-nos. Um ponto final SSH será criado, demasiado.

Depois de tem sido criados ambas as VMs, podemos irá tome nota da porta SSH para `hadb01` (TCP 22) e `hadb02` (automaticamente atribuídas ao Azure)

### <a name="attached-storage"></a>Armazenamento anexado

Vamos anexar um novo disco para ambas as VMs e criar novos discos 5 GB no processo. Os discos irão estar alojados no contentor de VHD utilizado para os nossos discos sistema operativo principal. Depois de discos são criados e anexados não é necessário para-nos reiniciar Linux como o kernel irá ver o novo dispositivo (normalmente `/dev/sdc`, pode verificar `dmesg` no resultado)

Cada VM Podemos continuar para criar um novo partição utilizando `cfdisk` (primária, Linux partição) e escreva a nova tabela partição. **Não fazer para criar um sistema de ficheiros nesta partição** .

## <a name="setting-up-the-cluster"></a>Configurar o cluster

Em ambos os VMs Ubuntu, é necessário utilizar APT para instalar Corosync, pacemakers e DRBD. Utilizar `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**Não instale MySQL neste momento** . Debian e scripts de instalação Ubuntu serão inicializado um diretório de dados do MySQL no `/var/lib/mysql`, mas uma vez que o diretório vai ser substituído por um sistema de ficheiros DRBD, precisamos de fazê-lo mais tarde.

Neste momento Recomendamos também deve verificar (utilizando `/sbin/ifconfig`) que estão a utilizar ambas as VMs endereços na sub-rede 10.10.10.0/24 e que eles podem executar o ping entre si pelo nome. Se pretender, também pode utilizar `ssh-keygen` e `ssh-copy-id` para se certificar de que ambos os VMs podem comunicar através de SSH sem necessidade de uma palavra-passe.

### <a name="setting-up-drbd"></a>Configurar o DRBD

Vamos criar um recurso DRBD que utiliza subjacente `/dev/sdc1` partição para produzir um `/dev/drbd1` recurso poder ser formatado com ext3 e utilizado em nós principais e secundários. Para executar esta tarefa, abra `/etc/drbd.d/r0.res` e copie a seguinte definição do recurso. Faça o seguinte em ambos os VMs:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Após fazer isto, iniciar a utilizando o recurso `drbdadm` em ambos os VMs:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

E, por fim, na página principal (`hadb01`) forçar propriedade (principal) do recurso DRBD:

    sudo drbdadm primary --force r0

Se examinar os conteúdos de/proc/drbd (`sudo cat /proc/drbd`) em ambos os VMs, deverá ver `Primary/Secondary` no `hadb01` e `Secondary/Primary` no `hadb02`consistente com a solução neste momento. O disco de 5 GB será sincronizado através da rede 10.10.10.0/24 sem encargos aos clientes.

Quando o disco está sincronizado pode criar o sistema de ficheiros no `hadb01`. Para fins de teste utilizámos ext2 mas a seguinte instrução irá criar um sistema de ficheiros ext3:

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>Montagem do recurso DRBD

No `hadb01` podemos está agora prontos para os recursos DRBD de montagem. Utilizar debian e derivados `/var/lib/mysql` como do diretório de dados do MySQL. Uma vez que recomendamos ainda não instalou MySQL, vamos criar o directório e montagem do recurso DRBD. On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>Configurar o MySQL

Agora está pronto para instalar o MySQL no `hadb01`:

    sudo apt-get install mysql-server

Para `hadb02`, tem duas opções. Pode instalar o-servidor do MySQL precisar agora, que irá criar /var/lib/mysql e preenchê-lo com um novo directório de dados e, em seguida, avance para remover o conteúdo. On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

A segunda opção é activação pós-falha para `hadb02` e, em seguida, instale servidor do MySQL precisar aí (scripts de instalação irá reparar a instalação existente e não lhe toque)

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se não planeia a activação pós-falha DRBD agora, a primeira opção é mais fácil embora pode menos elegante. Após ter configurado o, pode começar a trabalhar na sua base de dados do MySQL. No `hadb02` (ou independentemente dos servidores é a ativo, de acordo com DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Aviso**: esta declaração de última eficazmente desativa a autenticação para o utilizador de raiz nesta tabela. Isto deve ser substituído pelo seu grau de produção conceder declarações e está incluído apenas para fins ilustrativas.

Também tem de ativar o funcionamento em rede para MySQL se pretender tornar as consultas de fora da VMs, que é o objetivo deste guia. No ambas as VMs, abra `/etc/mysql/my.cnf` e procure `bind-address`, alterá-lo a partir do 127.0.0.1 para 0.0.0.0. Depois de guardar o ficheiro, emitir um `sudo service mysql restart` no seu primária atual.

### <a name="creating-the-mysql-load-balanced-set"></a>Criar a carga de MySQL balanceamento conjunto

Iremos voltar para o portal e navegue para a `hadb01` VM, em seguida, pontos finais. Vamos irá criar um novo ponto final, selecione MySQL (TCP 3306) a partir da lista pendente e a escala na caixa de *conjunto de balanceamento de carga de nova criar* . Irá chamamos nosso ponto final de balanceamento de carga `lb-mysql`. Vamos deixará a maioria das opções por si só, à exceção de tempo que irá reduzir a 5 (segundos, mínimos)

Depois do ponto final é criado podemos aceda a `hadb02`, pontos finais e criar um novo ponto final, mas irá optamos `lb-mysql`, em seguida, selecione MySQL no menu pendente. Também pode utilizar o clip do Azure para este passo.

Neste momento temos tudo que precisamos de uma operação manual do cluster.

### <a name="testing-the-load-balanced-set"></a>Testar a carga de balanceamento de conjunto

Testes podem ser executados a partir de uma máquina fora, utilizando qualquer cliente MySQL, bem como aplicações (por exemplo, phpMyAdmin em execução como um Web site Azure) neste caso utilizámos ferramenta de linha de comandos do MySQL noutra Linux caixa:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Manualmente a falhar ao longo

Agora pode simular activações pós-falha encerrar MySQL, mudar principal do DRBD e iniciar MySQL novamente.

No hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Em seguida, no hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Assim que activação pós-falha manualmente pode repetir a consulta remota e este deverão estar a trabalhar na perfeição.

## <a name="setting-up-corosync"></a>Configurar o Corosync

Corosync é a infraestrutura de cluster subjacente necessária para o pacemakers trabalhar. Para Heartbeat utilizadores v1 e v2 (e outros métodos como Ultramonkey) Corosync é uma divisão das funcionalidades CRM, enquanto pacemakers permanecem mais semelhante ao Hearbeat em termos de funcionalidade.

O constrangimento principal para Corosync no Azure é que Corosync prefere multicast através de difusão de comunicações unicast, mas redes do Microsoft Azure suporta apenas unicast.

Felizmente, Corosync tem um modo unicast de trabalho e o constrangimento real só é, uma vez que não estão a todos os nós comunicar entre si *automagically*, que precisa de definir os nós nos seus ficheiros de configuração, incluindo os respetivos endereços de IP. Pode utilizamos os ficheiros de exemplo Corosync para Unicast e basta alterar vincular endereço, listas de nó e diretório de registo (Ubuntu utiliza `/var/log/corosync` enquanto o exemplo ficheiros utilização `/var/log/cluster`) e activar ferramentas quórum.

**Nota o `transport: udpu` directiva abaixo e os endereços IP definidos manualmente para os nós**.

No `/etc/corosync/corosync.conf` em ambos os nós:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Recomendamos copiar este ficheiro de configuração em ambos os VMs e iniciar Corosync em ambos os nós:

    sudo service start corosync

Brevemente depois de iniciar o serviço de cluster deve ser estabelecido tocar atual e quórum deve ser constituída. Vamos pode verificar esta funcionalidade ao rever registos ou:

    sudo corosync-quorumtool –l

Deve seguir um resultado semelhante a imagem abaixo:

![corosync quorumtool - l saída de exemplo](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>Configurar o pacemakers

Pacemakers utiliza o cluster para monitorizar para recursos, definir quando primários da Ir para baixo e mudar os recursos para secundários. Podem ser definidos recursos a partir de um conjunto de scripts disponíveis ou de scripts de (como inicialização) LSB, entre outras opções.

Pretendemos pacemakers para "proprietário" recurso DRBD, o ponto de montagem e o serviço do MySQL. Se pacemakers podem ativar e desativar DRBD, montagem- / umount-lo e iniciar/parar MySQL na ordem correta quando algo incorretas acontece com a página principal, nosso programa de configuração está concluída.

Quando instala o pacemakers pela primeira vez, a sua configuração deve ser suficiente, simple algo como:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Verifique se-lo executando `sudo crm configure show`. Agora, crie um ficheiro (diga `/tmp/cluster.conf`) com os seguintes recursos:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

E agora carregá-lo para a configuração (apenas ter para o fazer um nó):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Além disso, certifique-se de que o inicia pacemakers durante o arranque em ambos os nós:

    sudo update-rc.d pacemaker defaults

Depois de aguardar alguns segundos e utilizar `sudo crm_mon –L`, verifique se um dos seus nós tornou o modelo global para cluster e está a ser executado todos os recursos. Pode utilizar ps e montagem para verificar se os recursos estão a ser executado.

A seguinte captura de ecrã mostra `crm_mon` com um nó parado (sair utilizando controlo-C)

![nó crm_mon parado](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

E esta captura de ecrã mostra ambos os nós, com um modelo global e uma secundária:

![modelo global de/secundária crm_mon](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>Testes

Estamos está preparados para uma simulação activação pós-falha automática. Existem duas formas para fazê-lo: contornos e disco rígido. A forma de contornos é utilizar a função de encerramento do cluster: ``crm_standby -U `uname -n` -v on``. Utilizar este modelo global de, irá demorar a secundária. Não se esqueça de definir esta novamente para desligado (crm_mon irá indicar-lhe um nó está em suspensão caso contrário)

O disco rígido forma é encerrar a VM principal (hadb01) através do portal do ou alterar runlevel na VM (por exemplo, paragem, encerramento), em seguida, podemos ajudá-Corosync e pacemakers por sinalização aceder da forma mestre para baixo. Podemos testar este (útil para o windows de manutenção), mas recomendamos pode também forçar o cenário fixando apenas a VM.

## <a name="stonith"></a>STONITH

Deve ser possível emitir um encerramento VM através do clip o Azure em vez de um script STONITH que controla um dispositivo físico. Pode utilizar `/usr/lib/stonith/plugins/external/ssh` como uma base de e ativar STONITH na configuração do cluster. Clip Azure deve ser instalada globalmente e o definições de publicar/perfil deve ser carregado para o utilizador o cluster.

Código de exemplo para o recurso disponível no [GitHub](https://github.com/bureado/aztonith). Precisar de alterar a configuração do cluster ao adicionar o seguinte procedimento para `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Nota:** o script não efetuar para cima/baixo verificações. O recurso SSH original tinha 15 ping verificações mas o tempo de recuperação para um VM Azure poderá ser mais variável.

## <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações:

- O script de recursos DRBD linbit gere DRBD como um recurso no utilizações pacemakers `drbdadm down` quando encerrar um nó, mesmo se o nó apenas vai em espera. Isto não é ideal para uma vez que o secundária será não estar a sincronizar o recurso DRBD enquanto o modelo global de obtém escritas. Se o modelo global de não graciously falhar, pode demorar a secundária através de um Estado de sistema de ficheiros mais antigo. Existem duas formas possíveis de resolver isto:
  - Impor um `drbdadm up r0` em todos os nós de cluster através de um local watchdog (não clusterized), ou,
  - Editar o linbit DRBD script certificando-se de que `down` não denomina-se, em `/usr/lib/ocf/resource.d/linbit/drbd`.
- Balanceador de carga necessita de pelo menos 5 segundos para responder, para que as aplicações devem ser clusters e ser mais tolerância a falhas de tempo limite; outras arquiteturas de também pode ajudar, por exemplo filas na aplicação, middlewares de consulta, etc.
- Otimização do MySQL é necessária para se certificar de escrita é feita a um ritmo sane e caches são descarregadas para frequentemente possível para minimizar as perdas da memória do disco
- Escrever será dependente na VM desempenho interligar na mudar virtual como este é o mecanismo utilizado pelo DRBD para criar uma réplica o dispositivo
