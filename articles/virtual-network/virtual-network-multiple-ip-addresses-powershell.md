<properties
   pageTitle="Vários endereços IP para máquinas virtuais - PowerShell | Microsoft Azure"
   description="Saiba como atribuir vários endereços IP para uma máquina de virtual através do Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Atribuir vários endereços IP para máquinas virtuais

Uma Máquina Virtual (VM Azure) pode ter um ou mais interfaces de rede (NIC) anexadas ao mesmo. Qualquer NIC pode ter um ou mais públicos ou privados endereços IP atribuídos à mesma. Se não estiver familiarizado com os endereços IP no Azure, leia o artigo [endereços IP no Azure](virtual-network-ip-addresses-overview-arm.md) para saber mais sobre os mesmos. Este artigo explica como utilizar o Azure PowerShell para atribuir uma VM no modelo de implementação de Gestor de recursos do Azure múltiplos endereços IP.

Atribuir vários endereços IP a uma VM permite que as seguintes funcionalidades:

- Vários Web sites ou serviços com diferentes endereços IP e certificados SSL num único servidor de alojamento.
- Agir como um aparelho virtual de rede, tal como uma firewall ou Balanceador de carga.
- A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma da NIC para um conjunto de dados back-end Balanceador de carga Azure. No passado, apenas o endereço IP principal para o NIC primário pode ser adicionado a um conjunto de dados back-end.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Para registar a pré-visualização, enviar uma mensagem de e-mail para [Vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com o seu ID de subscrição e se destina a utilizar.

## <a name="scenario"></a>Cenário

Neste artigo, irá associar três configurações de IP para uma interface de rede.
As configurações de exemplo que se seguem serão criadas e atribuídas a uma imagem que terão três endereços IP privados e um endereço IP público atribuído à mesma:

- IPConfig-1: Um endereço IP privado dinâmico (predefinição) e um público endereços IP a partir do recurso de endereço IP público com o nome PIP1.
- IPConfig-2: Um endereço IP estático privado e sem endereço IP público.
- IPConfig-3: Um endereço IP privado dinâmico e não endereço IP público.

    ![Texto alternativo da imagem](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Este cenário assume que tem um grupo de recursos denominado *RG1* dentro dos quais existe um VNet denominado *VNet1* e uma sub-rede denominado *Sub-rede1*. Além disso, assume que tem uma VM denominada *VM1*, uma interface de rede denominada *VM1 NIC1* associados à mesma e um endereço IP público denominado *PIP1*.

[Este artigo](./virtual-machines/virtual-machines-windows-ps-create.md ) explica como os recursos mencionados acima, caso não tiver criado-las antes de criar.

## <a name = "create"></a>Criar uma VM com vários endereços IP

1. Abra uma linha de comandos do PowerShell e conclua os restantes passos nesta secção dentro de uma única sessão PowerShell. Se ainda não tiver instalado e configurado o PowerShell, execute os passos no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .

2. Alterar os "valores" da $Variables seguintes para a Azure [localização](https://azure.microsoft.com/regions) da que sua rede virtual encontra, o nome do seu [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups), VNet dentro de grupo de recursos, sub-rede que pretende ligar o NIC para e o nome do NIC. Conclua os passos para adicionar vários endereços IP para qualquer NIC anexado a uma VM, à medida que necessita.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Se não souber o nome de uma localização Azure existente ou grupo de recursos, escreva os seguintes comandos:

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>NIC deve estar ligada a uma sub-rede dentro de uma Azure Virtual rede existente (VNet). Os três componentes: NIC, sub-rede e VNet, tem tudo de existir na mesma região e [subscrição](../azure-glossary-cloud-terminology.md#subscription).  Se não estiver familiarizado com VNets, leia o artigo [Descrição geral de rede Virtual](virtual-networks-overview.md) para saber mais sobre os mesmos ou ler o artigo [criar um VNet](virtual-networks-create-vnet-arm-ps.md) para saber como criar uma.

    Se não souber o nome de um VNet existente, introduza o seguinte comando e substitua *VNet1* na variável anterior com o nome de um VNet:

        Get-AzureRmVirtualNetwork | Format-Table Name

    Se a lista de devolvida estiver vazia, tem de criar um VNet. Para saber mais, leia o artigo [criar uma rede virtual](virtual-networks-create-vnet-arm-ps.md) .

    Escreva os seguintes comandos para obter o nome das sub-redes existentes na VNet e substituir *Sub-rede1* acima com o nome de uma sub-rede:

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Introduza o seguinte comando para obter a sub-rede e atribuí-lo a uma variável.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Definir as configurações de IP que pretende atribuir à NIC. Cada configuração pode ter uma estático ou dinâmico endereço IP privado e um associados recurso de endereço IP público com um endereço estático ou dinâmico.

    Adicionar ou remover qualquer número das configurações que se seguem dependendo quantos endereços IP que pretende associar a NIC e as definições que pretende configurar.

    **IPConfig-1**

    Altere o valor *PIP1* para o nome de um recurso de endereço IP de público existente que existe na localização que está a criar NIC e que não está atualmente associada a outra NIC. Alterar *RG1* para o nome do grupo de recursos o recurso de endereço IP público existentes nas. Alterar *IPConfig-1* para o nome que pretende atribuir para a configuração de IP primeira. Introduza os seguintes comandos:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Nota a *-primária* mudar. Ao atribuir várias configurações de IP a uma imagem, tem de ser atribuída uma configuração como a *principal*. Se não souber o nome de um recurso de endereço IP público existente, introduza o seguinte comando: Get-AzureRMPublicIPAddress | Formatar tabela nome, localização, o endereço IP, IpConfiguration

    Se a coluna **IPConfiguration** não tem um valor no resultado devolvido, o recurso de endereço IP público não está associado uma imagem existente e pode ser utilizado. Se a lista estiver em branco ou não existem não recursos disponíveis de endereço IP públicos, pode criar um utilizando o comando **Novo AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Endereços IP públicos tem uma taxa nominal. Para saber mais sobre preços de endereço IP, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Altere *IPConfig-2* para o nome que pretende conceder a segunda configuração IP e alterar *10.0.0.5* para um endereço IP válido não utilizado para a sub-rede que estiver a atribuir o NIC para. Introduza os seguintes comandos:

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Introduza o seguinte comando, se não souber o PI intervalo atribuído à sub-rede de endereços:

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Alterar *IPConfig-3* para o nome que pretende atribuir a para a configuração de IP de terceira e introduza os seguintes comandos:

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] Pode atribuir até 250 endereço IP privado a um NIC. Existe um limite para o número de endereços IP públicos que pode ser utilizada dentro de uma subscrição. Para obter mais informações, leia o artigo [limites de Azure](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Crie o NIC utilizando as configurações de IP definidas no passo anterior.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Anexe o NIC quando criar uma VM ao seguir os passos no artigo [criar uma VM](../virtual-machines/virtual-machines-windows-ps-create.md) . Apesar do artigo cria uma VM executar o Windows Server, os passos são os mesmos para um VM Linux, diferente de selecionar um sistema operativo diferente. Conclua os passos 1 a 3 deste artigo. Ignore os passos 4 e 5 e, em seguida, completa o passo 6 para criar um artigo VM.

    >[AZURE.WARNING] Passo 6 para criar um artigo VM falhará se alterado a variável de denominada $nic para algo no passo 6 deste artigo ou se ainda não o tiver concluído os passos anteriores deste artigo.

8. Vista de IP privado endereços esse DHCP Azure atribuído à NIC e o recurso de endereço IP público atribuído à NIC introduzindo o seguinte comando:

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Adicione manualmente todos os endereços IP privados (incluindo a página principal) para a configuração de TCP/IP no sistema operativo. 

**Windows**

1. A partir de uma linha de comandos, escreva *ipconfig/todos os*.  Se veja apenas o endereço IP privado *principal* (através do DHCP).
2. Em seguida, escreva *ncpa* na janela da linha de comandos. Isto irá abrir uma nova janela.
3. Abra as propriedades da **Ligação de rede Local**.
4. Faça duplo clique na versão de protocolo Internet (IPv4 4)
5. Selecione **utilizar o seguinte endereço IP** e introduza os seguinte valores:
    - **Endereço IP**: introduza o endereço IP privado *principal*
    - **Máscara de sub-rede**: conjunto com base na sua sub-rede aceda. Por exemplo, se a sub-rede é um /24 sub-rede, em seguida, a máscara de sub-rede é 255.255.255.0.
    - **Gateway predefinido**: O primeiro endereço IP na sub-rede. Se a sua sub-rede aceda for 10.0.0.0/24, o endereço IP gateway é 10.0.0.1.
    - Clique em **utilizar os seguintes endereços de servidor DNS** e introduza os seguinte valores:
        - **Servidor de DNS preferido:** Introduza 168.63.129.16 se não estiver a utilizar o seu servidor de DNS.  Se estiver, introduza o endereço IP do servidor de DNS.
    - Clique no botão **Avançadas** e adicione endereços IP adicionais. Adicione cada um dos secundários endereços IP privados listados no passo 8 ao NIC com a mesma sub-rede especificada para o endereço IP principal.
    - Clique em **OK** para fechar as definições de TCP/IP e, em seguida, **OK** novamente para fechar as definições da placa. Em seguida, isto irá restabelecer a ligação RDP.

6. A partir de uma linha de comandos, escreva *ipconfig/todos os*. Todos os endereços IP que adicionou são apresentados e DHCP está desativado.


**Linux (Ubuntu)**

1. Abra uma janela de terminal.
2. Certifique-se que o utilizador de raiz. Se não estiverem, pode fazê-lo utilizando o seguinte comando:

            sudo -i
3. Atualize o ficheiro de configuração da interface de rede (partindo do princípio 'eth0').
    - Mantenha o item de linha existente para dhcp. Isto irá configurar o endereço IP principal como utilizada para ser anterior.
    - Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

                cd /etc/network/interfaces.d/
                ls

        Deverá ver um ficheiro de .cfg.
4. Abra o ficheiro: vi *nome de ficheiro*.

    Deverá visualizar as linhas seguintes no final do ficheiro:

            auto eth0
            iface eth0 inet dhcp
5. Adicione as seguintes linhas após as linhas que existam neste ficheiro:

            iface eth0 inet static
            address <your private IP address here>
6. Guarde o ficheiro, utilizando o seguinte comando:

            :wq
7.  Repor a interface de rede com o seguinte comando:

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Execute ifdown e ifup na mesma linha se utilizar uma ligação remoto.
8. Verificar se que o endereço IP é adicionado à interface de rede com o seguinte comando:

            ip addr list eth0

    Deverá ver o endereço IP que adicionou como parte da lista.

**Linux (sistema Redhat, CentOS e outras pessoas)**

1. Abra uma janela de terminal.
2. Certifique-se que o utilizador de raiz. Se não estiverem, pode fazê-lo utilizando o seguinte comando:

            sudo -i
3. Introduza a palavra-passe e siga as instruções quando lhe for pedido. Assim que estiver o utilizador de raiz, navegue para a pasta de scripts de rede com o seguinte comando:

            cd /etc/sysconfig/network-scripts
4. Liste os ficheiros de ifcfg relacionados utilizando o seguinte comando:

            ls ifcfg-*

    Deverá ver *ifcfg eth0* como um dos ficheiros.
5. Copie o ficheiro *ifcfg eth0* e -lhe o nome *ifcfg-eth0:0* com o seguinte comando:

            cp ifcfg-eth0 ifcfg-eth0:0
6. Editar o *ifcfg-eth0:0* ficheiro com o seguinte comando:

            vi ifcfg-eth1
7. Alterar o dispositivo para o nome adequado no ficheiro; *eth0:0* neste caso, com o seguinte comando:

            DEVICE=eth0:0
8. Alterar o *ENDIP = YourPrivateIPAddress* linha para refletir o endereço IP.
9. Guarde o ficheiro com o seguinte comando:

            :wq
10. Reinicie os serviços de rede e certifique-se que as alterações estão bem sucedidas, executando os seguintes comandos:

            /etc/init.d/network restart
            Ipconfig

    Deverá ver o endereço IP que adicionou, *eth0:0*, na lista devolvida.

## <a name="add"></a>Adicionar endereços IP para uma VM existente

Conclua os passos seguintes para adicionar endereços IP adicionais a uma imagem existente:

1. Abra uma linha de comandos do PowerShell e conclua os restantes passos nesta secção dentro de uma única sessão PowerShell. Se ainda não tiver instalado e configurado o PowerShell, execute os passos no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) .

2. Altere os "valores" dos $Variables seguinte para o nome da imagem que pretende adicionar endereços IP para e o grupo de recursos e a localização que NIC existe no:

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Se não souber o nome da imagem que pretende alterar, introduza os seguintes comandos, em seguida, altere os valores das variáveis anterior:

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Criar uma variável e defina-o para a imagem existente, escrevendo o seguinte comando:

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Obter o ID da sub-rede NIC está ligado ao completar o [passo 3](#subnet) para criar uma VM com vários secção de endereços IP deste artigo.

5. Crie as configurações de IP que pretende adicionar à rede ao seguir as instruções no [passo 5](#ipconfigs) para criar uma VM com vários secção de endereços IP deste artigo.

6. Alterar *$IPConfigName4* para o nome da configuração de IP que criou no passo anterior. Para adicionar a configuração, introduza o seguinte comando:

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Para definir o NIC com a configuração de IP, introduza o seguinte comando:

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Adicione os endereços IP que adicionou à imagem para o sistema operativo VM ao seguir as instruções no [passo 9](#os) para criar uma VM com vários secção de endereços IP deste artigo.
