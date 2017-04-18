<properties 
pageTitle="As operações de indexador (REST API do serviço de pesquisa Azure: 2015-02-28-pré-visualização) | Pré-visualização de pesquisa Azure API" 
description="As operações de indexador (REST API do serviço de pesquisa Azure: 2015-02-28-pré-visualização)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#<a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>As operações de indexador (REST API do serviço de pesquisa Azure: 2015-02-28-pré-visualização)#

> [AZURE.NOTE] Este artigo descreve os indexadores na [2015 02-28 pré-visualização REST API](search-api-2015-02-28-preview.md). Esta versão da API adiciona versões de pré-visualização do indexador de armazenamento de Blobs do Azure com extração de documento e indexador de armazenamento de tabela do Windows Azure, assim como outras melhorias. API também suporta ficará disponíveis indexadores (das versões DG), incluindo indexadores para a base de dados do SQL Azure, SQL Server Azure VMs e Azure DocumentDB.

## <a name="overview"></a>Descrição geral ##

Pesquisa Azure pode integrar diretamente com algumas comuns origens de dados, remover a necessidade de escrever código para indexar os seus dados. Para configurar o seguinte para cima, pode ligar a API de pesquisa do Azure para criar e gerir **origens de dados**e **indexadores** . 

Um **indexador** é um recurso que liga as origens de dados com índices de procura de destino. Um indexador é utilizado das seguintes formas: 

- Execute uma única cópia dos dados para preencher um índice remissivo.
- Sincronize um índice com as alterações à origem de dados com base numa agenda. A agenda faz parte de definição do indexador.
- Invocar a pedido para atualizar um índice remissivo, conforme necessário. 

Um **indexador** é útil quando pretende actualizações regulares para um índice remissivo. Pode configurar uma agenda de inline como parte de uma definição de indexador ou executá-la a pedido utilizando [Indexador executar](#RunIndexer). 

Uma **origem de dados** Especifica dados que tem de ser indexadas, as credenciais para aceder aos dados e as políticas para ativar o Azure pesquisa eficazmente identificar alterações nos dados (tais como modificadas ou eliminadas linhas numa tabela da base de dados). É definida como um recurso independente para que possa ser utilizado por vários indexadores.

Atualmente são suportadas as seguintes origens de dados:

- **Base de dados Azure SQL** e o **SQL Server no Azure VMs**. Para uma guia passo a alvo passo, consulte [Este artigo](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). 
- **Azure DocumentDB**. Para uma guia passo a alvo passo, consulte [Este artigo](../documentdb/documentdb-search-indexer.md). 
- **Armazenamento de Blobs do azure**, incluindo os seguintes formatos de documento: ficheiros PDF, Microsoft Office (DOCX/documento, XSLX/XLS, PPTX/PPT, MSG), HTML, XML, ZIP e texto simples (incluindo JSON). Para uma guia passo a alvo passo, consulte [Este artigo](search-howto-indexing-azure-blob-storage.md).
- **Armazenamento de tabela do azure**. Para uma guia passo a alvo passo, consulte [Este artigo](search-howto-indexing-azure-tables.md).
     
Vamos está a considerar adicionar suporte para origens de dados adicionais no futuro. Para nos ajudar a atribuir prioridades a estas decisões, forneça os seus comentários no [Fórum de comentários do Azure pesquisa](http://feedback.azure.com/forums/263029-azure-search).

Consulte o artigo [Limites de serviço](search-limits-quotas-capacity.md) para limites máximos relacionadas com indexador e dados de recursos de origem.

## <a name="typical-usage-flow"></a>Fluxo de utilização típica ##

Pode criar e gerir indexadores e origens de dados através de pedidos HTTP simples (POST, GET, hoje, eliminar) relativamente a um determinado `data source` ou `indexer` recurso.

Configurar a indexação automática normalmente é um processo de quatro passos:

1. Identifique a origem de dados que contém os dados que tem de ser indexadas. Tenha em atenção que Azure pesquisa pode não suportar a todos os tipos de dados presentes na sua origem de dados. Consulte o artigo [tipos de dados suportados](https://msdn.microsoft.com/library/azure/dn798938.aspx) para a lista.

2. Crie um índice de pesquisa do Azure cujo esquema é compatível com a sua origem de dados.
  
3. Crie uma origem de dados de pesquisa do Azure, tal como descrito na [Origem de dados de criar](#CreateDataSource).
  
4. Crie um indexador Azure pesquisa como descrito [Indexador criar](#CreateIndexer).

Deverá planear sobre a criação de um indexador para todas as combinações de origem de dados em índices destino. Pode ter vários indexadores escrita para o mesmo índice e pode reutilizar a mesma origem de dados para os vários indexadores. No entanto, um indexador só pode consumir uma origem de dados uma vez e apenas pode escrever um único índice remissivo. 

Depois de criar um indexador, pode obter o respetivo estado de execução utilizando a operação de [Obter o estado do indexador](#GetIndexerStatus) . Também pode executar um indexador em qualquer altura (em vez de ou conjugadas executá-lo periodicamente numa agenda) utilizando a operação de [Indexador executar](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Criar a origem de dados ##

Pesquisa do Azure, uma origem de dados é utilizada com indexadores, fornecer as informações de ligação de atualização de dados agendada ou ad hoc de um índice de destino. Pode criar uma nova origem de dados dentro de um serviço de pesquisa do Azure utilizando um pedido de HTTP POST.
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Em alternativa, pode utilizar o local e especifique o nome da origem de dados no URI. Se a origem de dados não existir, será criado.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Nota**: O número máximo de origens de dados permitidas varia por preços de camadas. O serviço gratuito permite até 3 origens de dados. Serviço padrão permite 50 origens de dados. Consulte o artigo [Limites de serviço](search-limits-quotas-capacity.md) para obter detalhes.

**Pedido**

É necessário HTTPS para todos os pedidos de serviço. O pedido de **Criar a origem de dados** pode ser construído utilizando o método de uma mensagem ou um local. Ao utilizar a mensagem, tem de fornecer um nome de origem de dados no corpo do pedido juntamente com a definição de origem de dados. Com o local, o nome faz parte do URL. Se a origem de dados não existir, é criada. Se já existir, é atualizado para a nova definição. 

O nome da origem de dados deve estar em minúsculas, começar com uma letra ou um número, ter sem barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome da origem de dados com uma letra ou um número, o resto do nome pode incluir qualquer letra, o número e travessões, desde que não sejam consecutivos os traços. Consulte o artigo [regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para obter detalhes.

O `api-version` é necessário. A versão atual não for `2015-02-28`.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais. 

- `Content-Type`: Necessário. Defina esta opção para`application/json`
- `api-key`: Necessário. O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Criar a origem de dados** tem de incluir um `api-key` cabeçalho definido como a sua chave de administrador (por oposição a uma chave de consulta). 
 
Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do seu dashboard de serviço no [Portal do Azure](https://portal.azure.com/). Consulte o artigo [criar um serviço de pesquisa no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

<a name="CreateDataSourceRequestSyntax"></a>
**Sintaxe de corpo do pedido**

O corpo do pedido de contém uma definição de origem de dados, que inclui o tipo de origem de dados, credenciais para ler os dados, bem como um dados opcional alterar deteção e políticas de deteção de eliminação de dados que são utilizadas para identificar eficientemente alteradas ou eliminadas dados na origem de dados quando utilizado com um indexador periodicamente agendado. 

A sintaxe para estruturar a carga útil pedido é da seguinte forma. Um pedido de exemplo é fornecido continuar neste tópico.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Pedido de contém as seguintes propriedades: 

- `name`: Necessário. O nome da origem de dados. Um nome de origem de dados tem apenas conter letras em minúsculas, dígitos ou traços, não pode iniciar ou terminar com traços e está limitado a 128 caracteres.
- `description`: Uma descrição opcional. 
- `type`: Necessário. Tem de ser um dos tipos de origens de dados suportadas:
    - `azuresql`-Base de dados azure SQL ou do SQL Server no Azure VMs
    - `documentdb`-Azure DocumentDB
    - `azureblob`-Armazenamento Blob azure
    - `azuretable`-Armazenamento de tabela azure
- `credentials`:
    - Obrigatório `connectionString` propriedade especifica a cadeia de ligação da origem de dados. O formato da cadeia de ligação depende do tipo de origem de dados: 
        - Para Azure SQL, esta é a cadeia de ligação do SQL Server habitual. Se estiver a utilizar o portal do Azure para obter a cadeia de ligação, utilize o `ADO.NET connection string` opção.
        - Para DocumentDB, a cadeia de ligação tem de ser no seguinte formato: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Todos os valores são necessários. Pode localizá-las no [portal do Azure](https://portal.azure.com/).  
        - Para BLOBs do Azure e armazenamento de tabela, esta é a cadeia de ligação de conta de armazenamento. O formato é descrito [aqui](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). É necessário o protocolo de ponto final HTTPS.  
- `container`, necessários: Especifica os dados a incluir no índice remissivo utilizando o `name` e `query` propriedades: 
    - `name`, necessário:
        - Azure SQL: Especifica a tabela ou vista. Pode utilizar completamente qualificado esquema nomes, tais como `[dbo].[mytable]`.
        - DocumentDB: Especifica a coleção. 
        - Armazenamento de Blobs do Azure: Especifica o contentor de armazenamento.
        - Armazenamento de tabela do Azure: Especifica o nome da tabela. 
    - `query`, opcionais:
        - DocumentDB: permite-lhe especificar uma consulta que organiza o esquema do documento uma arbitrário JSON para um esquema simples que pode indexar Azure pesquisa.  
        - Armazenamento de Blobs do Azure: permite-lhe especificar uma pasta virtual dentro do contentor de Blobs. Por exemplo, caminho de Blobs do `mycontainer/documents/blob.pdf`, `documents` podem ser utilizados como a pasta virtual.
        - Armazenamento de tabela do Azure: permite-lhe especificar uma consulta que filtra o conjunto de linhas para serem importados.
        - Azure SQL: consulta não é suportada. Se precisar desta funcionalidade, consulte votar [Esta sugestão](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
- Opcional `dataChangeDetectionPolicy` e `dataDeletionDetectionPolicy` propriedades são descritas abaixo.

<a name="DataChangeDetectionPolicies"></a>
**Políticas de deteção de alteração de dados**

É a finalidade de uma política de deteção de alterar dados eficientemente identificar itens de dados alterados. Políticas de suportadas variam consoante o tipo de origem de dados. Secções abaixo descrevem cada política. 

***Política de deteção de alteração de alta marca d'água*** 

Utilize esta política quando a sua origem de dados contém uma coluna ou a propriedade que cumpre os seguintes critérios:
 
- Insere todas as especificar um valor para a coluna. 
- Todas as atualizações para um item também alteram o valor da coluna. 
- O valor desta coluna aumenta com cada alteração.
- Consultas que utilizam uma cláusula de filtro semelhante ao seguinte `WHERE [High Water Mark Column] > [Current High Water Mark Value]` pode ser executada de forma eficaz.

Por exemplo, ao utilizar origens de dados Azure SQL, indexadas `rowversion` coluna é o candidato ideal para utilização com a política de marca de água de alta. 

Esta política pode ser especificada da seguinte forma:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

> [AZURE.NOTE] Quando utilizar DocumentDB origens de dados, tem de utilizar o `_ts` propriedade fornecida pela DocumentDB. 

> [AZURE.NOTE] Quando utilizar origens de dados de Blobs do Azure, pesquisa Azure automaticamente utiliza uma marca d'água alta alterar política de detecção com base na hora da última modificação de um blob; não tem de especificar como uma política de si.   

***Política de alteração de deteção de integrada de SQL***

Se a sua base de dados do SQL suportar a [monitorização de alterações](https://msdn.microsoft.com/library/bb933875.aspx), recomendamos que utilize SQL integrada alterar política rastreio. Esta política permite que as alterações mais eficiente e permite que procura Azure identificar linhas eliminadas sem necessitar de ter uma coluna de explícitas "eliminar contornos" no seu esquema.

Monitorização integrada é suportada começando com as seguintes versões de base de dados do SQL Server: 
- SQL Server 2008 R2, se estiver a utilizar o SQL Server no Azure VMs.
- Azure SQL da base de dados V12, se estiver a utilizar a base de dados do SQL Azure.  

Quando utilizar a política de SQL integrada alterações, não especificar uma política de deteção de eliminação de dados separado - esta política tem suporte incorporado para identificar eliminadas linhas. 

Esta política só pode ser utilizada com tabelas; não podem ser utilizado com vistas. Tem de ativar o registo de alterações para a tabela que estiver a utilizar o antes de poder utilizar esta política. Consulte o artigo [Ativar e desativar o registo de alterações](https://msdn.microsoft.com/library/bb964713.aspx) para obter instruções.    
 
Quando estruturar o pedido de **Criar a origem de dados** , a política de controlo de alteração de SQL integrada pode ser especificado da seguinte forma:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Políticas de deteção de eliminação de dados**

A finalidade de uma política de deteção de eliminação de dados é eficazmente identificar itens de dados eliminados. Atualmente, a política de suportados apenas é o `Soft Delete` política, que lhe permite identificar itens eliminados com base em valores de um `soft delete` coluna ou a propriedade na origem de dados. Esta política pode ser especificada da seguinte forma:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**Nota:** Apenas as colunas com cadeia, número inteiro ou valores booleanos são suportadas. O valor utilizado como `softDeleteMarkerValue` tem de ser uma cadeia, mesmo se a coluna correspondente detém inteiros ou valores booleanos. Por exemplo, se o valor que é apresentado na sua origem de dados for 1, utilize `"1"` como o `softDeleteMarkerValue`.    

<a name="CreateDataSourceRequestExamples"></a>
**Exemplos de corpo do pedido**

Se pretender utilizar a origem de dados com um indexador que é executada numa agenda, este exemplo mostra como especificar políticas de deteção de eliminação e para alterar: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Se apenas pretender utilizar a origem de dados para uma única cópia dos dados, podem ser omitidas as políticas que foram:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Resposta**

Para um pedido de bem sucedido: 201 criado. 

<a name="UpdateDataSource"></a>
## <a name="update-data-source"></a>Atualizar a origem de dados ##

Pode atualizar uma origem de dados existente utilizando um pedido de HTTP colocar. Especifique o nome da origem de dados para atualizar a pedido URI:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

O `api-version` é necessário. A versão atual não for `2015-02-28`. [Controlo de versões de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) tem detalhes e obter mais informações sobre as versões alternativos.

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Pedido**

A sintaxe de corpo do pedido é o mesmo que para [pedidos de criar a origem de dados](#CreateDataSourceRequestSyntax).

> [AZURE.NOTE] Algumas propriedades não podem ser atualizadas na origem de dados existente. Por exemplo, não pode alterar o tipo de origem de dados existente.  

> [AZURE.NOTE] Se não quiser alterar a cadeia de ligação para uma origem de dados existente, pode especificar o literal `<unchanged>` para a cadeia de ligação. Isto é útil nas situações em que necessita para atualizar um dados de origem, mas não tenha acesso adequado para a cadeia de ligação, uma vez que é dados sensíveis de segurança.

**Resposta**

Para um pedido de bem sucedido: 201 criado se uma nova origem de dados foi criada e 204 sem conteúdo se foi atualizada uma origem de dados existente.

<a name="ListDataSource"></a>
## <a name="list-data-sources"></a>Origens de dados de lista ##

A operação de **Origens de dados de lista** devolve uma lista de origens de dados no seu serviço de pesquisa do Azure. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessário. A versão atual não for `2015-02-28`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Para um pedido de bem sucedido: 200 OK.

Eis um corpo de resposta de exemplo:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Tenha em atenção que pode filtrar a resposta para baixo até apenas as propriedades que lhe interessa. Por exemplo, se pretender apenas uma lista de nomes de origem de dados, utilizar OData `$select` opção de consulta:

    GET /datasources?api-version=205-02-28&$select=name

Neste caso, a resposta do exemplo acima apareceriam da seguinte forma: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Esta é uma técnica útil para guardar a largura de banda se tiver muitas origens de dados no seu serviço de pesquisa.

<a name="GetDataSource"></a>
## <a name="get-data-source"></a>Obter a origem de dados ##

A operação de **Obter a origem de dados** obtém a definição de origem de dados a partir da pesquisa do Azure.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessário. A versão atual não for `2015-02-28`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

A resposta é semelhante a exemplos nos [pedidos de exemplo de criar a origem de dados](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [AZURE.NOTE] Não fazer para definir o `Accept` cabeçalho pedido para `application/json;odata.metadata=none` quando esta API como fazê-lo a chamar originará `@odata.type` atributo para omitidos a resposta e não será possível diferenciar entre alteração de dados e políticas de deteção de eliminação de dados de diferentes tipos. 

<a name="DeleteDataSource"></a>
## <a name="delete-data-source"></a>Eliminar a origem de dados ##

A operação de **Eliminar a origem de dados** remove uma origem de dados do seu serviço de pesquisa do Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Se qualquer indexadores referenciam a origem de dados que estiver a eliminar, a operação de eliminação irá ainda continuar. No entanto, essas indexadores irão transitar para o estado de erro após a sua próxima execução.  

O `api-version` é necessário. A versão atual não for `2015-02-28`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Código de estado: 204 sem conteúdo é devolvido por uma resposta bem sucedida.

<a name="CreateIndexer"></a>
## <a name="create-indexer"></a>Criar indexador ##

Pode criar um novo indexador dentro de um serviço de pesquisa do Azure utilizando um pedido de HTTP POST.
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Em alternativa, pode utilizar o local e especifique o nome da origem de dados no URI. Se a origem de dados não existir, será criado.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] O número máximo de indexadores permitido varia por preços de camadas. O serviço gratuito permite indexadores até 3. Serviço padrão permite que os 50 indexadores. Consulte o artigo [Limites de serviço](search-limits-quotas-capacity.md) para obter detalhes.

O `api-version` é necessário. A versão atual não for `2015-02-28`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.


<a name="CreateIndexerRequestSyntax"></a>
**Sintaxe de corpo do pedido**

O corpo do pedido de contém uma definição de indexador, especifica a origem de dados e o índice de destino para indexação, bem como opcional agenda de indexação e parâmetros. 


A sintaxe para estruturar a carga útil pedido é da seguinte forma. Um pedido de exemplo é fornecido continuar neste tópico.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Agenda de indexador**

Um indexador opcionalmente, pode especificar uma agenda. Se existir uma agenda, o indexador será executada periodicamente por agenda. Agenda tem os seguintes atributos:

- `interval`: Necessário. Um valor de duração que especifica um intervalo ou período de indexador é executado. O intervalo mais pequeno permitido é de 5 minutos; célula mais longa é um dia. Este deve ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto de um valor de [duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) restrito). O padrão para este é: `"P[nD][T[nH][nM]]"`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` todas as horas de 2. 

- `startTime`: Necessário. Datetime de UTC quando o indexador deverá começar em execução. 

**Parâmetros de indexador**

Um indexador opcionalmente, pode especificar várias parâmetros que afetam o seu comportamento. Todos os parâmetros são opcionais.  

- `maxFailedItems`: O número de itens que a ser indexadas antes de executar um indexador é considerada uma falha de pode falhar. A predefinição é 0. Informações sobre itens do falhadas, são devolvidas pela operação [Obter o estado do indexador](#GetIndexerStatus) . 

- `maxFailedItemsPerBatch`: O número de itens que podem não conseguir ser indexadas em cada lote antes de executar um indexador é considerado uma falha. A predefinição é 0.

- `base64EncodeKeys`: Especifica se ou não teclas documento será codificado base-64. Pesquisa Azure impõe restrições carateres que podem estar presentes numa chave de documento. No entanto, os valores na sua origem de dados podem conter carateres são inválidos. Se for necessário indexar esses valores como chaves do documento, este sinalizador pode ser definido como verdadeiro. A predefinição é `false`.

- `batchSize`: Especifica o número de itens que são ler a partir da origem de dados e indexado como um único batch para melhorar o desempenho. A predefinição depende do tipo de origem de dados: é 1000 para Azure SQL e DocumentDB e 10 para o armazenamento de Blobs do Azure.

**Mapeamentos de campo**

Pode utilizar os mapeamentos de campos para mapear um nome de campo na origem de dados para um nome de campo diferente no índice de destino. Por exemplo, considere uma tabela de origem com um campo `_id`. Pesquisa Azure não permite um nome de campo começar com um sublinhado, para que o campo deverá ser mudado. Isto pode ser feito utilizando o `fieldMappings` propriedade do indexador da seguinte forma: 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Pode especificar várias mapeamentos de campo: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Nomes de campos de origem e destino são maiúsculas e minúsculas.

<a name="FieldMappingFunctions"></a>
***Funções de mapeamento do campo***

Mapeamentos de campo podem também ser utilizados para transformar valores de campo de origem utilizando *as funções de mapeamento*.

Apenas uma essa função é atualmente suportada: `jsonArrayToStringCollection`. Este analisa um campo que contém uma cadeia formatada como uma matriz JSON para um campo Collection(Edm.String) no índice de destino. -Destina-se para utilização com indexador Azure SQL em particular, uma vez que SQL não tem um tipo de dados nativa coleções de sites. Pode ser utilizado da seguinte forma: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Por exemplo, se o campo de origem contiver a cadeia `["red", "white", "blue"]`, em seguida, o campo de destino do tipo de `Collection(Edm.String)` será preenchida com os três valores `"red"`, `"white"` e `"blue"`.

Tenha em atenção que a `targetFieldName` propriedade é opcional; se para a esquerda, o `sourceFieldName` valor é utilizado. 

<a name="CreateIndexerRequestExamples"></a>
**Exemplos de corpo do pedido**

O exemplo seguinte cria um indexador que copia os dados a partir da tabela referenciada pelo `ordersds` da origem de dados para o `orders` índice numa agenda que começa na 1 de Janeiro de 2015 UTC e é executado por hora. Cada invocação indexador será bem sucedida de se mais do que 5 itens a ser indexadas em cada lote falharem e não mais de 10 itens não conseguem ser indexadas no total. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Resposta**

201 criado para um pedido de efetuada com êxito.


<a name="UpdateIndexer"></a>
## <a name="update-indexer"></a>Atualizar indexador ##

Pode atualizar um indexador existente utilizando um pedido de HTTP colocar. Especifique o nome do indexador para atualizar a pedido URI:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

O `api-version` é necessário. A versão atual não for `2015-02-28`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Pedido**

A sintaxe de corpo do pedido é o mesmo que para [pedidos de criar indexador](#CreateIndexerRequestSyntax).

**Resposta**

Para um pedido de bem sucedido: 201 criado se um novo indexador foi criado e 204 sem conteúdo se foi atualizado um indexador existente.


<a name="ListIndexers"></a>
## <a name="list-indexers"></a>Lista indexadores ##

A operação de **Lista indexadores** devolve a lista de indexadores no seu serviço de pesquisa do Azure. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


O `api-version` é necessário. A versão de pré-visualização é `2015-02-28-Preview`. [Controlo de versões de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) tem detalhes e obter mais informações sobre as versões alternativos.

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Para um pedido de bem sucedido: 200 OK.

Eis um corpo de resposta de exemplo:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Tenha em atenção que pode filtrar a resposta para baixo até apenas as propriedades que lhe interessa. Por exemplo, se pretender apenas uma lista de nomes do indexador, utilize OData `$select` opção de consulta:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Neste caso, a resposta do exemplo acima apareceriam da seguinte forma: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Esta é uma técnica útil para guardar a largura de banda se tiver muitas indexadores no seu serviço de pesquisa.


<a name="GetIndexer"></a>
## <a name="get-indexer"></a>Obter indexador ##

A operação de **Obter indexador** obtém a definição do indexador a partir da pesquisa do Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessário. A versão de pré-visualização é `2015-02-28-Preview`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

A resposta é semelhante a exemplos [indexador criar pedidos de exemplo](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## <a name="delete-indexer"></a>Eliminar indexador ##

A operação de **Eliminar indexador** remove um indexador do seu serviço de pesquisa do Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Quando é eliminado um indexador, as execuções indexador em curso nesse momento serão executado para a conclusão, mas não execuções mais serão agendadas. Tenta utilizar um indexador inexistente irá resultar em código de estado HTTP 404 não encontrado. 
 
O `api-version` é necessário. A versão de pré-visualização é `2015-02-28-Preview`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Código de estado: 204 sem conteúdo é devolvido por uma resposta bem sucedida.

<a name="RunIndexer"></a>
## <a name="run-indexer"></a>Executar indexador ##

Para além de executar periodicamente numa agenda, um indexador também é possível invocar a pedido através da operação de **Indexador executar** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessário. A versão de pré-visualização é `2015-02-28-Preview`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Código de estado: 202 aceites são devolvido por uma resposta bem sucedida.

<a name="GetIndexerStatus"></a>
## <a name="get-indexer-status"></a>Obter o estado do indexador ##

A operação de **Obter o estado do indexador** obtém o histórico de estado e execução atual de um indexador: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


O `api-version` é necessário. A versão de pré-visualização é `2015-02-28-Preview`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Código de estado: 200 OK para uma resposta com êxito.

O corpo da resposta contém informações sobre o estado de estado de funcionamento do indexador geral, a última invocação de indexador, bem como o histórico de exe do indexador recentes (se existir). 

Corpo de resposta um exemplo tem o seguinte aspeto: 

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

**Estado do indexador**

Estado do indexador pode ser um dos seguintes valores:

- `running`indica que o indexador está em execução normalmente. Tenha em atenção que algumas das execuções indexador ainda poderão falhar, pelo que é uma boa ideia verificar o `lastResult` propriedade também. 

- `error`indica que o indexador Ocorreu um erro que não pode ser corrigido sem intervenção humana. Por exemplo, poderão ter expirado as credenciais da origem de dados ou o esquema da origem de dados ou do índice de destino foi alterado numa quebra de forma. 

**Resultado de execução do indexador**

Um resultado de execução do indexador contém informações sobre a execução de um único indexador. O resultado mais recente está estar como o `lastResult` propriedade do Estado do indexador. Outros resultados recentes, se existirem, são devolvidos como o `executionHistory` propriedade do Estado do indexador. 

Resultado de execução do indexador contém as seguintes propriedades: 

- `status`: o estado de um execução. Ver o [Estado de execução do indexador](#IndexerExecutionStatus) abaixo para obter detalhes. 

- `errorMessage`: mensagem de erro para a execução de um falha. 

- `startTime`: a hora no UTC quando esta execução iniciado.

- `endTime`: a hora no UTC quando esta execução terminado. Este valor definido não se a execução do ainda estiver em curso.

- `errors`: uma matriz de erros ao nível do item, se existirem. Cada entrada contém uma chave de documento (`key` propriedade) e uma mensagem de erro (`errorMessage` propriedade). 

- `itemsProcessed`: o número de dados de origem itens (por exemplo, linhas de tabela) que o indexador tentou indexar durante este execução. 

- `itemsFailed`: o número de itens que falhou durante a este execução.  
 
- `initialTrackingState`: sempre `null` para a primeira execução do indexador, ou se os dados altere a política de controlo não estiver ativado na origem de dados utilizada. Se tal política estiver ativada, no execuções subsequentes, este valor indica a primeira alteração (mais baixa) valor processada por esta execução de controlo. 

- `finalTrackingState`: sempre `null` se os dados de alterar a política de controlo não está ativada na origem de dados utilizada. Caso contrário, indica a mais recente (o mais alto) monitorização valor com êxito processada por esta execução. 

<a name="IndexerExecutionStatus"></a>
**Estado de execução do indexador**

Estado de execução do indexador captura o estado de execução de um único indexador. Pode ter os seguintes valores:

- `success`indica que a execução do indexador foi concluída com êxito.

- `inProgress`indica que a execução do indexador está em curso. 

- `transientFailure`indica que um execução indexador falhou. Consulte o artigo `errorMessage` propriedade para obter detalhes. A falha pode ou não pode exigir intervenção humana para corrigir - por exemplo, corrigir uma incompatibilidade de esquema entre a origem de dados e o índice de destino necessita de ação do utilizador, enquanto que um tempo de inatividade da origem de dados temporários não. Exe indexador irão continuar por agenda, se existir. 

- `persistentFailure`indica que o indexador falhou de uma forma que requer a intervenção humana. Parar de execuções do indexador agendada. Depois de resolver o problema, utilize repor API do indexador para reiniciar as execuções agendadas. 

- `reset`indica que o indexador tiver sido reposto por uma chamada para repor a API do indexador (ver abaixo). 

<a name="ResetIndexer"></a>
## <a name="reset-indexer"></a>Repor indexador ##

A operação de **Repor indexador** repõe alterações de estado associado a indexador. Esta opção permite-lhe acionar a reindexação de raiz (por exemplo, se o esquema da origem de dados tiver sido alterada), ou para alterar a política de deteção de alteração de dados para uma origem de dados associada com o indexador.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

O `api-version` é necessário. A versão de pré-visualização é `2015-02-28-Preview`. 

O `api-key` tem de ser uma chave de administrador (por oposição a uma chave de consulta). Consulte a secção autenticação na [REST API do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn798935.aspx) para saber mais acerca das teclas. [Criar um serviço de pesquisa no portal do](search-create-service-portal.md) explica como obter o URL do serviço e propriedades da chave utilizadas no pedido.

**Resposta**

Código de estado: 204 sem conteúdo para uma resposta com êxito.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados de pesquisa Azure e tipos de dados SQL ##

<table style="font-size:12">
<tr>
<td>Tipo de dados SQL</td>  
<td>Permitido tipos de campo de índice de destino</td>
<td>Notas</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>flutuar real,</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, dinheiro<br>decimal<br>numéricos
</td>
<td>Edm.String</td>
<td>Pesquisa Azure não suporta a converter tipos de casas decimais Edm.Double porque este seria perder precisão
</td>
</tr>
<tr>
<td>CARÁCT, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>COLLECTION(EDM.String)</td>
<td>Consulte [Funções de mapeamento de campo](#FieldMappingFunctions) neste documento para obter detalhes sobre como transformar uma coluna de cadeia numa Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, data, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Geografia</td>
<td>Edm.GeographyPoint</td>
<td>São suportadas apenas geografia instâncias tipo de ponto com 4326 SRID (que é a predefinição)</td>
</tr>
<tr>
<td>ROWVERSION</td>
<td>N/D</td>
<td>Colunas de versão linha não podem ser armazenadas no índice de pesquisa, mas podem ser utilizadas para o registo de alterações</td>
</tr>
<tr>
<td>tempo, timespan<br>binário, varbinary, imagem,<br>XML, geometria, tipos CLR</td>
<td>N/D</td>
<td>Não é suportado</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapeamento entre os tipos de dados JSON e tipos de dados de pesquisa Azure ##

<table style="font-size:12">
<tr>
<td>Tipo de dados JSON</td> 
<td>Permitido tipos de campo de índice de destino</td>
<td>Notas</td>
</tr>
<tr>
<td>Booleano</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números integral</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Números de vírgula flutuante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>cadeia</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>tipos de matrizes de primitiva, por exemplo, ["a", "b", "c"]</td>
<td>COLLECTION(EDM.String)</td>
<td></td>
</tr>
<tr>
<td>Cadeias que aspeto datas</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objetos de ponto GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Pontos de GeoJSON são objetos JSON no seguinte formato: {"tipo": "Ponto", "coordenadas": [lat longo,]} </td>
</tr>
<tr>
<td>Outros objetos JSON</td>
<td>N/D</td>
<td>Não suportado Pesquisa Azure atualmente suporta apenas tipos primitivos e colecções de cadeia</td>
</tr>
</table>