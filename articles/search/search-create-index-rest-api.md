<properties
    pageTitle="Criar um índice de pesquisa do Azure utilizar a API REST | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Crie um índice remissivo no código Azure pesquisa HTTP REST API a utilizar."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>Criar um índice de pesquisa do Azure utilizar a API REST
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)


Este artigo irá guiá-lo durante o processo de criação de um [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) de pesquisa do Azure utilizando a pesquisa REST API do Azure.

Antes de seguir este guia e criar um índice remissivo, deverá ter já [criou um serviço de pesquisa do Azure](search-create-service-portal.md).

Para criar um índice de pesquisa do Azure utilizar a API REST, irá emitir um pedido de HTTP POST único para o ponto final de URL do seu serviço de pesquisa do Azure. Sua definição de índice remissivo irá estar contida no corpo do pedido como conteúdo JSON correcto.


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Posso. Identificar a chave de api do administrador do seu serviço de pesquisa do Azure
Agora que tem aprovisionado um serviço de pesquisa do Azure, pode emitir pedidos de HTTP contra ponto final de URL do seu serviço utilizar a API REST. No entanto, *todas as* API pedidos tem de incluir api-chave que foi gerada do serviço de pesquisa que aprovisionado. Está a ter uma chave de válida estabelece fidedignidade, pedido por base em, entre a aplicação de enviar o pedido e o serviço que trata-lo.

1. Para localizar as chaves de api do seu serviço tem de iniciar sessão para o [Portal do Azure](https://portal.azure.com/)
2. Aceda a pá do seu serviço de pesquisa do Azure
3. Clique no ícone do "Teclas"

O serviço terá *chaves de administração* e em *consulta*.

 - As *teclas de administração* principais e secundários conceder os direitos completos para todas as operações, incluindo a capacidade de gerir o serviço, criar e eliminar os índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária se optar por gerar novamente a chave primária e vice versa.
 - As *teclas de consulta* conceder acesso só de leitura ao índices e documentos e são distribuídos normalmente para as aplicações de cliente emitem pedidos de pesquisa.

Para efeitos de criação de um índice remissivo, pode utilizar a chave de administrador principal ou secundário.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Definir o índice de pesquisa do Azure utilizando JSON correctamente concebido
Um pedido de HTTP POST único para o seu serviço irá criar o índice remissivo. Corpo do seu pedido de HTTP POST irá conter um único objeto JSON que define o índice de pesquisa do Azure.

1. A primeira propriedade deste objecto JSON é o nome do seu índice.
2. A propriedade segunda deste objecto JSON é uma matriz JSON denominada `fields` que contém um objeto JSON separado para cada campo no seu índice. Cada um destes objectos JSON contêm vários pares valor/nome para cada um dos atributos de campo incluindo "nome", "tipo", etc.

É importante ter as pesquisa utilizador experiência empresas suas necessidades e em conta ao estruturar o índice remissivo, tal como cada campo tem de ser atribuído os [atributos inicial maiúscula](https://msdn.microsoft.com/library/azure/dn798941.aspx). Aplicar estas controlam atributos que procura funcionalidades (filtragem, faceting, ordenação pesquisa de texto completo, etc.) para os campos. Para qualquer atributo que não especificar, a predefinição será para ativar a funcionalidade de pesquisa correspondente, a menos que especificamente desativá-lo.

Para o nosso exemplo, com o nome nosso índice "alojamento" e os nossos campos definidos da seguinte forma:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Vamos cuidadosamente ter escolhido os atributos de índice remissivo para cada campo com base em como podemos pensa que serão utilizadas numa aplicação. Por exemplo, `hotelId` é uma chave exclusiva que as pessoas se procurar alojamento provável que não têm como saber, para que desativaremos o texto completo procurar esse campo definindo `searchable` para `false`, que permite poupar espaço no índice remissivo.

Tenha em atenção que exatamente um campo no seu índice do tipo de `Edm.String` tem de ser designado como o campo 'key'.

A definição de índice acima utiliza um analisador de idioma personalizadas para o `description_fr` campo porque destina-se para armazenar texto em francês. Consulte o artigo [o idioma que suporte o tópico sobre MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) , bem como a correspondente [no blogue](https://azure.microsoft.com/blog/language-support-in-azure-search/) para obter mais informações sobre analisadores de idioma.

## <a name="iii-issue-the-http-request"></a>III. Emitir o pedido de HTTP
1. Utilizar a sua definição de índice remissivo como o corpo do pedido, emita um pedido de HTTP POST para o seu URL de ponto final de serviço de pesquisa do Azure. No URL, certifique-se utilizar o seu nome de serviço como o nome de anfitrião e colocar boa `api-version` como um parâmetro da cadeia de consulta (a versão atual do API é `2015-02-28` no momento da publicação deste documento).
2. Nos cabeçalhos dos pedidos, especifique o `Content-Type` como `application/json`. Também terá de fornecer chave de administrador do seu serviço identificados no passo I a `api-key` cabeçalho.


Terá que fornecer o seu próprio chave de nome e api do serviço para emitir o pedido abaixo:


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


Para um pedido de efetuada com êxito, deverá ver código de estado 201 (criado). Para mais informações sobre como criar um índice remissivo através do REST API, visite a referência de API no [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Para mais informações sobre outros códigos de estado HTTP que podem ser devolvido em caso de falha, consulte o artigo [códigos de estado HTTP (Azure procura)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Quando terminar com um índice remissivo e pretende eliminá-lo, basta emita um pedido de HTTP eliminar. Por exemplo, este é como podemos eliminaria o índice de "alojamento":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Seguinte
Depois de criar um índice de pesquisa do Azure, estará pronto para [carregar o seu conteúdo para o índice remissivo](search-what-is-data-import.md) para poder começar a procurar os seus dados.
