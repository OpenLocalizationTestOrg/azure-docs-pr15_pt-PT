<properties
   pageTitle="Atualizar para o Azure pesquisa .NET SDK versão 1.1 | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
   description="Atualizar para o Azure pesquisa .NET SDK versão 1.1"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>Atualizar para o Azure pesquisa .NET SDK versão 2.0-pré-visualização

Se estiver a utilizar a versão 1.1 ou mais antiga do [Azure pesquisa .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx), este artigo irá ajudá-lo atualizar a aplicação para utilizar a versão principal seguinte, pré-visualização do 2.0.

Para mais geral obter instruções sobre SDK, incluindo exemplos, veja [como utilizar a pesquisa Azure a partir de uma aplicação do .NET](search-howto-dotnet-sdk.md).

Versão 2.0-pré-visualização do Azure pesquisa .NET SDK contém algumas alterações de versões anteriores. Estes principalmente são secundárias, para que o seu código deve necessário para alterar apenas esforço mínimo. Consulte [os passos para atualizar](#UpgradeSteps) para obter instruções sobre como alterar o seu código para utilizar a nova versão SDK.

> [AZURE.NOTE] Se estiver a utilizar a versão de pré-visualização do 0.13 ou mais antigo, deverá Atualize para a versão 1.1 primeiro e, em seguida, atualizar para o pré-visualização do 2.0. Consulte o artigo [anexo: passos para atualizar para a versão 1.1](#UpgradeStepsV1) para obter instruções.

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>Quais são as novidades na versão 2.0-pré-visualização

Versão 2.0-pré-visualização do é a primeira versão do Azure pesquisa .NET SDK para uma versão de pré-visualização do Azure pesquisa REST API, especificamente 2015-02-28-pré-visualização de destino. Isto torna possível utilizar muitas funcionalidades de pré-visualização do Azure pesquisa a partir de uma aplicação do .NET, incluindo o seguinte:

- [Analisadores personalizados](https://aka.ms/customanalyzers)
- Suporte de indexador de [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) e [Armazenamento de tabela do Azure](search-howto-indexing-azure-tables.md)
- Personalização de indexador através do [mapeamentos de campo](search-indexer-field-mappings.md)
- ETags suporte para ativar a atualizar em simultâneo seguros de definições de índice, indexadores e origens de dados
- Suporte para .NET Core e perfil Portable .NET 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>Passos para atualizar

Em primeiro lugar, atualizar referência NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou através da clicando com o botão direito nas referências do projecto e selecionar "Gerir NuGet pacotes..." no Visual Studio. Certifique-se de ativar a pré-lançamento pacotes selecionando "Incluir versão de pré-lançamento do" a "Gerir pacotes" NuGet janela no Visual Studio ou ao utilizar o `-IncludePrerelease` mudar se estiver a utilizar o Gestor de pacotes NuGet consola.

Assim que tiver transferido NuGet novos pacotes e respectivas dependências, reconstrua o seu projeto. Dependendo da forma como o seu código está estruturado,-reconstruir poderá com êxito. Se Sim, está pronto para ir!

Se a sua compilação falhar, deverá ver um erro de compilação semelhante ao seguinte:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

O passo seguinte é corrigir este erro compilação. Consulte o artigo [força alterações na versão 2.0-pré-visualização](#ListOfChanges) para obter detalhes sobre o que faz com que o erro e como corrigi-lo.

Poderá ver avisos de compilação adicionais relacionados com o métodos obsoletos ou propriedades. Os avisos irão incluir instruções sobre o que utilizar em vez da funcionalidade preterida. Por exemplo, se a aplicação utiliza o `SearchRequestOptions.RequestId` propriedade, deverá aparecer um aviso que indica que`"This property is deprecated. Please use ClientRequestId instead."`

Depois de ter corrigido algum erro compilação, pode efetuar alterações à aplicação para tirar partido das novas funcionalidades, se pretender. Novas funcionalidades no SDK encontram detalhadas mais [o que é novo na versão 2.0-pré-visualização](#WhatsNew).

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>Força alterações na versão de pré-visualização do 2.0

Existe apenas uma quebra de alteração na versão 2.0-pré-visualização que precisem de alterações de código para além de a reconstruir a sua aplicação.

### <a name="indexesgetclient-return-type"></a>Tipo de retorno Indexes.GetClient

O `Indexes.GetClient` método tem um novo tipo de retorno. Anteriormente, devolvido `SearchIndexClient`, mas isto ter sido alterado para `ISearchIndexClient` na versão 2.0-pré-visualização. Este é o suporte de clientes que pretende criar a `GetClient` método para carateres de testes de unidade, devolvendo uma implementação do fictício `ISearchIndexClient`.

#### <a name="example"></a>Exemplo

Se o seu código tem o seguinte aspeto:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Pode alterá-lo para esta opção para corrigir os erros de compilação:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como utilizar o Azure pesquisa .NET SDK, consulte o artigo nosso actualizados recentemente [instruções](search-howto-dotnet-sdk.md).

Vamos bem-vindo os seus comentários sobre o SDK. Se tiver problemas, à vontade pedir-nos ajuda no [Fórum MSDN do Azure pesquisa](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch). Se encontrar um erro, pode preencher um problema no [Azure .NET SDK GitHub repositório](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se para o título do problema do prefixo "pesquisa SDK:".

Obrigado por utilizar a pesquisa do Azure!

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>Anexo: Passos Atualize para a versão 1.1 

> [AZURE.NOTE] Esta secção aplica-se apenas para os utilizadores da versão Azure pesquisa .NET SDK pré-visualização do 0.13 e versões anterior.

Em primeiro lugar, atualizar referência NuGet para `Microsoft.Azure.Search` utilizando a consola de Gestor de pacotes NuGet ou através da clicando com o botão direito nas referências do projecto e selecionar "Gerir NuGet pacotes..." no Visual Studio.

Assim que tiver transferido NuGet novos pacotes e respectivas dependências, reconstrua o seu projeto.

Se anteriormente estava a utilizar a versão 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview a compilação deverá ter êxito e estiver pronto para ir!

Se estivesse a utilizar anteriormente versão 0.13.0-preview ou mais antigo, deverá ver construir erros semelhante ao seguinte:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

O passo seguinte é corrigir os erros de compilação um a um. A maior parte irão necessitar alterar alguns nomes de classe e método nomes foram mudados no SDK. [Lista da força de alterações na versão 1.1](#ListOfChangesV1) contém uma lista destas alterações de nome.

Se estiver a utilizar o classes personalizadas para modelar os seus documentos, e essas classes têm propriedades dos tipos de primitivos não anuláveis (por exemplo, `int` ou `bool` em c#), não existe um fix erros na versão 1.1 do SDK que devem ter em mente. Para obter mais detalhes, consulte [correções de erros na versão 1.1](#BugFixesV1) .

Por fim, depois de ter corrigido algum erro compilação, pode efetuar alterações à aplicação para tirar partido das novas funcionalidades, se pretender.

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>Lista de força alterações na versão 1.1

A lista seguinte é ordenada pela probabilidade que a alteração irá afetar o código da aplicação.

#### <a name="indexbatch-and-indexaction-changes"></a>Alterações de IndexBatch e IndexAction

`IndexBatch.Create`foi mudado para `IndexBatch.New` e já não tem um `params` argumento. Pode utilizar `IndexBatch.New` para lotes misturar diferentes tipos de ações (intercalações, elimina, etc.). Além disso, existem novos estáticos métodos para criar secções onde todas as acções são iguais: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`.

`IndexAction`já não tem construtores públicos e as respetivas propriedades estão agora imutáveis. Deve utilizar os novos estáticos métodos para a criação de ações para fins diferentes: `Delete`, `Merge`, `MergeOrUpload`, e `Upload`. `IndexAction.Create`foi removida. Se tiver utilizado a sobrecarga que entra em apenas um documento, certifique-se utilizar `Upload` em vez disso.

##### <a name="example"></a>Exemplo

Se o seu código tem o seguinte aspeto:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Pode alterá-lo para esta opção para corrigir os erros de compilação:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se pretender, pode simplificá-lo ainda mais a esta:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>Alterações de IndexBatchException

O `IndexBatchException.IndexResponse` propriedade foi mudada para `IndexingResults`, e o tipo de é agora `IList<IndexingResult>`.

##### <a name="example"></a>Exemplo

Se o seu código tem o seguinte aspeto:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Pode alterá-lo para esta opção para corrigir os erros de compilação:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>Alterações de método de operação

Cada operação de pesquisa Azure .NET SDK é exposta como um conjunto de overloads método síncronos e assíncronas os autores das chamadas. As assinaturas e factoring destes overloads método foi alterado na versão 1.1.

Por exemplo, a operação "Obter estatísticas de índice" em versões mais antigas do SDK expostas estas assinaturas:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

As assinaturas método para a mesma operação na versão 1.1 o seguinte aspeto:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Começar com a versão 1.1, o Azure pesquisa .NET SDK organiza os métodos de operação forma diferente:

 - Parâmetros opcionais são agora modelados como predefinido parâmetros preferir que overloads método adicional. Isto reduz o número de overloads método, por vezes significativamente.
 - Os métodos de extensão agora ocultar muitas os detalhes estranhos de HTTP a partir do autor da chamada. Por exemplo, as versões mais antigas do SDK devolvido um objeto de resposta com um código de estado HTTP, que muitas vezes não precisar de verificar porque métodos de operação accionar `CloudException` para qualquer código de estado que indica um erro. Os novos métodos de extensão apenas devolvem os objetos do modelo, pode guardar o trabalho de ter para anular a moldagem-los no seu código.
 - Por outro lado, o essencial interfaces agora os métodos de exposição que dar-lhe mais controlo ao nível da HTTP caso precise da mesma. Agora pode passar nos cabeçalhos de HTTP personalizados para ser incluídos nos pedidos e as novas `AzureOperationResponse<T>` tipo devolvido fornece-lhe acesso direto à `HttpRequestMessage` e `HttpResponseMessage` a operação de. `AzureOperationResponse`é definida na `Microsoft.Rest.Azure` espaço de nomes e substitui `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>Alterações de ScoringParameters

Uma nova com o nome de classe `ScoringParameter` foi adicionado no SDK mais recente para que seja mais fácil forneça os parâmetros para os perfis de pontuação numa consulta de pesquisa. Anteriormente a `ScoringProfiles` propriedade do `SearchParameters` classe foi introduzida como `IList<string>`; Agora está escrito como `IList<ScoringParameter>`.

##### <a name="example"></a>Exemplo

Se o seu código tem o seguinte aspeto:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Pode alterá-lo para esta opção para corrigir os erros de compilação: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Alterações do modelo de classe

Devido a assinatura é alterado descrito na [altera o método de operação](#OperationMethodChanges), muitas classes na `Microsoft.Azure.Search.Models` espaço de nomes foram cujo nome foi mudado ou removidos. Por exemplo:

 - `IndexDefinitionResponse`foi substituído pelo`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`foi mudado para`DocumentSearchResult`
 - `IndexResult`foi mudado para`IndexingResult`
 - `Documents.Count()`Devolve agora uma `long` com a contagem de documento em vez de um`DocumentCountResponse`
 - `IndexGetStatisticsResponse`foi mudado para`IndexGetStatisticsResult`
 - `IndexListResponse`foi mudado para`IndexListResult`

Resumindo, `OperationResponse`-derivado de classes que existiam só para moldar foram removidas um objeto de modelo. As classes restantes tiveram os respetivos sufixo mudou de `Response` para `Result`.

##### <a name="example"></a>Exemplo

Se o seu código tem o seguinte aspeto:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Pode alterá-lo para esta opção para corrigir os erros de compilação:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Classes de resposta e IEnumerable

Uma alteração adicional que possam afetar o seu código for classes de resposta que mantenha coleções de sites já não implementam `IEnumerable<T>`. Em vez disso, pode aceder diretamente a propriedade de coleções de sites. Por exemplo, se o seu código tem o seguinte aspeto:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Pode alterá-lo para esta opção para corrigir os erros de compilação:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>Nota importante para aplicações web

Se tiver uma aplicação web que serializes `DocumentSearchResponse` directamente para enviar os resultados da pesquisa para o browser, terá de alterar o seu código ou os resultados não irão serializar corretamente. Por exemplo, se o seu código tem o seguinte aspeto:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Pode alterá-lo ao obter o `.Results` propriedade da resposta à pesquisa para corrigir composição de resultado de pesquisa:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Terá de procure nestes casos no seu código si próprio; **O compilador irá não avisá-lo** porque `JsonResult.Data` é do tipo `object`.

#### <a name="cloudexception-changes"></a>Alterações de CloudException

O `CloudException` classe foi movido a partir do `Hyak.Common` espaço de nomes para o `Microsoft.Rest.Azure` espaço de nomes. Além disso, o `Error` propriedade foi mudada para `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>Alterações de SearchServiceClient e SearchIndexClient

O tipo do `Credentials` propriedade mudou de `SearchCredentials` a respectiva classe base, `ServiceClientCredentials`. Se precisar de aceder a `SearchCredentials` de um `SearchIndexClient` ou `SearchServiceClient`, utilize a nova `SearchCredentials` propriedade.

Em versões mais antigas do SDK `SearchServiceClient` e `SearchIndexClient` tinha construtores que tirou com um `HttpClient` parâmetro. Estes foram substituídas pelos construtores que um `HttpClientHandler` e uma matriz de `DelegatingHandler` objetos. Isto torna mais fácil de instalar personalizados processadores de pré-processamento de pedidos HTTP se for necessário.

Por fim, os construtores que tirou com um `Uri` e `SearchCredentials` foram alteradas. Por exemplo, se tiver o código que tem este aspeto:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Pode alterá-lo para esta opção para corrigir os erros de compilação:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Tenha também em atenção que o tipo do parâmetro credenciais foi alterado para `ServiceClientCredentials`. Este é pouco a afetar o seu código desde `SearchCredentials` deriva da `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Prisma um ID do pedido

Em versões mais antigas do SDK, que poderia definiu um ID de pedido de `SearchServiceClient` ou `SearchIndexClient` e seria serão incluída no cada pedido à REST API. Isto é útil para resolução de problemas com o seu serviço de pesquisa se precisar de contactar o suporte. No entanto, é mais útil para definir um ID exclusivo pedido para cada operação em vez de utilizar o mesmo ID para todas as operações. Por esta razão, o `SetClientRequestId` métodos de `SearchServiceClient` e `SearchIndexClient` foram removidas. Em vez disso, pode ser efetuada com um ID de pedido cada método de operação através do opcional `SearchRequestOptions` parâmetro.

> [AZURE.NOTE] Um lançamento futuro do SDK, irá adicionar um novo mecanismo para configurar um ID de pedido globalmente no cliente de estação de objetos que está consistente com a abordagem utilizada por outro SDK Azure.

#### <a name="example"></a>Exemplo

Se tiver o código que tem este aspeto:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Pode alterá-lo para esta opção para corrigir os erros de compilação:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Alterações de nome de interface

Os nomes de interface de grupo de operação foram alterados que sejam consistentes com nomes de propriedade correspondentes:

 - O tipo de `ISearchServiceClient.Indexes` ter mudado de `IIndexOperations` para `IIndexesOperations`.
 - O tipo de `ISearchServiceClient.Indexers` ter mudado de `IIndexerOperations` para `IIndexersOperations`.
 - O tipo de `ISearchServiceClient.DataSources` ter mudado de `IDataSourceOperations` para `IDataSourcesOperations`.
 - O tipo de `ISearchIndexClient.Documents` ter mudado de `IDocumentOperations` para `IDocumentsOperations`.

Esta alteração é provável que afetam o seu código, a menos que criou mocks destas interfaces para fins de teste.

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>Correções de erros na versão 1.1

Ocorreu um erro em versões mais antigas do Azure pesquisa .NET SDK relacionado com a serialização de classes modelo personalizado. O erro pode ocorrer se tiver criado uma classe de modelo personalizado com uma propriedade de um tipo de valor que não sejam anuláveis.

#### <a name="steps-to-reproduce"></a>Passos para reproduzir

Crie uma classe de modelo personalizado com uma propriedade de tipo de valor que não sejam anuláveis. Por exemplo, adicionar um público `UnitCount` propriedade do tipo `int` em vez de `int?`.

Se indexar um documento com o valor predefinido desse tipo (por exemplo, 0 para `int`), o campo será nulo na pesquisa Azure. Se procurar esse documento posteriormente o `Search` chamada irá gerar `JsonSerializationException` reclamar que não é possível converter `null` para `int`.

Além disso, os filtros poderão não funcionar como esperado, desde que foi escrita null ao índice em vez do valor pretendido.

#### <a name="fix-details"></a>Corrigir detalhes

Vamos ter fixo este problema na versão 1.1 do SDK. Agora, se tiver uma classe do modelo da seguinte forma:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e configurar o `IntValue` como 0, esse valor é agora corretamente serializado como "0" no fio e armazenado como 0 no índice remissivo. Arred subtensões também funciona conforme esperado.

Existe um problema potencial ter em consideração com esta abordagem: Se utilizar um tipo de modelo com uma propriedade não anuláveis, tem de **garantir** que não existem documentos no índice de contêm um valor nulo para o campo correspondente. Nem o SDK nem o Azure pesquisa REST API irá ajudá-lo para impor esta.

Não se trata de apenas uma questão hipotética: Imagine um cenário onde adicionar um novo campo a um índice existente que é do tipo `Edm.Int32`. Depois de atualizar a definição de índice, todos os documentos terá um valor nulo para esse campo novo (uma vez que todos os tipos de são anuláveis na pesquisa Azure). Se, em seguida, utilizar uma classe de modelo com uma não nulos `int` propriedade para esse campo, irá obter um `JsonSerializationException` da seguinte forma quando tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esta razão, recomendamos ainda que utilize tipos de anuláveis nas suas Turmas modelo como prática recomendada.

Para obter mais detalhes sobre este erro e o fix, consulte o artigo [este problema no GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).
