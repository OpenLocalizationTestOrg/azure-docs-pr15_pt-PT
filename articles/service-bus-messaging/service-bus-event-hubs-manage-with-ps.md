<properties
    pageTitle="Utilizar o PowerShell para gerir os recursos Bus de serviço e evento concentradores | Microsoft Azure"
    description="Utilizar o PowerShell para criar e gerir recursos Bus de serviço e concentradores de evento"
    services="service-bus,event-hubs"
    documentationCenter=".NET"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="use-powershell-to-manage-service-bus-and-event-hubs-resources"></a>Utilizar o PowerShell para gerir os recursos de serviço Bus e concentradores de evento

Microsoft Azure PowerShell é um ambiente de scripts que pode utilizar para automatizar a implementação e gestão de serviços do Azure e controlar para. Este artigo descreve como utilizar o PowerShell para aprovisionar e gerir entidades de serviço Bus como espaços de nomes, filas e concentradores evento utilizando uma consola Azure PowerShell local.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, terá o seguinte procedimento:

- Uma subscrição do Azure. Azure é uma plataforma com base em subscrição. Para mais informações sobre como obter uma subscrição, consulte o artigo [Opções de compra][], [oferece membro][]ou [conta gratuita][].

- Um computador com o Azure PowerShell. Para obter instruções, consulte o artigo [instalar e configurar o Azure PowerShell][].

- Compreender a de PowerShell scripts, NuGet pacotes e o .NET Framework.

## <a name="include-a-reference-to-the-net-assembly-for-service-bus"></a>Incluir uma referência a assemblagem .NET para Bus de serviço

Existem um número limitado dos cmdlets do PowerShell disponíveis para gerir o serviço Bus. Aprovisionar entidades que não são expostas através os cmdlets existentes, pode utilizar o cliente de .NET para Bus de serviço a partir do PowerShell ao referenciar o [pacote de serviço Bus NuGet].

Em primeiro lugar, certifique-se de que o script pode localizar a assemblagem **Microsoft.ServiceBus.dll** , que é instalada com o pacote de NuGet. Para poder ser flexíveis, o script executa estes passos:

1. Determina o caminho a partir do qual foi chamado.
2. Atravessam o caminho até encontrar uma pasta denominada `packages`. Esta pasta é criada quando instala NuGet pacotes.
3. As pesquisas de forma recursiva a `packages` pasta para uma assemblagem denominada **Microsoft.ServiceBus.dll**.
4. Referências a assemblagem para que os tipos estão disponíveis para utilização posterior.

Eis como um script PowerShell implementados estes passos:

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar um espaço de nomes de serviço Bus

Quando trabalha com espaços de nomes de serviço Bus, existem dois cmdlets, pode utilizar em vez do .NET SDK: [Get-AzureSBNamespace][] e [AzureSBNamespace novo][].

Este exemplo cria algumas variáveis locais no script; `$Namespace` and `$Location`.

- `$Namespace`é o nome do espaço de nomes serviço Bus com a qual pretende estamos a trabalhar.
- `$Location`identifica o Centro de dados no qual irá podemos aprovisionar o espaço de nomes.
- `$CurrentNamespace`armazena o espaço de nomes de referência que recomendamos obter (ou criar).

Num script real, `$Namespace` e `$Location` podem ser transmitidos como parâmetros.

Esta parte do script faz o seguinte:

1. Tenta obter um espaço de nomes de serviço Bus com o nome fornecido.
2. Se for encontrado o espaço de nomes, relatórios o que foi encontrado.
3. Se não for encontrado o espaço de nomes, cria o espaço de nomes e, em seguida, obtém o espaço de nomes recentemente criado.

    ``` powershell

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
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```
Aprovisionamento de outras entidades Bus de serviço, criar uma instância da `NamespaceManager` objecto a partir do SDK. Pode utilizar o cmdlet [Get-AzureSBAuthorizationRule][] para obter uma regra de autorização que é utilizada para fornecer uma cadeia de ligação. Este exemplo armazena uma referência para a `NamespaceManager` instância na `$NamespaceManager` variável. Mais tarde, utiliza o script `$NamespaceManager` aprovisionamento de outras entidades.

    ``` powershell
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    # Create the NamespaceManager object to create the Event Hub
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
    ```

## <a name="provisioning-other-service-bus-entities"></a>Outras entidades Bus de serviço de aprovisionamento

Aprovisionamento de outras entidades, tais como filas, tópicos e concentradores de evento, pode utilizar a [API .NET para Bus de serviço][]. Exemplos mais detalhados, incluindo outras entidades, são referenciados no final deste artigo.

### <a name="create-an-event-hub"></a>Criar um concentrador de evento

Esta parte do script cria quatro variáveis locais mais. Estas variáveis são utilizadas para criar uma instância uma `EventHubDescription` objeto. O script faz o seguinte:

1. Utilizar o `NamespaceManager` objecto, verifique se o concentrador de evento identificadas pelas `$Path` existir.
2. Se não existir, crie uma `EventHubDescription` e passam que para o `NamespaceManager` escolares `CreateEventHubIfNotExists` método.
3. Depois de determinar que o concentrador de evento está disponível, criar um grupo do consumidor utilizando `ConsumerGroupDescription` e `NamespaceManager`.

    ``` powershell

    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"

    # Check if the Event Hub already exists
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

### <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila ou tópico, execute uma verificação de espaço de nomes, tal como a secção anterior.

    if ($NamespaceManager.QueueExists($Path))
    {
        Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
        $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
        $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
        $QueueDescription.EnableExpress = $EnableExpress
        $QueueDescription.EnablePartitioning = $EnablePartitioning
        $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
        $QueueDescription.ForwardTo = $ForwardTo
        $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        if ($LockDuration -gt 0)
        {
            $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
        }
        $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
        $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        $QueueDescription.RequiresSession = $RequiresSession
        if ($EnablePartitioning)
        {
            $QueueDescription.SupportOrdering = $False
        }
        else
        {
            $QueueDescription.SupportOrdering = $SupportOrdering
        }
        $QueueDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateQueue($QueueDescription);
    }

### <a name="create-a-topic"></a>Criar um tópico

    if ($NamespaceManager.TopicExists($Path))
    {
        Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
    }
    else
    {
        Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
        $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
        if ($AutoDeleteOnIdle -ge 5)
        {
            $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
        }
        if ($DefaultMessageTimeToLive -gt 0)
        {
            $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
        }
        if ($DuplicateDetectionHistoryTimeWindow -gt 0)
        {
            $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
        }
        $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
        $TopicDescription.EnableExpress = $EnableExpress
        $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
        $TopicDescription.EnablePartitioning = $EnablePartitioning
        $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
        $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
        $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
        if ($EnablePartitioning)
        {
            $TopicDescription.SupportOrdering = $False
        }
        else
        {
            $TopicDescription.SupportOrdering = $SupportOrdering
        }
        $TopicDescription.UserMetadata = $UserMetadata
        $NamespaceManager.CreateTopic($TopicDescription);
    }

## <a name="next-steps"></a>Próximos passos

Este artigo fornecido um fluxo de trabalho básico para aprovisionar entidades de serviço Bus através do PowerShell. Apesar de existirem um número limitado dos cmdlets do PowerShell para gerir o serviço Bus mensagens entidades, ao referenciar a assemblagem Microsoft.ServiceBus.dll, praticamente qualquer operação pode efetuar utilizando as bibliotecas do cliente .NET que também pode executar um script PowerShell.

Existem mais detalhados exemplos disponíveis nestas mensagens no blogue:

- [Como criar serviço Bus filas, tópicos e subscrições utilizando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um espaço de nomes do serviço Bus e um concentrador de evento utilizando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns dos scripts prontos a utilizar também estão disponíveis para transferência:

- [Scripts de PowerShell Bus serviço](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[Opções de compra]: http://azure.microsoft.com/pricing/purchase-options/
[ofertas de membro]: http://azure.microsoft.com/pricing/member-offers/
[conta gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Serviço Bus NuGet pacote]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[Novo AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET para Bus de serviço]: https://msdn.microsoft.com/en-us/library/azure/mt419900.aspx
[Instalar e configurar o PowerShell do Azure]: ../powershell-install-configure.md
