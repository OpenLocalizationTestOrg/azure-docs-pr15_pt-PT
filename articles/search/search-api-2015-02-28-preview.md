<properties
   pageTitle="Azure serviço REST API versão 2015-02-28-pré-visualização Search | Microsoft Azure | Pré-visualização de pesquisa Azure API"
   description="Azure pesquisa serviço REST API versão 2015-02-28-pré-visualização inclui a experimentais funcionalidades como as pesquisas de linguagem Natural analisadores e moreLikeThis."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="09/07/2016"
   ms.author="brjohnst"/>

# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>API REST do serviço de pesquisa Azure: Versão 2015-02-28-pré-visualização

Este artigo é a documentação de referência para `api-version=2015-02-28-Preview`. Esta pré-visualização expande a versão atual ficará disponível, [versão api = 2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), ao fornecer as experimentais seguintes funcionalidades:

- `moreLikeThis`consulta parametrizada em [Procurar documentos](#SearchDocs) API. Localiza outros documentos relevantes para outro documento específico.

Algumas partes adicionais da `2015-02-28-Preview` REST API encontram-se documentados separadamente. Estes incluem:

- [Perfis de pontuação](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indexadores](search-api-indexers-2015-02-28-preview.md)

Serviço de procura do Azure está disponível em várias versões. Consulte para [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes.

## <a name="apis-in-this-document"></a>APIs neste documento

API do serviço de pesquisa Azure suporta duas sintaxes de URL para operações de API: simples e OData (consulte [suporte para OData (Azure pesquisa API)](http://msdn.microsoft.com/library/azure/dn798932.aspx) para obter mais detalhes). A lista seguinte mostra a sintaxe simple.

[Criar índice](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Atualizar índice remissivo](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obter o índice remissivo](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Índices de lista](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obter estatísticas de índice remissivo](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Análise de teste](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Eliminar um índice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Adicionar, eliminar e atualizar dados de um índice remissivo](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Procurar documentos](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Documento de pesquisa](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Contagem de documentos](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Sugestões](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## <a name="index-operations"></a>Operações de índice

Pode criar e gerir índices no serviço de pesquisa do Azure através da simples pedidos de HTTP (POST, GET, hoje, eliminar) relativamente a um recurso de índice remissivo determinado. Para criar um índice remissivo, pela primeira vez que publicar um documento JSON que descreve o esquema de índice remissivo. O esquema define os campos de índice, os tipos de dados e como pode ser utilizados (por exemplo, em pesquisas de texto completo, filtros, ordenação ou faceting). Também define perfis de pontuação, suggesters e outros atributos para configurar o comportamento do índice.

O exemplo seguinte fornece uma ilustração de um esquema, utilizado para procurar nas informações de hotel com o campo Descrição definido em dois idiomas. Repare como atributos controlam como o campo é utilizado. Por exemplo o `hotelId` é utilizado como chave documento (`"key": true`) e é excluída pesquisas de texto completo (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Depois do índice é criado, irá carregar documentos povoar o índice. Consulte o artigo [Adicionar ou atualizar documentos](#AddOrUpdateDocuments) para este passo seguinte.

Para um vídeo de introdução a indexação na pesquisa Azure, consulte o [canal 9 nuvem de folha de rosto episódio no Azure pesquisa](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## <a name="create-index"></a>Criar índice

Um índice remissivo é o principal meio de organizar e pesquisar documentos na pesquisa Azure, semelhante à forma como uma tabela organiza registos numa base de dados. Cada índice tem uma coleção de documentos que tudo está em conformidade com o esquema de índice remissivo (nomes de campos, tipos de dados e propriedades), mas índices também especificar adicionais construções (suggesters, pontuação perfis e opções de CORS) que definem outros comportamentos de pesquisa.

Pode criar um novo índice dentro de um serviço de pesquisa do Azure utilizando um pedido de HTTP POST ou colocar. O corpo do pedido de é um esquema JSON que especifica as índice e informações de configuração.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Em alternativa, pode utilizar o local e especifique o nome de índice remissivo no URI. Se o índice não existir, será criado.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

Criar um índice remissivo determina a estrutura dos documentos armazenados e utilizados em operações de pesquisa. Preencher o índice é uma operação em separado. Para este passo, pode utilizar um [indexador](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponível para origens de dados suportadas) ou uma operação de [Adicionar, atualizar ou eliminar documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) . É gerado o índice invertido quando os documentos são registados.

**Nota**: O número máximo de índices permitido varia por preços de camadas. O serviço gratuito permite até 3 índices. Serviço padrão permite 50 índices por serviço de pesquisa. Consulte o artigo [limites e restrições](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obter detalhes.

**Pedido**

É necessário HTTPS para todos os pedidos de serviço. O pedido de **Create Index** pode ser construído utilizando o método de uma mensagem ou um local. Ao utilizar a mensagem, tem de fornecer o nome de um índice remissivo no corpo do pedido juntamente com a definição de esquema de índice remissivo. Com o local, o nome do índice faz parte do URL. Se o índice não existir, é criada. Se já existir, é atualizado para a nova definição.

O nome do índice deve estar em minúsculas, começar com uma letra ou um número, ter sem barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome do índice com uma letra ou um número, o resto do nome pode incluir qualquer letra, o número e travessões, desde que não sejam consecutivos os traços.

O `api-version` é necessário. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter uma lista de versões disponíveis.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `Content-Type`: Necessário. Defina esta opção para`application/json`
- `api-key`: Necessário. O `api-key` é utilizada para
- autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Create Index** tem de incluir um `api-key` cabeçalho definido como a sua chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

<a name="RequestData"></a>
**Sintaxe de corpo do pedido**

O corpo do pedido de contém uma definição de esquema, que inclui a lista de campos de dados nos documentos que vai ser alimentados neste índice, tipos de dados, atributos, bem como uma lista opcional de pontuação perfis que são utilizados para pontuação documentos correspondentes no momento da consulta.

Tenha em atenção que para um pedido de mensagem, tem de especificar o nome do índice no corpo do pedido.

Só pode haver um campo de chave no índice remissivo. Tem de ser um campo de cadeia. Este campo representa o identificador exclusivo para cada documento armazenado no índice remissivo.

As partes principais de um índice remissivo incluem o seguinte:

- `name`
- `fields`que vai ser alimentado neste índice, incluindo o nome, tipo de dados e propriedades que definem ações permitidas nesse campo.
- `suggesters`utilizado para consultas de conclusão automática ou as sugestões automáticas.
- `scoringProfiles`utilizados para classificação de pontuação de pesquisa personalizada. Consulte o artigo [Adicionar pontuação perfis](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.
- `analyzers`, `charFilters`, `tokenizers`, `tokenFilters` utilizada para definir como documentos/consultas são divididas em tokens indexáveis/pesquisáveis. Consulte o artigo [análise na pesquisa Azure](https://aka.ms//azsanalysis) para obter detalhes.
- `defaultScoringProfile`utilizado para substituir a predefinição comportamentos de pontuação.
- `corsOptions`para permitir que as consultas de origem da publicação em contra o índice remissivo.

A sintaxe para estruturar a carga útil pedido é da seguinte forma. Um pedido de exemplo é fornecido continuar neste tópico.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Atributos do índice**

Os seguintes atributos podem ser definidos ao criar um índice remissivo. Para obter detalhes sobre os perfis de pontuação e pontuação, consulte o artigo [adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`-Define o nome do campo.

`type`-Define o tipo de dados para o campo. Consulte o artigo [Tipos de dados suportadas](#DataTypes) para ver uma lista dos tipos de suportados.

`searchable`-Marca o campo como texto completo capaz de pesquisa. Isto significa que-irão ser submetidos análise como o word recentes durante a indexação. Se definir uma `searchable` campo a um valor como "dia de sol", internamente-lo será dividido em tokens individuais "sol" e "dia". Permite que as pesquisas de texto completo para estes termos. Campos tipo `Edm.String` ou `Collection(Edm.String)` são `searchable` por predefinição. Campos de outros tipos de não podem ser `searchable`.

  - **Nota**: `searchable` campos consumam espaço extra no índice de uma vez que o Azure pesquisa irá guardar numa versão tokenized adicional do valor do campo para pesquisas de texto completo. Se pretende poupar espaço no seu índice e não precisa de um campo para ser incluídas nas pesquisas, defina `searchable` para `false`.

`filterable`-Permite que o campo a ser referenciada `$filter` consultas. `filterable`difere `searchable` no cadeias de que forma são processadas. Campos tipo `Edm.String` ou `Collection(Edm.String)` que são `filterable` não sofrem word recentes, para que sejam comparações para exata corresponde ao apenas. Por exemplo, se definir esse campo `f` para "dia de sol", `$filter=f eq 'sunny'` irá encontrar sem correspondências mas `$filter=f eq 'sunny day'` irá. Todos os campos estão `filterable` por predefinição.

`sortable`-Por predefinição o sistema ordena os resultados por pontuação, mas no experiências muitos utilizadores irão pretende ordenar por campos nos documentos. Campos tipo `Collection(Edm.String)` não podem ser `sortable`. Todos os outros campos estão `sortable` por predefinição.

`facetable`-Normalmente utilizado numa apresentação dos resultados da pesquisa que inclui a contagem de acessos por categoria (por exemplo, a pesquisa para câmaras e digitais Consulte acertos por marca, por megapixels, pelo preço, etc.). Esta opção não pode ser utilizada com campos tipo `Edm.GeographyPoint`. Todos os outros campos estão `facetable` por predefinição.

  - **Nota**: campos tipo `Edm.String` que são `filterable`, `sortable`, ou `facetable` pode ser no máximo 32 KB de comprimento. Esta é uma vez que esses campos são tratados como um termo de pesquisa único e o comprimento máximo de um termo de pesquisa do Azure é de 32KB. Se precisar para armazenar mais texto superior a esta num campo única cadeia, terá de definir explicitamente `filterable`, `sortable`, e `facetable` para `false` na sua definição de índice remissivo.

  - **Nota**: se um campo não tem nenhum dos atributos acima definidos para `true` (`searchable`, `filterable`, `sortable`, ou`facetable`) o campo é eficazmente excluído do índice invertido. Esta opção é útil para os campos que não são utilizados em consultas, mas são necessários nos resultados da pesquisa. Excluir esses campos do índice melhora o desempenho.

`key`-Marca campo como que contém os identificadores exclusivos para documentos no índice remissivo. Tem de ser escolhido exatamente um campo como o `key` campo e -tem de ser do tipo de `Edm.String`. Campos de chave podem ser utilizados para procurar documentos diretamente através da [API de pesquisa](#LookupAPI).

`retrievable`-Define se o campo pode ser devolvido num resultado de pesquisa.  Isto é útil quando pretende utilizar um campo (por exemplo, margem) como um filtro, ordenação ou de pontuação mecanismo, mas não pretende o campo a estar visíveis para o utilizador final. Este atributo tem de ser `true` para `key` campos.

`analyzer`-Define o nome do analyzer a utilizar para o campo na hora de pesquisa e a hora de indexação. Para o conjunto de valores permitido consulte [analisadores](https://msdn.microsoft.com/library/mt605304.aspx). Esta opção pode ser utilizada apenas com `searchable` campos e não podem ser definidos juntamente com um `searchAnalyzer` ou `indexAnalyzer`.  Assim que a análise de for escolhida, não pode ser alterado para o campo.

`searchAnalyzer`-Define o nome da análise de utilizadas em altura da pesquisa para o campo. Para o conjunto de valores permitido consulte [analisadores](https://msdn.microsoft.com/library/mt605304.aspx). Esta opção pode ser utilizada apenas com `searchable` campos. Tem de estar definida em conjunto com `indexAnalyzer` e não podem ser definido em conjunto com o `analyzer` opção. Este analyzer pode ser atualizado num campo existente.

`indexAnalyzer`-Define o nome da análise de utilizadas em altura indexação para o campo. Para o conjunto de valores permitido consulte [analisadores](https://msdn.microsoft.com/library/mt605304.aspx). Esta opção pode ser utilizada apenas com `searchable` campos. Tem de estar definida em conjunto com `searchAnalyzer` e não podem ser definido em conjunto com o `analyzer` opção. Assim que a análise de for escolhida, não pode ser alterado para o campo.

`suggesters`-Define o modo de procura e os campos que estão a origem de conteúdo para obter sugestões. Consulte o artigo [Suggesters](#Suggesters) para obter detalhes.

`scoringProfiles`-Define pontuação comportamentos personalizados que permitem-lhe influenciar quais os itens são apresentados mais elevados nos resultados da pesquisa. Perfis de pontuação são constituídos por espessuras de campo e funções. Consulte o artigo [adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para mais informações sobre os atributos utilizados num perfil de pontuação.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Suporte de idiomas**

Campos pesquisáveis submetidos análise que a maior parte frequentemente envolve word recentes, normalização de texto e a exclusão de termos. Por predefinição, os campos pesquisáveis na pesquisa Azure são analisados com o [Analisador de Apache Lucene padrão](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html) que divide o texto em elementos seguindo as regras do["segmentação de texto Unicode"](http://unicode.org/reports/tr29/) . Para além disso, a análise de padrão converte todos os carateres para o seu formato minúsculas. Documentos indexados e termos de pesquisa aceda através da análise durante a indexação e processamento de consultas.

Pesquisa Azure suporta vários idiomas. Cada idioma requer um analisador de texto não padrão que contas para as características de um determinado idioma. Pesquisa Azure oferece dois tipos de analisadores:

- 35 analisadores Lucene de segurança.
- 50 analisadores cópias ao proprietário idioma natural da Microsoft processamento tecnologia utilizada no Office e o Bing.

Alguns programadores poderão preferir a solução mais familiar, simple, abrir origem de Lucene. Lucene analisadores são mais rápidos, mas os analisadores Microsoft tem avançadas funcionalidades, como Lematização, word decompounding (em idiomas como alemão, dinamarquês, neerlandês, sueco, norueguês, estónio, concluir, húngaro, Eslovaco) e o reconhecimento de entidade (URLs, mensagens de correio eletrónico, datas, números). Se possível, deve executar comparações de Microsoft tanto Lucene analisadores para decidir qual lhe é um melhor ajuste.

***Como comparar***

Análise de Lucene para o inglês expande a análise de padrão. Remove possessivos (do à direita) de palavras, aplica-se de que radicais de acordo com o [algoritmo de incluir radicais](http://tartarus.org/~martin/PorterStemmer/)e remove em inglês [Parar de palavras](http://en.wikipedia.org/wiki/Stop_words).

Em comparação, o Microsoft analyzer executa Lematização em vez de radicais. Significa que pode processar formulários word inflexão e irregular imenso quais os resultados nos resultados de pesquisa mais relevantes (monitorização módulo 7 da [apresentação do Azure pesquisa MVA](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) para obter mais detalhes).

A indexação com analisadores Microsoft é em média duas a três vezes mais lenta do que os respetivos equivalentes Lucene, consoante o idioma. Desempenho de pesquisa não deve afetado significativamente para consultas de tamanho médio.

***Configuração***

Para cada campo na definição do índice remissivo, pode definir a `analyzer` propriedade para um nome de análise que especifica que idioma e fornecedor. O mesmo analisador será aplicado quando indexação e procurar esse campo.
Por exemplo, pode ter campos separados inglês, francês e espanhol hotel para as descrições de que existe lado a lado no índice do mesmo. Utilize o [parâmetro da consulta 'searchFields'](#SearchQueryParameters) para especificar o campo específicas do idioma para procurar contra nas suas consultas. Pode rever os exemplos de consulta que incluem o `analyzer` propriedade na [Pesquisa de documentos](#SearchDocs). 

***Lista de análise***

Segue-se a lista de idiomas suportados juntamente com nomes de análise de Lucene e Microsoft.

<table style="font-size:12">
    <tr>
        <th>Idioma</th>
        <th>Nome de análise da Microsoft</th>
        <th>Nome de análise de Lucene</th>
    </tr>
    <tr>
        <td>Árabe</td>
        <td>ar.Microsoft</td>
        <td>ar.lucene</td>      
    </tr>
    <tr>
        <td>Arménio</td>
        <td></td>
        <td>HY.lucene</td>
    </tr>
    <tr>
        <td>Bangla</td>
        <td>Bn.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Basco</td>
        <td></td>
        <td>eu.lucene</td>
    </tr>
    <tr>
        <td>Búlgaro</td>
        <td>BG.Microsoft</td>
        <td>BG.lucene</td>
    </tr>
    <tr>
        <td>Catalão</td>
        <td>CA.Microsoft</td>
        <td>CA.lucene</td>          
    </tr>
    <tr>
        <td>Chinês simplificado</td>
        <td>zh Hans.microsoft</td>
        <td>zh Hans.lucene</td>     
    </tr>
    <tr>
        <td>Chinês tradicional</td>
        <td>zh Hant.microsoft</td>
        <td>zh Hant.lucene</td>     
    <tr>
    <tr>
        <td>Croata</td>
        <td>hr.Microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>Checa</td>
        <td>CS.Microsoft</td>
        <td>CS.lucene</td>      
    </tr>    
    <tr>
        <td>Dinamarquês</td>
        <td>da.Microsoft</td>
        <td>da.lucene</td>      
    </tr>    
    <tr>
        <td>Neerlandês</td>
        <td>NL.Microsoft</td>
        <td>NL.lucene</td>  
    </tr>    
    <tr>
        <td>Inglês</td>        
        <td>en.Microsoft</td>
        <td>en.lucene</td>      
    </tr>
    <tr>
        <td>Estónio</td>
        <td>et.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Finlandês</td>
        <td>Fi.Microsoft</td>
        <td>Fi.lucene</td>      
    </tr>    
    <tr>
        <td>Francês</td>
        <td>FR.Microsoft</td>
        <td>FR.lucene</td>      
    </tr>
    <tr>
        <td>Galego</td>
        <td></td>
        <td>GL.lucene</td>      
    </tr>
    <tr>
        <td>Alemão</td>
        <td>de.Microsoft</td>
        <td>de.lucene</td>      
    </tr>
    <tr>
        <td>Grego</td>
        <td>EL.Microsoft</td>
        <td>EL.lucene</td>      
    </tr>
    <tr>
        <td>Guzarate</td>
        <td>Gu.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hebraico</td>
        <td>he.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hindi</td>
        <td>Hi.Microsoft</td>
        <td>Hi.lucene</td>      
    </tr>
    <tr>
        <td>Húngaro</td>      
        <td>HU.Microsoft</td>
        <td>HU.lucene</td>
    </tr>
    <tr>
        <td>Islandês</td>
        <td>is.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonésio (Bahasa)</td>
        <td>ID.Microsoft</td>
        <td>ID.lucene</td>      
    </tr>
    <tr>
        <td>Irlandês</td>
        <td></td>
        <td>GA.lucene</td>
    </tr>
    <tr>
        <td>Italiano</td>
        <td>IT.Microsoft</td>
        <td>IT.lucene</td>      
    </tr>
    <tr>
        <td>Japonês</td>
        <td>ja.Microsoft</td>
        <td>ja.lucene</td>
        
    </tr>
    <tr>
        <td>Canarim</td>
        <td>Ka.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Coreano</td>
        <td>ko.Microsoft</td>
        <td>ko.lucene</td>
    </tr>
    <tr>
        <td>Letão</td>        
        <td>LV.Microsoft</td>
        <td>LV.lucene</td>  
    </tr>
    <tr>
        <td>Lituano</td>
        <td>lt.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malayalam</td>
        <td>ml.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malaio (Latim)</td>
        <td>MS.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Marati</td>
        <td>MR.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Norueguês</td>
        <td>NB.Microsoft</td>
        <td>no.lucene</td>      
    </tr>
    <tr>
        <td>Persa</td>
        <td></td>
        <td>fa.lucene</td>      
    </tr>
    <tr>
        <td>Polaco</td>
        <td>PL.Microsoft</td>
        <td>PL.lucene</td>      
    </tr>
    <tr>
        <td>Português (Brasil)</td>
        <td>pt Br.microsoft</td>
        <td>pt Br.lucene</td>       
    </tr>
    <tr>
        <td>Português (Portugal)</td>
        <td>pt Pt.microsoft</td>        
        <td>pt Pt.lucene</td>
    </tr>
    <tr>
        <td>Punjabi</td>
        <td>Pa.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Romeno</td>
        <td>ro.Microsoft</td>
        <td>ro.lucene</td>
    </tr>
    <tr>
        <td>Russo</td>
        <td>RU.Microsoft</td>
        <td>RU.lucene</td>  
    </tr>
    <tr>
        <td>Sérvio (Cirílico)</td>
        <td>SR cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Sérvio (Latim)</td>
        <td>SR latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Eslovaco</td>
        <td>SK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Esloveno</td>
        <td>SL.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Espanhol</td>
        <td>es.Microsoft</td>
        <td>es.lucene</td>
    </tr>
    <tr>
        <td>Sueco</td>
        <td>SV.Microsoft</td>
        <td>SV.lucene</td>
    </tr>

    <tr>
        <td>Tamil</td>
        <td>ta.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Télego</td>
        <td>te.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Tailandês</td>
        <td>TH.Microsoft</td>
        <td>TH.lucene</td>
    </tr>
    <tr>
        <td>Turco</td>
        <td>TR.Microsoft</td>
        <td>TR.lucene</td>      
    </tr>
    <tr>
        <td>Ucraniano</td>
        <td>UK.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Urdu</td>
        <td>your.Microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Vietnamita</td>
        <td>VI.Microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">Além disso Azure pesquisa fornece configurações de análise de idioma desconhecido</td>
    <tr>
        <td>ASCII padrão de dobragem</td>
        <td>standardasciifolding.lucene</td>
        <td>
        <ul>
            <li>Segmentação de texto Unicode (atomizador padrão)</li>
            <li>Filtro de dobragem ASCII - converte carateres Unicode que não pertencem ao conjunto de caracteres ASCII pela primeira vez 127 respectivos equivalentes ASCII. Isto é útil para remover marcas diacríticas.</li>
        </ul>
        </td>
    </tr>
</table>

Todos os analisadores com nomes anotados com <i>lucene</i> são recorrendo a [Analisadores de idioma do Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Podem encontrar mais informações sobre ASCII dobrar filtro [aqui](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Suggesters**

A `suggester` define os campos num índice são utilizados para suportar a conclusão automática em pesquisas. Normalmente cadeias de pesquisa parcial são enviadas para a [API sugestões](#Suggestions) enquanto o utilizador escreve uma consulta de pesquisa e a API devolve um conjunto de expressões sugeridas. Um suggester que definem no índice remissivo determina quais os campos que são utilizados para criar os termos de pesquisa limitam. Consulte o artigo [Suggesters](#Suggesters) para obter detalhes de configuração.

**Perfis de pontuação**

A `scoringProfile` define pontuação comportamentos personalizados que permitem-lhe influenciar quais os itens são apresentados mais elevados nos resultados da pesquisa. Perfis de pontuação são constituídos por espessuras de campo e funções. Para utilizá-las, pode especificar um perfil por nome, na cadeia de consulta.

Uma predefinição de pontuação perfil opera nos bastidores para calcular uma pontuação de pesquisa para cada item de um conjunto de resultados. Pode utilizar interno, sem nome pontuação perfil. Em alternativa, definir `defaultScoringProfile` para utilizar um perfil personalizado como predefinição, chamado sempre que um perfil personalizado não for especificado na cadeia de consulta.

Consulte o artigo [adicionar perfis pontuação a um índice de pesquisa (API do resto de serviço de pesquisa Azure)](search-api-scoring-profiles-2015-02-28-preview.md) para obter detalhes.

**Opções de CORS**

Javascript do lado do cliente não é possível chamar qualquer APIs por predefinição, uma vez que o browser irá impedir que todos os pedidos de publicação em origem. Activar CORS (origem da publicação em partilha de recurso) através da definição de `corsOptions` atributo para permitir que a origem da publicação em consultas para o índice remissivo. Tenha em atenção essa consulta apenas APIs suporte CORS por motivos de segurança. As seguintes opções podem ser definidas para CORS:

- `allowedOrigins`(obrigatório): Esta é uma lista de origens diferentes que será concedido acesso ao seu índice. Isto significa que qualquer código Javascript servido essas origens diferentes terá autorização para consultar o seu índice (partindo do princípio de que fornece a chave de API correcta). Cada origem é normalmente do formulário `protocol://fully-qualified-domain-name:port` apesar da porta com frequência é omitida. Consulte [Este artigo](http://go.microsoft.com/fwlink/?LinkId=330822) para obter mais detalhes.
 - Se pretender permitir o acesso a todas as origens, incluir `*` como um único item na `allowedOrigins` matriz. Tenha em atenção que **prática para serviços de pesquisa de produção não é recomendada.** No entanto, poderá ser útil para desenvolvimento ou efeitos de depuração.
- `maxAgeInSeconds`(opcional): Browsers utilizam este valor para determinar a duração (em segundos) para as respostas a cache CORS prévia. Tem de ser um número inteiro não negativo. Maior este valor é, será um melhor desempenho, mas mais tempo irá demorar CORS alterações de política entre em vigor. Se não estiver definida, será utilizada uma duração predefinida de 5 minutos.

<a name="CreateUpdateIndexExample"></a>
**Exemplo de corpo do pedido**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Resposta**

Para um pedido de bem sucedido: "criado 201".

Por predefinição, o corpo da resposta irá conter JSON para a definição de índice que foi criada. Se o `Prefer` cabeçalho pedido está definido para `return=minimal`, o corpo da resposta estará vazio e o código de estado de sucesso serão "204 sem conteúdo" em vez de "criado 201". Esta situação for verdadeira, independentemente de se a mensagem ou colocar foi utilizada para criar o índice.

**Observações**

Atualmente, não existe suporte limitado para actualizações de esquema de índice remissivo. Atualizações de esquema que requerem a reindexação tal como alterar tipos de campo não são atualmente suportadas. Apesar de campos existentes não podem ser alterados ou eliminados, os novos campos podem ser adicionados a um índice existente em qualquer altura. Quando é adicionado um novo campo, todos os documentos existentes no índice terá automaticamente um valor nulo para esse campo. Será consumido sem espaço de armazenamento adicional até novos documentos são adicionados ao índice.

<a name="Suggesters"></a>
## <a name="suggesters"></a>Suggesters

A funcionalidade de sugestões do Azure pesquisa é uma funcionalidade de consulta limitam- ou de conclusão automática, fornecendo uma lista de potenciais termos de pesquisa em resposta ao entradas do tipo cadeia parcial introduzidos uma caixa de pesquisa. Provavelmente notou sugestões de consulta quando utilizar motores de busca web comercial: escrever ".NET" no Bing produz uma lista de termos para ".NET 4.5", ".NET Framework 3,5" e assim sucessivamente. Ao utilizar o serviço de pesquisa REST API, sugestões de execução de uma aplicação de pesquisa do Azure personalizada requer o seguinte:

- Ative as sugestões ao adicionar uma construção **suggester** no índice de dar o nome, o modo de procura e uma lista de campos para a qual as sugestões automáticas são chamada. Por exemplo, se especificar "Localidade" como um campo de origem, escrevendo uma cadeia de pesquisa parcial do "Mar" irá resultar em "Sintra", "Litoral" e "Seatac" (todos os três são nomes de cidades real) oferecidos até como as sugestões de consulta ao utilizador.

- Invocar sugestões por telefone [Sugestões API](#Suggestions) código da aplicação. Normalmente cadeias de pesquisa parcial são enviadas para o serviço enquanto o utilizador escreve uma consulta de pesquisa e esta API devolve um conjunto de expressões sugeridas.

Este artigo explica como configurar um **suggester**. Também devem consultar a [API sugestões](#Suggestions) para obter detalhes sobre como um suggester é utilizado.

**Utilização**

`Suggesters`são criados no índice e trabalho melhor quando utilizado para sugerir expressões em vez de termos se solte ou documentos específicos. Os campos de candidatos melhor são títulos, nomes e outras expressões relativamente curtas que podem identificar um item. Menos eficazes são campos repetitivos, como categorias e sinalizadores, ou campos longos como os campos descrições ou comentários.

Como parte da definição de índice, pode adicionar uma única suggester para o `suggesters` coleções de sites. Propriedades que definem um suggester incluem o seguinte:

- `name`: O nome do suggester. Utilizar o nome da suggester ao chamar o `suggest` API.
- `searchMode`: A estratégia utilizada para procurar expressões candidatos. O modo de apenas atualmente suportado é `analyzingInfixMatching`, que executa correspondência flexível das frases no início ou no meio de frases.
- `sourceFields`: Uma lista de um ou mais campos que estão a origem de conteúdo para obter sugestões. Apenas os campos do tipo de `Edm.String` e `Collection(Edm.String)` poderá estar das fontes para as sugestões. Apenas os campos que não têm um analisador de idioma personalizadas definir podem ser utilizados.

**Exemplo de suggester**

Um suggester faz parte do índice. Apenas uma suggester pode existir na `suggesters` coleções de sites na versão atual, juntamente com a coleção de campos e `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [AZURE.NOTE]  Se tiver utilizado a versão de pré-visualização público de pesquisa de Azure, `suggesters` substitui uma propriedade booleana mais antiga (`"suggestions": false`) que só é suportado prefixo sugestões cadeias breves (3-25 caracteres). Sua substituição `suggesters`, suporta infix correspondência que localiza termos correspondentes no início ou no meio de conteúdo do campo, com uma melhor tolerância para erros em cadeias de pesquisa. Começar com a versão ficará disponível, este é agora a execução das sugestões API apenas. O mais antigo `suggestions` propriedade que foi introduzida numa `api-version=2014-07-31-Preview` continuam a funcionar nessa versão, mas não é operacional no `2015-02-28` ou versões posteriores do Azure pesquisa.

<a name="UpdateIndex"></a>
## <a name="update-index"></a>Atualizar índice remissivo

Pode atualizar um índice existente dentro de procura de Azure utilizando um pedido de HTTP colocar. Podem incluir atualizações adicionar novos campos para o esquema existente, modificar as opções de CORS e modificar os perfis de pontuação. Para mais informações, consulte [Adicionar pontuação perfis](https://msdn.microsoft.com/library/azure/dn798928.aspx) . Especifique o nome do índice para atualizar a pedido URI:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importantes:** Suporte para actualizações de esquema de índice remissivo está limitado a operações que não exigem a reformulação repetida de índice de pesquisa. Atualizações de esquema que requerem a reindexação, tal como alterar tipos de campo, atualmente não são suportadas. Novos campos podem ser adicionados em qualquer altura, apesar de campos existentes não podem ser alterados ou eliminados. O mesmo se aplica ao `suggesters`. Podem ser adicionados novos campos para uma suggester ao tempo são adicionados os campos, mas não podem ser removidos campos `suggesters` e campos existentes não podem ser adicionados a `suggesters`.

Ao adicionar um novo campo a um índice remissivo, todos os documentos existentes no índice terá automaticamente um valor nulo para esse campo. Será consumido sem espaço de armazenamento adicional até novos documentos são adicionados ao índice.

**Pedido**

É necessário HTTPS para todos os pedidos de serviço. O pedido de **Actualizar índice** é criado utilizando HTTP colocar. Com o local, o nome do índice faz parte do URL. Se o índice não existir, é criada. Se o índice já existir, é atualizado para a nova definição.

O nome do índice deve estar em minúsculas, começar com uma letra ou um número, ter sem barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome do índice com uma letra ou um número, o resto do nome pode incluir qualquer letra, o número e travessões, desde que não sejam consecutivos os traços.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `Content-Type`: Necessário. Defina esta opção para`application/json`
- `api-key`: Necessário. O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Actualizar índice** tem de incluir um `api-key` cabeçalho definido como a sua chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Sintaxe de corpo do pedido**

Ao atualizar um índice existente, o corpo tem de incluir a definição de esquema original, assim como novos campos que está a adicionar, bem como os perfis de pontuação modificados, suggesters CORS opções e, se existirem. Se não estiver a modificar os perfis de pontuação e as opções de CORS, tem de incluir os originais a partir do quando o índice criado. Em geral o melhor padrão para utilizar a existência de actualizações é obter a definição de índice com obter, modificá-lo, em seguida, atualize-lo com local.

A sintaxe de esquema utilizada para criar um índice remissivo é reproduzida aqui para maior conveniência. Para obter mais detalhes, consulte [Criar índice](#CreateIndex) .

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Resposta**

Para um pedido de bem sucedido: "204 sem conteúdo".

Por predefinição, o corpo da resposta estará vazio. No entanto, se o `Prefer` cabeçalho pedido está definido para `return=representation`, o corpo da resposta irá conter JSON para a definição de índice que foi atualizada. Neste caso, o código de estado de sucesso serão "200 OK".

**Atualizar a definição de índice com analisadores personalizados**

Assim que um analisador, um atomizador, um filtro token ou um filtro de CARÁCT estiver definido, não pode ser modificada. Novos podem ser adicionados a um índice existente apenas se a `allowIndexDowntime` sinalizador está definido como verdadeiro no pedido de atualização de índice remissivo: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Tenha em atenção que esta operação irá colocar o índice offline para pelo menos de aguardar alguns segundos, a causar a indexação e pedidos de consulta falha. Disponibilidade de desempenho e escrita do índice remissivo pode ser visuais durante vários minutos depois do índice é atualizado, ou mais demorada para índices muito grandes.

<a name="ListIndexes"></a>
## <a name="list-indexes"></a>Índices de lista

A operação de **Lista índices** devolve uma lista dos índices de atualmente no seu serviço de pesquisa do Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Pedido**

É necessário HTTPS para todos os pedidos de serviço. O pedido de **Índices de lista** pode ser construído utilizando o método de introdução.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `api-key`: Necessário. O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Índices lista** tem de incluir um `api-key` definido para uma chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Nenhum.

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

Eis um corpo de resposta de exemplo:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Tenha em atenção que pode filtrar a resposta para baixo até apenas as propriedades que lhe interessa. Por exemplo, se pretender apenas uma lista de nomes de índice remissivo, utilize OData `$select` opção de consulta:

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Neste caso, a resposta do exemplo acima apareceriam da seguinte forma:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Esta é uma técnica útil para guardar a largura de banda se tiver muitas índices no seu serviço de pesquisa.

<a name="GetIndex"></a>
## <a name="get-index"></a>Obter o índice remissivo

A operação de **Índice remissivo obter** obtém a definição de índice a partir da pesquisa do Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Pedido**

É necessário HTTPS para pedidos de serviço. O pedido de **Índice remissivo obter** pode ser construído utilizando o método de introdução.

O [nome do índice] no pedido de URI Especifica que índice para devolver da coleção de índices.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `api-key`: O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Índice remissivo obter** tem de incluir um `api-key` definido para uma chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Nenhum.

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

Veja o exemplo JSON em [criação e atualizar um índice remissivo](#CreateUpdateIndexExample) para um exemplo de carga de útil a resposta.

<a name="DeleteIndex"></a>
## <a name="delete-index"></a>Eliminar o índice

A operação de **Índice remissivo eliminar** remove um índice remissivo e os documentos associados a partir do seu serviço de pesquisa do Azure. Pode obter o nome de índice remissivo a partir do dashboard de serviço no portal do Azure ou a partir da API. Consulte o artigo [Índices de lista](#ListIndexes) para obter detalhes.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Pedido**

É necessário HTTPS para pedidos de serviço. O pedido de **Índice remissivo eliminar** pode ser construído utilizando o método DELETE.

O [nome do índice] no pedido de URI Especifica que índice para eliminar da coleção de índices.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `api-key`: Necessário. O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo para o seu URL do serviço. O pedido de **Índice remissivo eliminar** tem de incluir um `api-key` cabeçalho definido como a sua chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Nenhum.

**Resposta**

Código de estado: 204 sem conteúdo é devolvido por uma resposta bem sucedida.

<a name="GetIndexStats"></a>
## <a name="get-index-statistics"></a>Obter estatísticas de índice remissivo

Devolve a operação de **Obter as estatísticas de índice remissivo** a partir da pesquisa do Azure uma contagem de documento para o índice atual, bem como a utilização de armazenamento.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Estatísticas de contagem e armazenamento o tamanho do documento são recolhidas em poucos minutos, não em tempo real. Por conseguinte, as estatísticas de devolvido por esta API poderão não refletir as alterações causados pela recentes operações de indexação.

**Pedido**

É necessário HTTPS para todos os pedidos de serviços. O pedido de **Obter as estatísticas de índice remissivo** pode ser construído utilizando o método de introdução.

O [nome do índice] no pedido de URI indica o serviço para devolver estatísticas de índice remissivo para o índice especificado.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.


**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `api-key`: O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Obter as estatísticas de índice remissivo** tem de incluir um `api-key` definido para uma chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Nenhum.

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

É o corpo da resposta no seguinte formato:

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>
## <a name="test-analyzer"></a>Análise de teste

**Analisar API** mostra como um analisador divide o texto em tokens.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Pedido**

É necessário HTTPS para todos os pedidos de serviços. O pedido de **Analisar API** pode ser construído utilizando o método de mensagem.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.


**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `api-key`: O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Analisar API** tem de incluir um `api-key` definido para uma chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do índice e o nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

ou

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

O `analyzer_name`, `tokenizer_name`, `token_filter_name` e `char_filter_name` tem de ser os nomes válidos de analisadores predefinidos ou personalizados, tokenizers, filtros tokens e filtros de CARÁCT para o índice. Para saber mais sobre o processo de análise lexical consulte [análise na pesquisa Azure](https://aka.ms/azsanalysis).

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

É o corpo da resposta no seguinte formato:

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analisar exemplo API**

**Pedido**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Resposta**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

________________________________________
<a name="DocOps"></a>
## <a name="document-operations"></a>Operações de documento

Na pesquisa Azure, um índice remissivo é armazenado na nuvem e povoada com JSON documentos que carregar para o serviço. Todos os documentos que carregar compõem o corpo dos seus dados de pesquisa. Os documentos contêm campos, algumas das quais são com token para termos de pesquisa à medida que são carregados. O `/docs` segmento de URL na API de pesquisa do Azure representa a coleção de documentos num índice remissivo. Todas as operações realizadas na coleção de como carregar, intercalar, eliminar ou consultar documentos assumir colocar no contexto de um único índice remissivo, por isso, os URLs para estas operações iniciará sempre com `/indexes/[index name]/docs` um nome de índice remissivo determinado.

Código da aplicação deverá quer gerar documentos JSON para carregar para o Azure pesquisa ou pode utilizar um [indexador](https://msdn.microsoft.com/library/dn946891.aspx) para carregar documentos, se a origem de dados for base de dados do SQL Azure ou DocumentDB. Normalmente, os índices são preenchidos a partir de um único conjunto de dados que indicar.

Deve planeia ter um documento para cada item que pretende procurar. Uma aplicação de aluguer filme poderá ter um documento por filme, uma aplicação da loja poderá ter um documento por SKU, uma aplicação de software didáctico online poderá ter um documento por curso, poderá tem um documento para cada papel académico no respetivo repositório de uma empresa de pesquisa e assim sucessivamente.

Documentos são compostos por um ou mais campos. Campos podem conter texto que é com token pela pesquisa Azure para termos de pesquisa, bem como com não token ou valores de não sejam de texto que podem ser utilizados em perfis de pontuação ou filtros. Os nomes, tipos de dados e funcionalidades de pesquisa são suportadas para cada campo são determinadas pelo esquema de índice remissivo. Um dos campos no cada esquema de índice remissivo deve ser designado como um ID e cada documento tem de ter um valor para o campo ID exclusivo que identifica esse documento no índice remissivo. Todos os outros campos do documento são opcionais e valor predefinido de um valor nulo se deixado em branco. Tenha em atenção que valores nulos não ocupar espaço no índice de pesquisa.

Antes de que possa carregar documentos, tem de ter já criado o índice no serviço. Consulte o artigo [Criar índice](#CreateIndex) para obter detalhes sobre neste primeiro passo.

<a name="AddOrUpdateDocuments"></a>
## <a name="add-update-or-delete-documents"></a>Adicionar, atualizar ou eliminar documentos

Pode carregar, documentos de impressão em série, impressão em série ou carregar ou eliminar a partir de um índice especificado utilizando HTTP POST. Para grandes quantidades de atualizações, processamento de documentos até (documentos de 1000 por lote) ou cerca de 16 MB por lote em lotes é recomendado.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Pedido**

É necessário HTTPS para todos os pedidos de serviço. Pode carregar, documentos de impressão em série, impressão em série ou carregar ou eliminar a partir de um índice especificado utilizando HTTP POST.

O pedido URI inclui [nome do índice remissivo], especificar qual índice para publicar documentos. Apenas pode publicar documentos para um índice ao mesmo tempo.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `Content-Type`: Necessário. Defina esta opção para`application/json`
- `api-key`: Necessário. O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo no seu serviço. O pedido de **Adicionar documentos** tem de incluir um `api-key` cabeçalho definido como a sua chave de administrador (por oposição a uma chave de consulta).

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

O corpo do pedido de contém um ou mais documentos para ser indexadas. Documentos são identificados por uma chave exclusiva. Cada documento está associado a uma ação: carregar, impressão em série, mergeOrUpload ou eliminar. Pedidos de carregamento devem incluir os dados de documento como um conjunto de pares valor/chave.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Teclas de documento só podem conter letras, números, travessões ("-"), sublinhado ("_") e sinais de igual ("="). Para obter mais detalhes, consulte o artigo [regras de atribuição de nomes](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Acções do documento**

- `upload`: Uma ação de carregamento é semelhante a "upsert" onde o documento será inserido se for novas e atualizadas/substituído se existir. Tenha em atenção que todos os campos são substituídos no caso de atualização.
- `merge`: Impressão em série atualiza um documento existente com os campos especificados. Se o documento não existir, irá falhar a impressão em série. Qualquer campo que especificar numa intercalação de irá substituir o campo existente no documento. Isto inclui campos tipo `Collection(Edm.String)`. Por exemplo, se o documento contém um campo "etiquetas" com valor `["budget"]` e executar uma impressão em série com valor `["economy", "pool"]` para "códigos", o valor final do campo "etiquetas" será `["economy", "pool"]`. É provável **não** ser `["budget", "economy", "pool"]`.
- `mergeOrUpload`: comporta-se como `merge` se já existe um documento com a tecla determinado no índice remissivo. Se o documento não existir-comporta-se como `upload` com um novo documento.
- `delete`: Eliminar remove o documento especificado do índice. Tenha em atenção que quaisquer campos que especificou num `delete` operação que não seja o campo de chave será ignorada. Se pretende remover um campo individual de um documento, utilize `merge` em vez disso e basta definir o campo explicitamente para `null`.

**Resposta**

Código de estado 200 (OK) é devolvido por uma resposta bem sucedida, que significa que todos os itens foram indexados com êxito. Isto é indicado pelo `status` a ser propriedade definido como verdadeiro para todos os itens, bem como a `statusCode` propriedade a ser definida para 201 (para documentos carregados recentemente) ou 200 (para documentos intercalados ou eliminados):

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

Código de estado 207 (Estado com várias) é devolvido quando não foi indexado com êxito, pelo menos, um item. Itens que não foram indexadas tem o `status` campo definido como falso. O `errorMessage` e `statusCode` propriedades irão indicar a razão para o erro de indexação:

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

A seguinte tabela explica os códigos de estado por documento vários que podem ser devolvidos na resposta. Tenha em atenção que indicam alguns problemas com o pedido propriamente dito, enquanto outras pessoas indicam condições de erro temporário. O último que deve ser repetida após um atraso.

<table style="font-size:12">
    <tr>
        <th>Código de estado</th>
        <th>Significado</th>
        <th>Repetição</th>
        <th>Notas</th>
    </tr>
    <tr>
        <td>200</td>
        <td>Documento com êxito foi modificado ou eliminado.</td>
        <td>n/d</td>
        <td>Operações de eliminação são <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>. Isto é, mesmo se não existir uma chave de documento no índice remissivo, tentativa uma operação de eliminação com essa chave resultará um código de 200 estado.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>Documento foi criado com êxito.</td>
        <td>n/d</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Ocorreu um erro no documento que impedido de que está a ser indexadas.</td>
        <td>N</td>
        <td>A mensagem de erro na resposta irá indicar qual é o problema com o documento.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>O documento não foi possível intercalar porque não existe a chave indicada no índice remissivo.</td>
        <td>N</td>
        <td>Este erro não ocorrer para carregamentos desde que criar novos documentos, e não ocorrer para elimina uma vez que são <a href="https://en.wikipedia.org/wiki/Idempotence">idempotent</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>Foi detetado um conflito de versão ao tentar indexar um documento.</td>
        <td>Sim</td>
        <td>Isto pode acontecer quando indexar mais do que uma vez em simultâneo no mesmo documento que está a tentar.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>O índice está temporariamente indisponível porque foram atualizados com o conjunto de sinalizador 'allowIndexDowntime' para 'true' vez.</td>
        <td>Sim</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>Serviço de pesquisa está temporariamente indisponível, possivelmente devido a carga elevada.</td>
        <td>Sim</td>
        <td>O código deverá aguardar antes de repetir neste caso, ou corre o risco de prolongar a indisponibilidade de serviço.</td>
    </tr>
</table> 

**Nota**: se o seu código do cliente frequentemente encontrar uma resposta 207, uma das razões possíveis é que o sistema está em caso de carga. Pode confirmar isto, verificando o `statusCode` propriedade de 503. Se este for o caso, recomendamos que ***limitação pedidos de indexação***. Caso contrário, se a indexação tráfego não subside, o sistema conseguiu iniciar rejeitar todos os pedidos com 503 erros.

Código de estado 429 indica que tiver excedido a quota no número de documentos por índice remissivo. Tem de criar um novo índice ou atualizar para limites de capacidade superiores.

**Exemplo:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## <a name="search-documents"></a>Procurar documentos

Uma operação de **pesquisa** é emitida como um pedido GET ou POST e especifica parâmetros que fornecem os critérios para selecionar documentos correspondentes.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando utilizar a mensagem em vez de obter**

Quando utiliza HTTP GET para ligar a **pesquisa** API, tem de ter em mente que o comprimento do URL pedido não pode exceder 8 KB. Este nome costuma suficiente para a maioria das aplicações. No entanto, algumas aplicações produzem consultas muito grandes ou expressões de filtro de OData. Para estas aplicações, utilizar HTTP POST é uma boa opção porque permite maior filtros e consultas que obter. Com a mensagem, o número de termos ou cláusulas numa consulta é o factor de limitação, não o tamanho da consulta não processado uma vez que o limite de tamanho pedido para mensagem é aproximadamente 16 MB.

> [AZURE.NOTE] Apesar do limite de tamanho de pedido de mensagem for muito grande, consultas de pesquisa e expressões de filtro não podem ser arbitrariamente complexas. Consulte o artigo [Lucene sintaxe de consulta](https://msdn.microsoft.com/library/mt589323.aspx) e [sintaxe das expressões OData](https://msdn.microsoft.com/library/dn798921.aspx) para obter mais informações sobre as limitações de complexidade de consulta e de filtro de pesquisa.

**Pedido**

É necessário HTTPS para pedidos de serviço. O pedido de **pesquisa** pode ser construído utilizando os métodos GET ou mensagem.

O pedido URI Especifica que índice a consulta, para todos os documentos que correspondem aos parâmetros de. Parâmetros forem especificados na cadeia de consulta, no caso de pedidos de obtenção de e, no pedido de pedidos de corpo no caso de mensagem.

Como prática recomendada quando criar os pedidos GET, lembre-se aos parâmetros de consulta específica de [codificação de URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) ao chamar REST API diretamente. Para operações de **pesquisa** , isto inclui:

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

Codificação de URL só é recomendado os parâmetros de consulta acima. Se que codificar inadvertidamente URL a cadeia de consulta inteira (tudo após o?), irão interromper pedidos.

Além disso, a codificação de URL só é necessário ao chamar a API REST directamente utilizando obter. Sem codificação de URL é necessário durante a chamada de **pesquisa** com a mensagem ou ao utilizar a [biblioteca do cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), que processam a codificação de URL para si.

<a name="SearchQueryParameters"></a>
**Parâmetros de consulta**

**Pesquisa** aceita vários parâmetros que fornecem critérios de consulta e também especificam comportamento de procura. Forneça a que cadeia de consulta estes parâmetros no URL, ao chamar **pesquisa** através de obter e como propriedades JSON no corpo do pedido ao chamar **pesquisa** através da mensagem. A sintaxe para alguns parâmetros é ligeiramente diferente entre GET e POST. Estas diferenças estão anotadas conforme aplicável abaixo:

`search=[string]`(opcional) - o texto a procurar. Todos os `searchable` campos são procurados por predefinição, a menos que `searchFields` for especificado. Quando a procurar `searchable` campos, o próprio texto de pesquisa é com token, para múltiplos termos podem ser separados por espaço em branco (por exemplo: `search=hello world`). Para fazer corresponder qualquer termo, utilize `*` (Isto pode ser útil para consultas de filtro booleano). Omissão este parâmetro tem o mesmo efeito como defini-la para `*`. Consulte o artigo [Simples sintaxe de consulta](https://msdn.microsoft.com/library/dn798920.aspx) para detalhes sobre a sintaxe da pesquisa.

  - **Nota**: os resultados por vezes, podem ser surpreendente quando consultar sobre `searchable` campos. O atomizador inclui uma lógica para processar casos comuns em inglês texto como apóstrofos, vírgulas em números, etc. Por exemplo, `search=123,456` irá corresponder a um único termo 123,456 em vez dos termos individuais 123 e 456, uma vez que vírgulas são utilizadas como separadores de mil para números grandes em inglês. Por esta razão, recomendamos que utilize o espaço em branco em vez de pontuação para separar os termos no `search` parâmetro.

`searchMode=any|all`(opcional, assume a predefinição de `any`) - se algumas ou todas os termos de pesquisa tem deverá ser correspondidas para contar o documento como uma correspondência.

`searchFields=[string]`(opcional) - a lista de nomes de campo separados por vírgulas para procurar o texto especificado. Campos de destino tem de ser marcados como `searchable`.

`queryType=simple|full`(opcional, assume a predefinição de `simple`) - quando definida como o texto de pesquisa "simples" é interpretada a utilizar um idioma de consulta simples que lhe permite para símbolos, tal como +, * e "". Consultas são avaliadas em todos os domínios pesquisáveis (ou campos indicado na `searchFields`) em cada documento por predefinição. Quando o tipo de consulta está definido como `full` texto de pesquisa é interpretado utilizando o idioma de consulta Lucene que lhe permite específicas do campo e ponderadas pesquisas. Consulte o artigo [Sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx) e [Sintaxe da consulta Lucene](https://msdn.microsoft.com/library/mt589323.aspx) para detalhes sobre as sintaxes de pesquisa. 
 
> [AZURE.NOTE] Intervalo de pesquisa no Lucene linguagem de consulta não é suportada favor $filter que disponibiliza uma funcionalidade semelhante.

`moreLikeThis=[key]`(opcional) **Importantes:** Esta funcionalidade só está disponível no `2015-02-28-Preview`. Esta opção não pode ser utilizada numa consulta que contém o parâmetro de pesquisa de texto, `search=[string]`. O `moreLikeThis` parâmetro localiza documentos que são semelhantes ao documento especificado pela chave de documento. Quando um pedido de pesquisa é tornado com `moreLikeThis`, uma lista de termos de pesquisa é gerada com base na frequência e a raridade de termos no documento de origem. Os termos, em seguida, são utilizados para fazer o pedido. Por predefinição, os conteúdos de todas `searchable` campos são considerados, a menos que `searchFields` é utilizado para restringir os campos que são procurados.  

`$skip=#`(opcional) - o número de resultados de pesquisa para ignorar; Não pode ser maior do que 100,000. Se necessita de digitalizar documentos numa sequência, mas não é possível utilizar `$skip` devido a esta limitação, considere utilizar `$orderby` numa chave totalmente encomendou e `$filter` com um intervalo de consulta em vez disso.

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `skip` em vez de `$skip`.

`$top=#`(opcional) - o número de resultados de pesquisa para recuperar. Isto pode ser utilizado em conjunto com `$skip` para implementar a paginação do lado do cliente dos resultados da pesquisa.

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `top` em vez de `$top`.

`$count=true|false`(opcional, assume a predefinição de `false`)-Especifica se pretende obter a contagem total dos resultados. Esta é a contagem de todos os documentos que correspondem a `search` e `$filter` parâmetros, ignorando `$top` e `$skip`. A definição deste valor `true` poderá ter um impacto no desempenho. Tenha em atenção que a contagem de devolvido é uma aproximação.

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `count` em vez de `$count`.

`$orderby=[string]`(opcional) - uma lista de expressões separados por vírgulas para ordenar os resultados por. Cada expressão pode ser um nome de campo ou uma chamada para o `geo.distance()` função. Cada expressão pode ser seguido por `asc` para indicado por ordem ascendente, e `desc` para indicar descendente. A predefinição é ordem ascendente. TIES vai ser divididos pelas pontuações de correspondência de documentos. Se não `$orderby` especificados, a sequência de ordenação predefinida é descendente por pontuação de correspondência de documento. Existe um limite de 32 cláusulas para `$orderby`.

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `orderby` em vez de `$orderby`.

`$select=[string]`(opcional) - uma lista de campos separados por vírgulas para obter. Se não for especificado, todos os campos marcados como recuperáveis no esquema de são incluídos. Pode também explicitamente pedir todos os campos ao definir este parâmetro `*`.

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `select` em vez de `$select`.

`facet=[string]`(igual a zero ou mais) - um campo para faceta por. Opcionalmente, a cadeia pode conter parâmetros para personalizar faceting expresso como separados por vírgulas `name:value` pares. Parâmetros válidos são:

- `count`(max número das cláusulas faceta; valor predefinido é 10). Não existe nenhuma máximo, mas os valores mais elevados implicam uma sanções desempenho correspondente, sobretudo se o campo por facetas contém um grande número de termos exclusivos.
  - Por exemplo: `facet=category,count:5` recebe as categorias de cinco principais nos resultados de faceta.  
  - **Nota**: se o `count` parâmetro é menor que o número de termos exclusivos, os resultados podem não ser exatas. Este é devido a forma como as consultas de faceting estão distribuídas shards. Aumentar `count` geralmente aumenta a precisão das contagens de termo, mas um custo de desempenho.
- `sort`(uma das `count` para ordenar *descendente* por contagem, `-count` para ordenar *por ordem ascendente* por contagem, `value` para ordenar *por ordem ascendente* por valor, ou `-value` para ordenar *descendente* pelo valor)
  - Por exemplo: `facet=category,count:3,sort:count` recebe as categorias de primeiros três nos resultados de faceta por ordem descendente pelo número de documentos com cada nome de localidade. Por exemplo, se a três categorias principais são orçamento, companhia e luxo e orçamento tem 5 acertos, companhia tem 6, e luxo tem 4, em seguida, os registos será pela ordem companhia, o orçamento, luxo.
  - Por exemplo: `facet=rating,sort:-value` produz grupos para todas as classificações possíveis, por ordem descendente por valor. Por exemplo, se são as classificações de 1 a 5, os registos serão ser ordenados, 5, 4, 3, 2, 1, independentemente quantos documentos correspondem cada classificação.
- `values`(delimitado por pipe numérico ou `Edm.DateTimeOffset` valores especificação de um conjunto de valores de entrada faceta dinâmico)
  - Por exemplo: `facet=baseRate,values:10|20` produz os três registos: uma para taxa base 0 até ao mas não incluindo até 10, uma para 10, mas não incluindo 20 e outra para 20 ou superior.
  - Por exemplo: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` produz dois registos: uma para alojamento renovated antes de Fevereiro de 2010 e para alojamento renovated Fevereiro 1. º, 2010 ou posterior.
- `interval`(número inteiro intervalo de tempo superior a 0 para números, ou `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` para valores de data hora)
  - Por exemplo: `facet=baseRate,interval:100` produz registos com base em intervalos de taxa de base de tamanho 100. Por exemplo, se forem taxas base todas as entre $60 e $600, vai ser grupos para 0-100, 100-200, 200 300, 300 400, 400-500 e 500 600.
  - Por exemplo: `facet=lastRenovationDate,interval:year` produz balde um para cada ano quando foram renovated alojamento.
- `timeoffset`([+-] hh: mm, [+-] hhmm, ou [+-] hh) `timeoffset` é opcional. Só pode ser combinado com o `interval` opção e apenas quando aplicado a um campo do tipo de `Edm.DateTimeOffset`. O valor Especifica o desvio de tempo UTC para ter em conta na definição de limites de tempo.
  - Por exemplo: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` utiliza o limite do dia que começa na UTC 01:00:00 (meia-noite no fuso horário do destino)
- **Nota**: `count` e `sort` podem ser combinados a mesma especificação de Faceta, mas não pode ser combinados com `interval` ou `values`, e `interval` e `values` não pode ser combinado em conjunto.
- **Nota**: facetas intervalo em data hora são calculadas com base na hora UTC se `timeoffset` não for especificado. Por exemplo: para `facet=lastRenovationDate,interval:day`, o dia limite começa em 00:00:00 UTC. 

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `facets` em vez de `facet`. Além disso, especifique-la como uma matriz JSON de cadeias onde cada cadeia é uma expressão de faceta em separado.

`$filter=[string]`(opcional) - uma expressão de pesquisa estruturadas sintaxe OData padrão. Consulte o artigo [Da sintaxe das expressões OData](#ODataExpressionSyntax) para obter detalhes sobre o subconjunto da OData expressão a gramática que suporte de pesquisa do Azure.

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `filter` em vez de `$filter`.

`highlight=[string]`(opcional) - realça um conjunto de nomes de campos separados por ponto e vírgula utilizado para acertos. Apenas `searchable` campos podem ser utilizados para realçar visitas.

`highlightPreTag=[string]`(opcional, assume a predefinição de `<em>`) - A etiqueta que anexa para acertar realces de cadeia. Tem de ser definido com `highlightPostTag`.

> [AZURE.NOTE] Quando **pesquisa** utilizando GET, caracteres reservados no URL que entra em contacto deve ser percentagem codificada (por exemplo, % 23 em vez de #).

`highlightPostTag=[string]`(opcional, assume a predefinição de `</em>`)-uma etiqueta de cadeia que acrescenta para acertar realces. Tem de ser definido com `highlightPreTag`.

> [AZURE.NOTE] Quando **pesquisa** utilizando GET, caracteres reservados no URL que entra em contacto deve ser percentagem codificada (por exemplo, % 23 em vez de #).

`scoringProfile=[string]`(opcional) - o nome de um perfil de pontuação avaliar corresponder pontuações correspondentes documentos para ordenar os resultados.

`scoringParameter=[string]`(igual a zero ou mais) - indicam os valores para cada parâmetro definido numa função pontuação (por exemplo, `referencePointParameter`) utilizando o formato `name-value1,value2,...`.

- Por exemplo, se o perfil pontuação define uma função com um parâmetro chamado "mylocation" a opção de cadeia de consulta seria `&scoringParameter=mylocation--122.2,44.8`. O travessão primeiro separa o nome a partir da lista de valor, enquanto o segundo travessão faz parte do primeiro valor (longitude neste exemplo).
- Para os parâmetros de pontuação, tal como etiqueta aumentar que pode conter vírgulas, pode escape qualquer esses valores na lista utilizando plicas. Se os próprios valores contiverem aspas simples, pode escape-los por duplicar.
  - Por exemplo, se tiver uma etiqueta aumentar parâmetro chamado "mytag" e que pretende impulsionar a etiqueta valores "Olá, O'Brien" e "Silva", a consulta opção cadeia seria `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Tenha em atenção que mostra as propostas só são necessários para os valores que contêm vírgulas.

> [AZURE.NOTE] Durante a chamada de **pesquisa** com a mensagem, este parâmetro chama-se `scoringParameters` em vez de `scoringParameter`. Além disso, especificá-la como uma matriz JSON de cadeias onde cada cadeia está separado `name-values` par.

`minimumCoverage`(opcional, a predefinição é 100)-um número entre 0 e 100 que indica a percentagem do índice que deve ser coberta por uma consulta de pesquisa para que a consulta a ser comunicado como um sucesso. Por predefinição, todo o índice tem de estar disponível ou `Search` irá devolver o código de estado HTTP 503. Se definir `minimumCoverage` e `Search` ser bem sucedida, será devolvido HTTP 200 e incluir uma `@search.coverage` valor na resposta que indica a percentagem do índice que foi incluída na consulta.

> [AZURE.NOTE] Definir este parâmetro para um valor inferior a 100 pode ser úteis para assegurar a disponibilidade de pesquisa mesmo para serviços com apenas uma réplica. No entanto, não em todos os documentos correspondentes são garante estar presentes nos resultados da pesquisa. Se procura resgate é mais importante para a sua aplicação disponibilidade, em seguida, é melhor deixar `minimumCoverage` pelo seu valor predefinido de 100.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Nota: para esta operação, o `api-version` especificado como um parâmetro de consulta no URL, independentemente de se ligar para **Procurar** com GET ou mensagem.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `api-key`: O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo para o seu URL do serviço. O pedido de **pesquisa** pode especificar uma chave de administrador ou chave de consulta para `api-key`.

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Para obter: nenhuma.

Para registar:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Continuação de notas de pesquisa parcial respostas**

Por vezes Azure pesquisa não pode devolver os resultados pedidos numa única resposta de pesquisa. Isto pode acontecer por diversas razões, tal como quando a pedidos de consulta demasiados documentos ao especificar não `$top` ou especificar um valor para `$top` que é demasiado grande. Nestes casos, Azure pesquisa irá incluir o `@odata.nextLink` anotação no corpo da resposta e também `@search.nextPageParameters` se fosse um pedido de mensagem. Pode utilizar os valores destes anotações precisar outro pedido de pesquisa para obter a parte da resposta à procura seguinte. Esta opção é denominada ***continuação de notas*** do pedido de pesquisa original e as anotações são geralmente denominadas ***tokens de continuação de notas***. Veja [o exemplo abaixo](#SearchResponse) para obter detalhes sobre a sintaxe destes anotações e onde são apresentados no corpo da resposta. 

As razões porque é que o Azure pesquisa poderá devolver tokens de continuação de notas são específico de implementação e sujeitos a alteração. Clientes robustos devem estar sempre prontos para processar casos em que os documentos menos que o esperado são devolvidos e um token de continuação de notas está incluído para continuar a obter documentos. Tenha em atenção que tem de utilizar o método de HTTP mesmo como o pedido original para poder continuar. Por exemplo, se tiver enviado num pedido GET, quaisquer pedidos de continuação de notas que envia tem também utilizar GET (e da mesma forma para a mensagem).

<a name="SearchResponse"></a>
**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Exemplos:**

Pode encontrar mais exemplos na página [Da sintaxe das expressões OData para a pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx) .

1)  O índice de pesquisa ordenado de forma descendente por data.


    OBTER /indexes/hotels/docs? pesquisa = * & $orderby = lastRenovationDate desc & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "*", "OrdenarPor": "lastRenovationDate desc"}

2)  Numa pesquisa por facetas, procure no índice remissivo e obter facetas para categorias, classificação, etiquetas, bem como itens com baseRate em intervalos específicos:


    OBTER /indexes/hotels/docs? pesquisa = teste & faceta = categoria & faceta = classificação & faceta = etiquetas e faceta = baseRate, valores: 80 | 150 | 220 & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "Testar", "facetas": ["categoria", "classificação", "códigos", "baseRate, valores: 80 | 150 | 220"]}

3)  Utilizar um filtro, limitar os resultados da consulta por facetas anterior depois do utilizador clica no classificação 3 e categoria "Companhia":


    OBTER /indexes/hotels/docs? pesquisa = teste & faceta = etiquetas e faceta = baseRate, valores: 80 | 150 | 220 & $filter = classificação eq 3 e categoria eq 'companhia' e versão da api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015-02-28-pré-visualização do {"search": "Testar", "facetas": ["códigos", "baseRate, valores: 80 | 150 | 220"], "filtro": "classificação eq 3 e categoria eq 'Companhia'"}

4) Numa pesquisa por facetas, defina um limite superior termos exclusivos devolvidos numa consulta. A predefinição é 10, mas pode aumentar ou diminuir a utilizar este valor a `count` parâmetro na `facet` atributo:


    OBTER /indexes/hotels/docs? pesquisa = teste & faceta = cidade, contar: 5 & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "Testar", "facetas": ["Cidade, contar: 5"]}

5)  Procure no índice remissivo dentro de campos específicos; Por exemplo, um campo específicas do idioma:


    OBTER /indexes/hotels/docs? pesquisa = hôtel & searchFields = description_fr & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "hôtel", "searchFields": "description_fr"}

6) Procure no índice remissivo através de vários campos. Por exemplo, pode armazenar e campos pesquisáveis em vários idiomas, tudo dentro do mesmo índice de consulta.  Se as descrições de inglês e francês coexistência no mesmo documento, poderá regressar algumas ou todas nos resultados da consulta:


    OBTER /indexes/hotels/docs? pesquisa = hotel & searchFields = descrição, description_fr & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "hotel", "searchFields": "descrição, description_fr"}

Tenha em atenção que só pode consultar um índice ao mesmo tempo. Não crie índices múltiplos para cada idioma, a menos que pretenda consultar uma de cada vez.

7)  Paginação - obter a página 1. º de itens (o tamanho da página é 10):


    OBTER /indexes/hotels/docs? pesquisa = * & $skip = 0 & $top = 10 & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "*", "Ignorar": 0, "superior": 10}

8)  Paginação - obter a página 2. de itens (o tamanho da página é 10):


    OBTER /indexes/hotels/docs? pesquisa = * & $skip = 10 & $top = 10 & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "*", "Ignorar": 10, "superior": 10}

9)  Obter um conjunto específico de campos:


    OBTER /indexes/hotels/docs? pesquisa = * & $select = hotelName, descrição e versão da api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "*", "selecione": "hotelName, descrição"}

10)  Obter documentos correspondência de uma expressão de filtro específico:


    OBTER /indexes/hotels/docs? $filter = (baseRate página 60 e baseRate lt 300) ou eq hotelName 'Manter-se sofisticado' e versão da api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015-02-28-pré-visualização do {"filtro": "(baseRate página 60 e baseRate lt 300) ou hotelName eq mantenha sofisticado-se"}

11) Procurar a fragmentos índice e regressar com realces visitas


    OBTER /indexes/hotels/docs? pesquisa = algo & Realçar = descrição & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "algo", "Realçar": "Descrição"}

12) Procure no índice remissivo e devolver documentos ordenados da mais perto mais longe uma referência de localização


    OBTER /indexes/hotels/docs? pesquisa = algo & $orderby=geo.distance (localização, geography'POINT(-122.12315 47.88121)') & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "algo", "OrdenarPor": "geo.distance (localização, geography'POINT(-122.12315 47.88121)')"}

13) Procurar o índice partindo do princípio de que existe um perfil de pontuação denominado "geo" com duas funções de pontuação distância, um que define um parâmetro denominado "currentLocation" e um que define um parâmetro chamado "lastLocation"


    OBTER /indexes/hotels/docs? pesquisa = algo & scoringProfile = geo & scoringParameter = currentLocation – 122.123,44.77233 & scoringParameter = lastLocation – 121.499,44.2113 & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "algo", "scoringProfile": "geo", "scoringParameters": ["currentLocation – 122.123,44.77233", "lastLocation – 121.499,44.2113"]}

14) Localize documentos no índice que utilizam [sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx). Esta consulta devolve alojamento onde campos pesquisáveis contenham os termos "segurança" e "localização" mas não "companhia":


    OBTER /indexes/hotels/docs? pesquisa = segurança + localização-companhia & searchMode = all & versão api = 2015-02-28-pré-visualização

    MENSAGEM /indexes/hotels/docs/search? versão api = 2015 02-28 pré-visualização {"search": "segurança + localização-companhia", "searchMode": "todos"}

Tenha em atenção a utilização de `searchMode=all` acima. Incluindo este parâmetro substitui a predefinição da `searchMode=any`, garantir que `-motel` significa "E não" em vez de "Ou não". Sem `searchMode=all`, obtém "Ou não" qual expande-se em vez de restringe os resultados da pesquisa e pode ser intuitivo para alguns utilizadores.

15) Localize documentos no índice que utilizam [sintaxe de consulta lucene](https://msdn.microsoft.com/library/mt589323.aspx). Esta consulta devolve alojamento onde o campo categoria contém o termo "além do orçamento" e todos os campos pesquisáveis que contém a expressão "recentemente renovated". Documentos que contém a expressão "recentemente renovated" são classificados superior estatístico como resultado do valor de intensidade do termo (3)

    OBTER /indexes/hotels/docs?search = categoria: orçamento e \"recentemente renovated\"^ 3 & searchMode = all & versão da api = 2015 02-28 pré-visualização & querytype = completo

    MENSAGEM /indexes/hotels/docs/search?api-version = 2015 02-28 pré-visualização {"search": "categoria: orçamento e \"recentemente renovated\"^ 3", "queryType": "completa", "searchMode": "todos"}

<a name="LookupAPI"></a>
## <a name="lookup-document"></a>Documento de pesquisa

A operação de **Pesquisa documento** obtém um documento a partir da pesquisa do Azure. Isto é útil quando um utilizador clica num resultado de pesquisa específicos e que pretende procurar detalhes específicos sobre esse documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Pedido**

É necessário HTTPS para pedidos de serviço. O pedido de **Pesquisa documento** pode ser construído do seguinte modo.

    GET /indexes/[index name]/docs/key?[query parameters]

Em alternativa, pode utilizar a sintaxe de OData tradicional para pesquisa de chave:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

O pedido URI inclui um [nome do índice remissivo] e [chave], especificar qual o documento para obter a partir do qual índice. Só pode obter um documento ao mesmo tempo. Utilize a **pesquisa** para obter vários documentos num único pedido.

**Parâmetros de consulta**

`$select=[string]`(opcional) - uma lista de campos separados por vírgulas para obter. Se não for especificado ou definido como `*`, todos os campos marcados como recuperáveis no esquema de estão incluídos na projecções.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Nota: para esta operação, o `api-version` especificado como um parâmetro de consulta.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `api-key`: O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo para o seu URL do serviço. O pedido de **Pesquisa documento** pode especificar uma chave de administrador ou chave de consulta para `api-key`.

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Nenhum.

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Exemplo**

Pesquisa o documento que tem a chave '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Pesquisa do documento que tenha chave '3' utilizando OData sintaxe:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## <a name="count-documents"></a>Contagem de documentos

A operação de **Contagem de documentos** obtém uma contagem do número de documentos no índice de pesquisa. O `$count` sintaxe faz parte do protocolo de OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Pedido**

É necessário HTTPS para pedidos de serviço. O pedido de **Contagem de documentos** pode ser construído utilizando o método de introdução.

O [nome do índice] no pedido de URI indica o serviço para devolver uma contagem de todos os itens na coleção de documentos do índice especificado.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais.

- `Accept`: Este valor tem de estar definida `text/plain`.
- `api-key`: O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo para o seu URL do serviço. O pedido de **Contagem de documentos** pode especificar uma chave de administrador ou chave de consulta para `api-key`.

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Nenhum.

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

O corpo da resposta contém o valor de contagem como um número inteiro formatado em texto simples.

<a name="Suggestions"></a>
## <a name="suggestions"></a>Sugestões

A operação de **sugestões** obtém sugestões com base em pesquisa parcial entrada. É normalmente utilizado nas caixas de pesquisa para fornecer sugestões automáticas de como os utilizadores estão a introduzir termos de pesquisa.

Sugestão pedidos como objectivo sugerir documentos de destino, para que o texto sugerido poderá ser repetido se vários documentos candidatos correspondem a mesma procura de entrada. Pode utilizar `$select` para obter outros campos de documento (incluindo a chave de documento), de modo a que possa saber qual o documento é a origem de cada sugestão.

Uma operação de **sugestões** é emitida como um pedido GET ou mensagem.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando utilizar a mensagem em vez de obter**

Quando utiliza HTTP GET para ligar as **sugestões de** API, tem de ter em mente que o comprimento do URL pedido não pode exceder 8 KB. Este nome costuma suficiente para a maioria das aplicações. No entanto, algumas aplicações produzem consultas muito grandes, especificamente expressões de filtro de OData. Para estas aplicações, utilizar HTTP POST é uma boa opção porque permite filtros de maiores que obter. Com a mensagem, o número de cláusulas um filtro é o factor de limitação, não o tamanho da cadeia de filtro não processado uma vez que o limite de tamanho pedido para mensagem é aproximadamente 16 MB.

> [AZURE.NOTE] Apesar do limite de tamanho de pedido de mensagem for muito grande, expressões de filtro não podem ser arbitrariamente complexas. Consulte o artigo [da sintaxe das expressões OData](https://msdn.microsoft.com/library/dn798921.aspx) para obter mais informações sobre as limitações de complexidade de filtro.

**Pedido**

É necessário HTTPS para pedidos de serviço. O pedido de **sugestões** pode ser construído utilizando os métodos GET ou mensagem.

O pedido URI Especifica o nome do índice remissivo a consulta. Parâmetros, tal como o termo de pesquisa parcialmente entrada forem especificados na cadeia de consulta, no caso de pedidos de obtenção de e, no pedido de pedidos de corpo no caso de mensagem.

Como prática recomendada quando criar os pedidos GET, lembre-se aos parâmetros de consulta específica de [codificação de URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) ao chamar REST API diretamente. **Sugestões** para operações de, isto inclui:

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

Codificação de URL só é recomendado os parâmetros de consulta acima. Se que codificar inadvertidamente URL a cadeia de consulta inteira (tudo após o?), irão interromper pedidos.

Além disso, a codificação de URL só é necessário ao chamar a API REST directamente utilizando obter. Sem codificação de URL é necessário ao chamar **sugestões** utilizando a mensagem ou ao utilizar a [biblioteca do cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), que processam a codificação de URL para si.

**Parâmetros de consulta**

**Sugestões de** aceita vários parâmetros que fornecem critérios de consulta e também especificam comportamento de procura. Forneça a que cadeia de consulta estes parâmetros no URL, ao chamar **sugestões** através de obter e como propriedades JSON no corpo do pedido ao chamar **sugestões** através da mensagem. A sintaxe para alguns parâmetros é ligeiramente diferente entre GET e POST. Estas diferenças estão anotadas conforme aplicável abaixo:

`search=[string]`-o texto de pesquisa a utilizar para sugerir consultas. Tem de ter, pelo menos, 1 carácter e mais do que 100 carateres.

`highlightPreTag=[string]`(opcional) - uma cadeia de etiqueta que anexa para procurar acertos. Tem de ser definido com `highlightPostTag`.

> [AZURE.NOTE] Quando chamar **sugestões** utilizando GET, caracteres reservados no URL deve ser percentagem codificada (por exemplo, % 23 em vez de #).

`highlightPostTag=[string]`(opcional) - uma cadeia de etiqueta que acrescenta para procurar acertos. Tem de ser definido com `highlightPreTag`.

> [AZURE.NOTE] Quando chamar **sugestões** utilizando GET, caracteres reservados no URL deve ser percentagem codificada (por exemplo, % 23 em vez de #).

`suggesterName=[string]`-o nome do suggester conforme especificado na `suggesters` coleções de sites que faz parte da definição de índice. A `suggester` determina quais os campos que são analisados para termos de sugerido da consulta. Consulte o artigo [Suggesters](#Suggesters) para obter detalhes.

`fuzzy=[boolean]`(opcional, predefinido = false)-quando definida como VERDADEIRO, esta API irá encontrar as sugestões mesmo se existir um caráter substituído ou em falta no texto de pesquisa. Enquanto este procedimento fornece uma melhor experiência em alguns cenários inclui um um desempenho como sugestão nítidos pesquisas são mais lentas e consumam mais recursos de custo.

`searchFields=[string]`(opcional) - a lista de nomes de campo separados por vírgulas para procurar o texto de pesquisa especificado. Campos de destino tem de estar ativados para obter sugestões.

`$top=#`(opcional, predefinido = 5)-o número de sugestões para obter. Tem de ser um número entre 1 e 100.

> [AZURE.NOTE] Quando chamar **sugestões** utilizando a mensagem, este parâmetro chama-se `top` em vez de `$top`.

`$filter=[string]`(opcional) - considerada uma expressão que filtra os documentos para obter sugestões.

> [AZURE.NOTE] Quando chamar **sugestões** utilizando a mensagem, este parâmetro chama-se `filter` em vez de `$filter`.

`$orderby=[string]`(opcional) - uma lista de expressões separados por vírgulas para ordenar os resultados por. Cada expressão pode ser um nome de campo ou uma chamada para o `geo.distance()` função. Cada expressão pode ser seguido por `asc` para indicado por ordem ascendente, e `desc` para indicar descendente. A predefinição é ordem ascendente. Existe um limite de 32 cláusulas para `$orderby`.

> [AZURE.NOTE] Quando chamar **sugestões** utilizando a mensagem, este parâmetro chama-se `orderby` em vez de `$orderby`.

`$select=[string]`(opcional) - uma lista de campos separados por vírgulas para obter. Se não for especificado, é devolvido apenas o chave e sugestão de texto do documento. Pode pedir explicitamente todos os campos ao definir este parâmetro `*`.

> [AZURE.NOTE] Quando chamar **sugestões** utilizando a mensagem, este parâmetro chama-se `select` em vez de `$select`.

`minimumCoverage`(opcional, a predefinição é 80)-um número entre 0 e 100 que indica a percentagem do índice que deve ser coberta por uma consulta de sugestões para que a consulta a ser comunicado como um sucesso. Por predefinição, pelo menos 80% do índice remissivo tem de estar disponível ou `Suggest` irá devolver o código de estado HTTP 503. Se definir `minimumCoverage` e `Suggest` ser bem sucedida, será devolvido HTTP 200 e incluir uma `@search.coverage` valor na resposta que indica a percentagem do índice que foi incluída na consulta.

> [AZURE.NOTE] Definir este parâmetro para um valor inferior a 100 pode ser úteis para assegurar a disponibilidade de pesquisa mesmo para serviços com apenas uma réplica. No entanto, nem todas as sugestões correspondentes são garante estar presentes nos resultados de. Se resgate é mais importante para a sua aplicação disponibilidade, em seguida, é melhor não baixar `minimumCoverage` abaixo o valor predefinido de 80.

`api-version=[string]`(obrigatório). A versão de pré-visualização é `api-version=2015-02-28-Preview`. Consulte o artigo [Controlo de versões de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Nota: para esta operação, o `api-version` especificado como um parâmetro de consulta no URL, independentemente de se ligar **as sugestões** com GET ou mensagem.

**Pedido de cabeçalhos**

A lista seguinte descreve os cabeçalhos de pedido de necessários e opcionais

- `api-key`: O `api-key` é utilizada para autenticar o pedido de serviço de pesquisa. É um valor de cadeia exclusivo para o seu URL do serviço. O pedido de **sugestões** pode especificar uma chave de administrador ou chave de consulta como a `api-key`.

Também terá do nome do serviço para construir o URL do pedido. Pode obter o nome do serviço e `api-key` a partir do dashboard do serviço no Portal do Azure. Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para navegação entre páginas ajudar.

**Corpo do pedido**

Para obter: nenhuma.

Para registar:

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Resposta**

Código de estado: 200 OK é devolvido por uma resposta bem sucedida.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Se a opção projecções é utilizada para obter os campos que estão incluídos na cada elemento dessas matrizes:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Exemplo**

Obter sugestões de 5, onde a entrada de pesquisa parcial é 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
