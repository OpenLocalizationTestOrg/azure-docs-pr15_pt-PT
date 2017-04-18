<properties
pageTitle="Indexação blobs CSV com indexador de Blobs do Azure pesquisa | Microsoft Azure"
description="Saiba como indexar blobs CSV com a pesquisa do Azure"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexação blobs CSV com indexador de Blobs do Azure pesquisa 

Por predefinição, [indexador de Blobs do Azure pesquisa](search-howto-indexing-azure-blob-storage.md) analisa delimitado por blobs de texto como um único bloco de texto. No entanto, com blobs que contêm dados CSV, muitas vezes pretende trate cada linha na blob como um documento separado. Por exemplo, tendo em conta o seguinte texto delimitado por: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

poderá pretender analisá-lo para 2 documentos, cada que contêm "id", "datePublished" e "etiquetas" campos.

Este artigo vai aprender a analisar blobs CSV com um indexador de Blobs do Azure pesquisa. 

> [AZURE.IMPORTANT] Esta funcionalidade está atualmente na pré-visualização. Está disponível apenas no API REST utilizando a versão **2015-02-28-pré-visualização**. Verifique se lembra, pré-visualizar APIs destinam-se para testar e avaliação e não deve ser utilizados em ambientes de produção. 

## <a name="setting-up-csv-indexing"></a>Configurar o CSV indexação

Pretende indexar CSV blobs, criar ou atualizar uma definição de indexador com o `delimitedText` análise modo:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Para obter mais detalhes sobre a API do indexador criar, consulte o artigo [Criar indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

`firstLineContainsHeaders`indica que a primeira linha de (não vazio) de cada blob contém os cabeçalhos.
Se blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos deverão ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Atualmente, é suportada apenas a codificação UTF-8. Além disso, apenas o ponto e vírgula `','` caráter é suportado como o delimitador. Se precisar de suporte para outros codificações ou delimitadores, consulte diga-nos sabe no [site do site](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Quando utiliza o modo de análise de texto delimitado, pesquisa Azure parte do princípio de que todos os blobs na origem de dados serão CSV. Se precisar de suportar uma mistura de blobs CSV e que não sejam CSV na mesma origem de dados, consulte diga-nos sabe no [site do site](https://feedback.azure.com/forums/263029-azure-search).

## <a name="request-examples"></a>Exemplos de pedido

Colocar em isto todas as juntas, aqui estão os exemplos de carga útil concluída. 

Origem de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure pesquisa

Se tiver pedidos de funcionalidade ou ideias para melhorias, consulte comunique-no nosso [site do site](https://feedback.azure.com/forums/263029-azure-search/).