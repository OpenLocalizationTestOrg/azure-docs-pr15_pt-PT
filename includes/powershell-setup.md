<properties services="virtual-machines" title="Setting up PowerShell" authors="JoeDavies-MSFT" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm=""
   ms.workload="infrastructure"
   ms.date="05/12/2015"
   ms.author="rasquill" />

## <a name="setting-up-powershell"></a>Configurar o PowerShell

Antes de poder utilizar o PowerShell Azure, siga estes passos.

### <a name="verify-powershell-versions"></a>Verificar as versões do PowerShell

Antes de poder utilizar o Windows PowerShell, tem de ter o Windows PowerShell, versão 3.0 ou 4.0. Para localizar a versão do Windows PowerShell, escreva este comando na linha de comandos do Windows PowerShell.

    $PSVersionTable

Deverá ver algo parecido com.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Para instalar uma versão compatível, consulte o artigo [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Também deverá ter o Azure PowerShell versão 0.8.0 ou posterior. Pode verificar a versão do Azure PowerShell, que tem instalada com este comando na linha de comandos do Azure PowerShell.

    Get-Module azure | format-table version

Deverá ver algo parecido com.

    Version
    -------
    0.8.16.1

Para obter instruções e uma ligação para a versão mais recente, consulte o artigo [como instalar e configurar o Azure PowerShell](powershell-install-configure.md).


### <a name="set-your-azure-account-and-subscription"></a>Configurar a sua conta Azure e subscrição

Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra uma linha de comandos do PowerShell do Azure e inicie sessão no Azure com este comando.

    Add-AzureAccount

Se tiver múltiplas subscrições Azure, pode listar as suas subscrições Azure com este comando.

    Get-AzureSubscription

Irá receber o seguinte tipo de informações:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Pode definir a subscrição atual Azure executando estes comandos na linha de comandos do Azure PowerShell. Substituir tudo dentro de aspas, incluindo o < e > carateres, com o nome correto.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current 

Para mais informações sobre contas e subscrições Azure, consulte o artigo [como: ligar à sua subscrição](powershell-install-configure.md#Connect).
