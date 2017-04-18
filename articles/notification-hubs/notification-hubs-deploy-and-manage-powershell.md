<properties 
    pageTitle="Implementar e gerir notificação concentradores através do PowerShell" 
    description="Como criar e gerir concentradores notificação através do PowerShell para a automatização" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Implementar e gerir notificação concentradores através do PowerShell

##<a name="overview"></a>Descrição geral

Este artigo mostra-lhe como utilizar criar e gerir Azure notificação concentradores através do PowerShell. São apresentadas as seguintes tarefas comuns de automatização neste tópico.

+ Criar um concentrador de notificação
+ Definir credenciais

Se precisar também criar um novo serviço bus espaço de nomes para sua concentradores de notificação, consulte o artigo [Gerir o serviço Bus com PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Gerir notificações concentradores não é suportada diretamente pelos cmdlets incluídos com o Azure PowerShell. É a melhor abordagem a partir do PowerShell fazer referência a assemblagem Microsoft.Azure.NotificationHubs.dll. A assemblagem é distribuída com o [pacote do Microsoft Azure notificação concentradores NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- Uma subscrição do Azure. Azure é uma plataforma com base em subscrição. Para mais informações sobre como obter uma subscrição, consulte o artigo [Opções de compra], o [Membro oferece]ou a [Versão de avaliação gratuita].

- Um computador com o Azure PowerShell. Para obter instruções, consulte o artigo [instalar e configurar o Azure PowerShell].

- Compreender a de PowerShell scripts, NuGet pacotes e o .NET Framework.


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência a assemblagem .NET para Bus de serviço

Gerir Azure notificação concentradores ainda não está incluído com os cmdlets do PowerShell no Azure PowerShell. Aprovisionar concentradores de notificação, pode utilizar o cliente de .NET fornecido no [Microsoft Azure notificação concentradores NuGet pacote](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Em primeiro lugar, certifique-se de que o script pode localizar a assemblagem de **Microsoft.Azure.NotificationHubs.dll** , que é instalada como um pacote de NuGet num projeto do Visual Studio. Para poder ser flexíveis, o script executa estes passos:

1. Determina o caminho no qual foi chamado.
2. Atravessam o caminho até encontrar uma pasta denominada `packages`. Esta pasta é criada quando instala NuGet pacotes para projetos do Visual Studio.
3. As pesquisas de forma recursiva a `packages` pasta para uma assemblagem denominada **Microsoft.Azure.NotificationHubs.dll**.
4. Referências a assemblagem para que os tipos estão disponíveis para utilização posterior.

Eis como um script PowerShell implementados estes passos:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Criar a classe de NamespaceManager

Aprovisionar concentradores de notificação, crie uma instância da classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) a partir do SDK. 

Pode utilizar o cmdlet [Get-AzureSBAuthorizationRule] incluído com o Azure PowerShell para obter uma regra de autorização que é utilizada para fornecer uma cadeia de ligação. Vamos irá armazenar uma referência para a `NamespaceManager` instância na `$NamespaceManager` variável. Vamos utilizar o `$NamespaceManager` aprovisionar a um concentrador de notificação.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Aprovisionar um concentrador de notificação de novo 

Aprovisionar um concentrador de notificação de novo, utilize a [API .NET para concentradores de notificação].

Nesta peça do script configurar quatro variáveis locais. 

1. `$Namespace`: Defina esta opção para o nome do espaço de nomes onde pretende criar um concentrador de notificação.
2. `$Path`: Defina este caminho para o nome do novo concentrador de notificação.  Por exemplo, "MyHub".    
3. `$WnsPackageSid`: Defina esta opção para o pacote SID para a aplicação do Windows no [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409)de.
4. `$WnsSecretkey`: Defina esta opção para a chave secreta para a aplicação do Windows no [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409)de.

Estas variáveis são utilizadas para ligar o espaço de nomes e criar um concentrador de notificação de novo configurado para processar as notificações de serviços de notificação do Windows (WNS) com credenciais WNS para uma aplicação do Windows. Para obter informações sobre como obter o pacote de SID e chave secreta consulte o artigo, o tutorial de [Introdução ao concentradores de notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) . 

+ O fragmento de script utiliza o `NamespaceManager` objeto para verificar se o concentrador de notificação identificadas pelas `$Path` existir.

+ Se não existir, o script irá criar um `NotificationHubDescription` com WNS credenciais e passam que para o `NamespaceManager` escolares `CreateNotificationHub` método.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Recursos adicionais

- [Gerir o serviço Bus com PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Como criar serviço Bus filas, tópicos e subscrições utilizando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um espaço de nomes do serviço Bus e um concentrador de evento utilizando um script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns dos scripts prontos a utilizar também estão disponíveis para transferência:
- [Scripts de PowerShell Bus serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[Opções de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas de membro]: http://azure.microsoft.com/pricing/member-offers/
[Versão de avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[Instalar e configurar o PowerShell do Azure]: ../powershell-install-configure.md
[API .NET para concentradores de notificação]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
