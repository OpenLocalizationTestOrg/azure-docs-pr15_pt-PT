## <a name="setting-up-powershell-for-resource-manager-templates"></a>Configurar o PowerShell para modelos de Gestor de recursos

Antes de poder utilizar Azure PowerShell com o Gestor de recursos, terá de ter à direita do Windows PowerShell e versões do Azure PowerShell.

### <a name="verify-powershell-versions"></a>Verificar as versões do PowerShell

Certifique-se de que tem a versão 3.0 ou 4.0 do Windows PowerShell. Para localizar a versão do Windows PowerShell, escreva este comando na linha de comandos do Windows PowerShell.

    $PSVersionTable

Irá receber o seguinte tipo de informações:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Verifique se o valor de **PSVersion** é 3.0 ou 4.0. Caso contrário, consulte o artigo [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [o Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Configurar a sua conta Azure e subscrição

Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Abra uma linha de comandos do PowerShell do Azure e inicie sessão no Azure com este comando.

    Login-AzureRmAccount

Se tiver múltiplas subscrições Azure, pode listar as suas subscrições Azure com este comando.

    Get-AzureRmSubscription

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

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Para mais informações sobre contas e subscrições Azure, consulte o artigo [como: ligar à sua subscrição](powershell-install-configure.md#Connect).
