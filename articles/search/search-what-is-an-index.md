<properties
    pageTitle="Criar um índice de pesquisa do Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="O que é um índice remissivo no Azure pesquisa e como é utilizado?"
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

# <a name="create-an-azure-search-index"></a>Criar um índice de pesquisa do Azure
> [AZURE.SELECTOR]
- [Descrição geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>O que é um índice remissivo?

Um *índice remissivo* é um arquivo persistente de *documentos* e outros construções utilizadas por um serviço de pesquisa do Azure. Um documento é uma única unidade de dados pesquisáveis no seu índice. Por exemplo, um revendedor comércio electrónico pode ter um documento para cada item que vendem, uma organização de notícias pode ter um documento para cada artigo, etc. Mapear estes conceitos para os equivalentes de base de dados mais familiares: um *índice remissivo* é conceptual semelhante a uma *tabela*e *os documentos* são aproximadamente equivalente a *linhas* numa tabela.

Quando adicionar/carregar documentos e submeter consultas de pesquisa para pesquisa Azure, submeta pedidos de para um índice específico no seu serviço de pesquisa.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Tipos de campo e os atributos de um índice de pesquisa do Azure

Como definir o seu esquema, tem de especificar o nome, tipo e atributos de cada campo no seu índice. O tipo de campo classifica os dados armazenados nesse campo. Atributos são definidos em campos individuais para especificar como o campo é utilizado. As tabelas seguintes enumerar os tipos e atributos que pode especificar.


### <a name="field-types"></a>Tipos de campo
|Tipo|Descrição|
|------------|-----------|
|*Edm.String*|Texto com, opcionalmente, pode ser token para pesquisa de texto completo (word recentes, radicais, etc).|
|*COLLECTION(EDM.String)*|Uma lista de cadeias com, opcionalmente, pode ser token para pesquisa de texto completo. Não existe limite superior teórica no número de itens numa coleção, mas o limite superior 16 MB de tamanho de carga útil aplica-se para coleções de sites.|
|*Edm.Boolean*|Contém valores verdadeiro/falso.|
|*Edm.Int32*|valores de número inteiro de 32 bits.|
|*Edm.Int64*|valores de número inteiro de 64 bits.|
|*Edm.Double*|Dados numéricos de precisão dupla.|
|*Edm.DateTimeOffset*|Data representados no formato do OData V4 os valores de tempo (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Um ponto de que representa uma localização geográfica no globo.|

Pode encontrar informações mais detalhadas sobre [tipos de dados no MSDN suportados a pesquisa Azure](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### <a name="field-attributes"></a>Atributos de campo
|Atributo|Descrição|
|------------|-----------|
|*Chave*|Uma cadeia que fornece o ID exclusivo de cada documento, utilizado para aspecto do documento para cima. Cada índice tem de ter uma tecla. Apenas um campo pode ser a tecla e o tipo de tem de estar definido para Edm.String.|
|*Recuperável*|Especifica se um campo pode ser devolvido num resultado de pesquisa.|
|*Filtráveis*|Permite que o campo a ser utilizados em consultas de filtro.|
|*Ordenável*|Permite que uma consulta para ordenar os resultados da pesquisa utilizando este campo.|
|*Facetable*|Permite que um campo ser utilizado numa estrutura de [Navegação por facetas](search-faceted-navigation.md) de filtragem personalizada direccionado de utilizador. Normalmente, os campos que contêm valores repetitivas que pode utilizar para agrupar vários documentos (por exemplo, vários documentos que se inserem numa única marca ou categoria de serviço) funcionam melhor como facetas.|
|*Pesquisável*|Marca pesquisável o campo como texto completo.|

Pode encontrar informações mais detalhadas sobre do Azure pesquisa [atributos de índice remissivo no MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx).



## <a name="guidance-for-defining-an-index-schema"></a>Orientações para definir um esquema de índice remissivo

Como estruturar o índice remissivo, leve o tempo na fase de planeamento a ter em conta através de cada decisão. É importante ter as pesquisa utilizador experiência empresas suas necessidades e em conta ao estruturar o índice remissivo, tal como cada campo tem de ser atribuído os [atributos inicial maiúscula](https://msdn.microsoft.com/library/azure/dn798941.aspx). Alterar um índice remissivo após é implementada envolve a reformulação repetida e recarregar os dados.


Se alterar os requisitos de armazenamento de dados ao longo do tempo, pode aumentar ou diminuir capacidade adicionando ou removendo a partições. Para obter detalhes, consulte o artigo [Gerir o serviço de pesquisa no Azure](search-manage.md) ou os [Limites de serviço](search-limits-quotas-capacity.md).
