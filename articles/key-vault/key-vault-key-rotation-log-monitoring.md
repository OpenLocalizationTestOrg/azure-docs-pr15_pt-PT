<properties
    pageTitle="Como configurar o Cofre chave com rotação da chave de ponto a ponto e auditorias | Microsoft Azure"
    description="Utilize este instruções para ajudá-lo configuração com rotação da chave e monitorizar os registos do cofre chave"
    services="key-vault"
    documentationCenter=""
    authors="swgriffith"
    manager="mbaldwin"
    tags=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="jodehavi;stgriffi"/>
#<a name="how-to-setup-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Como configurar o Cofre chave com rotação da chave de ponto a ponto e de auditoria

##<a name="introduction"></a>Introdução

Depois de criar o seu Cofre de chave do Azure, lhe poderão começar a tirar partido desse cofre para armazenar os seus teclas e segredos. As aplicações já não precisa de persistir seu teclas ou segredos, mas irá pedi-los do Cofre de palavras chave conforme necessário. Esta opção permite-lhe actualizar teclas e segredos sem que afetam o comportamento da sua aplicação, que abre-se para cima de uma vasta gama de possibilidades à volta da sua chave e o comportamento de gestão secreta.

Este artigo explica um exemplo de tirar partido da Azure chave cofre para armazenar uma palavra-passe, neste caso, uma chave de conta de armazenamento do Azure que pode é acedida por uma aplicação. Também vai demonstrar pós-implementação de uma rotação agendada dessa chave da conta de armazenamento. Por fim, este irá guiá-lo através de uma demonstração sobre como monitorizar os registos de auditoria cofre chave e elevar alertas quando são feitos pedidos inesperados.

> \[AZURE. Nota\] este tutorial não se destina a explicar detalhadamente o conjunto inicial para cima do Cofre de chave Azure. Para estas informações, consulte o artigo [Introdução ao Azure chave cofre](key-vault-get-started.md). Em alternativa, para obter instruções de Interface de comandos em diferentes plataformas, consulte o artigo [Este tutorial equivalente](key-vault-manage-with-cli.md).

##<a name="setting-up-keyvault"></a>Configurar o KeyVault

Para poder ativar uma aplicação obter uma palavra-passe do Azure chave cofre, primeiro tem de criar o segredo e carregue-o para cofre. Isto pode ser feito facilmente através do PowerShell conforme apresentado abaixo.

Iniciar uma sessão de PowerShell do Azure e inicie sessão na sua conta Azure com o seguinte comando:

```powershell
Login-AzureRmAccount
```

Na janela do browser de pop-up, introduza o seu nome de utilizador da conta Azure e a palavra-passe. Azure PowerShell receberão todas as subscrições que estão associadas com esta conta e por predefinição, utiliza a primeira parte.

Se tiver múltiplas subscrições, poderá ter que especificar um específico que foi utilizado para criar o seu Cofre de chave do Azure. Escreva o seguinte para ver as subscrições para a sua conta:

```powershell
Get-AzureRmSubscription
```

Em seguida, para especificar a subscrição da qual está associada à sua cofre chave que vai iniciar sessão, escreva:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Como este artigo demonstra armazenamento de uma chave de conta de armazenamento como uma palavra-passe, terá de obter a chave de conta que armazenamento.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de obter a sua palavra-passe, neste caso a chave de conta de armazenamento, terá de converter que para uma cadeia segura e, em seguida, criar uma palavra-passe com esse valor no cofre chave.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Seguinte que pretende obter o URI para o segredo que acabou de criar. Isto vai ser utilizado num passo posterior quando está a telefonar cofre chave para obter a sua palavra-passe. Execute o seguinte comando PowerShell e tome nota do valor 'Id', que é o URI secreto.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##<a name="setting-up-application"></a>Configurar a aplicação

Agora que tem uma palavra-passe armazenada irá pretende obter esse secreta e utilizá-lo a partir do código. Existem alguns passos necessários para alcançar isto, a primeira e mais importantes que está a registar a sua aplicação com o Azure Active Directory e, em seguida, informar Azure chave cofre as informações da aplicação para que pode permitir que os pedidos a partir da sua aplicação.

> \[AZURE. Nota\] a aplicação tem de ser criada ao mesmo inquilino Azure Active Directory como Cofre de chave. 

Primeiro abra o separador de aplicações do Azure Active Directory

![Aplicações abertas no Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Selecione adicionar para adicionar uma nova aplicação para o Azure AD

![Selecione Adicionar aplicação](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Deixe o tipo de aplicação como 'Web e/ou aplicação WEB API' e dê um nome à sua aplicação.

![Nome da aplicação](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Dar a aplicação de um 'Sign-On URL' e um 'URI de ID de aplicação'. Estes podem ser qualquer coisa que pretende para esta demonstração e podem ser alteradas mais tarde, se necessário.

![Fornecer URIs necessários](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Assim que a aplicação é adicionada ao Azure AD, vai ser introduzidos a página de aplicação. A partir desse momento clique no separador 'Configurar' e, em seguida, localize e copie o valor de ID de cliente. Tome nota do ID do cliente para obter os passos posteriores.

Seguinte que terá gerar uma chave para a sua aplicação possam interagir com o Azure AD. Pode criar isto na secção 'Teclas' na caixa de diálogo 'Configuração'. Tome nota da a chave gerada recentemente a partir da sua aplicação do Azure AD para utilização num passo posterior.

![Teclas de aplicação do Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Antes de estabelecer quaisquer chamadas a partir da sua aplicação para o chave cofre terá de onde está o Cofre de tecla sobre a aplicação e o respectivo ' permissões. O seguinte comando leva-o até o nome do cofre e o ID do cliente da sua aplicação do Azure AD e concede 'Get' o acesso ao seu Cofre chave para a aplicação.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Neste momento está pronto para começar a construir as chamadas de aplicação. Na sua aplicação primeiro terá de instalar os pacotes de NuGet necessários para interagir com Cofre de chave do Azure e o Azure Active Directory. A partir da consola do Gestor de pacotes do Visual Studio introduza os seguintes comandos. Note que na escrita deste artigo a versão atual do pacote ActiveDirectory é 3.10.305231913, para que pretende confirmar a versão mais recente e atualizar, consoante adequado.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No seu código de aplicação, crie uma aula que mantenha o método de autenticação do Active Directory. Neste exemplo que classe denomina-'Utilitários'. Em seguida, terá de adicionar a utilizando o seguinte.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para obter o token JWT a partir do Azure AD. Para manutenção que pretende mover o disco rígido codificadas com valores de cadeia para sua configuração web ou de aplicação.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Por fim, pode adicionar o código necessário para ligar para cofre chave e obter o valor secreto. Primeiro tem de adicionar a seguinte instrução a utilizar.

```csharp
using Microsoft.Azure.KeyVault;
```

Seguinte irá adicionar as chamadas de método para invocar cofre chave e obter a sua palavra-passe. Este método fornecerá o segredo URI que guardou no passo anterior. Repare na utilização do método GetToken da classe utilitários criado anteriormente.
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Quando executar a aplicação, agora deverá autenticar para o Azure Active Directory e, em seguida, obter o valor de secreto do Azure chave cofre.

##<a name="key-rotation-using-azure-automation"></a>Rotação da chave utilizando a automatização do Azure

Existem várias opções para a implementação de uma estratégia de rotação para valores que armazenar como segredos Azure chave cofre. Podem ser rodados segredos como parte de um processo manual, poderá ser rodados forma programática por tirar partido da API chamadas ou podem ser rodados através de um script de automatização. Para efeitos deste artigo podemos vai ser tirar partido da PowerShell Azure combinado com Azure automatização para alterar uma tecla de acesso de conta de armazenamento do Azure e, em seguida, podemos irão atualizar uma palavra-passe cofre chave com essa nova chave. 

Para permitir Azure automatização definir valores secretas no cofre chave terá de obter o ID do cliente para a ligação com o nome 'AzureRunAsConnection' qual foi criada quando é estabelecida a instância de automatização do Azure. Pode aceder a este ID ao selecionar «Activos» a partir do seu instância de automatização do Azure. A partir desse local escolher 'Ligações' e, em seguida, selecione o princípio do serviço de 'AzureRunAsConnection'. Pretende que tome nota do ID especiais de aplicação. 

![ID de cliente de automatização Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Se estiver ainda na janela de activos que será também pretende escolher 'Módulos'. Módulos irá Selecione 'Galeria' e, em seguida, procure e 'Import' atualizadas versões de cada um dos seguintes módulos.

    Azure
    Azure.Storage   
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage
    
> \[AZURE. Nota\] na escrita deste artigo indicadas apenas anteriores, módulos necessários para ser atualizados para o script partilhado abaixo. Se encontrar a tarefa de automatização está a falhar, deve confirmar que tem todos os módulos necessários e respectivas dependências importadas.

Depois de ter obtido o ID da aplicação para a sua ligação do Azure automatização, terá de indicar ao seu Cofre de chave do Azure que esta aplicação tem acesso ao atualizar segredos no cofre. Pode fazê-lo com o seguinte comando PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, que irá selecionar o recurso 'Runbooks' em sua instância de automatização do Azure e selecione 'Adicionar um livro de execuções'. Selecione 'Rápida criar'. Nome do seu livro de execuções e selecione 'PowerShell' como o tipo de livro execuções. Opcionalmente, adicione uma descrição. Por fim, clique em 'Criar'.

![Criar livro execuções](./media/keyvault-keyrotation/Create_Runbook.png)

No painel de editor para o seu novo livro de execuções onde irá colar o script PowerShell seguinte.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

A partir do painel de editor pode escolher 'Teste painel' testar o script. Assim que está a executar o script sem erro pode selecionar a opção 'Publicar' e, em seguida, pode aplicar uma agenda para o livro de execuções novamente no painel de configuração do livro execuções.

##<a name="key-vault-auditing-pipeline"></a>Chave cofre auditoria em curso

Quando configura um cofre de chave do Azure pode ativar auditoria recolher registos no efetuadas ao Cofre de palavras chave de pedidos de acesso. Estes registos são armazenados numa conta de armazenamento do Windows Azure designada e, em seguida, podem ser movidos, monitorizadas e analisadas. Abaixo descontraídos através de um cenário que tira partido das funções Azure, aplicações de lógica do Azure e chave cofre registos de auditoria para criar uma tubagem para enviar um e-mail quando segredos do Cofre de palavras são obtidos por uma aplicação que corresponde ao id da aplicação da aplicação web.

Em primeiro lugar, terá de activar o registo no seu Cofre chave. Isto pode ser feito através do PowerShell seguintes comandos (detalhes completos podem ser vistos [aqui](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Assim que este é activado, em seguida, registos de auditoria iniciará recolher na conta do armazenamento designado. Estes registos irão conter eventos sobre como e quando são acedidos seu cofres chave e por quem. 

> \[AZURE. Nota\] pode aceder à sua informação de registo no máximo, 10 minutos depois da chave de operação do Cofre de palavras. Na maioria dos casos, vai ser mais rápido superior a esta.

O passo seguinte é [criar uma fila Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Este será onde são seguia registos de auditoria chave cofre. Uma vez na fila de espera, a aplicação de lógica irá Pegue-los e agir em-los. Para criar um serviço Bus é relativamente rectas reencaminhar e abaixo estão os passos de alto níveis:

1. Crie um espaço de nomes de serviço Bus (se tiver já que pretende utilizar para este, em seguida, avance para o passo 2).
2. Navegue para o Bus de serviço no portal do e selecione o espaço de nomes que pretende criar a fila na.
3. Selecione nova e escolha serviço Bus -> fila de espera e introduza os detalhes necessários.
4. Espreite as informações de ligação de serviço Bus ao escolher o espaço de nomes e clicar em _Informações de ligação_. Terão estas informações para a parte seguinte.

Em seguida, irá [criar uma função Azure](../azure-functions/functions-create-first-azure-function.md) para consultar os registos de chave cofre dentro da conta de armazenamento e escolha o novos eventos. Esta será uma função que é acionada uma agenda.

Criar uma função Azure (selecione novo -> função aplicação no portal do). Durante a criação de pode utilizar um plano de alojamento existente ou crie um novo. Também pode optar por para alojamento dinâmicos. Obter mais detalhes sobre a função alojamento opções podem ser encontradas [aqui](../azure-functions/functions-scale.md).

Quando a função Azure é criada, navegue para a mesma e selecione um temporizador função e C\# , em seguida, clique em **Criar** a partir do ecrã Iniciar.

![Funções Azure iniciar pá](./media/keyvault-keyrotation/Azure_Functions_Start.png)

No separador _desenvolver_ , substitua o código de run.csx com o seguinte:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> \[AZURE. Nota\] Certifique-se substituir as variáveis no código de acima para apontar para a sua conta de armazenamento onde os registos de chave cofre são escritos, para o Bus de serviço que criou anteriormente e para o caminho específico para registos de armazenamento do cofre chave.

A função escolhe o ficheiro de registo mais recente da conta de armazenamento onde os registos de chave cofre são escritos, grabs os eventos mais recentes a partir desse ficheiro e envia-los para uma fila Bus de serviço. Uma vez que um único ficheiro poderia ter vários eventos, por exemplo, ao longo de uma hora completa, em seguida, criamos um ficheiro de _sync.txt_ que a função também analisa para determinar o carimbo de hora do último evento que foi selecionado. Isto irá garantir que recomendamos não emissão o mesmo evento várias vezes. Este ficheiro _sync.txt_ simplesmente contém um carimbo de data/hora do último detetado evento. Os registos, quando carregado, tem de ser ordenados com base no carimbo de hora para se certificar de que são ordenadas corretamente.

Para que esta função, podemos referenciar algumas das bibliotecas adicionais que não estão disponíveis terminar a caixa no Azure funções. Para incluir estas, precisamos Azure funções para separá-las utilizando nuget. Escolha a opção de _Ver ficheiros_ 

![Ver a opção de ficheiros](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

e adicionar um novo ficheiro denominado _project.json_ com o seguinte conteúdo:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Após _Guardar_ Isto irá acionar Azure funções para transferir os binários necessários. 

Mudar para o separador de **integrar** e atribua um nome significativo para utilizar a função do parâmetro do temporizador. No código, espera o temporizador para ser chamado _myTimer_. Especificar uma [expressão CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) da seguinte forma: 0 \* \* \* \* \* para o temporizador que fará com que a função executar uma vez por um minuto. 

No separador **integrar** mesmo, adicione uma entrada que será do tipo de _Armazenamento de Blobs do Azure_. Isto irá apontar para o ficheiro de _sync.txt_ que contém o carimbo de data/hora do último evento visualizou pela função. Esta será disponibilizada dentro da função pelo nome do parâmetro. No código, a entrada de armazenamento de Blobs do Azure espera o nome do parâmetro para ser _inputBlob_. Selecione a conta de armazenamento onde o ficheiro _sync.txt_ residirão (podem ser do mesmo livro ou uma conta de armazenamento diferente) e no campo caminho, forneça o caminho onde o ficheiro encontra-se, no formato de {container-name}/path/to/sync.txt.

Adicione um resultado que será do tipo de saída de _Armazenamento de Blobs do Azure_ . Isto também irá apontar para o ficheiro de _sync.txt_ que definiu na entrada de dados. Será utilizado pela função para escrever o carimbo de data/hora do último evento visualizou. O código acima espera este parâmetro para ser chamado _outputBlob_.

Neste momento, a função está pronta. Certifique-se mudar para o separador de **desenvolver** e, em seguida, _guarde_ o código. Verifique a janela de saída para qualquer erros de compilação e corrija os em conformidade. Se ela compila, em seguida, o código deverá agora estar a executar o e minuto será Verifique os registos de chave cofre e quaisquer novos eventos para a fila de serviço Bus definido de emissão. Deverá ver informações de registo de de escrita para a janela de registo sempre que a função é acionada.

###<a name="azure-logic-app"></a>Aplicação de lógica Azure

Seguinte precisamos de criar uma aplicação de lógica Azure que Pegue os eventos que a função é conduza para a fila de serviço Bus, analisar o conteúdo e enviar um e-mail com base numa condição a ser correspondida.

[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) ao aceder a New -> lógica de aplicação. 

Quando a aplicação de lógica estiver criada, navegue para a mesma e escolha _Editar_. No editor do lógica aplicação, selecione que API gerida na _Fila de Bus de serviço_ e introduza as suas credenciais de serviço Bus ligá-lo para a fila.

![Lógica Azure Bus de aplicação de serviço](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Em seguida, selecione para _Adicionar uma condição_. Em condição, mude para o _editor avançado_ e introduza o seguinte, substituindo o APP_ID a APP_ID real da sua aplicação web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Esta expressão, essencialmente, irá devolver **Falso** se a propriedade **appid** do evento recebida (que é o corpo da mensagem serviço Bus) não for o **appid** da aplicação. 

Agora, crie uma ação na opção _se não, não faça nada..._ .

![Azure lógica aplicação selecione ação](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione _Office 365 - enviar mensagens de e-mail_. Preencha os campos para criar uma mensagem de e-mail para enviar quando a condição definida devolve falsa. Se não tiver o Office 365 foi observe alternativas para alcançar o mesmo.

Neste momento tem um pipeline de ponto a ponto que, uma vez por minuto, irá procurar novos registos de auditoria de chave cofre. Qualquer novos registos encontrados,-lo será emissão-los para uma fila de Bus de serviço. A aplicação de lógica será acionada assim que uma nova mensagem cair na fila de espera e se o appid dentro do evento não corresponder o id da aplicação da aplicação de chamada, em seguida, enviar uma mensagem de e-mail. 
