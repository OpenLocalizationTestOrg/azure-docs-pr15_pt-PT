<properties
   pageTitle="Criar VNet efectuado utilizar modelos de Gestor de recursos | Microsoft Azure"
   description="Saiba como criar uma rede virtual efectuado utilizando os modelos no Gestor de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>Criar VNet efectuado utilizar modelos de Gestor de recursos

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet efectuado com os modelos de Gestor de recursos, siga os passos abaixo:

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.

    > [AZURE.NOTE] O cmdlet do PowerShell para gerir VNet efectuado é fornecido com [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. O texto abaixo mostra a definição de uma ligação de peering VNet para VNet1 para VNet2, com base no cenário acima. Copie o conteúdo abaixo e guardá-lo num ficheiro denominado VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. A secção abaixo mostra a definição de uma ligação de peering VNet para VNet2 para VNet1, com base no cenário acima.  Copie o conteúdo abaixo e guardá-lo num ficheiro denominado VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Conforme visto no modelo acima, existem algumas propriedades configuráveis para VNet efectuado:

  	|Opção|Descrição|Predefinido|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Se está ou não o espaço de endereços de um ponto a ponto VNet incluído como parte da etiqueta virtual_network.|Sim|
  	|AllowForwardedTraffic|Se o tráfego não proveniente de uma VNet dispõe é aceites ou ignorado.|N|
  	|AllowGatewayTransit|Permite que o peer VNet para utilizar o seu gateway VNet.|N|
  	|UseRemoteGateways|Utilize o gateway do seu ponto VNet. A ponto a ponto VNet tem de ter um gateway configurado e AllowGatewayTransit selecionado. Não pode utilizar esta opção se tiver um gateway configurado.|N|

    Cada ligação no VNet efectuado tem o conjunto de propriedades acima. Por exemplo, pode definir AllowVirtualNetworkAccess como verdadeiro para ligação efectuada VNet VNet1 para VNet2 e configurá-lo a FALSO para a ligação peering VNet na direção em outros.


4. Para implementar o ficheiro de modelo, pode executar o cmdlet AzureRmResourceGroupDeployment novo para criar ou atualizar a implementação. Para mais informações sobre como utilizar modelos de Gestor de recursos, consulte este [artigo](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Substitua o ficheiro de modelo e do nome do grupo de recursos conforme adequado.

    Abaixo está um exemplo baseado no cenário acima:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    Mostra o resultado:

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    Mostra o resultado:

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Depois de concluída a implementação, pode executar o cmdlet abaixo para ver o estado peering:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Mostra o resultado:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Depois de efectuado é estabelecida neste cenário, deverá conseguir iniciar as ligações a partir de qualquer máquina virtual para qualquer máquina virtual em ambos os VNets. Por predefinição, AllowVirtualNetworkAccess for VERDADEIRO e VNet efectuado irá aprovisionar as ACL inicial para permitir a comunicação entre VNets. Ainda pode aplicar regras de grupo (NSG) de segurança de rede para bloquear a conectividade entre sub-redes específicas ou máquinas virtuais obtenha o controlo de grão fino do access entre duas redes virtuais.  Para mais informações de criação de regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar um VNet efectuado entre subscrições, siga os passos abaixo:

1. Inicie sessão no Azure com privilégios de utilizador da conta a subscrição e execute o cmdlet seguinte:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Não se trata de um requisito efectuado pode ser estabelecida mesmo se os utilizadores elevar individualmente efectuados pedidos para os respetivos Vnets respetivos desde que os pedidos de correspondem. Adicionar um utilizador com privilégios do outros VNet como os utilizadores a VNet local torna mais fácil de fazer a configuração.

2. Inicie sessão no Azure com a conta com privilégios utilizador-B para subscrição-B e execute o cmdlet seguinte:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. No utilizador A estiverem sessão de início de sessão, executar este cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Eis como o ficheiro JSON é definido.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. Na sessão de início de sessão do utilizador-B, execute o cmdlet seguinte:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Eis como o ficheiro JSON é definido:

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Depois de efectuado é estabelecida neste cenário, deverá conseguir iniciar as ligações a partir de qualquer máquina virtual para qualquer máquina virtual de ambas as VNets entre diferentes subscrições.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, pode implementar o modelo de exemplo abaixo para estabelecer o VNet efectuado.  Terá de definir a propriedade AllowForwardedTraffic como True, que permite que o aparelho virtual de rede na VNet dispõe para enviar e receber tráfego.

    Eis o modelo para criar um VNet efectuado a partir do HubVNet para VNet1. Tenha em atenção que AllowForwardedTraffic está definido como falso.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Eis o modelo para criar um VNet efectuado a partir do VNet1 para HubVnet. Tenha em atenção que AllowForwardedTraffic está definida como verdadeiro.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Depois de efectuado é estabelecida, pode consultar a este [artigo](virtual-network-create-udr-arm-ps.md) para definir routes(UDR) definidos pelo utilizador para redirecionar o tráfego de VNet1 através de um virtual aparelho para utilizar as suas capacidades. Quando especificar o endereço salto seguinte nos artigos encaminhar, pode defini-lo para o endereço IP do aparelho virtual no peer VNet HubVNet.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para criar um efectuado entre redes virtuais a partir de modelos de implementação diferente, siga os passos abaixo:
1. O texto abaixo mostra a definição de uma ligação de peering VNet para VNET1 para VNET2 neste cenário. Apenas uma ligação é necessária para peer uma rede virtual clássica a uma rede virtual do Gestor de recursos Azure.

    Certifique-se colocar o seu ID da subscrição para onde se encontra a rede virtual clássico ou VNET2 e altere MyResouceGroup para o nome do grupo de recursos adequado.

    {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parâmetros": {}, "variáveis": {}, "recursos": [{"apiVersion": "2016-06-01", "tipo": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings", "nome": "VNET1/LinkToVNET2", "localização": "[resourceGroup () .location]", "Propriedades": {"allowVirtualNetworkAccess": VERDADEIRO, "allowForwardedTraffic": FALSO, "allowGatewayTransit": FALSO, "useRemoteGateways": FALSO, "remoteVirtualNetwork": {"id": "[resourceId (' Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"}}}]}

2. Para implementar o ficheiro de modelo, execute o cmdlet seguinte para criar ou atualizar a implementação.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Depois da implementação com êxito, pode executar o cmdlet seguinte para ver o estado peering:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Depois de efectuado é estabelecida entre um VNet clássica e um Gestor de recursos VNet, deverá poder iniciar ligações a partir de qualquer máquina virtual VNET1 a qualquer máquina virtual no VNET2 e vice versa.
