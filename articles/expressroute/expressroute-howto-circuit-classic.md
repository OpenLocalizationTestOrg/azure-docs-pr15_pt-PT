<properties
   pageTitle="Criar e modificar um circuito ExpressRoute utilizando o modelo de implementação clássica e PowerShell | Microsoft Azure"
   description="Este artigo explica os passos para criar e aprovisionar um circuito ExpressRoute. Este artigo também mostra-lhe como consultar o estado, atualizar ou eliminar ou deprovision seu circuito."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Criar e modificar um circuito ExpressRoute

> [AZURE.SELECTOR]
[Portal Azure - Gestor de recursos](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Gestor de recursos](expressroute-howto-circuit-arm.md)
[PowerShell - clássico](expressroute-howto-circuit-classic.md)

Este artigo explica os passos para criar um circuito Azure ExpressRoute utilizando os cmdlets do PowerShell e o modelo de implementação clássica. Este artigo também irá mostrar como verificar o estado, atualizar ou eliminar e deprovision um circuito ExpressRoute.

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>Antes de começar

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1. rever a pré-requisitos e artigos de fluxo de trabalho

Certifique-se de que reviu a [Pré-requisitos](expressroute-prerequisites.md) e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2. instalar as versões mais recentes dos módulos PowerShell do Azure 

Siga as instruções no [artigo como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter orientações passo a passo sobre como configurar o seu computador para utilizar os módulos Azure PowerShell.

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. Inicie sessão na sua conta Azure e selecione uma subscrição

1. Execute o cmdlet seguinte utilizando uma elevada do Windows PowerShell:

        Add-AzureAccount
2. Início de sessão no ecrã apresentado, inicie sessão na sua conta.

3. Obter uma lista das suas subscrições.

        Get-AzureSubscription
4. Selecione a subscrição que pretende utilizar.
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Criar e aprovisionar um circuito ExpressRoute

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1. Importe os módulos de PowerShell para ExpressRoute

 Se ainda não o tiver feito, tem de importar os módulos Azure e ExpressRoute para a sessão do PowerShell para poder começar a utilizar os cmdlets ExpressRoute. Importe os módulos desde a localização que foram instalados no seu computador local. Consoante o método que utilizou para instalar os módulos, a localização pode ser diferente que mostra o exemplo seguinte. Modificar o exemplo, se necessário.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. a obter a lista de fornecedores suportados, localizações e larguras de banda

Antes de criar um circuito ExpressRoute, tem a lista de fornecedores de conectividade suportados, localizações e opções de largura de banda.

O cmdlet do PowerShell `Get-AzureDedicatedCircuitServiceProvider` devolve estas informações, que irá utilizar nos passos posteriores:

    Get-AzureDedicatedCircuitServiceProvider

Verifique se o seu fornecedor de conectividade está listado aí. Uma vez que terá de-la mais tarde quando cria um circuito, tome nota as seguintes informações:

- Nome

- PeeringLocations

- BandwidthsOffered

Agora está pronto para criar um circuito ExpressRoute.         

### <a name="3-create-an-expressroute-circuit"></a>3. Crie um circuito ExpressRoute

O exemplo seguinte mostra como criar um Mbps 200 ExpressRoute circuito através de Equinix no silício do vale do. Se estiver a utilizar outro fornecedor e definições diferentes como, substitua essas informações quando faz o seu pedido.

>[AZURE.IMPORTANT] Seu circuito ExpressRoute vai ser faturado a partir do momento em que é emitida uma chave de serviço. Certifique-se de que efetuar esta operação quando estiver pronto para aprovisionar o circuito o fornecedor de conectividade.


Segue-se um pedido de exemplo para uma nova chave de serviço:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Em alternativa, se pretender criar um circuito ExpressRoute com o suplemento premium, utilize o exemplo seguinte. Consulte as [ExpressRoute FAQ](expressroute-faqs.md) para obter mais detalhes sobre o suplemento premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


A resposta irá conter a chave do serviço. Pode obter descrições detalhadas dos todos os parâmetros, executando o seguinte:

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4. listar todos os circuitos ExpressRoute

Pode executar o `Get-AzureDedicatedCircuit` comando para obter uma lista de todos os circuitos ExpressRoute que criou:


    Get-AzureDedicatedCircuit

A resposta será algo semelhante ao exemplo seguinte:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Pode obter estas informações em qualquer altura, utilizando o `Get-AzureDedicatedCircuit` cmdlet. Fazer a chamada sem quaisquer parâmetros de uma lista de todos os circuitos. A chave de serviço será listada no campo *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Pode obter descrições detalhadas dos todos os parâmetros, executando o seguinte:

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. enviar a chave do serviço para o seu fornecedor de conectividade para aprovisionamento


*ServiceProviderProvisioningState* fornece informações sobre o estado atual de aprovisionamento do lado do fornecedor de serviços. *Estado* fornece o estado no lado Microsoft. Para mais informações sobre circuito aprovisionamento Unidos, consulte o artigo de [fluxos de trabalho](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quando cria um novo circuito ExpressRoute, o circuito será no estado de seguinte:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


O circuito irá vá para o estado seguinte quando o fornecedor de conectividade é o processo de ativação-la por si:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Tem de ser um circuito ExpressRoute no estado seguinte para que possam utilizá-la:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. periodicamente verificar o estado e o estado da chave de circuito

Permite-lhe saber quando o seu fornecedor de tiver ativado o circuito. Depois de ter sido configurado o circuito, *ServiceProviderProvisioningState* será apresentado como *Provisioned* , conforme mostrado no exemplo seguinte:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7. Crie a sua configuração de encaminhamento

Referir a [ExpressRoute circuito configuração de encaminhamento (criar e modificar circuito peerings)](expressroute-howto-routing-classic.md) artigo para obter instruções passo a passo.

>[AZURE.IMPORTANT] Estas instruções apenas se aplicam a circuitos que são criados com outros fornecedores de serviço que oferecem serviços de conectividade de 2 a camada. Se estiver a utilizar um fornecedor de serviços que oferece geridos layer 3 serviços (normalmente uma IP VPN, como MPLS), o seu fornecedor de conectividade irá configurar e gerir o encaminhamento para si.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. ligação uma rede virtual para um circuito ExpressRoute

Em seguida, ligar uma rede virtual a sua circuito ExpressRoute. Consulte [circuitos ligar ExpressRoute para redes virtuais](expressroute-howto-linkvnet-classic.md) para instruções passo a passo. Se necessitar de criar uma rede virtual utilizando o modelo clássico de implementação para ExpressRoute, consulte o artigo [criar uma rede virtual para ExpressRoute](expressroute-howto-vnet-portal-classic.md) para obter instruções.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obter o estado de um circuito ExpressRoute

Pode obter estas informações em qualquer altura, utilizando o `Get-AzureCircuit` cmdlet. Fazer a chamada sem quaisquer parâmetros de uma lista de todos os circuitos.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Pode obter informações sobre um circuito ExpressRoute específico, passando a chave do serviço como um parâmetro para a chamada:

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Pode obter descrições detalhadas dos todos os parâmetros, executando o seguinte:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modificar um circuito ExpressRoute

Pode modificar determinadas propriedades de um circuito ExpressRoute sem que afetam a conectividade.

Pode efetuar as seguintes ações com sem tempo de inatividade:

- Ativar ou desativar um suplemento de premium ExpressRoute para sua circuito ExpressRoute.
- Aumente a largura de banda do seu circuito ExpressRoute. Tenha em atenção que mudar a largura de banda de um circuito não é suportada.
- Altere o plano de medição a partir de dados com tráfego limitado para dados ilimitado. Tenha em atenção que a alteração o plano de medição a partir de dados ilimitado aos dados com tráfego limitado não é suportada.
- Pode ativar e desativar *Permitir operações clássica*.

Consulte as [ExpressRoute FAQ](expressroute-faqs.md) para mais informações sobre limitações e limites.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Para ativar o suplemento do ExpressRoute premium

Pode ativar o suplemento de premium ExpressRoute para sua circuito existente utilizando o seguinte cmdlet do PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Seu circuito irão agora ter as funcionalidades de suplemento ExpressRoute premium ativadas. Tenha em atenção que recomendamos será iniciado faturação para a funcionalidade de suplemento premium assim que o comando tem executar com êxito.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Para desativar o suplemento do ExpressRoute premium

>[AZURE.IMPORTANT] Esta operação pode falhar se estiver a utilizar recursos que sejam maiores do que o que é permitido para o circuito padrão.

Tenha em atenção o seguinte procedimento:

- Tem de garantir que o número de redes virtuais ligado ao circuito é menor que 10 antes de alterar a partir do premium para padrão. Se não fazer isto, o seu pedido de atualização falhará e será faturada as taxas premium.

- Tem de desligar todas as redes virtuais noutras regiões geopolítica. Se não fazer isto, o seu pedido de atualização falhará e será faturada as taxas premium.

- Tabela de rotas tem de ser menos de 4.000 rotas para efectuado privado. Se o tamanho da tabela rota for maior que 4.000 rotas, a sessão BGP irá largue e não ser reactivada até que o número de prefixos anunciados ficar abaixo de 4.000.

Pode desativar o suplemento de premium ExpressRoute para sua circuito existente utilizando o seguinte cmdlet do PowerShell:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Para atualizar a largura de banda de circuito ExpressRoute

Consulte as [ExpressRoute FAQ](expressroute-faqs.md) para opções de largura de banda suportados para o seu fornecedor. Pode escolher qualquer tamanho maior que o tamanho do seu circuito existente desde que permite a porta física (no qual os seus circuito é criado).

>[AZURE.IMPORTANT] Não pode reduzir a largura de banda num circuito ExpressRoute sem interrupção. Mudar a largura de banda necessitam que deprovision o circuito ExpressRoute e, em seguida, volte a aprovisionar um novo circuito ExpressRoute.

Depois de decidir qual o tamanho que necessita, pode utilizar o seguinte comando para redimensionar o circuito:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Seu circuito irá ter sido dimensionado para cima no lado Microsoft. Terá de contactar o seu fornecedor de conectividade para actualizar configurações no respetivo lado para corresponder a esta alteração. Tenha em atenção que recomendamos será iniciado faturação o para a opção de largura de banda actualizada a partir deste momento no.

Se vir a seguinte mensagem de erro ao aumentar a largura de banda de circuito, existem significa sem largura de banda suficiente resta porta física onde o seu circuito existente é criado. Tem de eliminar este circuito e criar um novo circuito do tamanho que precisa. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Deprovisioning e eliminar um circuito ExpressRoute

Tenha em atenção o seguinte procedimento:

- Tem de desligar todas as redes virtuais a partir do circuito ExpressRoute para esta operação ser concluída com êxito. Verifique se tem as redes virtuais que estão ligadas a circuito se esta operação falhar.

- Se o ExpressRoute circuito fornecedor aprovisionamento estado do serviço for **aprovisionar** ou **Provisioned** tem de trabalhar com o seu fornecedor de serviços deprovision circuito no respetivo lado. Vamos irão continuar a reserva de recursos e faturar até o fornecedor de serviços conclui deprovisioning o circuito e notifica-nos.

- Se o fornecedor de serviços tem descontinuada circuito (o estado de aprovisionamento do fornecedor serviço está definido para **não aprovisionado**), em seguida, pode eliminar o circuito. Isto irá parar faturação para o circuito.

Pode eliminar o seu circuito ExpressRoute executando o seguinte comando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Próximos passos

Depois de criar o seu circuito, certifique-se de efetue o seguinte procedimento:

- [Criar e modificar o encaminhamento para sua circuito ExpressRoute](expressroute-howto-routing-classic.md)
- [Ligar a sua rede virtual a sua circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)
