<properties
   pageTitle="Introdução ao utilizar docker com enxame no Azure"
   description="Descreve como criar um grupo de VMs com a extensão de VM Docker e utilizar enxame para criar um cluster de Docker."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# <a name="how-to-use-docker-with-swarm"></a>Como utilizar docker com enxame

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Este tópico mostra uma forma muito simples para utilizar [docker](https://www.docker.com/) com [swarm](https://github.com/docker/swarm) para criar um cluster gerido enxame no Azure. Quatro máquinas virtuais que cria no Azure, uma para funcionar como o Gestor de enxame e três como parte do cluster de anfitriões docker. Quando tiver terminado, pode utilizar enxame para ver o cluster e, em seguida, começar a utilizar docker no mesmo. Além disso, as chamadas Azure clip neste tópico utilizam o modo de gestão (asm) de serviço. 

> [AZURE.NOTE] Este tópico utiliza docker com enxame e o Azure clip *sem* utilizar **docker máquina** para mostrar como as ferramentas de diferentes funcionam em conjunto, mas permanecem independentes. tem de **máquina docker** **– swarm** parâmetros permitem-lhe utilizar **docker máquina** para adicionar diretamente nós para um enxame. Por exemplo, consulte a documentação de [máquina docker](https://github.com/docker/machine) . Se não atendidas **docker computador** a executar o contra Azure VMs, consulte o artigo [como utilizar docker-máquina com Azure](virtual-machines-linux-docker-machine.md).

## <a name="create-docker-hosts-with-azure-virtual-machines"></a>Criar anfitriões docker com máquinas virtuais do Azure

Este tópico cria quatro VMs, mas pode utilizar qualquer número que pretende. Ligar as seguintes ações com * &lt;palavra-passe&gt; * substituído pela palavra-passe que escolheu.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Quando tiver terminado deverá conseguir utilizar **azure vm lista** para ver o seu VMs Azure:

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## <a name="installing-swarm-on-the-swarm-master-vm"></a>Instalar o enxame no modelo global de enxame VM

Este tópico utiliza o [modelo de contentor da instalação a partir do docker swarm documentação](https://github.com/docker/swarm#1---docker-image) – mas também pode SSH para o **modelo global de enxame**. **Swarm** é transferido neste modelo, como um contentor docker executar enxame. Abaixo, vamos executar este passo *remotamente a partir do nosso portátil utilizando docker* para ligar para o **modelo global de enxame** VM e informe-o para utilizar o comando de criação de id cluster, **enxame criar**. O id de cluster é como **swarm** detetar os membros do grupo enxame. (Também é possível clonar do repositório e criá-la por si mesmo, que irá dar-lhe controlo total e ativar a depuração.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

Que a última linha é o id de cluster; Copie-o num local porque irá utilizá-lo novamente quando participa o nó VMs ao modelo global de enxame para criar "enxame". Neste exemplo, o id de cluster é **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Apenas para seja claro, estamos a utilizar os nossos instalação local docker para ligar para o **modelo global de enxame** VM no Azure e instrução do **modelo global de enxame** transferir, instalar e executar o comando **Criar** , que devolve o nosso id de cluster que utilizamos para fins de deteção mais tarde.
<!-- -->
> Para confirmar, execute o `docker -H tcp://` * &lt;hostname&gt; * ` images` para listar os processos de contentor no **modelo global de enxame** computador e noutro nó para comparação (uma vez que recomendamos executou o comando enxame anterior com o parâmetro **– rm** , o contentor foi removido depois de concluída-lo, por isso, utilizar **docker ps - um** não devolve nada).:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
>Se estiver familiarizado com **docker**, saberá que a que os outros nós têm sem entradas porque foram transferidas e ainda executadas sem imagens.

## <a name="join-the-node-vms-to-our-docker-cluster"></a>Associar o nó VMs a nossa cluster de docker

Para cada nó, lista as informações de ponto final utilizando o clip do Azure. Abaixo estamos fazê-lo para o anfitrião de docker **enxame-nó-1** para poder obter a porta de docker o nó.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


Utilizar **docker** e o `-H` aderir a opção para aponte para o cliente docker o nó do seu VM, esse nó para enxame estiver a criar, passando o id de cluster e uma porta docker o nó (o último utilizando **– addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Que gostar. Para confirmar que **swarm** está em execução no **enxame-nó-1** , que vamos escrever:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Repita para todos os outros nós no cluster. No nosso caso, podemos fazê-lo para **enxame-nó-2** e **enxame-nó-3**.

## <a name="begin-managing-the-swarm-cluster"></a>Começar a gerir o cluster enxame

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

e, em seguida, pode listar saída os nós no seu cluster:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Aceda a executar ações no seu enxame. Para procurar inspiração, consulte o artigo [https://github.com/docker/swarm/](https://github.com/docker/swarm/)ou talvez um [vídeo](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 
