<properties
   pageTitle="Criar um balanceador de carga interno utilizando o clip do Azure no modelo de implementação clássica | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno utilizando o clip do Azure no modelo de implementação clássico"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Começar a criar um balanceador de carga interna (clássico) utilizando o clip do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Para criar um balanceador de carga interno configurar para máquinas virtuais

Para criar um conjunto de Balanceador de carga interno e os servidores que irão enviar o tráfego para o mesmo, têm de fazer o seguinte:

1. Crie uma instância do interno balanceamento de carga em que será o ponto final de tráfego de entrada para ser balanceada pelos servidores de um conjunto de balanceamento de carga de carga.

1. Adicione pontos finais correspondente para as máquinas virtuais que irão receber o tráfego de entrada.

1. Configure os servidores que irão enviar o tráfego para ser com a enviar o tráfego para o endereço IP (VIP) virtual da instância interno balanceamento de carga de balanceamento de carga.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Passo a passo Criar um balanceador de carga interno utilizando clip

Este guia mostra como criar um balanceador de carga interno com base no cenário acima.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.

2. Execute o comando de **modo azure config** para mudar para modo clássico, conforme apresentado abaixo.

        azure config mode asm

    Saída esperada:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Criar o ponto final e carregar balanceador conjunto

O cenário assume máquinas virtuais "DS1" e "DB2" num serviço de nuvem denominado "mytestcloud". Ambas as máquinas virtuais estiver a utilizar uma rede virtual denominado meu "testvnet" com sub-rede "sub-rede-1".

Este guia irá criar um conjunto de Balanceador de carga interno utilizando porta 1433 como privada porta e 1433 como porta local.

Este é um cenário comum onde tem máquinas virtuais SQL na back-end utilizando um balanceador de carga interna para garantir que os servidores de base de dados não expostos diretamente com um endereço IP público.


### <a name="step-1"></a>Passo 1

Criar um balanceador de carga interno conjunto utilizando `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parâmetros utilizados:

**-r** - nome do serviço na nuvem<BR>
**-n** - nome do Balanceador de carga interno<BR>
**-t** - nome de sub-rede (sub-rede mesmo por máquinas virtuais que irá adicionar ao balanceador de carga interna)<BR>
**-a** - (opcional) adicione um endereço IP estático privado<BR>

Dar saída `azure service internal-load-balancer --help` para obter mais informações.

Pode verificar as propriedades do Balanceador de carga interno utilizando o comando `azure service internal-load-balancer list` *nome do serviço na nuvem*.

Aqui segue-se um exemplo do resultado:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Passo 2

Configurar o conjunto de Balanceador de carga interno ao adicionar o primeiro ponto final. Irá associar o ponto final, máquina virtual e sonda porta para o conjunto de Balanceador de carga interno neste passo.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parâmetros utilizados:

**-k** - porta de máquina virtual local<BR>
**-t** - sonda porta<BR>
**-r** - sonda protocolo<BR>
**-e** - intervalo sonda em segundos<BR>
**-f** - intervalo de tempo limite em segundos <BR>
**-i** - nome do Balanceador de carga interno <BR>


## <a name="step-3"></a>Passo 3

Verifique se o Balanceador de carga configuração utilizando `azure vm show` *nome da máquina virtual*

    azure vm show DB1

O resultado será:

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto de final de ambiente de trabalho remoto para uma máquina virtual

Pode criar um ponto final de ambiente de trabalho remoto para reencaminhar o tráfego de rede a partir de uma porta pública para uma porta local para uma máquina virtual específica utilizando `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Remover máquina virtual do Balanceador de carga

Pode remover uma máquina virtual a partir de um balanceador de carga interno definir eliminando o ponto final associado. Assim que o ponto final é removido, a máquina virtual não pertencer à Balanceador de carga definir deixem.

 Utilizar o exemplo acima, pode remover o ponto final criado para máquina virtual "DS1" a partir do Balanceador de carga interno "ilbset" utilizando o comando `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Dar saída `azure vm endpoint --help` para obter mais informações.


## <a name="next-steps"></a>Próximos passos

[Configurar um modo de distribuição do Balanceador de carga utilizando afinidade IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)