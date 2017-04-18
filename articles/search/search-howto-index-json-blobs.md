<properties
pageTitle="Indexação JSON blobs com indexador de Blobs do Azure pesquisa"
description="Indexação JSON blobs com indexador de Blobs do Azure pesquisa"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="07/26/2016"
ms.author="eugenesh" />

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexação JSON blobs com indexador de Blobs do Azure pesquisa 

Este artigo mostra como configurar indexador de Blobs do Azure pesquisa para extrair o conteúdo estruturado de blobs que contêm JSON.

## <a name="scenarios"></a>Cenários

Por predefinição, o [indexador de Blobs do Azure pesquisa](search-howto-indexing-azure-blob-storage.md) analisa JSON blobs como um único bloco de texto. Muitas vezes, pretende manter a estrutura dos seus documentos JSON. Por exemplo, tendo em conta o documento JSON 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

poderá pretender analisá-lo para um documento de pesquisa do Azure com "texto", "datePublished" e "etiquetas" campos.

Em alternativa, quando os blobs contiverem uma **matriz de objetos JSON**, poderá querer cada elemento dessas matrizes para se tornar um documento separado do Azure pesquisa. Por exemplo, tendo em conta um blob com este JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Pode preencher o índice de pesquisa do Azure com 3 documentos em separado, cada uma com campos de "id" e "texto". 

> [AZURE.IMPORTANT] Esta funcionalidade está atualmente na pré-visualização. Está disponível apenas no API REST utilizando a versão **2015-02-28-pré-visualização**. Verifique se lembra, pré-visualizar APIs destinam-se para testar e avaliação e não deve ser utilizados em ambientes de produção. 

## <a name="setting-up-json-indexing"></a>Configurar o JSON indexação

Para indexar JSON blobs, defina o `parsingMode` o parâmetro de configuração para `json` (incluir no índice remissivo cada blob como um único documento) ou `jsonArray` (se o seu blobs contenham uma matriz JSON): 

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

Se for necessário, utilize **mapeamentos de campos** para escolher as propriedades do documento JSON origem utilizada para preencher o índice de pesquisa de destino.  Isto é descrito detalhadamente abaixo. 

> [AZURE.IMPORTANT] Quando utilizar `json` ou `jsonArray` modo de análise, pesquisa Azure parte do princípio de que todos os blobs na origem de dados serão JSON. Se precisar de suportar uma mistura de blobs JSON e que não sejam JSON na mesma origem de dados, consulte diga-nos sabe no [site do site](https://feedback.azure.com/forums/263029-azure-search).

## <a name="using-field-mappings-to-build-search-documents"></a>Utilizar os mapeamentos de campo para criar documentos de pesquisa 

Atualmente, Azure pesquisa não é possível indexar arbitrários JSON documentos diretamente, porque suporta tipos de dados apenas primitivos, matrizes de cadeia e GeoJSON pontos. No entanto, pode utilizar **mapeamentos de campos** para escolher partes do seu documento JSON e "levantá-los" para os campos de nível superiores do documento de pesquisa. Para saber mais sobre as noções básicas de mapeamentos de campo, consulte o artigo [mapeamentos de campos do Azure pesquisa indexador una as diferenças entre as origens de dados e índices de procura](search-indexer-field-mappings.md).

Voltar a chegar ao nosso documento JSON de exemplo: 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Digamos que tem um índice de pesquisa com os seguintes campos: `text` tipo Edm.String, `date` tipo Edm.DateTimeOffset, e `tags` tipo Collection(Edm.String). Para mapear os seus JSON dentro da forma pretendida, utilize os mapeamentos de campos seguintes: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]

Os nomes dos campos de origem na mapeamentos estão especificados utilizando a notação [JSON ponteiro](http://tools.ietf.org/html/rfc6901) . Começar com uma barra para consultar a raiz do seu documento JSON, então pormenorizar na propriedade desejada (a arbitrário nível de aninhamento) utilizando reencaminhar caminho barra separados por vírgulas. 

Também pode consultar a elementos da matriz individuais ao utilizar um índice baseado em zero. Por exemplo, para selecionar o primeiro elemento dessas matrizes "etiquetas" a partir do exemplo acima, utilize um mapeamento de campos da seguinte forma:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Se um nome de campo de origem de um caminho de mapeamento do campo que se refere a uma propriedade que não existe no JSON, é ignorado desse mapeamento sem um erro. Isto é feito para que pode suportamos documentos com um esquema diferente (que é um caso de utilização comum). Porque não existe nenhuma validação, tem de encarregam-se para evitar erros tipográficos na sua especificação de mapeamento do campo. 

Se os seus documentos JSON contiverem apenas propriedades de nível superiores simples, poderá não ter a mapeamentos de campos de todo. Por exemplo, se o seu JSON tem este aspeto, as propriedades de nível superior "texto", "datePublished" e "etiquetas" irão diretamente mapear para os campos correspondentes no índice de pesquisa: 
 
    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
    }

## <a name="indexing-nested-json-arrays"></a>Indexação matrizes JSON aninhadas

O que acontece se pretender indexar uma matriz de objetos JSON, mas esse matriz estiver aninhado num local do documento? Pode escolher qual propriedade contém a matriz utilizando o `documentRoot` propriedade de configuração. Por exemplo, se o seu blobs o seguinte aspeto: 

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

Utilize esta configuração para indexar a matriz contida na propriedade "level2": 

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>Exemplos de pedido

Colocar em isto todas as juntas, aqui estão os exemplos de payloads concluída. 

Origem de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure pesquisa

Se tiver pedidos de funcionalidade ou ideias para melhorias, consulte comunique-no nosso [site do site](https://feedback.azure.com/forums/263029-azure-search/).