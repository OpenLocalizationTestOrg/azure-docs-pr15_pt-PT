<properties
   pageTitle="Como criar NSGs no modo clássico utilizando o clip do Azure | Microsoft Azure"
   description="Saiba como criar e implementar NSGs no modo clássico utilizando o clip do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Como criar NSGs (clássico) no clip do Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Também pode [Criar NSGs no modelo de implementação de Gestor de recursos](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Os comandos do Azure clip de exemplo abaixo esperam um ambiente simple já criado com base num cenário acima. Se pretender executar os comandos, tal como estes são apresentados neste documento, primeiro a criar o ambiente de teste através da [criação de um VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Como criar NSG para a sub-rede front-end
Para criar um NSG denominada com nome **NSG FrontEnd** com base no cenário acima, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.

2. Executar o **`azure config mode`** comando para mudar para modo clássico, conforme apresentado abaixo.

        azure config mode asm

    Saída esperada:

        info:    New mode is asm

3. Executar o **`azure network nsg create`** comando para criar um NSG.

        azure network nsg create -l uswest -n NSG-FrontEnd

    Saída esperada:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parâmetros:

    - **-l (ou – localização)**. Azure região onde o NSG novo será criado. Para o cenário, *westus*.
    - **-n (ou – nome)**. Nome para o novo NSG. Para o cenário, *NSG FrontEnd*.

4. Executar o **`azure network nsg rule create`** comando para criar uma regra que permite o acesso a porta 3389 (RDP) a partir da Internet.

        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

    Saída esperada:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK

    Parâmetros:

    - **-a (ou nome – nsg)**. Nome da NSG em que a regra será criada. Para o cenário, *NSG FrontEnd*.
    - **-n (ou – nome)**. Nome para a nova regra. Para o cenário, *rdp regra*.
    - **-c (ou – ação)**. Nível de acesso para a regra (negar ou permitir).
    - **-p (ou – protocol)**. Protocolo (Tcp, Udp, ou *) para a regra.
    - **-r (ou – tipo)**. Direcção de ligação (entrada ou saída).
    - **-y (ou – prioridade)**. Prioridade para a regra.
    - **-f (ou – prefixo de endereço de origem)**. Prefixo de endereço de origem no CIDR ou utilizar etiquetas predefinido.
    - **+ o (ou – o intervalo de porta de origem)**. Porta de origem, ou intervalo.
    - **-"e" (ou – prefixo de endereço de destino)**. Prefixo do endereço de destino em CIDR ou utilizar etiquetas predefinido.
    - **-u (ou – o intervalo de porta de destino)**. Porta de destino, ou intervalo.

5. Executar o **`azure network nsg rule create`** comando para criar uma regra que permita aceder à porta 80 (HTTP) a partir da Internet.

        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

    Putput esperada:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Executar o **`azure network nsg subnet add`** comando para ligar a NSG à sub-rede front-end.

        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

    Saída esperada:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Como criar NSG para a sub-rede back-end
Para criar um NSG denominada com nome *NSG-back-end* , com base no cenário acima, siga os passos abaixo.

3. Executar o **`azure network nsg create`** comando para criar um NSG.

        azure network nsg create -l uswest -n NSG-BackEnd

    Saída esperada:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parâmetros:

    - **-l (ou – localização)**. Azure região onde o NSG novo será criado. Para o cenário, *westus*.
    - **-n (ou – nome)**. Nome para o novo NSG. Para o cenário, *NSG FrontEnd*.

4. Executar o **`azure network nsg rule create`** comando para criar uma regra que permite o acesso a porta 1433 (SQL) da sub-rede front-end.

        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

    Saída esperada:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK


5. Executar o **`azure network nsg rule create`** comando para criar uma regra que recusa o acesso à Internet.

        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

    Putput esperada:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Executar o **`azure network nsg subnet add`** comando para ligar a NSG à sub-rede back-end.

        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

    Saída esperada:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK
