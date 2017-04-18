<properties
    pageTitle="Ligar DocumentDB com a pesquisa Azure utilizando indexadores | Microsoft Azure"
    description="Este artigo mostra-lhe como utilizar para indexador de procura do Azure com DocumentDB como origem de dados."
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>Ligar DocumentDB com a pesquisa Azure utilizando indexadores

Se estiver à procura para implementar o pesquisa excelente experiências sobre os seus dados DocumentDB, utilize indexador de procura do Azure de DocumentDB! Neste artigo, iremos mostrar-lhe como integrar o Azure DocumentDB com a pesquisa do Azure sem ter de escrever qualquer código para manter a infraestrutura de indexação!

Para configurar estas definições, que tem de [Configurar uma conta de pesquisa do Azure](../search/search-create-service-portal.md) (não ter de atualizar para pesquisa padrão) e, em seguida, ligar [Azure pesquisa REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) para criar uma **origem de dados** de DocumentDB e um **indexador** para essa origem de dados.

Em ordem enviar pedidos para interagir com os REST APIs, pode utilizar [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler)ou qualquer ferramenta da sua preferência.


##<a id="Concepts"></a>Azure conceitos de indexador de pesquisa

Azure pesquisa suporta a criação e gestão de dados de origens (incluindo DocumentDB) e indexadores que funcionam com essas origens de dados.

Uma **origem de dados** Especifica dados que tem de ser indexadas, as credenciais para aceder aos dados e as políticas para ativar o Azure pesquisa eficazmente identificar alterações nos dados (tais como modificadas ou eliminadas documentos dentro da sua coleção). A origem de dados é definida como um recurso independente, para que possa ser utilizado por vários indexadores.

Um **indexador** descreve como os dados flui da origem de dados para um índice de pesquisa de destino. Deverá planear sobre a criação de um indexador para todas as combinações de origem de dados em índices destino. Embora possa ter várias indexadores escrita para o mesmo índice, um indexador só pode escrever para um único índice remissivo. Um indexador é utilizado para:

- Execute uma única cópia dos dados para preencher um índice remissivo.
- Sincronize um índice com as alterações à origem de dados com base numa agenda. A agenda faz parte de definição do indexador.
- Invocar atualizações a pedido a um índice, conforme necessário.

##<a id="CreateDataSource"></a>Passo 1: Criar uma origem de dados

Emita um pedido de HTTP POST para criar uma nova origem de dados no seu serviço de pesquisa do Azure, incluindo os cabeçalhos de pedido seguinte.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

O `api-version` é necessário. Os valores válidos incluem `2015-02-28` ou uma versão posterior. Visite [as versões API na pesquisa Azure](../search/search-api-versions.md) para ver todas as API de procura versões suportadas.

O corpo do pedido de contém a definição de origem de dados, o que deve incluir os seguintes campos:

- **nome**: selecione um nome para representar a base de dados DocumentDB.

- **tipo**: utilizar `documentdb`.

- **credenciais**:

    - **connectionString**: obrigatório. Especificar as informações de ligação para a base de dados do Azure DocumentDB no seguinte formato:`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **contentor**:

    - **nome**: obrigatório. Especificar o id da coleção de DocumentDB para ser indexadas.

    - **consulta**: opcional. Pode especificar uma consulta para aplanar um documento JSON arbitrário para um esquema simples que pode indexar Azure pesquisa.

- **dataChangeDetectionPolicy**: opcional. Consulte a [política de deteção de alterar dados](#DataChangeDetectionPolicy) abaixo.

- **dataDeletionDetectionPolicy**: opcional. Consulte o artigo [política de deteção de eliminação de dados](#DataDeletionDetectionPolicy) .

Consulte abaixo para um [corpo do pedido de exemplo](#CreateDataSourceExample).

###<a id="DataChangeDetectionPolicy"></a>Capturar documentos alterados

É a finalidade de uma política de deteção de alterar dados eficientemente identificar itens de dados alterados. Atualmente, a política de suportados apenas é o `High Water Mark` política utilizando o `_ts` propriedade da última modificação carimbo fornecida pela DocumentDB - o que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Também será necessário adicionar `_ts` na projecções e `WHERE` cláusula para a sua consulta. Por exemplo:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Capturar documentos eliminados

Quando são eliminadas linhas da tabela de origem, deverá eliminar essas linhas do índice de pesquisa. A finalidade de uma política de deteção de eliminação de dados é eficazmente identificar itens de dados eliminados. Atualmente, a política de suportados apenas é o `Soft Delete` política (eliminação estiver marcada com um sinalizador de algum tipo), que é especificado um da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] Irá precisar de incluir a propriedade softDeleteColumnName na sua cláusula SELECT se estiver a utilizar uma projecções personalizada.

###<a id="CreateDataSourceExample"></a>Exemplo de corpo do pedido

O exemplo seguinte cria uma origem de dados com uma sugestões de consulta e política personalizada:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>Resposta

Receberá uma resposta HTTP 201 criado se a origem de dados foi criada com êxito.

##<a id="CreateIndex"></a>Passo 2: Criar um índice remissivo

Crie um índice de pesquisa do Azure de destino, se ainda não tiver um. Pode fazê-lo a partir do [Azure Portal IU](../search/search-create-index-portal.md) ou ao utilizar a [API do índice de criar](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Certifique-se de que o esquema do seu índice de destino é compatível com o esquema dos documentos JSON de origem ou o resultado do seu projecções de consulta personalizadas.

>[AZURE.NOTE] Para coleções de sites com partições, a chave de documento predefinido é de DocumentDB `_rid` propriedade, que é o nome alterada para `rid` na pesquisa Azure. Do além disso, DocumentDB `_rid` valores contêm caracteres que são inválidos teclas de pesquisa do Azure; Por conseguinte, a `_rid` valores são codificada Base64.

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figura r: mapeamento entre os tipos de dados JSON e tipos de dados de pesquisa Azure

| TIPO DE DADOS JSON|   TIPOS DE CAMPO DE ÍNDICE REMISSIVO DESTINO COMPATÍVEIS|
|---|---|
|Booleano|Edm.Boolean, Edm.String|
|Números com aspeto semelhante a números inteiros|Edm.Int32, Edm.Int64, Edm.String|
|Números que parecem pontos flutuantes|Edm.Double, Edm.String|
|Cadeia|Edm.String|
|Por exemplo, "a", "b", "c" os tipos de matrizes de primitiva |COLLECTION(EDM.String)|
|Cadeias que aspeto datas| Edm.DateTimeOffset, Edm.String|
|GeoJSON objectos, por exemplo, {"tipo": "Ponto", "coordenadas": [lat longo,]} | Edm.GeographyPoint |
|Outros objetos JSON|N/D|

###<a id="CreateIndexExample"></a>Exemplo de corpo do pedido

O exemplo seguinte cria um índice remissivo com um campo de id e a descrição:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>Resposta

Receberá uma resposta HTTP 201 criado se o índice foi criado com êxito.

##<a id="CreateIndexer"></a>Passo 3: Criar um indexador

Pode criar um novo indexador dentro de um serviço de pesquisa do Azure utilizando um pedido de HTTP POST com os seguintes cabeçalhos.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

O corpo do pedido de contém a definição de indexador, o que deve incluir os seguintes campos:

- **nome**: obrigatório. O nome do indexador.

- **nomedaorigemdedados**: obrigatório. O nome da origem de dados existente.

- **targetIndexName**: obrigatório. O nome de um índice remissivo existente.

- **agenda**: opcional. Consulte o artigo a [indexação agenda](#IndexingSchedule) abaixo.

###<a id="IndexingSchedule"></a>Executar indexadores uma agenda

Um indexador opcionalmente, pode especificar uma agenda. Se existir uma agenda, o indexador será executada periodicamente por agenda. Agenda tem os seguintes atributos:

- **intervalo**: obrigatório. Um valor de duração que especifica um intervalo ou período de indexador é executado. O intervalo mais pequeno permitido é de 5 minutos; célula mais longa é um dia. Este deve ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto de um valor de [duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) restrito). O padrão para este é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` todas as horas de 2.

- **hora de início**: obrigatório. Um campo datetime UTC que especifica quando o indexador deverá começar em execução.

###<a id="CreateIndexerExample"></a>Exemplo de corpo do pedido

O exemplo seguinte cria um indexador que copia os dados de coleção de referenciado pelo `myDocDbDataSource` da origem de dados para o `mySearchIndex` índice numa agenda que começa na 1 de Janeiro de 2015 UTC e é executado por hora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>Resposta

Receberá uma resposta HTTP 201 criado se o indexador foi criado com êxito.

##<a id="RunIndexer"></a>Passo 4: Executar um indexador

Para além de executar periodicamente numa agenda, um indexador pode também ser chamado a pedido por emitir o pedido de HTTP POST seguinte:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Resposta

Receberá uma resposta HTTP 202 aceites se o indexador foi chamado com êxito.

##<a name="GetIndexerStatus"></a>Passo 5: Obter o estado do indexador

Pode emitir um pedido HTTP GET para obter o histórico de estado e execução atual de um indexador:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Resposta

Verá uma resposta de HTTP 200 OK devolvida juntamente com um corpo de resposta que contenha informações sobre o estado de estado de funcionamento do indexador geral, a última invocação de indexador, bem como o histórico de exe do indexador recentes (se existir).

A resposta deverá ter um aspeto semelhante ao seguinte:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Histórico de execução contém até as 50 execuções concluídas mais recentes, que são ordenadas por ordem cronológica inversa (de modo a execução do mais recente vem primeira na resposta).

##<a name="NextSteps"></a>Próximos passos

Parabéns! Aprendeu como integrar o Azure DocumentDB com a pesquisa Azure utilizando o indexador para DocumentDB apenas.

 - Para saber mais como sobre Azure DocumentDB, consulte a [página de serviço DocumentDB](https://azure.microsoft.com/services/documentdb/).

 - Para saber como mais informações sobre o Azure pesquisa, consulte a [página de serviço de pesquisa](https://azure.microsoft.com/services/search/).
