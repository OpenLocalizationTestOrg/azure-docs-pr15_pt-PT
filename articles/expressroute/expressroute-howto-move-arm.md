<properties
   pageTitle="Deslocar-se ExpressRoute circuitos de clássico para o Gestor de recursos | Microsoft Azure"
   description="Esta página descreve como mover um circuito clássico para o modelo de implementação do Gestor de recursos."
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


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mova ExpressRoute circuitos a partir do clássico para o modelo de implementação de Gestor de recursos

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

- Tem a versão mais recente dos módulos Azure PowerShell (, pelo menos, versão 1.0).
- Certifique-se de que reviu a [Pré-requisitos](expressroute-prerequisites.md), [Encaminhamento requisitos](expressroute-routing.md)e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Antes de ainda mais que antecede, reveja as informações fornecidas em [Mover um circuito ExpressRoute a partir do clássico para o Gestor de recursos](expressroute-move.md). Certifique-se de totalmente compreensão dos limites e limitações do que é possível.
- Se pretender mover um circuito Azure ExpressRoute do modelo clássico de implementação para o modelo de implementação do Gestor de recursos do Azure, tem de ter o circuito totalmente configurado e operacional no modelo de implementação clássica.
- Certifique-se de que tem um grupo de recursos que foi criado no modelo de implementação de Gestor de recursos.

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Mova o circuito ExpressRoute para o modelo de implementação do Gestor de recursos

Terá de mover um circuito ExpressRoute para o modelo de implementação do Gestor de recursos para que pode utilizá-lo ao longo dos separadores e os modelos de implementação do Gestor de recursos. Pode fazê-lo executando os seguintes comandos do PowerShell.

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Passo 1: Recolher detalhes de circuito do modelo clássico de implementação

Necessita de reunir informações sobre o circuito ExpressRoute pela primeira vez.

Inicie sessão no ambiente clássico do Azure e reúna a chave do serviço. Pode utilizar o fragmento de PowerShell que se segue para recolher as informações:

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Copie a **chave do serviço** do circuito que pretende deslocar-se ao longo para o modelo de implementação do Gestor de recursos.

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>Passo 2: Iniciar sessão para o ambiente do Gestor de recursos e criar um novo grupo de recursos

Pode criar um novo grupo de recursos utilizando o fragmento que se segue:

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Também pode utilizar um grupo de recursos existente se já tiver um.

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Passo 3: Mova o circuito ExpressRoute para o modelo de implementação do Gestor de recursos

Agora está pronto para mover ao longo do seu circuito ExpressRoute do clássico para o modelo de implementação do Gestor de recursos. Reveja as informações fornecidas em [Mover um circuito ExpressRoute de clássico ao modelo de implementação de Gestor de recursos](expressroute-move.md) antes de continuar.

Pode fazê-lo executando o seguinte fragmento de:

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Depois do mover for concluída, o novo nome que está listado na anterior cmdlet será utilizado para o recurso de endereço. O circuito essencialmente será possível mudar o nome.

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>Ativar um circuito ExpressRoute para ambos os modelos de implementação

Terá de mover o circuito ExpressRoute para o modelo de implementação do Gestor de recursos antes de controlar o acesso ao modelo de implementação.

Execute o cmdlet seguinte para permitir o acesso a ambos os modelos de implementação:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Após esta operação terminou com êxito, será capaz de ver o circuito no modelo de implementação clássica.

Execute o seguinte para obter detalhes do circuito ExpressRoute:

    get-azurededicatedcircuit

Tem de conseguir ver a chave do serviço listada. Agora pode gerir ligações para o circuito ExpressRoute com os seus comandos de modelo clássico de implementação padrão para VNets clássica e os seus comandos processador padrão para processador VNETs. Os seguintes artigos irão guiá-lo através de como gerir as ligações para o circuito ExpressRoute:

- [Ligar a sua rede virtual a sua circuito ExpressRoute no modelo de implementação de Gestor de recursos](expressroute-howto-linkvnet-arm.md)
- [Ligar a sua rede virtual ao seu circuito ExpressRoute no modelo de implementação clássico](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>Desativar o circuito ExpressRoute para o modelo de implementação clássico

Execute o cmdlet seguinte para desativar o acesso ao modelo de implementação clássico:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Após esta operação terminou com êxito, não conseguir ver o circuito no modelo de implementação clássica.

## <a name="next-steps"></a>Próximos passos

Depois de criar o seu circuito, certifique-se de efetue o seguinte procedimento:

- [Criar e modificar o encaminhamento para sua circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Ligar a sua rede virtual a sua circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
