<properties
   pageTitle="Começar a criar uma Internet opostas Balanceador de carga no modelo de implementação clássica utilizando o clip do Azure | Microsoft Azure"
   description="Saiba como criar uma Internet opostas Balanceador de carga no modelo de implementação clássica utilizando o clip do Azure"
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

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Começar a criar uma Internet opostas Balanceador de carga (clássico) no clip do Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Pode também [saber como criar um opostas Balanceador de carga de utilizar o Gestor de recursos do Azure da Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Passo a passo Criar um opostas Balanceador de carga de utilizar o clip da Internet

Este guia mostra como criar um balanceador de carga de Internet com base no cenário acima.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.

2. Execute o comando de **modo azure config** para mudar para modo clássico, conforme apresentado abaixo.

        azure config mode asm

    Saída esperada:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Criar o ponto final e carregar balanceador conjunto

O cenário assume as máquinas virtuais "web1" e "web2" foram criadas.
Este guia irá criar um conjunto de Balanceador de carga utilizando as portas 80 como pública porta e 80 como porta local. Uma porta sonda também é configurada no porta 80 e o conjunto de Balanceador de carga "lbset" com o nome.


### <a name="step-1"></a>Passo 1

Criar o primeiro ponto final e conjunto utilizando do Balanceador de carga `azure network vm endpoint create` para máquina virtual "web1".

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

Parâmetros utilizados:

**-k** - porta de máquina virtual local<br>
**+ o** - protocolo<BR>
**-t** - sonda porta<BR>
**-b** - nome do Balanceador de carga<BR>

## <a name="step-2"></a>Passo 2

Adicione uma segunda máquina de virtual "web2" para o conjunto de Balanceador de carga.

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>Passo 3

Verifique se o Balanceador de carga configuração utilizando `azure vm show` .

    azure vm show web1

O resultado será:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto de final de ambiente de trabalho remoto para uma máquina virtual

Pode criar um ponto final de ambiente de trabalho remoto para reencaminhar o tráfego de rede a partir de uma porta pública para uma porta local para uma máquina virtual específica utilizando `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Remover máquina virtual do Balanceador de carga

Tem de eliminar o ponto final associado ao balanceador de carga definir a partir de máquina virtual. Assim que o ponto final é removido, a máquina virtual não pertencer à Balanceador de carga definir deixem.

 Utilizar o exemplo acima, pode remover o ponto final criado para máquina virtual "web1" a partir do Balanceador de carga "lbset" utilizando o comando `azure vm endpoint delete`.

    azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] Pode explorar mais opções para gerir os pontos finais utilizando o comando`azure vm endpoint --help`


## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)

