<properties
    pageTitle="Gestão de recursos com o lote .NET de gestão de contas | Microsoft Azure"
    description="Criar, eliminar e modificar recursos de conta Azure lote com a biblioteca de lote gestão .NET."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Gerir contas do Azure lote e quotas com o lote de gestão de .NET

> [AZURE.SELECTOR]
- [Portal do Azure](batch-account-create-portal.md)
- [Gestão de batch .NET](batch-management-dotnet.md)

Pode diminuir manutenção sobrecarga nas suas aplicações lote Azure utilizando o [Lote de gestão de .NET] [ api_mgmt_net] biblioteca para automatizar a criação de contas do lote, eliminação, gestão de chave e deteção de quota.

- **Criar e eliminar contas lote** dentro de qualquer região. Se, como um fornecedor de software independente (ISV) por exemplo, fornecer um serviço para os seus clientes na qual cada é atribuída a uma conta do lote separada para fins de faturaçãohttps, pode adicionar funcionalidades de criação e a eliminação de conta para o seu portal de cliente.
- **Obter e conta gerar chaves** através de programação para qualquer uma das suas contas do lote. Isto pode ajudar a cumprir as políticas de segurança impõem periódica passagem com o rato ou termo de teclas de conta. Quando tiver várias contas de lote em várias regiões Azure, automatização deste processo de sobreposição aumenta eficiência da sua solução.
- **Verifique as quotas de conta** e tomar suposições a versão de avaliação e erro terminar para determinar quais as contas do lote têm os limites. Ao selecionar as quotas de conta antes de iniciar a tarefas, criar conjuntos de dados ou adicionar nós de cluster, pode ajustar importante onde ou quando estes calcular recursos são criados. Pode determinar quais as contas que necessitam de quota aumenta antes de a atribuir recursos adicionais nessas contas.
- **Combinar as funcionalidades de outros serviços Azure** para uma experiência de gestão de completa – utilizando o lote de gestão de .NET, [Azure Active Directory][aad_about]e o [Gestor de recursos do Azure] [ resman_overview] em conjunto na mesma aplicação. Ao utilizar estas funcionalidades e os respetivos APIs, pode fornecer uma experiência de autenticação frictionless, a capacidade de criar e eliminar grupos de recursos e as funcionalidades que são descritas acima para uma solução de gestão de fim para fim.

> [AZURE.NOTE] Enquanto este artigo foca-se sobre a gestão dos seus lote contas, chaves e as quotas de programação, pode efetuar muitas destas actividades utilizando o [Azure portal][azure_portal]. Para mais informações, consulte o artigo [criar uma conta do lote Azure através do portal Azure](batch-account-create-portal.md) e [as Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md).

## <a name="create-and-delete-batch-accounts"></a>Criar e eliminar lote contas

Tal como mencionado, uma das funcionalidades principais da API de gestão do lote é criar e eliminar contas lote numa região Azure. Para fazê-lo, utilize [BatchManagementClient.Account.CreateAsync] [ net_create] e [DeleteAsync][net_delete], ou os seus homólogos síncronos.

O fragmento de código seguinte cria uma conta, obtém a conta recentemente criada a partir do serviço lote e, em seguida, elimina-lo. Neste fragmento e as outras neste artigo, `batchManagementClient` é uma instância totalmente inicializada de [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] As aplicações que utilizam a biblioteca do lote gestão .NET e a respectiva classe BatchManagementClient necessitam de acesso de **administrador do serviço** ou **coadministrator** para a subscrição que proprietário da conta do lote ser gerido. Para obter mais informações, consulte a secção do [Azure Active Directory](#azure-active-directory) e o [AccountManagement] [ acct_mgmt_sample] código de exemplo.

## <a name="retrieve-and-regenerate-account-keys"></a>Obter e gerar chaves de conta

Obter teclas principais e secundários conta a partir de qualquer conta lote dentro da sua subscrição, utilizando [ListKeysAsync][net_list_keys]. Pode voltar a gerar essas chaves utilizando [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] Pode criar um fluxo de trabalho simplificada ligação para as suas aplicações de gestão. Em primeiro lugar, obter uma chave de conta para a conta do lote que pretende gerir com [ListKeysAsync][net_list_keys]. Em seguida, utilize esta tecla quando a inicialização da biblioteca de lote .NET [BatchSharedKeyCredentials] [ net_sharedkeycred] classe, que é utilizado quando a inicialização [BatchClient][net_batch_client].

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verificar a subscrição do Azure e as quotas de conta do lote

Subscrições do Azure e os serviços Azure individuais como Batch todos têm quotas predefinidas que limitar o número de determinadas entidades nelas. As predefinição para quotas de para subscrições do Azure, consulte o artigo [subscrição Azure e limites de serviço, quotas e as restrições](./../azure-subscription-service-limits.md). Para as quotas de predefinida do serviço do lote, consulte o artigo [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md). Ao utilizar a biblioteca de lote gestão .NET, pode verificar estas quotas nas suas aplicações. Permite-lhe tomar decisões de alocação antes de adicionar contas ou calcular recursos como conjuntos de dados e calcular nós.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificar uma subscrição do Azure para as quotas de conta do lote

Antes de criar uma conta do lote numa região, pode verificar a sua subscrição do Azure para verificar se é possível adicionar uma conta nesse região.

O fragmento de código abaixo, podemos pela primeira vez utilizar [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] para obter uma coleção de todas as contas do lote que estão dentro de uma subscrição. Assim que recomendamos tenha obtido nesta coleção, podemos determinar a quantas contas estão na região de destino. Em seguida, utilizamos [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] para obter a quota de conta do lote e determinar a quantas contas (se existir) podem ser criadas na região.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No fragmento de acima, `creds` é uma instância de [TokenCloudCredentials][azure_tokencreds]. Para ver um exemplo de criação deste objeto, consulte o [AccountManagement] [ acct_mgmt_sample] amostra de código GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificar existência de quotas de recursos do cluster de uma conta de Batch

Antes de aumentar recursos de cluster na sua solução lote, pode verificar garantir que os recursos que pretende atribuir não excede quotas a conta. O fragmento de código abaixo, podemos imprimir as informações de quota para a conta do lote denominada `mybatchaccount`. Na sua própria aplicação, pode utilizar essas informações para determinar se a conta pode processar os recursos adicionais para ser criado.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Apesar de existirem quotas predefinidas para subscrições do Azure e serviços, muitos destes limites podem ser criados por emitir um pedido de no [portal do Azure][azure_portal]. Por exemplo, consulte o artigo [Quotas e os limites para o serviço do Azure lote](batch-quota-limit.md) para obter instruções sobre como aumentar as quotas de conta do lote.

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Batch gestão .NET, Azure AD e o Gestor de recursos

Quando trabalha com a biblioteca de lote gestão .NET, normalmente também é utilizar [Azure Active Directory] [ aad_about] (Azure AD) e o [Gestor de recursos do Azure][resman_overview]. O exemplo o project abordado abaixo utiliza Azure Active Directory e o Gestor de recursos enquanto demonstra a API lote gestão .NET.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure propriamente dito utiliza Azure AD para a autenticação dos seus clientes, os administradores de serviço e os utilizadores organizacionais. O contexto for lote gestão .NET, utiliza o Azure AD para autenticar uma subscrição cocriação administrador ou. Esta opção permite-a biblioteca de gestão consultar o serviço de lote e executar as operações descritas neste artigo.

Exemplo de projecto abordado abaixo, a [Biblioteca de autenticação do Active Directory] Azure[ aad_adal] (ADAL) é utilizada para pedir ao utilizador para as respetivas credenciais da Microsoft. Quando são fornecidas credenciais de administrador ou coadministrator de serviço, a aplicação pode consultar Azure para obter uma lista de subscrições – e pode criar e eliminar grupos de recursos e contas do lote.

### <a name="resource-manager"></a>Gestor de recursos

Quando criar contas do lote com a biblioteca de lote gestão .NET, vai normalmente criar-os dentro de um [grupo de recursos][resman_overview]. Pode criar o grupo de recursos através de programação, utilizando o [ResourceManagementClient] [ resman_client] escolares no [Gestor de recursos do .NET] [ resman_api] biblioteca. Ou pode adicionar uma conta a um grupo de recursos existente que criou anteriormente utilizando o [Azure portal][azure_portal].

## <a name="sample-project-on-github"></a>Exemplo de projecto no GitHub

Para ver o lote de gestão de .NET em ação, consulte o artigo o [AccountManagment] [ acct_mgmt_sample] projeto de exemplo no GitHub. Esta aplicação consola mostra a criação e a utilização da [BatchManagementClient] [ net_mgmt_client] e [ResourceManagementClient][resman_client]. Também demonstra a utilização da [Biblioteca de autenticação do Active Directory] Azure[ aad_adal] (ADAL), que é necessário por ambos os clientes.

Para executar a aplicação de exemplo com êxito, tem primeiro de registá-lo com o Azure AD utilizando o portal do Azure. Siga os passos na secção [Adicionar uma aplicação](../active-directory/active-directory-integrating-applications.md#adding-an-application) nas [aplicações de integração com o Azure Active Directory] [ aad_integrate] para registar a aplicação de exemplo dentro de uma conta pessoal do diretório predefinido. Certifique-se selecionar a **Aplicação de cliente nativo** para o tipo de aplicação e pode especificar quaisquer URI válido (tais como `http://myaccountmanagementsample`) para **Redirecionar URI**–-não necessitam de ser um ponto final de real.

Depois de adicionar a aplicação, delegar a permissão de **Gestão de serviços do Access Azure como organização** para a aplicação de *API de gestão de serviço do Windows Azure* nas definições da aplicação no portal do:

![Permissões da aplicação no portal do Azure][2]

> [AZURE.TIP] Se **API de gestão de serviço do Windows Azure** não aparecer em *permissões para outras aplicações*, clique em **Adicionar aplicação**, selecione **API de gestão de serviço do Windows Azure**, em seguida, clique no botão de marca de verificação. Em seguida, delegar permissões conforme especificado acima.

Depois de adicionar a aplicação, tal como descrito acima, atualizar `Program.cs` na [AccountManagment] [ acct_mgmt_sample] projeto de exemplo com a aplicação URI redirecionar e ID de cliente. Pode localizar estes valores no separador **Configurar** da sua aplicação:

![Configuração da aplicação no portal do Azure][3]

O [AccountManagment] [ acct_mgmt_sample] demonstra a aplicação de exemplo as seguintes operações:

1. Adquirir um token de segurança a partir do Azure AD utilizando [ADAL][aad_adal]. Se o utilizador já não é iniciar sessão, são-lhe pedidos para as respetivas credenciais Azure.
2. Ao utilizar o token de segurança obtido a partir do Azure AD, criar uma [SubscriptionClient] [ resman_subclient] a consulta Azure para obter uma lista de subscrições associado à conta. Isto permite a seleção de uma subscrição se vários forem encontrados.
3. Crie um objeto de credenciais associado a subscrição seleccionada.
4. Criar [ResourceManagementClient] [ resman_client] utilizando as credenciais.
5. Utilizar [ResourceManagementClient] [ resman_client] para criar um grupo de recursos.
6. Utilizar [BatchManagementClient] [ net_mgmt_client] para realizar várias operações de conta Batch:
  - Crie uma conta do lote no novo grupo de recursos.
  - Aceder a conta recentemente criada a partir do serviço do lote.
  - Imprima as teclas de conta para a nova conta.
  - Voltar a gerar uma nova chave primária da conta.
  - Imprima as informações de quota da conta.
  - Imprima as informações de quota para a subscrição.
  - Imprima todas as contas na subscrição.
  - Elimine a conta recentemente criada.
7. Elimine o grupo de recursos.

Antes de eliminar o grupo de conta e de recursos lote recentemente criado, pode inspecionar ambos no [portal do Azure][azure_portal]:

![Portal Azure apresentando o grupo de recursos e conta Batch][1]
<br />
*Portal Azure apresentando novo grupo de recursos e conta Batch*

[aad_about]: ../active-directory/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação de Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrar aplicações com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
