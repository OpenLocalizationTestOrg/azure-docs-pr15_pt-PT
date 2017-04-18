<properties
    pageTitle="Índice de pesquisa Azure utilizando o SDK .NET de consulta | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Criar uma consulta de pesquisa na pesquisa Azure e utilizar parâmetros de pesquisa para filtrar e ordenar resultados de pesquisa."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Índice de pesquisa do Azure utilizando o SDK .NET de consulta
> [AZURE.SELECTOR]
- [Descrição geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

Este artigo mostrar-lhe como consultar um índice utilizando o [Azure pesquisa .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de iniciar este tutorial, deverá já tiver [criado um índice de pesquisa do Azure](search-what-is-an-index.md) e [povoada-la com os dados](search-what-is-data-import.md).

Tenha em atenção que todos os códigos de exemplo neste artigo escrito c#. Pode encontrar a origem completa código [no GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Posso. Identificar o api-chave de consulta seu serviço de pesquisa do Azure
Agora que criou um índice de pesquisa do Azure, são quase pronto para emitir consultas utilizando o SDK .NET. Em primeiro lugar, terá de obter um das api-teclas de consulta que foi gerado para o serviço de pesquisa que aprovisionado. O .NET SDK irá enviar esta tecla api em todos os pedidos no seu serviço. Está a ter uma chave de válida estabelece fidedignidade, pedido por base em, entre a aplicação de enviar o pedido e o serviço que trata-lo.

1. Para localizar as chaves de api do seu serviço tem de iniciar sessão para o [Portal do Azure](https://portal.azure.com/)
2. Aceda a pá do seu serviço de pesquisa do Azure
3. Clique no ícone do "Teclas"

O serviço terá *chaves de administração* e em *consulta*.

  - As *teclas de administração* principais e secundários conceder os direitos completos para todas as operações, incluindo a capacidade de gerir o serviço, criar e eliminar os índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária se optar por gerar novamente a chave primária e vice versa.
  - As *teclas de consulta* conceder acesso só de leitura ao índices e documentos e são distribuídos normalmente para as aplicações de cliente emitem pedidos de pesquisa.

Para efeitos de consultar um índice remissivo, pode utilizar uma das suas chaves de consulta. As suas chaves administração podem também ser utilizados para consultas, mas deve utilizar uma chave de consulta no código da aplicação como isto segue melhor o [princípio do menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II. Criar uma instância da classe SearchIndexClient
Para consultas de problema com o Azure pesquisa .NET SDK, terá de criar uma instância da `SearchIndexClient` escolares. Esta classe tem várias construtores. Aquele que pretende leva-o até o nome do serviço de pesquisa, o nome do índice e um `SearchCredentials` objeto como parâmetros. `SearchCredentials`molda-se a sua chave de api.

O código abaixo cria um novo `SearchIndexClient` para o índice de "alojamento" (criado no [artigo criar um índice de pesquisa do Azure utilizando o SDK .NET](search-create-index-dotnet.md)) utilizando valores para o nome do serviço de pesquisa e a api-chave que estão armazenados no ficheiro de configuração da aplicação (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`tem um `Documents` propriedade. Esta propriedade fornece todos os métodos de que necessita de consultar índices de procura do Azure.

## <a name="iii-query-your-index"></a>III. Consultar o índice remissivo
Procurar com o .NET SDK é tão simple como estiver a ligar a `Documents.Search` método no seu `SearchIndexClient`. Este método demora alguns parâmetros, incluindo o texto de pesquisa, juntamente com um `SearchParameters` objeto que pode ser utilizado para refinar mais a consulta.

#### <a name="types-of-queries"></a>Tipos de consultas
Os dois principais [consultas de tipos de](search-query-overview.md#types-of-queries) que irá utilizar são `search` e `filter`. A `search` pesquisas para termos de uma ou mais em todos os campos _pesquisável_ no índice da consulta. A `filter` consulta avalia uma expressão booleana sobre todos os _filtráveis_ campos num índice remissivo.

As pesquisas e filtros são executados com a `Documents.Search` método. Uma consulta de pesquisa é possível passar a `searchText` parâmetro, enquanto uma expressão de filtro é possível passar a `Filter` propriedade da `SearchParameters` escolares. Para filtrar sem procurar, basta passar `"*"` para o `searchText` parâmetro. Para procurar sem filtragem, basta deixar o `Filter` propriedade anular, ou não passar num `SearchParameters` instância de todo.

#### <a name="example-queries"></a>Consultas de exemplo

O código de exemplo seguinte mostra algumas maneiras diferentes de índice "alojamento" definido no [artigo criar um índice de pesquisa do Azure utilizando o SDK .NET](search-create-index-dotnet.md#DefineIndex)de consulta. Note que os documentos devolvidos com os resultados de pesquisa são instâncias da `Hotel` classe, que tenha sido definida na [Importação de dados na pesquisa Azure utilizando o SDK .NET](search-import-data-dotnet.md#HotelClass). O código de exemplo utiliza um `WriteDocuments` método para os resultados da procura na consola de saída. Este método é descrito na secção seguinte.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV. Alça de resultados de pesquisa
O `Documents.Search` método devolve um `DocumentSearchResult` objeto que contém os resultados da consulta. O exemplo na secção anterior utilizado um método chamado `WriteDocuments` para exportar os resultados da procura na consola:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Eis o que o aspeto dos resultados de consultas, na secção anterior, partindo do princípio de que o índice de "alojamento" é povoado com os dados de exemplo na [Importação de dados na pesquisa Azure utilizando o SDK .NET](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

O código de exemplo acima utiliza a consola para exportar os resultados da pesquisa. Da mesma forma terá apresentar os resultados da pesquisa na sua própria aplicação. Consulte [Este exemplo no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) para um exemplo de como para compor resultados de pesquisa numa aplicação web baseada em ASP.NET MVC.
