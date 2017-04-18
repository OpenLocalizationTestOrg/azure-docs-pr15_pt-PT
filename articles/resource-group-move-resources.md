<properties 
    pageTitle="Mova recursos para o novo grupo de recursos | Microsoft Azure" 
    description="Utilize o Gestor de recursos do Azure para mover recursos para um novo grupo de recursos ou subscrição." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/21/2016" 
    ms.author="tomfitz"/>

# <a name="move-resources-to-new-resource-group-or-subscription"></a>Mova recursos para o novo grupo de recursos ou a subscrição

Este tópico mostra-lhe como pode mover recursos para uma nova subscrição ou um novo grupo de recursos na mesma subscrição. Pode utilizar o portal, PowerShell, Azure clip ou REST API para mover o recurso. As operações de mover neste tópico estão disponíveis para si sem qualquer assistência a partir do Azure suporte.

Normalmente, mover recursos se decidir que:

- Para fins de faturaçãohttps, necessita de um recurso encontram-se numa subscrição diferente.
- Um recurso partilha já não o ciclo de vida mesmo como recursos que estava anteriormente agrupado com. Pretende movê-lo para um novo grupo de recursos para que possa gerir esse recurso separadamente a partir de outros recursos.

Quando mover recursos, o grupo de origem e o grupo de destino estão bloqueadas durante a operação. Escrever e operações de eliminar os grupos bloqueados até a mudança estar concluída.

Não é possível alterar a localização do recurso. Mover um recurso apenas move-a para um novo grupo de recursos. O novo grupo de recursos pode ter uma localização diferente, mas que não altera a localização do recurso.

> [AZURE.NOTE] Este artigo descreve como mover recursos dentro de um existente Azure conta oferta. Se realmente quiser alterar a sua conta Azure oferecer (como atualizar a partir da repartição previamente pagar) enquanto continua a trabalhar com os recursos existentes, consulte o artigo [mudar a sua subscrição Azure noutra oferta](billing-how-to-switch-azure-offer.md). 

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de passar recursos

Existem alguns passos importantes para efetuar antes de passar um recurso. Verificando estas condições, pode evitar erros.

1. O serviço tem de ativar a capacidade de mover recursos. Consulte a lista abaixo para obter informações sobre quais [os serviços permitem mover recursos](#services-that-enable-move).
1. As subscrições de origem e destino tem de existir dentro ao mesmo [inquilino do Active Directory](./active-directory/active-directory-howto-tenant.md). Para mover para um novo inquilino, contacte o suporte.
2. A subscrição de destino tem de estar registada para o fornecedor de recursos do recurso a ser movido. Caso contrário, receber um erro a informar que a **subscrição não está registada para um tipo de recurso**. Poderá encontrar este problema ao mover um recurso para uma nova subscrição, mas que nunca tiver sido utilizada subscrição com esse tipo de recurso. Para saber como verificar o estado de registo e registar fornecedores de recursos, consulte o artigo [fornecedores de recursos e tipos de](../resource-manager-supported-services.md#resource-providers-and-types).
4. Se está a mover a aplicação de serviço de aplicação, reviu as [limitações da aplicação de serviço](#app-service-limitations).
4. Se está a mover recursos associados a serviços de recuperação, reviu as [limitações de serviços de recuperação](#recovery-services-limitations)
5. Se está a mover implementados através de modelo clássico de recursos, rever a [limitações de implementação de clássica](#classic-deployment-limitations).

## <a name="when-to-call-support"></a>Quando ligar para o suporte

Pode mover a maior parte dos recursos através de operações de gestão personalizada mostrados neste tópico. Utilize as operações de gestão personalizada para:

- Deslocar-se os recursos de Gestor de recursos.
- Deslocar-se recursos clássicos de acordo com as [limitações de implementação clássica](#classic-deployment-limitations). 

Ligar para o suporte quando precisar de:

- Deslocar-se os recursos para uma nova conta Azure (e o inquilino do Active Directory).
- Mover recursos clássicos, mas está a ter problemas com as limitações.

## <a name="services-that-enable-move"></a>Serviços que permitem aos mover

Por agora, os serviços que permitem o mover para um novo grupo de recursos e de subscrição são:

- Gestão de API
- Aplicações de serviço de aplicação (web apps) - consulte [limitações da aplicação de serviço](#app-service-limitations)
- Automatização
- Batch
- CDN
- Serviços em nuvem - consulte [limitações de implementação de clássico](#classic-deployment-limitations)
- Fábrica de dados
- DNS
- DocumentDB
- HDInsight clusters
- IoT concentradores
- Cofre chave 
- Serviços de multimédia
- Cativação móvel
- Notificação concentradores
- Informações operacionais avançadas
- Redis Cache
- Programador
- Pesquisa
- Serviço Bus
- Armazenamento
- Armazenamento (clássico) - consulte [limitações de implementação de clássico](#classic-deployment-limitations)
- Servidor de base de dados SQL - da base de dados e o servidor tem de estar no mesmo grupo de recursos. Quando mover SQL server, todos das suas bases de dados também são movidos.
- Máquinas virtuais - no entanto, faz não suporta deslocar-se para uma nova subscrição quando os seus certificados são armazenados num cofre chave
- Máquinas virtuais (clássico) - consulte [limitações de implementação de clássico](#classic-deployment-limitations)
- Redes virtuais

## <a name="services-that-do-not-enable-move"></a>Serviços de que não ativar mover

Os serviços que atualmente não permitem mover um recurso são:

- Gateway de aplicação
- Informações de aplicação
- Encaminhar Express
- Serviços de recuperação do Cofre de palavras - também não mover os recursos de cluster, rede e armazenamento associados com o serviços de recuperação cofre, consulte [limitações de serviços de recuperação](#recovery-services-limitations).
- Máquinas virtuais com armazenados num cofre de chave do certificado
- Conjuntos de escala máquinas virtuais
- Redes virtuais (clássico) - consulte [limitações de implementação de clássico](#classic-deployment-limitations)
- VPN Gateway

## <a name="app-service-limitations"></a>Limitações da aplicação de serviço

Quando trabalha com aplicações de serviço de aplicação, não pode mover apenas um plano de serviço de aplicação. Para mover as aplicações de serviço de aplicação, as opções são:

- Mova o plano de serviço de aplicação e todos os outros recursos da aplicação de serviço nesse grupo de recursos para um novo grupo de recursos que ainda não tiver recursos de aplicação de serviço. Este requisito significa que terá de mover mesmo os recursos de aplicação de serviço que não estão associados o plano de serviço de aplicação. 
- Deslocar-se as aplicações para um grupo de recursos diferentes, mas mantenha todos os planos de aplicação de serviço no grupo de recursos original.

Se o seu grupo de recursos original também inclui um recurso de informações da aplicação, não pode mover esse recurso, uma vez que as informações de aplicação atualmente não ativar a operação de mover. Se incluir o recurso de informações da aplicação ao mover as aplicações de serviço de aplicação, a operação de mover toda falha. No entanto, as informações de aplicação e o plano de aplicação de serviço não necessita residem no mesmo grupo de recursos como a aplicação para a aplicação a funcionar corretamente.

Por exemplo, se contiver do seu grupo de recursos:

- **Web-a** que está associado **plano-a** e **aplicação-informações-a**
- **web-b** que está associado **plano-b** e **aplicação-informações-b**

As opções são:

- Mover **um web**, **plano-a**, **web b**e **plano-b**
- Mover **web-a** e **web b**
- Mover **um web**
- Mover **web-b**

Todas as outras combinações envolvem em mover um tipo de recurso que não é possível mover (aplicação informações) ou sair do atrás de um tipo de recurso que não pode ser deixado atrás quando mover um plano de serviço de aplicação (qualquer tipo de recurso de aplicação de serviço).

Se a aplicação web do se encontra num grupo de recursos diferentes que o seu plano de serviço de aplicação, mas que pretende mover ambos a um novo grupo de recursos, tem de executar o comando mover em dois passos. Por exemplo:

- **Web-a** se encontra no **grupo web**
- **plano-a** se encontra no **plano de grupo**
- Pretende **web-a** e **plano-a** para residem no **grupo combinados**

Para realizar este mover, execute duas operações separadas mover pela seguinte ordem:

1. Deslocar-se a **web-** ao **grupo de plano**
2. Mover **web-a** e **plano-** ao **grupo combinadas**.

Pode mover um certificado de serviço de aplicação para um novo grupo de recursos ou de uma subscrição sem problemas. No entanto, se a sua aplicação web incluir um certificado SSL que comprou externamente e carregado para a aplicação, tem de eliminar o certificado antes de passar a aplicação web. Por exemplo, pode efetuar os seguintes passos:

1. Eliminar o certificado carregado a partir da aplicação web
2. Deslocar-se a aplicação web
3. Carregar o certificado para a aplicação web

## <a name="recovery-services-limitations"></a>Limitações de serviços de recuperação

Deslocar-se de que não está ativado para armazenamento, rede, ou calcular recursos utilizados para configurar a recuperação de falhas com Azure recuperação de Site. 

Por exemplo, suponha que configurou a replicação do seu máquinas no local para uma conta de armazenamento (Storage1) e pretende que o computador protegido para que diz respeito após activação pós-falha ao Azure como uma máquina de virtual (VM1) anexada a uma rede virtual (Network1). Não pode mover qualquer um destes recursos Azure - Storage1, VM1 e Network1 - grupos de recursos dentro da mesma subscrição ou entre subscrições.

## <a name="classic-deployment-limitations"></a>Limitações de implementação clássico

As opções para mover recursos implementados através do modelo clássico diferem com base na se que está a mover os recursos dentro de uma subscrição ou para uma nova subscrição. 

### <a name="same-subscription"></a>Mesma subscrição

Quando mover recursos de grupo de um recurso para outro grupo de recursos dentro da mesma subscrição, as restrições seguintes aplicam-se:

- Não podem ser movidas redes virtuais (clássicos).
- Máquinas virtuais (clássico) têm de ser movidas com o serviço de nuvem. 
- Só pode ser movido serviço na nuvem, quando o comando Mover inclui todas as suas máquinas virtuais.
- Serviço em só nuvem pode ser movido uma vez.
- Apenas a uma conta de armazenamento (clássica) pode ser movida uma vez.
- Conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou num serviço na nuvem.

Para mover clássicos recursos a um novo grupo de recursos dentro da mesma subscrição, utilize as operações de mover padrão através do [portal](#use-portal), [Azure PowerShell](#use-powershell), [Azure clip](#use-azure-cli)ou [REST API](#use-rest-api). Utilize as mesmas operações à medida que utiliza para mover os recursos de Gestor de recursos.

### <a name="new-subscription"></a>Nova subscrição

Quando mover recursos para uma nova subscrição, as restrições seguintes aplicam-se:

- Todos os recursos clássicos na subscrição têm de ser movidos na mesma operação.
- A subscrição de destino não pode conter quaisquer outros recursos clássicos.
- Só pode ser pedida a mover através de um REST API separada para clássico move o cursor. Os comandos de mover padrão do Gestor de recursos não funcionam quando mover clássicos recursos para uma nova subscrição.

Para mover clássicos recursos para uma nova subscrição, tem de utilizar operações de restantes são específicas para recursos clássicos. Execute os seguintes passos para mover clássicos recursos para uma nova subscrição.

1. Verifique se a subscrição de origem pode participar uma subscrição de publicação em mover. Utilize a operação seguinte:

         POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
     No corpo do pedido, incluem:

         {
           "role": "source"
         }

     A resposta a operação de validação de é no seguinte formato:

         {
           "status": "{status}",
           "reasons": [
             "reason1",
             "reason2"
           ]
         }

2. Verifique se a subscrição de destino pode participar uma subscrição de publicação em mover. Utilize a operação seguinte:

         POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01

     No corpo do pedido, incluem:

         {
           "role": "target"
         }

     A resposta é o mesmo formato como a validação de subscrição de origem.

3. Se ambas as subscrições passam validação, mova todos os recursos clássicos de uma subscrição para outra subscrição com a operação seguinte:

         POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

    No corpo do pedido, incluem:

         {
           "target": "/subscriptions/{target-subscription-id}"
         }

A operação pode executar durante vários minutos. 

## <a name="use-portal"></a>Utilizar o portal

Para mover recursos para um novo grupo de recursos na **mesma subscrição**, selecione o grupo de recursos que contenham esses recursos e, em seguida, selecione o botão **Mover** .

![Deslocar-se de recursos](./media/resource-group-move-resources/edit-rg-icon.png)

Em alternativa, para mover recursos para uma **nova subscrição**, selecione o grupo de recursos que contenham esses recursos e, em seguida, selecione o ícone de subscrição de edição.

![Deslocar-se de recursos](./media/resource-group-move-resources/change-subscription.png)

Selecione os recursos para mover e o grupo de recursos de destino. Confirme que necessita de atualizar scripts para estes recursos e selecione **OK**. Se tiver seleccionado o ícone de subscrição de edição no passo anterior, também tem de selecionar a subscrição de destino.

![Selecionar destino](./media/resource-group-move-resources/select-destination.png)

No **notificações**, verá que a operação de mover está em execução.

![Mostrar o estado de mover](./media/resource-group-move-resources/show-status.png)

Quando ela estar concluída, o utilizador será notificado do resultado.

![Mostrar o resultado de mover](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Utilizar o PowerShell

Para mover os recursos existentes para outra subscrição ou grupo de recursos, utilize o comando **Mover AzureRmResource** .

O primeiro exemplo mostra como mover um recurso para um novo grupo de recursos.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

O segundo exemplo mostra como mover vários recursos para um novo grupo de recursos.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para mover para uma nova subscrição, inclua um valor para o parâmetro **DestinationSubscriptionId** .

São-lhe pedido para confirmar que pretende mover os recursos especificados.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## <a name="use-azure-cli"></a>Utilizar o clip Azure

Para mover os recursos existentes para outra subscrição ou grupo de recursos, utilize o comando **Mover azure recurso** . Tem de fornecer os ids de recursos dos recursos para mover. Pode obter ids de recurso com o seguinte comando:

    azure resource list -g sourceGroup --json

Que devolve o seguinte formato:

    [
      {
        "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
        "name": "storagedemo",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "southcentralus",
        "tags": {},
        "kind": "Storage",
        "sku": {
          "name": "Standard_RAGRS",
          "tier": "Standard"
        }
      }
    ]

O exemplo seguinte mostra como mover uma conta de armazenamento para um novo grupo de recursos. O parâmetro **-i** , fornece que uma lista separados por vírgulas do id do recurso estiverem mover.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
    
São-lhe pedido para confirmar que pretende mover o recurso especificado.

## <a name="use-rest-api"></a>Utilizar a REST API

Para mover os recursos existentes para outra subscrição ou grupo de recursos, execute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

No corpo do pedido, especifique o grupo de recursos de destino e os recursos para se deslocar. Para mais informações sobre a operação de resto mover, consulte o artigo [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).


## <a name="next-steps"></a>Próximos passos
- Para saber mais sobre os cmdlets do PowerShell para gerir a sua subscrição, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos](powershell-azure-resource-manager.md).
- Para saber mais sobre comandos do Azure clip para gerir a sua subscrição, consulte o artigo [utilizar o clip do Azure com o Gestor de recursos](xplat-cli-azure-resource-manager.md).
- Para saber mais sobre funcionalidades portais para gerir a sua subscrição, consulte o artigo [utilizar o portal Azure para gerir os recursos](./azure-portal/resource-group-portal.md).
- Para obter informações sobre como aplicar uma organização lógica para os recursos, consulte o artigo [utilizar etiquetas para organizar os seus recursos](resource-group-using-tags.md).
