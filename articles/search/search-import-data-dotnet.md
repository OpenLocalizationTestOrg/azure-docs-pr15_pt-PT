<properties
    pageTitle="Carregar dados na pesquisa Azure utilizando o SDK .NET | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Saiba como carregar dados para um índice remissivo na pesquisa Azure utilizando o SDK .NET."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Carregar dados para pesquisa Azure utilizando o SDK .NET
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTO](search-import-data-rest-api.md)

Este artigo mostrar-lhe como utilizar o [Azure pesquisa .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) para importar dados para um índice de pesquisa do Azure.

Antes de iniciar este tutorial, já deve ter [criado um índice de pesquisa do Azure](search-what-is-an-index.md). Este artigo também assume que já criou um `SearchServiceClient` objecto, conforme mostrado no [artigo criar um índice de pesquisa do Azure utilizando o SDK .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

Tenha em atenção que todos os códigos de exemplo neste artigo escrito c#. Pode encontrar a origem completa código [no GitHub](http://aka.ms/search-dotnet-howto).

Para transmitir documentos para o índice remissivo utilizando o SDK .NET, terá:

  1. Criar um `SearchIndexClient` objeto para ligar ao índice de pesquisa.
  2. Criar um `IndexBatch` que contém os documentos para ser adicionadas, modificadas ou eliminadas.
  3. Ligar a `Documents.Index` método do seu `SearchIndexClient` para enviar a `IndexBatch` ao seu índice de pesquisa.

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>Posso. Criar uma instância da classe SearchIndexClient
Para importar dados para o índice remissivo utilizando o SDK do Azure pesquisa .NET, terá de criar uma instância da `SearchIndexClient` escolares. Pode construir esta instância de si, mas é mais fácil se já tiver uma `SearchServiceClient` instância para ligar a sua `Indexes.GetClient` método. Por exemplo, eis como obteria um `SearchIndexClient` para o índice chamado "alojamento" a partir de um `SearchServiceClient` denominada `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] Numa aplicação de pesquisa típica, gestão de índice remissivo e a população é processada por um componente separado de consultas de pesquisa. `Indexes.GetClient`é conveniente para preencher um índice remissivo, uma vez que guarda os problemas ao fornecer a outro `SearchCredentials`. Faz isto, passando a chave de administrador que utilizou para criar o `SearchServiceClient` para o novo `SearchIndexClient`. No entanto, na parte da sua aplicação que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que possa passar de uma chave de consulta em vez de uma chave de administrador. Este é compatível com o [princípio do menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e irá ajudar a tornar a sua aplicação mais segura. Pode encontrar mais informações sobre chaves de administração e consulta na [referência Azure pesquisa REST API no MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient`tem um `Documents` propriedade. Esta propriedade fornece todos os métodos de que precisa de adicionar, modificar, eliminar ou consultar documentos no seu índice.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decidir qual ação indexação para utilizar
Para importar dados utilizando o .NET SDK, terá de compactar dos seus dados para um `IndexBatch` objeto. Um `IndexBatch` contém uma coleção de `IndexAction` objetos, cada um dos quais contém um documento e uma propriedade que lhe diz Azure pesquisa que ação a efetuar nesse documento (carregamento, impressão em série, eliminar, etc). Dependendo de qual da abaixo ações escolher, apenas determinados campos devem ser incluídos para cada documento:

Ação | Descrição | Campos necessários para cada documento | Notas
--- | --- | --- | ---
`Upload` | Um `Upload` acção é semelhante a um "upsert" onde o documento será inserido se é novo e atualizados/substituído se existir. | chave, bem como quaisquer outros campos que pretende definir | Quando atualizar/substituição de um documento existente, qualquer campo que não for especificado no pedido de terão o campo definido como `null`. Isto ocorre o mesmo quando o campo anteriormente foi definido como um valor não nulo.
`Merge` | Atualiza um documento existente com os campos especificados. Se o documento não existir no índice remissivo, irá falhar a impressão em série. | chave, bem como quaisquer outros campos que pretende definir | Qualquer campo que especificar numa intercalação de irá substituir o campo existente no documento. Isto inclui campos tipo `DataType.Collection(DataType.String)`. Por exemplo, se o documento contém um campo `tags` com valor `["budget"]` e executar uma impressão em série com valor `["economy", "pool"]` para `tags`, o valor final da `tags` campo será `["economy", "pool"]`. Não serão `["budget", "economy", "pool"]`.
`MergeOrUpload` | Esta ação comporta-se como `Merge` se já existe um documento com a tecla determinado no índice remissivo. Se o documento não existir, comporta-se como `Upload` com um novo documento. | chave, bem como quaisquer outros campos que pretende definir | -
`Delete` | Remove o documento especificado do índice. | chave apenas | Quaisquer campos que especificar que o campo de chave será ignorado. Se pretende remover um campo individual de um documento, utilize `Merge` em vez disso e basta definir o campo explicitamente nulo.

Pode especificar que ação que pretende utilizar com os diversos estáticos métodos da `IndexBatch` e `IndexAction` classes, conforme mostrado na secção seguinte.

## <a name="iii-construct-your-indexbatch"></a>III. Construir a sua IndexBatch
Agora que já conhece as ações para efetuar nos seus documentos, está pronto para construir o `IndexBatch`. O exemplo abaixo mostra como criar um lote com algumas ações diferentes. Note que o nosso exemplo usa uma classe personalizada denominada `Hotel` que mapas a um documento no índice de "alojamento".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Neste caso, estamos a utilizar `Upload`, `MergeOrUpload`, e `Delete` como nossas ações de pesquisa, conforme especificado pelo métodos designado pelo `IndexAction` escolares.

Partem do princípio de que este índice do exemplo "alojamento" já é povoada com um número de documentos. Tenha em atenção como não temos que especificar a todos os campos de documento possíveis ao utilizar `MergeOrUpload` e como podemos especificado apenas a chave de documento (`HotelId`) ao utilizar `Delete`.

Além disso, tenha em atenção que só pode incluir até 1000 documentos num único pedido de indexação.

> [AZURE.NOTE] Neste exemplo, vamos está a aplicar ações diferentes para tipos diferentes de documentos. Se pretender executar as mesmas ações em todos os documentos o lote e, em vez de chamada `IndexBatch.New`, pode utilizar os outros métodos de estáticos `IndexBatch`. Por exemplo, poderia criar secções ao contactar o suporte `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, ou `IndexBatch.Delete`. Estes métodos tirar uma coleção de documentos (objetos do tipo de `Hotel` neste exemplo) em vez de `IndexAction` objetos.

## <a name="iv-import-data-to-the-index"></a>IV. Importar dados para o índice remissivo
Agora que tem um inicializado `IndexBatch` objecto, pode enviá-la para o índice ao contactar o suporte `Documents.Index` no seu `SearchIndexClient` objeto. O exemplo seguinte mostra como chamar `Index`, bem como alguns passos adicionais que terá de realizar:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Nota a `try` / `catch` adjacente a chamada para o `Index` método. O bloco de capturas trata um caso de erro importantes a indexação. Se o seu serviço de pesquisa do Azure falha incluir no índice remissivo alguns documentos no lote de um `IndexBatchException` é devolvido por `Documents.Index`. Isto pode acontecer se está a indexar documentos enquanto o seu serviço está em caso de carga muito carregado. **Recomendamos vivamente explicitamente processamento neste caso no seu código.** Pode atrasar e, em seguida, volte a tentar indexação os documentos que ocorreu uma falha ou pode iniciar sessão e continuar a como é que a amostra, ou que pode fazer algo dependendo requisitos de consistência de dados da sua aplicação.

Por fim, o código no exemplo acima atrasos durante dois segundos. Indexação acontece modo assíncrono no seu serviço de pesquisa do Azure, para que a aplicação de exemplo necessita de esperar uma hora abreviada para se certificar de que os documentos estão disponíveis para procurar. Atrasos da seguinte forma apenas são normalmente necessários no demos, testes e aplicações de exemplo.

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>Como o .NET SDK trata os documentos

Poderá estar a pensar como é poderá carregar instâncias de uma classe definidos pelo utilizador, como o Azure pesquisa .NET SDK `Hotel` ao índice. Para ajudar a responder essa pergunta, vamos ver o `Hotel` classe, que mapas ao esquema de índice remissivo definidos no [artigo criar um índice de pesquisa do Azure utilizando o SDK .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

A primeira coisa a Repare é que cada propriedade pública de `Hotel` corresponde a um campo na definição do índice remissivo, mas com uma diferença crucial: O nome de cada campo começa por uma letra minúscula ("camelo caso"), enquanto o nome de cada propriedade público de `Hotel` começa por uma letra de maiúsculas ("Pascal maiúsculas/minúsculas"). Este é um cenário comuns nas aplicações de .NET que efetuar a ligação de dados onde o esquema de destino está fora do controlo do programador da aplicação. Em vez de ter violar .NET nomenclatura diretrizes ao tornar a propriedade nomes camelo-maiúsculas/minúsculas, pode descobrir o SDK para mapear os nomes das propriedades para camelo-maiúsculas/minúsculas automaticamente com a `[SerializePropertyNamesAsCamelCase]` atributo.

> [AZURE.NOTE] O Azure pesquisa .NET SDK utiliza a biblioteca de [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) serializar e serialização os objetos do modelo personalizado de e para a JSON. Pode personalizar este serialização caso seja necessário. Pode encontrar mais detalhes no [actualizar para o Azure pesquisa .NET SDK versão 1.1](search-dotnet-sdk-migration.md#WhatsNew). Um exemplo de esta é a utilização do `[JsonProperty]` atributo na `DescriptionFr` propriedade no código de exemplo acima.

O segundo dado importante sobre o `Hotel` classe são os tipos de dados das propriedades do públicos. Mapear os tipos de .NET estas propriedades para os tipos de campo equivalente a definição de índice. Por exemplo, o `Category` cadeia mapas de propriedade para o `category` campo, que é do tipo `DataType.String`. Existem semelhantes mapeamentos de tipo entre `bool?` e `DataType.Boolean`, `DateTimeOffset?` e `DataType.DateTimeOffset`, etc. As regras específicas para o mapeamento de tipo encontram-se documentados com o `Documents.Get` método no [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Esta capacidade de utilizar a suas própria classes como documentos funciona em ambas as direções; Também pode obter resultados de pesquisa e ter o SDK automaticamente serialização-los para um tipo de sua escolha, conforme mostrado no [artigo seguinte](search-query-dotnet.md).

> [AZURE.NOTE] O Azure pesquisa .NET SDK também suporta dinamicamente escreveu documentos utilizando o `Document` classe, que é um mapeamento de valor/chave de nomes de campos para valores do campo. Isto é útil em cenários em caso não saiba qual é o esquema de índice remissivo no momento da estruturação ou seria não vincular a classes modelo específico. Todos os métodos o SDK lidar com documentos tem overloads que funcionam com o `Document` escolares, bem como overloads escreveu vivamente que assumem um parâmetro de tipo genérico. Apenas o último é utilizado no código de exemplo neste artigo. Pode saber mais sobre o `Document` classe [no MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Uma nota importante sobre tipos de dados**

Ao estruturar o seus próprio classes de modelo para mapear para um índice de pesquisa do Azure, recomendamos que declarar como propriedades de tipos de valor `bool` e `int` para ser anuláveis (por exemplo, `bool?` em vez de `bool`). Se utilizar uma propriedade não anuláveis, tem de **garantir** que não existem documentos no índice de contêm um valor nulo para o campo correspondente. Nem o SDK nem o serviço de pesquisa do Azure irá ajudá-lo para impor esta.

Não se trata de apenas uma questão hipotética: Imagine um cenário onde adicionar um novo campo a um índice existente que é do tipo `DataType.Int32`. Depois de atualizar a definição de índice, todos os documentos terá um valor nulo para esse campo novo (uma vez que todos os tipos de são anuláveis na pesquisa Azure). Se, em seguida, utilizar uma classe de modelo com uma não nulos `int` propriedade para esse campo, irá obter um `JsonSerializationException` da seguinte forma quando tentar obter documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esta razão, recomendamos que utilize tipos de anuláveis nas suas Turmas modelo como prática recomendada.

## <a name="next"></a>Seguinte
Depois de povoar o índice de pesquisa do Azure, estará pronto para começar a emitir consultas para procurar documentos. Consulte o artigo [Consulta o índice de pesquisa de Azure](search-query-overview.md) para obter detalhes.
