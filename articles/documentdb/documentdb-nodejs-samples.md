<properties
    pageTitle="Exemplos de NoSQL Node.js para DocumentDB | Microsoft Azure"
    description="Encontre NoSQL Node.js exemplos no github para tarefas comuns no DocumentDB, incluindo operações CRUD para documentos JSON em NoSQL bases de dados."
    keywords="Exemplos de node.js"
    services="documentdb"
    authors="moderakh"
    manager="jhubbard"
    editor="monicar"
    documentationCenter="nodejs"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="moderakh"/>


# <a name="documentdb-nodejs-examples"></a>Exemplos de DocumentDB Node.js

> [AZURE.SELECTOR]
- [Exemplos de .NET](documentdb-dotnet-samples.md)
- [Exemplos de node.js](documentdb-nodejs-samples.md)
- [Exemplos de Python](documentdb-python-samples.md)
- [Galeria de exemplo de código do Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Soluções de exemplo que desempenhar operações CRUD e outras operações comuns no Azure DocumentDB recursos estão incluídas no repositório de GitHub [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) . Este artigo fornece:

- Ligações para as tarefas em cada um dos ficheiros de projeto de exemplo Node.js.
- Ligações à API relacionado referenciam conteúdo.

**Pré-requisitos**

1. Precisa de uma conta Azure para utilizar estes exemplos de Node.js:
    - Pode [Abrir uma conta Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/): obter o créditos pode utilizar para experimentar o nosso pagos serviços Azure e mesmo depois de que estão habituados até pode manter a conta e utilização livre Azure serviços, como sites. O cartão de crédito nunca será cobrado, exceto se alterar as definições e pedir para ser cobrada explicitamente.
   - Pode [Ativar benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): subscrição o Visual Studio fornece-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.
2. Também precisa do [Node.js SDK](documentdb-sdk-node.md).

    > [AZURE.NOTE] Cada amostra é autónoma, configura próprio e limpa após próprio. Como tal, as amostras emitem várias chamadas para [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). Sempre que isto é feito a sua subscrição será de ser faturada para 1 hora de utilização pela camada de desempenho da coleção de criação.

## <a name="database-examples"></a>Exemplos de base de dados

O ficheiro [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) do projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar uma base de dados](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) | [DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase)
[Uma conta para uma base de dados da consulta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) | [DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases)
[Ler uma base de dados por Id](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) | [DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Bases de dados de lista para uma conta](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) | [DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase)
[Eliminar uma base de dados](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) | [DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase)

## <a name="collection-examples"></a>Exemplos de coleções de sites

O ficheiro [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) do projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar uma coleção de](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)
[Ler uma lista de todas as colecções de uma base de dados](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) | [DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections)
[Obter uma coleção de ao auto](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obtenha uma colecção por Id](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) | [DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection)
[Obter a camada de desempenho de uma coleção de](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) | [DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers)
[Alterar a camada de desempenho de uma coleção de](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) | [DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer)
[Eliminar uma coleção de](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) | [DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection)

## <a name="document-examples"></a>Exemplos de documento

O ficheiro [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) do projeto [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar documentos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument)
[Ler o documento feed para uma coleção de](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Ler um documento por ID](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)
[Ler o documento apenas se o documento tiver sido alterada](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) | [DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Consulta de documentos](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) | [DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments)
[Substituir um documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)
[Substituir o documento com a verificação de ETag condicional](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |  [DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Eliminar um documento](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) | [DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument)

## <a name="indexing-examples"></a>Exemplos de indexação

O ficheiro [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) do projeto [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
Criar uma coleção de com a indexação predefinido | [DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html)
[Indexar manualmente um documento específico](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) | [indexingDirective: 'incluir'](http://azure.github.io/azure-documentdb-node/global.html#indexingDirective)
[Excluir manualmente um documento específico do índice](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) | [RequestOptions.indexingDirective](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions)
[Utilizar a indexação preguiça para importação em volume ou ler grossas coleções de sites](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) | [IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode)
[Incluir caminhos específicos de um documento na indexação](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) | [IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[Excluir determinados caminhos da indexação](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) | [ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy)
[Permitir que uma pesquisa um caminho de cadeia durante uma operação de intervalo](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347)| [ExcludedPath.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions)
[Criar um índice de intervalo no caminho de uma cadeia](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) | [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument)
[Criar uma coleção de com indexPolicy predefinido, em seguida, actualizar esta online](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) | [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html)

Para mais informações acerca da indexação, consulte o artigo [DocumentDB políticas de indexação](documentdb-indexing-policies.md).

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor

O ficheiro [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) do projeto [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar um procedimento armazenado](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) | [DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure)
[Executar um procedimento armazenado](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) | [DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure)

Para mais informações sobre a programação do lado do servidor, consulte o artigo [programação do lado do servidor DocumentDB: procedimentos armazenados, accionadores da base de dados e UDFs](documentdb-programming.md).

## <a name="partitioning-examples"></a>Criar a partições exemplos

O ficheiro [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) do projeto [divisão](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Utilizar um HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) | [HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html)

Para mais informações sobre como criar a partições dados DocumentDB, consulte [dados partição e a escala DocumentDB](documentdb-partition-data.md).
