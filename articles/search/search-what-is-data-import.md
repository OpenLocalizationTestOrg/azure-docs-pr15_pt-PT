<properties
    pageTitle="Carregar dados na pesquisa Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Saiba como carregar dados para um índice remissivo no Azure pesquisa."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>Carregar dados para pesquisa do Azure
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTO](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>Dados carregar modelos na pesquisa Azure
Existem duas formas para preencher o índice de pesquisa do Azure com os seus dados. A primeira opção é conduza manualmente os seus dados para o índice utilizando a pesquisa do Azure [REST API](search-import-data-rest-api.md) ou [.NET SDK](search-import-data-dotnet.md). A segunda opção é para o [ponto de uma origem de dados suportados](search-indexer-overview.md) para o índice de pesquisa do Azure e permitir que procura Azure extrair automaticamente os seus dados para o serviço de pesquisa.

Este guia só irá abranger instruções sobre como utilizar o modelo de push do carregamento de dados (o que é suportado apenas nos [REST API](search-import-data-rest-api.md) e [.NET SDK](search-import-data-dotnet.md)), mas ainda pode obter mais informações sobre o modelo recolher abaixo.

### <a name="push-data-to-an-index"></a>Transmitir dados para um índice remissivo

Esta abordagem que se refere a através de programação enviar os seus dados para Azure pesquisa para o tornar disponível para procurar. Para aplicações de requisitos de latência muito baixo (por exemplo, se precisar de procurar operações para ser sincronizado com bases de dados de inventário dinâmico), o modelo de push é a sua única opção.

Pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [.NET SDK](search-import-data-dotnet.md) para push dados para um índice remissivo. Atualmente, não existe suporte ferramenta para colocar dados através do portal.

Esta abordagem é mais flexível do que o modelo recolher uma vez que pode carregar documentos individualmente ou em lotes (até 1000 por lote ou 16 MB, independentemente limite vem primeira). O modelo de push também permite-lhe carregar documentos para pesquisa Azure, independentemente de onde os dados estiverem.

### <a name="pull-data-into-an-index"></a>Importar dados para um índice remissivo

O modelo recolher percorre uma origem de dados suportados e automaticamente os carregamentos pendentes os dados para o índice de pesquisa do Azure por si. Ao controlar as alterações e elimina a documentos existentes além de novos documentos a reconhecer as, indexadores remover a necessidade de ativamente gerir os dados no seu índice.

Na pesquisa Azure, esta funcionalidade é implementada através de *indexadores*, atualmente disponíveis para [o armazenamento de BLOBs (pré-visualização)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [base de dados do Azure SQL e o SQL Server no Azure VMs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

A funcionalidade de indexador é exposta no [Portal do Azure](search-import-data-portal.md) , assim como a [REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx).
