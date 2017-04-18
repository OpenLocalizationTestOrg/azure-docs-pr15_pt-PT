<properties 
    pageTitle="Exemplos de NoSQL Python para DocumentDB | Microsoft Azure" 
    description="Encontre NoSQL Python exemplos no github para tarefas comuns no DocumentDB, incluindo operações CRUD para documentos JSON em NoSQL bases de dados." 
    keywords="Exemplos de Python"
    services="documentdb" 
    authors="moderakh" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter="python"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/18/2016" 
    ms.author="moderakh"/>


# <a name="documentdb-python-examples"></a>Exemplos de DocumentDB Python

> [AZURE.SELECTOR]
- [Exemplos de .NET](documentdb-dotnet-samples.md)
- [Exemplos de node.js](documentdb-nodejs-samples.md)
- [Exemplos de Python](documentdb-python-samples.md)
- [Galeria de exemplo de código do Azure](https://azure.microsoft.com/documentation/samples/?service=documentdb)

Soluções de exemplo que desempenhar operações CRUD e outras operações comuns no Azure DocumentDB recursos estão incluídas no repositório de GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) . Este artigo fornece:

- Ligações para as tarefas em cada um dos ficheiros de projeto de exemplo Python. 
- Ligações à API relacionado referenciam conteúdo.

**Pré-requisitos**

1. Precisa de uma conta Azure para utilizar estes exemplos de Python:
    - Pode [Abrir uma conta Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/): obter o créditos pode utilizar para experimentar o nosso pagos serviços Azure e mesmo depois de que estão habituados até pode manter a conta e utilização livre Azure serviços, como sites. O cartão de crédito nunca será cobrado, exceto se alterar as definições e pedir para ser cobrada explicitamente.
   - Pode [Ativar benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/): subscrição o Visual Studio fornece-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.
2. Também precisa do [Python SDK](documentdb-sdk-python.md). 

    > [AZURE.NOTE] Cada amostra é autónoma, configura propriamente dito e limpa após a próprio. Como tal, as amostras emitem várias chamadas para [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). Sempre que isto é feito a sua subscrição será de ser faturada para 1 hora de utilização pela camada de desempenho da coleção de criação. 

## <a name="database-examples"></a>Exemplos de base de dados

O ficheiro [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) do projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document_client. CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Uma conta para uma base de dados da consulta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document_client. QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Ler uma base de dados por Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document_client. ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Bases de dados de lista para uma conta](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document_client. ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[Eliminar uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document_client. DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## <a name="collection-examples"></a>Exemplos de coleções de sites 

O ficheiro [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) do projeto [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) mostra como efetuar as seguintes tarefas.

Tarefa | Referência da API
--- | ---
[Criar uma coleção de](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document_client. CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Ler uma lista de todas as colecções de uma base de dados](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document_client. ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obtenha uma colecção por Id](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document_client. ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Obter a camada de desempenho de uma coleção de](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Alterar a camada de desempenho de uma coleção de](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document_client. ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[Eliminar uma coleção de](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document_client. DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
