<properties
    pageTitle="Gerir o serviço Bus com PowerShell | Microsoft Azure"
    description="Gerir o serviço Bus com scripts de PowerShell"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>Gerir o serviço Bus com PowerShell

## <a name="overview"></a>Descrição geral

Microsoft Azure PowerShell é um ambiente de scripts que pode utilizar para controlar e automatizar a implementação e gestão dos seus das cargas de trabalho no Azure. Este artigo descreve como utilizar o PowerShell para aprovisionar e gerir entidades de serviço Bus como espaços de nomes, filas e concentradores evento utilizando uma consola Azure PowerShell local.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter os pré-requisitos seguintes:

- Uma subscrição do Azure. Azure é uma plataforma com base em subscrição. Para mais informações sobre como obter uma subscrição, consulte o artigo [Opções de compra][], o [Membro oferece][]ou a [Versão de avaliação gratuita][].

- Um computador com o Azure PowerShell. Para obter instruções, consulte o artigo [instalar e configurar o Azure PowerShell][].

- Compreender a de PowerShell scripts, NuGet pacotes e o .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência a assemblagem .NET para Bus de serviço

Existem um número limitado dos cmdlets do PowerShell disponíveis para gerir o serviço Bus. Aprovisionar entidades que não são expostas através os cmdlets existentes, pode utilizar o cliente de .NET para Bus de serviço no [serviço Bus NuGet pacote][].

Em primeiro lugar, certifique-se de que o script pode localizar a assemblagem **Microsoft.ServiceBus.dll** , que é instalada com o pacote de NuGet. Para poder ser flexíveis, o script executa estes passos:

1. Determina o caminho no qual foi chamado.
2. Atravessam o caminho até encontrar uma pasta denominada `packages`. Esta pasta é criada quando instala NuGet pacotes.
3. As pesquisas de forma recursiva a `packages` pasta para uma assemblagem denominada **Microsoft.ServiceBus.dll**.
4. Referências a assemblagem para que os tipos estão disponíveis para utilização posterior.

Eis como um script PowerShell implementados estes passos:

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar um espaço de nomes de serviço Bus

Os cmdlets do PowerShell duas suportam operações de espaço de nomes de serviço Bus. Em vez da API do .NET SDK, pode utilizar [Get-AzureSBNamespace][] e [AzureSBNamespace novo][].

Este exemplo cria algumas variáveis locais no script; `$Namespace` and `$Location`.

- `$Namespace`é o nome do espaço de nomes serviço Bus com a qual pretende estamos a trabalhar.
- `$Location`identifica o Centro de dados na qual o script disposições o espaço de nomes.
- `$CurrentNamespace`armazena o espaço de nomes de referência que o script obtém (ou cria).

Num script real, `$Namespace` e `$Location` podem ser transmitidos como parâmetros.

Esta parte do script executa as seguintes tarefas:

1. Tenta obter um espaço de nomes de serviço Bus com o nome fornecido.
2. Se for encontrado o espaço de nomes, relatórios o que foi encontrado.
3. Se não for encontrado o espaço de nomes, cria o espaço de nomes e, em seguida, obtém o espaço de nomes recentemente criado.

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Aprovisionamento de outras entidades Bus de serviço, crie uma instância da classe [NamespaceManager][] a partir do SDK.
Pode utilizar o cmdlet [Get-AzureSBAuthorizationRule][] para obter uma regra de autorização que é utilizada para fornecer uma cadeia de ligação. Vamos irá armazenar uma referência para a `NamespaceManager` ocorrência na `$NamespaceManager` variável. Vamos utilizar o `$NamespaceManager` posterior no script de aprovisionamento de outras entidades.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Outras entidades Bus de serviço de aprovisionamento

Para poder aprovisionar outras entidades, tais como filas, tópicos e concentradores de evento, utilize a [API .NET para Bus de serviço][]. Este artigo foca-se apenas na concentradores de evento, mas os passos para outras entidades são semelhantes. Além disso, são referenciados exemplos mais detalhados, incluindo outras entidades, no final deste artigo.

Esta parte do script cria quatro variáveis locais mais. Estas variáveis são utilizadas para criar uma instância uma `EventHubDescription` objeto. O script executa as seguintes tarefas:

1. Utilizar o `NamespaceManager` objecto, verifique se o concentrador de evento identificadas pelas `$Path` existir.
2. Se não existir, crie uma `EventHubDescription` e passam que para o `NamespaceManager` escolares `CreateEventHubIfNotExists` método.
3. Depois de determinar que o concentrador de evento está disponível, criar um grupo do consumidor utilizando `ConsumerGroupDescription` e `NamespaceManager`.

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Migrar um espaço de nomes para outra subscrição do Azure

A seguinte sequência de comandos move um espaço de nomes a partir de uma subscrição Azure para outra. Para executar esta operação, o espaço de nomes tem já estará ativo e o utilizador a executar os comandos do PowerShell tem de ser um administrador de subscrições de origem e destino.

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Próximos passos

Este artigo fornecido um destaque básico para aprovisionar entidades de serviço Bus através do PowerShell. Alguma coisa que que pode fazer com as bibliotecas do cliente .NET, também pode fazer num script PowerShell.

Existem mais detalhados exemplos disponíveis nestas mensagens no blogue:

- [Como criar serviço Bus filas, tópicos e subscrições utilizando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um espaço de nomes do serviço Bus e um concentrador de evento utilizando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns dos scripts prontos a utilizar também estão disponíveis para transferência:
- [Scripts de PowerShell Bus serviço](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Opções de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas de membro]: http://azure.microsoft.com/pricing/member-offers/
[Versão de avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure]: ../powershell-install-configure.md
[Serviço Bus NuGet pacote]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Novo AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET para Bus de serviço]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
