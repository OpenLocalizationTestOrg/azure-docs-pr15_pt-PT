<properties
pageTitle="Armazenamento de tabela do Azure indexação com a pesquisa Azure"
description="Saiba como indexar dados armazenados em tabelas do Azure com a pesquisa do Azure"
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
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>Armazenamento de tabela do Azure indexação com a pesquisa Azure

Este artigo mostra como utilizar o Azure pesquisa para indexar dados armazenados no armazenamento de tabela do Azure. A novo pesquisa Azure tabela do indexador faz com que este processo rápida e simples. 

> [AZURE.IMPORTANT] Atualmente, esta funcionalidade está na pré-visualização. Está disponível apenas no API REST utilizando a versão **2015 02-28 pré-visualização** e na versão 2.0-pré-visualização do .NET SDK. Verifique se lembra, pré-visualizar APIs destinam-se para testar e avaliação e não deve ser utilizados em ambientes de produção.

## <a name="setting-up-azure-table-indexing"></a>Configurar a indexação da tabela do Azure

Para instalar e configurar um indexador de tabela do Azure, pode utilizar a pesquisa Azure REST API para criar e gerir **origens de dados** e **indexadores** conforme descrito no [operações de indexador](https://msdn.microsoft.com/library/azure/dn946891.aspx). Também pode utilizar a [versão 2.0-pré-visualização](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) do .NET SDK. No futuro, o suporte para tabela indexação será adicionado ao Portal do Azure.

Uma origem de dados indica quais os dados para indexar, credenciais necessárias para aceder aos dados e as políticas que permitem o Azure pesquisa eficazmente identificar alterações nos dados (novos, modificadas ou eliminadas linhas).

Um indexador lê dados a partir de uma origem de dados e carrega-lo para um índice de pesquisa de destino.

Para configurar a indexação da tabela:

1. Criar uma origem de dados
    - Definir o `type` parâmetro para`azuretable`
    - Passar na sua cadeia de ligação de conta de armazenamento como o `credentials.connectionString` parâmetro
    - Especifique o nome de tabela utilizando o `container.name` parâmetro
    - Opcionalmente, especifique uma consulta utilizando a `container.query` parâmetro. Sempre que possível, utilize um filtro no PartitionKey para um melhor desempenho; qualquer outra consulta irá resultar numa pesquisa completa a tabela, que pode resultar em desempenho fraco para tabelas grandes.
2. Crie um índice de pesquisa com o esquema que corresponde às colunas na tabela que pretende indexar. 
3. Crie o indexador ligando a sua origem de dados para o índice de pesquisa.

### <a name="create-data-source"></a>Criar a origem de dados

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Para saber mais sobre a API para criar a origem de dados, consulte o artigo [Criar a origem de dados](search-api-indexers-2015-02-28-preview.md#create-data-source).

### <a name="create-index"></a>Criar índice 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

Para saber mais sobre a API do índice remissivo criar, consulte o artigo [Criar índice](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Criar indexador 

Por fim, crie o indexador que referencia a origem de dados e o índice de destino. Por exemplo:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Para obter mais detalhes sobre a API do indexador criar, consulte o artigo [Criar indexador](search-api-indexers-2015-02-28-preview.md#create-indexer).

Há existe-lhe - indexação satisfeito!

## <a name="dealing-with-different-field-names"></a>Trabalhar com nomes de campos diferentes

Muitas vezes, os nomes de campo no seu índice existente serão diferentes dos nomes das propriedades da tabela. Pode utilizar os **mapeamentos de campos** para mapear os nomes das propriedades da tabela para os nomes dos campos no índice de pesquisa. Para saber mais sobre os mapeamentos de campo, consulte o artigo [mapeamentos de campos do Azure pesquisa indexador una as diferenças entre as origens de dados e índices de procura](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Tratamento de teclas de documento

Na pesquisa Azure, a tecla de documento identifica de um documento. Cada índice de pesquisa tem de ter exatamente um campo de chave do tipo de `Edm.String`. O campo de chave é necessário para cada documento que está a ser adicionado ao índice (na verdade, é o único campo necessário).

Uma vez que as linhas da tabela têm uma chave composta, pesquisa Azure gera um campo síntese denominado `Key` que é uma concatenação dos valores chaves partição chave e linha. Por exemplo, se uma linha estiverem PartitionKey é `PK1` e RowKey é `RK1`, em seguida, `Key` valor do campo será `PK1RK1`. 

> [AZURE.NOTE] O `Key` valor pode conter carateres são inválidos nas chaves do documento, tal como traços. Pode fazer em relação aos carateres inválidos, utilizando o `base64Encode` [função de mapeamento do campo](search-indexer-field-mappings.md#base64EncodeFunction). Se efetuar o seguinte, lembre-se também utilizar codificação de URL seguro Base64 quando prisma teclas de documento na API chama como pesquisa.

## <a name="incremental-indexing-and-deletion-detection"></a>Deteção utilizarão de indexação e eliminação
 
Quando configurar indexador uma tabela para executar uma agenda,-reindexa linhas apenas novas ou actualizadas, tal como é determinado por uma linha `Timestamp` valor. Não tem de especificar uma política de deteção de alteração – utilizarão a indexação está ativada automaticamente para si. 

Para indicar que determinados documentos tem de ser removidos do índice, pode utilizar uma estratégia de eliminar contornos – em vez de eliminar uma linha, adicione uma propriedade para indicar-é eliminada e configurar uma política de detecção eliminação contornos a origem de dados. Por exemplo, a política é mostrada abaixo considerar que uma linha é eliminada, se tiver uma propriedade `IsDeleted` com o valor `"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Ajude-na melhorar o Azure pesquisa

Se tiver pedidos de funcionalidade ou ideias para melhorias, consulte comunique-no nosso [site do site](https://feedback.azure.com/forums/263029-azure-search/).