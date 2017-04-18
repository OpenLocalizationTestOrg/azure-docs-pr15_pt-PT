<properties 
   pageTitle="Criar zonas de DNS e registar conjuntos no DNS Azure utilizando o SDK .NET | Microsoft Azure" 
   description="Como criar zonas de DNS e conjuntos de registos no Azure DNS utilizando o .NET SDK." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Criar zonas de DNS e conjuntos de registos utilizando o SDK .NET

Pode automatizar operações para criar, eliminar ou atualizar zonas, conjuntos de registos e registos DNS utilizando SDK de DNS com a biblioteca de gestão de DNS .NET. Um projeto do Visual Studio completo está disponível [aqui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Criar uma conta do serviço principal

Normalmente, é concedido acesso de programação para recursos Azure através de uma conta dedicada em vez das suas credenciais de utilizador. Nestas contas dedicadas chamam contas 'serviço principal'. Para utilizar o projeto de exemplo do Azure DNS SDK, tem primeiro criar uma conta do serviço de principal e atribuir-lhe as permissões corretas.

1. Siga [estas instruções](../resource-group-authenticate-service-principal.md) para criar uma conta principal de serviço (o projeto de exemplo do Azure DNS SDK assume uma autenticação baseada em palavra-passe).

2. Criar um grupo de recursos ([Eis como](../azure-portal/resource-group-portal.md)).

3. Utilize o Azure RBAC para conceder a conta de serviço de principal permissões de 'DNS Zone Contribuinte' ao grupo de recursos ([Eis como](../active-directory/role-based-access-control-configure.md).)

4. Se utilizar o projeto de exemplo do Azure DNS SDK, edite o ficheiro de 'program.cs' da seguinte forma:
    * Inserir os valores corretos para tenantId, clientId (também conhecido como ID de conta), secreta (serviço conta principal palavra-passe) e subscriptionId tal como é utilizado no passo 1.
    * Introduza o nome do grupo de recursos escolhido no passo 2.
    * Introduza um nome de zona DNS da sua escolha.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacotes de NuGet e declarações de espaço de nomes

Para utilizar o Azure DNS .NET SDK, tem de instalar o pacote de NuGet de **Biblioteca de gestão de DNS do Azure** e outros volumes Azure necessários.
 
1. No **Visual Studio**, abra um projeto ou um novo projeto. 

2. Aceda a **Ferramentas** **>** **Gestor de pacotes NuGet** **>** **Gerir pacotes de NuGet para solução...**. 

3. Clique em **Procurar**, ativar a caixa de verificação **incluir a versão de pré-lançamento** e escreva **Microsoft.Azure.Management.Dns** na caixa de pesquisa.

4. Selecione o pacote e clique em **instalar** para adicioná-lo ao seu projeto do Visual Studio.
 
5. Repita o processo acima para também de instalar os seguintes pacotes: **Microsoft.Rest.ClientRuntime.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicionar as seguintes declarações de espaço de nomes

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>Iniciar o cliente de gestão de DNS

O *DnsManagementClient* contém os métodos e propriedades necessárias para gerir zonas de DNS e conjuntos de registos.  O código seguinte inicia sessão para a conta de serviço de principal e cria um objeto de DnsManagementClient.

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>Criar ou actualizar uma zona de DNS

Para criar uma zona de DNS, pela primeira vez "Zona" é criado um objecto para conter os parâmetros de zona DNS. Uma vez que não estão ligadas zonas de DNS para uma região específica, a localização está definida para 'global'. Neste exemplo, um [Gestor de recursos do Azure 'etiqueta'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) também é adicionada à zona.

Para realmente criar ou actualizar a zona no Azure DNS, o objeto de zona que contém os parâmetros de zona é transmitido para o método de *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

>[AZURE.NOTE] DnsManagementClient suporta três modos de operação: síncrono ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrona com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Pode escolher qualquer um destes modos, consoante as suas necessidades de aplicação.

Azure DNS suporta simultaneidade optimista, denominada [Etags](dns-getstarted-create-dnszone.md). Neste exemplo, especificar "*" para o 'se nenhum-correspondência' cabeçalho indica Azure DNS para criar uma zona de DNS, se um ainda não existir.  Se já existe uma zona com o nome especificado no grupo de recursos determinado uma falha a chamada.

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>Criar conjuntos de registos de DNS e registos

Registos DNS são geridos como um conjunto de registos. Um conjunto de registo é um conjunto de registos com o mesmo nome e tipo de registo dentro de uma zona.  O nome do conjunto de registos é relativo o nome da zona, não é o nome DNS completamente qualificado.

Para criar ou actualizar um registo do conjunto, um "conjunto de registos" objeto de parâmetros é criado e transmitido para *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Como com zonas de DNS, existem três modos de operação: síncrono ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrona com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Tal como acontece com zonas de DNS, operações em conjuntos de registos incluem suporte para simultaneidade optimista.  Neste exemplo, uma vez que não 'Se CORRESP' 'Se nenhum-correspondência' é especificado nem, o conjunto de registos sempre é criado.  Esta chamada substitui qualquer registo existente definido com o mesmo nome e tipo de registo nesta zona DNS.

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>Obter zonas e conjuntos de registos

Os métodos de *DnsManagementClient.Zones.Get* e *DnsManagementClient.RecordSets.Get* obtenham zonas individuais e conjuntos de registos, respetivamente. Conjuntos de registos são identificados pelo respetivo tipo, nome e o grupo de zona e recursos que existam em. As zonas são identificadas ao respetivo nome e o grupo de recursos que existam em.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>Atualizar um conjunto de registos existente

Para atualizar o conjunto de registos de DNS existente, pela primeira vez obter o conjunto de registos, em seguida, atualizar o conteúdo do conjunto de registos, em seguida, submeta a alteração.  Neste exemplo, vamos especificar 'Etag' a partir do conjunto de registos obtidos no parâmetro 'Se CORRESP'. Falha a chamada se uma operação em simultâneo tem modificado o entretanto conjunto de registos.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>Zonas de lista e conjuntos de registos

Para as zonas de lista, utilize os métodos de *DnsManagementClient.Zones.List...* , que suportam a listagem em todas as zonas num determinado grupo de recursos ou todas as zonas numa subscrição do Azure determinada (em grupos de recursos.) Lista de conjuntos de registos, utilize *… DnsManagementClient.RecordSets.List* métodos, os quais quer listar todos os conjuntos de registos numa determinada zona ou apenas esses conjuntos de registos de um tipo específico de suporte.

Tenha em atenção quando listar zonas e conjuntos de registo que resulta podem ser paginados.  O exemplo seguinte mostra como iteração as páginas de resultados. (Um tamanho de página artificialmente pequenas de '2' é utilizado para forçar paginação; na prática deve ser omitido, este parâmetro e o tamanho de página predefinido utilizado).

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>Próximos passos

Transferir o [projeto de exemplo do Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), que inclui mais exemplos de como utilizar o SDK do .NET Azure DNS, incluindo exemplos para outros tipos de registo DNS.
