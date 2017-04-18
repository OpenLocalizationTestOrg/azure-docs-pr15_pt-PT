<properties 
    pageTitle="Gerir Azure pesquisa com os scripts de Powershell | Microsoft Azure | Serviço de pesquisa alojado na nuvem" 
    description="Gerir o serviço de pesquisa do Azure com scripts de PowerShell. Criar ou actualizar um serviço de pesquisa do Azure e gerir as teclas de administração de pesquisa do Azure" 
    services="search" 
    documentationCenter="" 
    authors="seansaleh" 
    manager="mblythe" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="powershell" 
    ms.date="08/15/2016" 
    ms.author="seasa"/>

# <a name="manage-your-azure-search-service-with-powershell"></a>Gerir o seu serviço de pesquisa do Azure com PowerShell
> [AZURE.SELECTOR]
- [Portal](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST API](search-get-started-management-api.md)

Este tópico descreve os comandos do PowerShell para efetuar muitas das tarefas de gestão de serviços de pesquisa do Azure. Vamos irá guiá-lo através da criação de um serviço de pesquisa, o dimensionamento-lo e gerir as suas chaves API.
Estes comandos, paralelas as opções de gestão disponíveis no [Azure pesquisa gestão REST API](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Pré-requisitos
 
- Tem de ter Azure PowerShell 1.0 ou superior. Para obter instruções, consulte o artigo [instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
- Tem de ter sessão iniciada sua subscrição do Azure no PowerShell, conforme descrito abaixo.

Em primeiro lugar, tem de iniciar sessão no Azure com este comando:

    Login-AzureRmAccount

Especifique o endereço de e-mail da sua conta Azure e a palavra-passe na caixa de diálogo de início de sessão do Microsoft Azure.

Em alternativa, pode [início de sessão que não sejam de forma interativa com um serviço principal](../resource-group-authenticate-service-principal.md).

Se tiver múltiplas subscrições Azure, tem de configurar a sua subscrição do Azure. Para ver uma lista das suas subscrições atuais, execute este comando.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a subscrição, execute o seguinte comando. No exemplo seguinte, o nome da subscrição é `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Comandos para o ajudar a começar a trabalhar

    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1
    
    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
    
    # View your resource
    $resource
    
    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key
    
    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
        
    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource
    
    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource
    
## <a name="next-steps"></a>Próximos passos
    
Agora que o seu serviço é criado, que pode tomar os passos seguintes: criar um [índice remissivo](search-what-is-an-index.md), [consulta um índice remissivo](search-query-overview.md), por fim criar e gerir a sua própria aplicação de pesquisa que utiliza a pesquisa Azure.

- [Criar um índice de pesquisa do Azure no portal do Azure](search-create-index-portal.md)

- [Consultar um índice de pesquisa do Azure através do Explorador de pesquisa no portal do Azure](search-explorer.md)

- [Configurar um indexador para carregar os dados de outros serviços](search-indexer-overview.md)

- [Como utilizar a pesquisa do Azure no .NET](search-howto-dotnet-sdk.md)

- [Analisar o tráfego da sua pesquisa do Azure](search-traffic-analytics.md)
