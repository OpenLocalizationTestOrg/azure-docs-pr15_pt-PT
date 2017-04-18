<properties
    pageTitle="Carregar dados na pesquisa Azure utilizar a API REST | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Saiba como carregar dados para um índice remissivo na pesquisa Azure utilizar a API REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Carregar dados para pesquisa Azure utilizar a API REST
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTO](search-import-data-rest-api.md)

Este artigo mostrar-lhe como utilizar o [Azure pesquisa REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) para importar dados para um índice de pesquisa do Azure.

Antes de iniciar este tutorial, já deve ter [criado um índice de pesquisa do Azure](search-what-is-an-index.md).

Para transmitir documentos para o índice de utilizar a API REST, irá emitir um pedido de HTTP POST ao ponto final de URL do seu índice. Corpo do corpo do pedido HTTP é um objeto JSON que contém os documentos para ser adicionadas, modificadas ou eliminadas.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Posso. Identificar a chave de api do administrador do seu serviço de pesquisa do Azure
Quando é emitido pedidos de HTTP contra seu serviço de utilizar a API REST, *cada* API pedido tem de incluir api-chave que foi gerada do serviço de pesquisa que aprovisionado. Está a ter uma chave de válida estabelece fidedignidade, pedido por base em, entre a aplicação de enviar o pedido e o serviço que trata-lo.

1. Para localizar as chaves de api do seu serviço tem de iniciar sessão para o [Portal do Azure](https://portal.azure.com/)
2. Aceda a pá do seu serviço de pesquisa do Azure
3. Clique no ícone do "Teclas"

O serviço terá *chaves de administração* e em *consulta*.

  - As *teclas de administração* principais e secundários conceder os direitos completos para todas as operações, incluindo a capacidade de gerir o serviço, criar e eliminar os índices, indexadores e origens de dados. Existem duas chaves para que possa continuar a utilizar a chave secundária se optar por gerar novamente a chave primária e vice versa.
  - As *teclas de consulta* conceder acesso só de leitura ao índices e documentos e são distribuídos normalmente para as aplicações de cliente emitem pedidos de pesquisa.

Para efeitos de importação de dados para um índice remissivo, pode utilizar a chave de administrador principal ou secundário.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Decidir qual ação indexação para utilizar
Ao utilizar a API REST, vai emitir pedidos de HTTP POST com organismos de pedido JSON para o URL do seu índice de pesquisa do Azure ponto final. O objeto JSON no seu corpo do pedido HTTP irá conter uma única matriz JSON denominada "valor" que contém objetos JSON que representa a documentos que gostaria de adicionar ao índice remissivo, atualizar ou eliminar.

Cada objeto JSON na matriz "valor" representa um documento para ser indexadas. Cada um destes objectos contém chave do documento e especifica a acção indexação pretendida (carregamento, impressão em série, eliminar, etc). Dependendo de qual da abaixo ações escolher, apenas determinados campos devem ser incluídos para cada documento:

@search.action | Descrição | Campos necessários para cada documento | Notas
--- | --- | --- | ---
`upload` | Um `upload` acção é semelhante a um "upsert" onde o documento será inserido se é novo e atualizados/substituído se existir. | chave, bem como quaisquer outros campos que pretende definir | Quando atualizar/substituição de um documento existente, qualquer campo que não for especificado no pedido de terão o campo definido como `null`. Isto ocorre o mesmo quando o campo anteriormente foi definido como um valor não nulo.
`merge` | Atualiza um documento existente com os campos especificados. Se o documento não existir no índice remissivo, irá falhar a impressão em série. | chave, bem como quaisquer outros campos que pretende definir | Qualquer campo que especificar numa intercalação de irá substituir o campo existente no documento. Isto inclui campos tipo `Collection(Edm.String)`. Por exemplo, se o documento contém um campo `tags` com valor `["budget"]` e executar uma impressão em série com valor `["economy", "pool"]` para `tags`, o valor final da `tags` campo será `["economy", "pool"]`. Não serão `["budget", "economy", "pool"]`.
`mergeOrUpload` | Esta ação comporta-se como `merge` se já existe um documento com a tecla determinado no índice remissivo. Se o documento não existir, comporta-se como `upload` com um novo documento. | chave, bem como quaisquer outros campos que pretende definir | -
`delete` | Remove o documento especificado do índice. | chave apenas | Quaisquer campos que especificar que o campo de chave será ignorado. Se pretende remover um campo individual de um documento, utilize `merge` em vez disso e basta definir o campo explicitamente nulo.

## <a name="iii-construct-your-http-request-and-request-body"></a>III. Construir o seu pedido HTTP e pedir corpo
Agora que recolheu os valores de campo necessárias para as suas ações de índice remissivo, está pronto para construir o pedido de HTTP real e o corpo do pedido JSON para importar os dados.

#### <a name="request-and-request-headers"></a>Pedido e pedido de cabeçalhos
O URL será necessário fornecer o nome do serviço, nome do índice ("alojamento" neste caso), bem como a versão da API inicial maiúscula (a versão atual do API é `2015-02-28` no momento da publicação deste documento). Terá de definir o `Content-Type` e `api-key` request cabeçalhos. Para o último utilize uma das chaves do seu serviço de administração.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corpo do pedido

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Neste caso, estamos a utilizar `upload`, `mergeOrUpload`, e `delete` como nossas ações de pesquisa.

Partem do princípio de que este índice do exemplo "alojamento" já é povoada com um número de documentos. Tenha em atenção como não temos que especificar a todos os campos de documento possíveis ao utilizar `mergeOrUpload` e como podemos especificado apenas a chave de documento (`hotelId`) ao utilizar `delete`.

Além disso, tenha em atenção que apenas pode incluir até 1000 documentos (ou 16 MB) num único pedido de indexação.

## <a name="iv-understand-your-http-response-code"></a>IV. Compreender o código de resposta HTTP
#### <a name="200"></a>200
Depois de submeter um pedido de indexação bem sucedido recebe uma resposta de HTTP com código de estado de `200 OK`. Corpo JSON da resposta HTTP será da seguinte forma:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Um código de estado de `207` será devolvido quando não foi indexado com êxito, pelo menos, um item. Corpo JSON da resposta HTTP irá conter informações sobre os documentos sem êxito.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Muitas vezes isto significa que a carregar no seu serviço de pesquisa chegar a um ponto onde a indexação pedidos começará a devolver `503` respostas. Neste caso, estamos altamente recomendável que o código do cliente voltar a desativar e a aguardar antes de repetir. Isto irá dar-o sistema de algum tempo para recuperar, aumentando a probabilidade de pedidos futuros serão bem sucedida. Rapidamente a repetir pedidos de só será prolongar a situação.

#### <a name="429"></a>429
Um código de estado de `429` será devolvido quando se tiver excedido o seu quota no número de documentos por índice remissivo.

#### <a name="503"></a>503
Um código de estado de `503` será devolvido se nenhum dos itens no pedido de foram indexados com êxito. Este erro significa que o sistema estiver em caso de carga frequente e o seu pedido não é possível processar neste momento.

> [AZURE.NOTE] Neste caso, estamos altamente recomendável que o código do cliente voltar a desativar e a aguardar antes de repetir. Isto irá dar-o sistema de algum tempo para recuperar, aumentando a probabilidade de pedidos futuros serão bem sucedida. Rapidamente a repetir pedidos de só será prolongar a situação.

Para mais informações sobre acções do documento e respostas de sucesso/erro, consulte o artigo [Adicionar, atualizar ou eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx). Para mais informações sobre outros códigos de estado HTTP que podem ser devolvido em caso de falha, consulte o artigo [códigos de estado HTTP (Azure procura)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## <a name="next"></a>Seguinte
Depois de povoar o índice de pesquisa do Azure, estará pronto para começar a emitir consultas para procurar documentos. Consulte o artigo [Consulta o índice de pesquisa de Azure](search-query-overview.md) para obter detalhes.
