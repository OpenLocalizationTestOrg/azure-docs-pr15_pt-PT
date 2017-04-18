<properties
    pageTitle="Criar um índice de pesquisa do Azure utilizando o SDK .NET | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Crie um índice remissivo no código utilizando o Azure pesquisa .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Criar um índice de pesquisa do Azure utilizando o SDK .NET
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)


Este artigo irá guiá-lo durante o processo de criação de um [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) de pesquisa do Azure utilizando o [Azure pesquisa .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de seguir este guia e criar um índice remissivo, deverá ter já [criou um serviço de pesquisa do Azure](search-create-service-portal.md).

Tenha em atenção que todos os códigos de exemplo neste artigo escrito c#. Pode encontrar a origem completa código [no GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Posso. Identificar a chave de api do administrador do seu serviço de pesquisa do Azure
Agora que tem aprovisionado um serviço de pesquisa do Azure, são quase pronto para emitir pedidos do ponto final de serviço utilizando o SDK .NET. Em primeiro lugar, terá de obter um das administração api-chaves que foi gerado para o serviço de pesquisa que aprovisionado. O .NET SDK irá enviar esta tecla api em todos os pedidos no seu serviço. Está a ter uma chave de válida estabelece fidedignidade, pedido por base em, entre a aplicação de enviar o pedido e o serviço que trata-lo.

1. Para localizar as chaves de api do seu serviço tem de iniciar sessão para o [Portal do Azure](https://portal.azure.com/)
2. Aceda a pá do seu serviço de pesquisa do Azure
3. Clique no ícone do "Teclas"

O serviço terá *chaves de administração* e em *consulta*.

  - As *teclas de administração* principais e secundários conceder os direitos completos para todas as operações, incluindo a capacidade de gerir o serviço, criar e eliminar os índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária se optar por gerar novamente a chave primária e vice versa.
  - As *teclas de consulta* conceder acesso só de leitura ao índices e documentos e são distribuídos normalmente para as aplicações de cliente emitem pedidos de pesquisa.

Para efeitos de criação de um índice remissivo, pode utilizar a chave de administrador principal ou secundário.

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Criar uma instância da classe SearchServiceClient
Para começar a utilizar o Azure pesquisa .NET SDK, terá de criar uma instância da `SearchServiceClient` escolares. Esta classe tem várias construtores. Aquele que pretende entra em seu nome de serviço de pesquisa e uma `SearchCredentials` objeto como parâmetros. `SearchCredentials`molda-se a sua chave de api.

O código abaixo cria um novo `SearchServiceClient` utilizando valores para o nome do serviço de pesquisa e a api-chave que estão armazenados no ficheiro de configuração da aplicação (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`tem um `Indexes` propriedade. Esta propriedade fornece todos os métodos de que precisa para criar, lista, atualizar ou eliminar índices de procura do Azure.

> [AZURE.NOTE] O `SearchServiceClient` classe gere ligações para o serviço de pesquisa. Para evitar abrir demasiadas ligações, deverá tentar partilhar uma única ocorrência de `SearchServiceClient` na sua aplicação se possível. Seus métodos estão seguros tópico para ativar a essa partilha.

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. Definir o seu índice de pesquisa do Azure utilizando o `Index` classe
Uma única chamada para o `Indexes.Create` método irá criar o índice remissivo. Este método demora como um parâmetro uma `Index` objeto que define o índice de pesquisa do Azure. Tem de criar um `Index` objeto e inicialização-lo da seguinte forma:

1. Definir o `Name` propriedade do `Index` objeto para o nome do seu índice.
2. Definir o `Fields` propriedade do `Index` objeto para uma matriz de `Field` objetos. Cada um da `Field` objetos define o comportamento de um campo no seu índice. Pode fornecer o nome do campo para o construtor, juntamente com o tipo de dados (ou analyzer para campos de cadeia). Também pode definir outras propriedades como `IsSearchable`, `IsFilterable`, etc.

É importante ter as pesquisa utilizador experiência empresas suas necessidades e em conta ao estruturar o seu índice como cada `Field` tem de ser atribuída as [propriedades adequadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Aplicar estas controlar de propriedades que procura funcionalidades (filtragem, faceting, ordenação pesquisa de texto completo, etc.) a quais os campos. Para qualquer propriedade não explicitamente definir, o `Field` classe predefinições para desativar a funcionalidade de pesquisa correspondente, a menos que especificamente ativá-la.

Para o nosso exemplo, com o nome nosso índice "alojamento" e os nossos campos definidos da seguinte forma:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Vamos cuidadosamente escolheu os valores de propriedade para cada `Field` com base em como podemos pensa que serão utilizadas numa aplicação. Por exemplo, é provável que as pessoas procurar alojamento irão estar interessadas correspondências de palavra-chave na `description` de campo, para que recomendamos Active a pesquisa de texto completo para esse campo definindo `IsSearchable` para `true`.

Tenha em atenção que exatamente um campo no seu índice do tipo de `DataType.String` tem de ser designado como o campo de _chave_ definindo `IsKey` para `true` (consulte o artigo `hotelId` no exemplo acima).

A definição de índice acima utiliza um analisador de idioma personalizadas para o `description_fr` campo porque destina-se para armazenar texto em francês. Consulte o artigo [o idioma que suporte o tópico sobre MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) , bem como a correspondente [no blogue](https://azure.microsoft.com/blog/language-support-in-azure-search/) para obter mais informações sobre analisadores de idioma.

> [AZURE.NOTE]  Tenha em atenção que, passando `AnalyzerName.FrLucene` no construtor, o `Field` vão ser automaticamente do tipo de `DataType.String` e terão `IsSearchable` definido para `true`.

## <a name="iv-create-the-index"></a>IV. Criar um índice
Agora que tem um inicializado `Index` objecto, pode criar o índice basta ao contactar o suporte `Indexes.Create` no seu `SearchServiceClient` objeto:

```csharp
serviceClient.Indexes.Create(definition);
```

Para um pedido de efetuada com êxito, o método irá devolver normalmente. Se existir um problema com o pedido, tal como um parâmetro inválido, o método irá gerar `CloudException`.

Quando terminar com um índice remissivo e pretende eliminá-lo, apenas ligar a `Indexes.Delete` método no seu `SearchServiceClient`. Por exemplo, este é como podemos eliminaria o índice de "alojamento":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] O código de exemplo neste artigo utiliza os métodos síncronos do Azure pesquisa .NET SDK para simplificar. Recomendamos que utilize os métodos assíncronos nas suas próprias aplicações para mantê-las dimensionáveis e capacidade de resposta. Por exemplo, nos exemplos acima é utilizar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.

## <a name="next"></a>Seguinte
Depois de criar um índice de pesquisa do Azure, estará pronto para [carregar o seu conteúdo para o índice remissivo](search-what-is-data-import.md) para poder começar a procurar os seus dados.
