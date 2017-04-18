<properties
    pageTitle="Como utilizar Fiddler para avaliar e testar o Azure pesquisa REST APIs | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Utilize Fiddler para uma abordagem de código de royalties para verificar a disponibilidade de pesquisa do Azure e experimentar as APIs restantes."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Utilizar Fiddler para avaliar e testar o Azure pesquisa REST APIs
> [AZURE.SELECTOR]
- [Descrição geral](search-query-overview.md)
- [Explorador de procura](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

Este artigo explica como utilizar Fiddler, disponível como [poderá transferir gratuitamente Telerik](http://www.telerik.com/fiddler), para problema HTTP pedidos e respostas de vista utilizando a pesquisa Azure REST API, sem ter de escrever qualquer código. Procura Azure é completamente-gerido, alojado serviço de pesquisa na nuvem no Microsoft Azure programável facilmente através de .NET e os REST APIs. O serviço de pesquisa do Azure REST APIs encontram-se documentados no [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Os passos seguintes, irá criar um índice remissivo, carregar documentos, o índice de consulta e, em seguida, o sistema de informações do serviço de consulta.

Para concluir estes passos, terá de um serviço de pesquisa do Azure e `api-key`. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções sobre como começar.

## <a name="create-an-index"></a>Criar um índice remissivo

1. Inicie o Fiddler. No menu **ficheiro** , desative **O tráfego capturar** para ocultar estranhos de atividades HTTP está relacionado com a tarefa atual.

3. No separador **Compositor** , irá elaborar um pedido de com um aspeto semelhante a captura de ecrã seguinte.

    ![][1]

2. Selecione **colocar**.

3. Introduza o URL que especifica o URL do serviço, atributos do pedido e a versão da api. Algumas sugestões a ter em conta:
   + Utilize HTTPS como o prefixo.
   + Pedido de atributo é "/ índices/alojamento". Isto indica que a pesquisa para criar um índice remissivo 'alojamento' com o nome.
   + API versão é minúscula, especificado como "? versão api = 2015-02-28". Versões de API são importantes porque procura Azure implementa atualizações regularmente. Em raros ocasiões, uma atualização de serviço pode introduzir uma alteração de última hora à API. Por este motivo, pesquisa Azure requer uma versão api em cada pedido para que fiquem na controlo total sobre o qual é utilizado.

    O URL completo deverá ter um aspeto semelhante ao exemplo seguinte.

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Especifique o cabeçalho do pedido, substituindo o anfitrião e a chave de api com valores que são válidos para o seu serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  No corpo do pedido, cole os campos que compõem a definição de índice.
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  Clique em **Executar**.

Em alguns segundos, deverá ver uma resposta de HTTP 201 na lista de sessões, indicando que o índice foi criado com êxito.

Se obtiver HTTP 504, verifique se que o URL especifica HTTPS. Se vir HTTP 400 ou 404, selecione o corpo do pedido para confirmar que ocorreram erros sem copiar colar. Um HTTP 403 normalmente indica um problema com a api-chave (uma chave inválida ou um problema de sintaxe com como a chave de api for especificada).

## <a name="load-documents"></a>Carregar documentos

No separador **Compositor** , o seu pedido para publicar documentos terá o seguinte aspeto o seguinte. O corpo do pedido de contém os dados de pesquisa de 4 alojamento.

   ![][2]

1. Selecione **Publicar**.

2.  Introduza o URL que começa com HTTPS, seguido do seu URL do serviço, seguida por "/indexes/ <'indexname ' >/documentos/índice? versão api = 2015-02-28". O URL completo deverá ter um aspeto semelhante ao exemplo seguinte.

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  Cabeçalho do pedido deve ser igual antes. Lembre-se de que substituídos o anfitrião e a chave de api com valores que são válidos para o seu serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  O corpo do pedido contém quatro documentos a serem adicionados ao índice de alojamento.

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
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
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  Clique em **Executar**.

Em alguns segundos, deverá ver uma resposta de HTTP 200 na lista de sessão. Isto indica que os documentos foram criados com êxito. Se obtiver um 207, pelo menos uma falha na documento carregar. Se obtiver um 404, tem um erro de sintaxe no cabeçalho ou corpo do pedido.

## <a name="query-the-index"></a>O índice de consulta

Agora que são carregados um índice remissivo e documentos, pode emitir consultas nas mesmas.  No separador **Compositor** , um comando **GET** que seu serviço de consulta terá uma aspeto semelhante ao seguinte captura de ecrã.

   ![][3]

1.  Selecione **obter**.

2.  Introduza o URL que começa com HTTPS, seguido do seu URL do serviço, seguida por "/indexes/ <'indexname ' > / documentos?", seguido de parâmetros de consulta. Título de exemplo, utilize o seguinte URL, substituindo o nome do anfitrião de exemplo com um que seja válido para o seu serviço.
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Esta consulta procura no termo "companhia" e obtém categorias de faceta para classificações.

3.  Cabeçalho do pedido deve ser igual antes. Lembre-se de que substituídos o anfitrião e a chave de api com valores que são válidos para o seu serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

O código de resposta deve ser 200 e o resultado da resposta deverá ter um aspeto semelhante ao seguinte captura de ecrã.

   ![][4]

A seguinte consulta de exemplo é a partir da [operação de índice de pesquisa (Azure pesquisa API)](http://msdn.microsoft.com/library/dn798927.aspx) no MSDN. Muitos das consultas de exemplo neste tópico incluem espaços, que não são permitidos na Fiddler. Substituir cada um espaço com um carácter de + antes de colar na consulta de cadeia antes de tentar a consulta na Fiddler.

**Antes de espaços são substituídos:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Depois de espaços são substituídos por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>O sistema de consulta

Também pode consultar o sistema para obter as contagens de documento e consumo de armazenamento. No separador **Compositor** , o seu pedido terá uma aspeto semelhante ao seguinte e, a resposta irá devolver uma contagem do número de documentos e o espaço utilizado.

 ![][5]

1.  Selecione **obter**.

2.  Introduza o URL que inclua o URL do serviço, seguido de "/ alojamento/índices/estatística? versão api = 2015-02-28":

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Especifique o cabeçalho do pedido, substituindo o anfitrião e a chave de api com valores que são válidos para o seu serviço.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Deixe o corpo do pedido vazia.

5.  Clique em **Executar**. Deverá visualizar um código de estado de HTTP 200 na lista de sessão. Selecione a entrada registada para o seu comando.

6.  Clique no separador **Inspetores** , clique no separador **cabeçalhos** e, em seguida, selecione o formato JSON. Deverá visualizar o tamanho de contar e armazenamento de documento (em KB).

## <a name="next-steps"></a>Próximos passos

Consulte o artigo [Gerir o serviço de pesquisa no Azure](search-manage.md) para sem código abordagem para gerir e utilizando a pesquisa do Azure.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
