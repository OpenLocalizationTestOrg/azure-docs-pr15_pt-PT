<properties
   pageTitle="Criar e modificar um circuito ExpressRoute utilizando o Gestor de recursos e PowerShell | Microsoft Azure"
   description="Este artigo descreve como criar, aprovisionar, certifique-se, atualizar, eliminar e deprovision um circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito ExpressRoute

> [AZURE.SELECTOR]
[Portal Azure - Gestor de recursos](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestor de recursos](expressroute-howto-circuit-arm.md)
[PowerShell - clássico](expressroute-howto-circuit-classic.md)


Este artigo descreve como criar um circuito Azure ExpressRoute ao utilizar cmdlets do Windows PowerShell e o modelo de implementação do Azure o Gestor de recursos. Este artigo também irá mostrar como verificar o estado do circuito, atualizar, ou eliminar e deprovision-lo.

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Antes de começar


- Obter a versão mais recente do Azure PowerShell módulos (, pelo menos, versão 1.0). Para obter orientações passo a passo sobre como configurar o seu computador para utilizar os PowerShell módulos, siga as instruções no [artigo como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

- Reveja os [Pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

## <a name="create-and-provision-an-expressroute-circuit"></a>Criar e aprovisionar um circuito ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Inicie sessão na sua conta Azure e selecione a sua subscrição

Para começar a sua configuração, inicie sessão sua conta Azure. Para mais informações sobre o PowerShell, consulte o artigo [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md). Utilize os exemplos seguintes para ajudá-lo ligar:

    Login-AzureRmAccount

Verificar as subscrições para a conta:

    Get-AzureRmSubscription

Selecione a subscrição à qual pretende criar um circuito ExpressRoute para:

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. a obter a lista de fornecedores suportados, localizações e larguras de banda

Antes de criar um circuito ExpressRoute, tem a lista de fornecedores de conectividade suportados, localizações e opções de largura de banda.

O cmdlet do PowerShell `Get-AzureRmExpressRouteServiceProvider` devolve estas informações, que irá utilizar nos passos posteriores:

    Get-AzureRmExpressRouteServiceProvider

Verifique se o seu fornecedor de conectividade está listado aí. Uma vez que terá de-la mais tarde quando cria um circuito, tome nota as seguintes informações:

- Nome

- PeeringLocations

- BandwidthsOffered

Agora está pronto para criar um circuito ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. Crie um circuito ExpressRoute

Se ainda não tiver um grupo de recursos, terá de criar um antes de criar o seu circuito ExpressRoute. Pode fazê-lo executando o seguinte comando:


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


O exemplo seguinte mostra como criar um Mbps 200 ExpressRoute circuito através de Equinix no silício do vale do. Se estiver a utilizar outro fornecedor e definições diferentes como, substitua essas informações quando faz o seu pedido. Segue-se um pedido de exemplo para uma nova chave de serviço:

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Certifique-se de que especificar a camada SKU correta e família SKU:

- Camadas SKU determina se um padrão de ExpressRoute ou um suplemento de premium ExpressRoute está ativado. Pode especificar *padrão* para obter o SKU ou *Premium* padrão para o suplemento premium.

- Família SKU determina o tipo de faturação. Pode especificar *Metereddata* para um plano de dados com tráfego limitado e *Unlimiteddata* para um plano de dados ilimitado. Tenha em atenção que pode alterar o tipo de faturação do *Metereddata* para *Unlimiteddata*, mas não é possível alterar o tipo de *Unlimiteddata* para *Metereddata*.


>[AZURE.IMPORTANT] Seu circuito ExpressRoute vai ser faturado a partir do momento em que é emitida uma chave de serviço. Certifique-se de que efetuar esta operação quando estiver pronto para aprovisionar o circuito o fornecedor de conectividade.

A resposta contém a chave do serviço. Pode obter descrições detalhadas dos todos os parâmetros, executando o seguinte:


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4. listar todos os ExpressRoute circuitos

Para obter uma lista de todos os circuitos ExpressRoute que criou, execute o `Get-AzureRmExpressRouteCircuit` comando:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

A resposta terá uma aspeto semelhante ao exemplo seguinte:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Pode obter estas informações em qualquer altura, utilizando o `Get-AzureRmExpressRouteCircuit` cmdlet. Fazer a chamada com sem parâmetros de uma lista de todos os circuitos. A chave de serviço será listada no campo *ServiceKey* :


    Get-AzureRmExpressRouteCircuit


A resposta terá uma aspeto semelhante ao exemplo seguinte:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Pode obter descrições detalhadas dos todos os parâmetros, executando o seguinte:


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. enviar a chave do serviço para o seu fornecedor de conectividade para aprovisionamento

*ServiceProviderProvisioningState* fornece informações sobre o estado atual de aprovisionamento do lado do fornecedor de serviços. Estado fornece o estado no lado Microsoft. Para mais informações sobre circuito aprovisionamento Unidos, consulte o artigo de [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando cria um novo circuito ExpressRoute, o circuito será no estado de seguinte:


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



O circuito irá alterar o estado seguintes quando o fornecedor de conectividade é o processo de ativação-la por si:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Para que possam utilizar um circuito ExpressRoute, tem de ser no estado de seguinte:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periodicamente verificar o estado e o estado da chave de circuito

Verificar o estado e o estado da chave de circuito permite-lhe saber quando o seu fornecedor de tiver ativado o circuito. Depois do circuito ter sido configurado, *ServiceProviderProvisioningState* aparece como *Provisioned*, conforme mostrado no exemplo seguinte:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


A resposta terá uma aspeto semelhante ao exemplo seguinte:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                    }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Crie a sua configuração de encaminhamento

Para obter instruções passo a passo, consulte o artigo de [configuração de encaminhamento de circuito ExpressRoute](expressroute-howto-routing-arm.md) para criar e modificar circuito peerings.


>[AZURE.IMPORTANT] Estas instruções apenas se aplicam a circuitos que são criados com outros fornecedores de serviço que oferecem serviços de conectividade de 2 a camada. Se estiver a utilizar um fornecedor de serviços que oferece geridos layer 3 serviços (normalmente uma IP VPN, como MPLS), o seu fornecedor de conectividade irá configurar e gerir o encaminhamento para si.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. ligação uma rede virtual para um circuito ExpressRoute

Em seguida, ligar uma rede virtual a sua circuito ExpressRoute. Utilize o artigo [Ligar redes virtuais ExpressRoute circuitos](expressroute-howto-linkvnet-arm.md) quando trabalha com o modelo de implementação do Gestor de recursos.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obter o estado de um circuito ExpressRoute

Pode obter estas informações em qualquer altura, utilizando o `Get-AzureRmExpressRouteCircuit` cmdlet. Fazer a chamada com sem parâmetros de uma lista de todos os circuitos.

    Get-AzureRmExpressRouteCircuit


A resposta será semelhante ao exemplo seguinte:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Pode obter informações sobre um circuito ExpressRoute específico, passando o nome do grupo de recursos e o nome de circuito como um parâmetro para a chamada:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


A resposta terá uma aspeto semelhante ao exemplo seguinte:


    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Pode obter descrições detalhadas dos todos os parâmetros, executando o seguinte:

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Modificar um circuito ExpressRoute

Pode modificar determinadas propriedades de um circuito ExpressRoute sem que afetam a conectividade.

Pode efetuar as seguintes ações com sem tempo de inatividade:

- Ativar ou desativar um suplemento de premium ExpressRoute para sua circuito ExpressRoute.
- Aumente a largura de banda do seu circuito ExpressRoute. Tenha em atenção que mudar a largura de banda de um circuito não é suportada.
- Altere o plano de medição a partir de dados com tráfego limitado para dados ilimitado. Tenha em atenção que a alteração o plano de medição a partir de dados ilimitado aos dados com tráfego limitado não é suportada.
-  Pode ativar e desativar *Permitir operações clássica*.

Para mais informações sobre os limites e limitações, consulte as [ExpressRoute FAQ](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o suplemento do ExpressRoute premium

Pode ativar o suplemento de premium ExpressRoute para sua circuito existente utilizando o fragmento de PowerShell que se segue:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


O circuito irão agora ter as funcionalidades de suplemento ExpressRoute premium ativadas. Tenha em atenção que recomendamos vai começar a faturação para a funcionalidade de suplemento premium assim que o comando tem executar com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o suplemento do ExpressRoute premium

>[AZURE.IMPORTANT] Esta operação pode falhar se estiver a utilizar recursos que sejam maiores do que o que é permitido para o circuito padrão.

Tenha em atenção o seguinte procedimento:

- Antes de alterar a partir do premium para padrão, certifique-se de que o número de redes virtuais que estão ligadas a circuito é menor que 10. Se não fazer isto, falha do seu pedido de atualização e podemos irá cobrar a taxas de prémio.

- Tem de desligar todas as redes virtuais noutras regiões geopolítica. Se não fazer isto, o pedido de atualização falhará e podemos irá cobrar a taxas de prémio.

- Tabela de rotas tem de ser menos de 4.000 rotas para efectuado privado. Se o tamanho da tabela rota for maior que 4.000 rotas, a sessão BGP coloca e não ser reactivada até que o número de prefixos anunciados ficar abaixo de 4.000.

Pode desativar o suplemento de premium ExpressRoute para o circuito existente utilizando o seguinte cmdlet do PowerShell:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda de circuito ExpressRoute

Para opções de largura de banda suportados para o seu fornecedor, consulte as [ExpressRoute FAQ](expressroute-faqs.md). Pode escolher qualquer tamanho maior que o tamanho do seu circuito existente.

>[AZURE.IMPORTANT] Não pode reduzir a largura de banda num circuito ExpressRoute sem interrupção. Mudar a largura de banda requer que deprovision o circuito ExpressRoute e, em seguida, volte a aprovisionar um novo circuito ExpressRoute.

Depois de decidir qual o tamanho que precisa, utilize o seguinte comando para redimensionar o circuito:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Seu circuito vai ser dimensionado para cima no lado Microsoft. Em seguida, terá de contactar o seu fornecedor de conectividade para actualizar configurações no respetivo lado para corresponder a esta alteração. Depois de efetuar esta notificação, vamos começar faturação o para a opção de largura de banda actualizada.


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Para mover o SKU a partir com tráfego limitado para ilimitado

Pode alterar o SKU num circuito ExpressRoute utilizando o fragmento de PowerShell que se segue:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Para controlar o acesso a clássica e ambientes de Gestor de recursos  

Rever as instruções em [Mover ExpressRoute circuitos de clássico ao modelo de implementação de Gestor de recursos](expressroute-howto-move-arm.md).  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Deprovisioning e eliminar um circuito ExpressRoute

Tenha em atenção o seguinte procedimento:

- Tem de desligar todas as redes virtuais a partir do circuito ExpressRoute. Se esta operação falhar, verifique se as redes virtuais estão ligadas ao circuito.

- Se o ExpressRoute circuito fornecedor aprovisionamento estado do serviço for **aprovisionar** ou **Provisioned** tem de trabalhar com o seu fornecedor de serviços deprovision circuito no respetivo lado. Vamos irão continuar a reserva de recursos e faturar até o fornecedor de serviços conclui deprovisioning o circuito e notifica-nos.

- Se o fornecedor de serviços tem descontinuada circuito (o estado de aprovisionamento do fornecedor serviço está definido para **não aprovisionado**), em seguida, pode eliminar o circuito. Isto irá parar faturação para o circuito

Pode eliminar o seu circuito ExpressRoute executando o seguinte comando:

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>Próximos passos

Depois de criar o seu circuito, certifique-se de efetue o seguinte procedimento:

- [Criar e modificar o encaminhamento para sua circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Ligar a sua rede virtual a sua circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
