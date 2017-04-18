<properties
    pageTitle="Índice de pesquisa Azure utilizar a API REST de consulta | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Criar uma consulta de pesquisa na pesquisa Azure e utilizar parâmetros de pesquisa para filtrar e ordenar resultados de pesquisa."
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Índice de pesquisa do Azure utilizar a API REST de consulta
> [AZURE.SELECTOR]
- [Descrição geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

Este artigo mostrar-lhe como consultar um índice remissivo utilizando o [Azure pesquisa REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Antes de iniciar este tutorial, deverá já tiver [criado um índice de pesquisa do Azure](search-what-is-an-index.md) e [povoada-la com os dados](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Posso. Identificar o api-chave de consulta seu serviço de pesquisa do Azure
Um componente da chave de cada operação de pesquisa contra o Azure pesquisa REST API é a *api chave* que foi criado para o serviço que aprovisionado. Está a ter uma chave de válida estabelece fidedignidade, pedido por base em, entre a aplicação de enviar o pedido e o serviço que trata-lo.

1. Para localizar as chaves de api do seu serviço tem de iniciar sessão para o [Portal do Azure](https://portal.azure.com/)
2. Aceda a pá do seu serviço de pesquisa do Azure
3. Clique no ícone do "Teclas"

O serviço terá *chaves de administração* e em *consulta*.

 - As *teclas de administração* principais e secundários conceder os direitos completos para todas as operações, incluindo a capacidade de gerir o serviço, criar e eliminar os índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária se optar por gerar novamente a chave primária e vice versa.
 - As *teclas de consulta* conceder acesso só de leitura ao índices e documentos e são distribuídos normalmente para as aplicações de cliente emitem pedidos de pesquisa.

Para efeitos de consultar um índice remissivo, pode utilizar uma das suas chaves de consulta. As suas chaves administração podem também ser utilizados para consultas, mas deve utilizar uma chave de consulta no código da aplicação como isto segue melhor o [princípio do menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Elaborar a sua consulta
Existem duas formas de [índice de utilizar a API REST de pesquisa](https://msdn.microsoft.com/library/azure/dn798927.aspx). Uma forma é emitir um pedido de HTTP POST onde os parâmetros de consulta serão definidos num objeto JSON no corpo do pedido. A forma como é emitir um pedido HTTP GET onde os parâmetros de consulta serão definidos dentro o URL do pedido. Note que mensagem tem mais [reduzida limites de](https://msdn.microsoft.com/library/azure/dn798927.aspx) tamanho dos parâmetros de consulta que obter. Por esta razão, recomendamos que utilize mensagem, exceto se tiver circunstâncias especiais onde utilizando GET seria mais conveniente.

Para registar e obter, tem de fornecer o *nome do serviço*, *nome do índice*e a *versão da API* de adequada (a versão atual do API é `2015-02-28` no momento da publicação deste documento) no URL pedido. Para obter, a *cadeia de consulta* no final do URL será onde fornecer os parâmetros de consulta. Consulte abaixo para o formato de URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

O formato de mensagem é o mesmo, mas com apenas api versão no parâmetros da cadeia de consulta.



#### <a name="example-queries"></a>Consultas de exemplo

Aqui estão alguns consultas de exemplo de um índice com o nome "alojamento". Estas consultas são apresentadas no formato GET e POST.

Todo o índice de pesquisa para o termo 'Orçamento' e regressar apenas a `hotelName` campo:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Aplicar um filtro a um índice para encontrar o alojamento menos dispendioso a $150 da noite e voltar a `hotelId` e `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Pesquisar todo o índice, ordem por um campo específico (`lastRenovationDate`) por ordem descendente, ter os resultados de dois primeiros e mostrar apenas `hotelName` e `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Submeter o pedido de HTTP
Agora que tem elaborado a sua consulta como parte do seu URL do pedido HTTP (para obter) ou o corpo (para registar), pode definir cabeçalhos ao pedido e submeter a sua consulta.

#### <a name="request-and-request-headers"></a>Pedido e pedido de cabeçalhos
Tem de definir dois cabeçalhos de pedido para obter ou três para mensagem:
1. O `api-key` cabeçalho tem de estar definido para a chave de consulta que que se encontram no passo posso acima. Tenha em atenção que também pode utilizar uma chave de administração, como o `api-key` cabeçalho, mas é recomendado que utilize uma chave de consulta como exclusivamente concede acesso só de leitura para índices e documentos.
2. O `Accept` cabeçalho tem de estar definido `application/json`.
3. Para publicar apenas, o `Content-Type` cabeçalho também deve ser definido para `application/json`.

Consulte abaixo para um pedido HTTP GET pesquisar o índice de "alojamento" utilizar a API Azure pesquisa resto, utilizando uma consulta simple que procura para o termo "companhia":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Eis a mesma consulta de exemplo, desta vez utilizando HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Um pedido de consulta efetuada com êxito irá resultar num código de estado de `200 OK` e os resultados da pesquisa são devolvidos como JSON no corpo da resposta. Eis os resultados para o aspeto de consulta acima, como partindo do princípio de que o índice de "alojamento" é povoado com os dados de exemplo na [Importação de dados na pesquisa Azure utilizar a API REST](search-import-data-rest-api.md) (tenha em atenção que a JSON tiver sido formatado para maior clareza).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Para saber mais, visite a secção de "Resposta" de [Documentos de pesquisa](https://msdn.microsoft.com/library/azure/dn798927.aspx). Para mais informações sobre outros códigos de estado HTTP que podem ser devolvido em caso de falha, consulte o artigo [códigos de estado HTTP (Azure procura)](https://msdn.microsoft.com/library/azure/dn798925.aspx).
