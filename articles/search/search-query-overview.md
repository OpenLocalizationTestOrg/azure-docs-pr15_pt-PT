<properties
    pageTitle="Índice de pesquisa Azure de consulta | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Criar uma consulta de pesquisa na pesquisa Azure e utilizar parâmetros de pesquisa para filtrar e ordenar resultados de pesquisa."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index"></a>Consulta de índice de pesquisa do Azure
> [AZURE.SELECTOR]
- [Descrição geral](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTO](search-query-rest-api.md)

Quando a apresentação de pedidos de pesquisa para pesquisa Azure, existem um número de parâmetros que pode ser especificado juntamente com as palavras reais que são introduzidas na caixa de pesquisa da sua aplicação. Estes parâmetros de consulta permitem-lhe obter algumas um controlo mais aprofundado da experiência de pesquisa de texto completo.

Segue-se uma lista que explica brevemente utilizações comuns de parâmetros de consulta na pesquisa Azure. Para uma cobertura completa dos parâmetros de consulta e os respetivos comportamento, consulte o artigo as páginas detalhadas para a [REST API](https://msdn.microsoft.com/library/azure/dn798927.aspx) e [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.searchparameters_properties.aspx).

## <a name="types-of-queries"></a>Tipos de consultas

Pesquisa Azure oferece várias opções para criar consultas extremamente eficientes. Os dois tipos principais de consulta que irá utilizar são `search` e `filter`. A `search` procura um ou mais termos em todos os campos _pesquisável_ no índice de consulta e funciona do modo seria esperado um motor de busca como o Bing ou o Google para trabalhar. A `filter` consulta avalia uma expressão booleana sobre todos os _filtráveis_ campos num índice remissivo. Ao contrário `search` consultas, `filter` consultas correspondem o conteúdo de um campo, o que significa a maiúsculas e minúsculas para campos de cadeia exato.

Pode utilizar pesquisas e filtros em conjunto ou separadamente. Se utiliza o-las em conjunto, o filtro é aplicado pela primeira vez para todo o índice e, em seguida, a pesquisa é executada nos resultados do filtro. Filtros, por conseguinte, podem ser uma técnica útil para melhorar o desempenho de consulta uma vez que estes reduzem o conjunto de documentos que precisa de consulta de pesquisa para processar.

A sintaxe para expressões de filtro é um subconjunto do [idioma do filtro de OData](https://msdn.microsoft.com/library/azure/dn798921.aspx). Para consultas de pesquisa pode utilizar a [sintaxe da simplificado](https://msdn.microsoft.com/library/azure/dn798920.aspx) ou a [sintaxe da consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) que são descritos abaixo.

### <a name="simple-query-syntax"></a>Sintaxe de consulta simples
A [sintaxe da consulta simples](https://msdn.microsoft.com/library/azure/dn798920.aspx) é a linguagem de consulta predefinida utilizada na pesquisa Azure. A sintaxe da consulta simples suporta um número de operadores de pesquisa comuns, incluindo AND, ou, não, frase, sufixo e operadores precedência.

### <a name="lucene-query-syntax"></a>Sintaxe de consulta Lucene
A [sintaxe da consulta Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) permite-lhe para utilizarem o idioma de consulta amplamente aprovadas e modelar desenvolvido como parte do [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Utilizar esta sintaxe de consulta permite-lhe alcançar facilmente as seguintes capacidades: [confinados campo consultas](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fields), [pesquisa nítida](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_fuzzy), [pesquisa proximidade](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_proximity), [aumentar a termos](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_termboost), [pesquisa de expressão regular](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_regex), [universais pesquisa](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_wildcard), [os conceitos básicos da sintaxe](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_syntax)e [consultas utilizando operadores lógicos](https://msdn.microsoft.com/library/azure/mt589323.aspx#bkmk_boolean).



## <a name="ordering-results"></a>Ordenação de resultados
Quando receber resultados para uma consulta de pesquisa, pode pedir que o Azure pesquisa serve os resultados ordenados por valores num campo específico. Por predefinição, pesquisa Azure encomendas de resultados da pesquisa com base na classificação de pontuação de pesquisa de cada documento, que deriva da [TF FIL](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se pretender que o Azure pesquisa para devolver os resultados da sua ordenada por um valor diferente de pontuação pesquisa, pode utilizar o `orderby` parâmetro de procura. Pode especificar o valor da `orderby` parâmetro para incluir os nomes dos campos e chamadas para o [ `geo.distance()` função](https://msdn.microsoft.com/library/azure/dn798921.aspx) para geoespaciais valores. Cada expressão pode ser seguido por `asc` para indicar que os resultados são solicitados por ordem ascendente, e `desc` para indicar que os resultados são solicitados por ordem descendente. A classificação de predefinido por ordem ascendente.

## <a name="paging"></a>Paginação
Pesquisa Azure torna mais fácil implementar o paginação dos resultados da pesquisa. Ao utilizar o `top` e `skip` parâmetros, sem problemas pode emitir pedidos de pesquisa que permitem-lhe receber o conjunto total dos resultados da pesquisa no fácil, ordenados subconjuntos que permitem facilmente práticas de IU boa pesquisa. Quando receber estes subconjuntos mais pequenos de resultados, também pode receber a contagem de documentos no conjunto de total dos resultados da pesquisa.

Pode obter mais informações sobre os resultados da pesquisa no artigo [como à página de resultados na pesquisa Azure de pesquisa](search-pagination-page-layout.md)de paginação.


## <a name="hit-highlighting"></a>Acertar realce
Em pesquisa Azure, realçar a parte dos resultados de pesquisa que correspondem a consulta de pesquisa exata é feita fácil utilizando o `highlight`, `highlightPreTag`, e `highlightPostTag` parâmetros. Pode especificar quais os campos _pesquisável_ deve ter os respetivos texto correspondido realçado assim como especificando devolve os códigos de cadeia exata para acrescentar para o início e de fim do texto correspondido que Azure a pesquisa.
