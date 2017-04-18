<properties
    pageTitle="Pontuação perfis (Azure pesquisa REST API versão 2015-02-28-pré-visualização) | Microsoft Azure | Pré-visualização de pesquisa Azure API"
    description="Pesquisa Azure é um serviço de pesquisa de nuvem alojado que suporte de sintonização de resultados classificados com base em perfis de pontuação definidos pelo utilizador."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.author="heidist"
    ms.date="08/29/2016" />

# <a name="scoring-profiles-azure-search-rest-api-version-2015-02-28-preview"></a>Perfis de pontuação (pesquisa Azure REST API versão 2015-02-28-pré-visualização)

> [AZURE.NOTE] Este artigo descreve os perfis de pontuação na [2015-02-28-pré-visualização](search-api-2015-02-28-preview.md). Neste momento não existe nenhuma diferença entre o `2015-02-28` versão documentado no [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) e o `2015-02-28-Preview` versão descritas aqui, mas oferecemos este documento mesmo assim para poder fornecem cobertura do documento ao longo de toda API.

## <a name="overview"></a>Descrição geral

Pontuação refere-se para o cálculo de uma classificação de pesquisa para cada item devolvido nos resultados da pesquisa. Pontuação é um indicador de relevância de um item no contexto da operação de pesquisa atual. Quanto maior pontuação, mais relevantes o item. Nos resultados de pesquisa, os itens são ordenada de cima para baixo, com base em pesquisa pontuação calculada para cada item de classificação.

Pesquisa Azure utiliza predefinida para calcular uma pontuação inicial de pontuação, mas pode personalizar o cálculo através de um perfil de pontuação. Perfis de pontuação dar-lhe maior controlo sobre a classificação de itens nos resultados da pesquisa. Por exemplo, poderá pretender impulsionar itens com base no seu potencial de receitas, promover os itens mais recentes ou talvez impulsionar itens que tenham sido inventário demasiado tempo.

Um perfil de pontuação faz parte da definição de índice composto por campos, funções e parâmetros.

Para dar-lhe uma ideia de como o aspeto de um perfil de pontuação, o exemplo seguinte mostra um perfil simple chamado 'geo'. Aumenta deste itens que tenham o termo de pesquisa na `hotelName` campo. Também utiliza o `distance` função para preferir itens que estão dentro de dez quilómetros da localização atual. Se alguém procura no termo 'DCI' e 'DCI' acontece façam parte do nome do hotel, os documentos que incluem o alojamento com 'DCI' irão aparecer superiores nos resultados da pesquisa.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Para utilizar este perfil pontuação, a sua consulta é elaborada para especificar o perfil na cadeia de consulta. Na consulta abaixo, repare o parâmetro de consulta, `scoringProfile=geo` no pedido de.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2015-02-28-Preview

Esta consulta procura no termo 'DCI' e transmite na localização atual. Tenha em atenção que esta consulta inclui outros parâmetros, tal como `scoringParameter`. Parâmetros de consulta são descritos nos [Documentos de pesquisa (Azure pesquisa API)](search-api-2015-02-28-preview.md#SearchDocs).

Clique em [exemplo](#example) para rever um exemplo de um perfil de pontuação mais detalhado.

## <a name="what-is-default-scoring"></a>O que é de pontuação predefinido?

Pontuação calcula uma pontuação de pesquisa para cada item num conjunto de resultados ordenada classificação. Todos os itens num conjunto de resultados de pesquisa são atribuído uma classificação de pesquisa, em seguida, classificados maior para o menor. Itens com as pontuações mais elevadas são devolvidos à aplicação. Por predefinição, o 50 superior são devolvidos, mas pode utilizar o `$top` parâmetro para devolver um número maior ou menor de itens (até 1000 numa única resposta).

Por predefinição, uma classificação de pesquisa é calculada com base nas propriedades estatísticas dos dados e a consulta. Procura Azure localiza documentos que incluem os termos de pesquisa na cadeia de consulta (alguns ou todos os, cálculo consoante a forma `searchMode`), optar por documentos que contenham várias instâncias do termo de pesquisa. Pontuação pesquisa vai mesmo superior, se o termo for raro ao longo do corpo dados, mas comuns no documento. A base para esta abordagem para computação relevância é conhecida como TF FIL ou (frequência de documento frequência inverso termos).

Os resultados partindo do princípio de que não haja nenhuma ordenação personalizada, são, em seguida, classificados por pontuação de pesquisa antes de serem devolvidos à aplicação de chamada. Se `$top` não for especificado, 50 itens, tendo a procura de mais alta pontuação é devolvidas.

Valores de pontuação pesquisa podem ser repetidos ao longo de um conjunto de resultados. Por exemplo, poderá ter 10 itens com uma classificação de 1.2, 20 itens com uma classificação de 1.0 e 20 itens com uma classificação de 0,5. Quando vários acertos têm a mesma pontuação de pesquisa, a ordenação dos mesmos itens visita não está definida e não é estável. Executar a consulta novamente e, poderá ver itens shift posição. Dada dois itens com uma pontuação idêntica, há garantias qual deles aparece em primeiro lugar.

## <a name="when-to-use-custom-scoring"></a>Quando utilizar pontuação personalizado

Deve criar um ou mais perfis pontuação quando a predefinição comportamento de classificação não extremidade suficiente nos objetivos do negócio da reunião. Por exemplo, pode decidir que relevância da pesquisa deve preferir itens recentemente adicionados. Da mesma forma, poderá ter um campo que contém a margem de lucro ou outro campo indicar que a receita potencial. Aumentar a acertos trazer benefícios para a sua empresa pode ser um factor importante para decidir utilizar perfis de pontuação.

Com base em relevância corrigida também é implementada através de perfis de pontuação. Considere a hipótese de páginas de resultados de pesquisa que utilizou no passado que permitem-lhe ordenar por preço, data, classificação ou relevância. Na pesquisa Azure, pontuação perfis unidade a opção 'relevância'. A definição de relevância é controlada pelo utilizador, determinada por objetivos empresas e o tipo de experiência de pesquisa que pretende efetuar.

<a name="example"></a>
## <a name="example"></a>Exemplo

Tal como indicado, pontuação personalizada é implementada através de pontuação perfis definidos num esquema índice remissivo.

Este exemplo mostra o esquema de um índice com dois perfis de pontuação (`boostGenre`, `newAndHighlyRated`). Qualquer consulta contra este índice que inclui um dos perfil como um parâmetro de consulta irá utilizar o perfil para o conjunto de resultados de pontuação.

[Experimente este exemplo](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
      "fields": [
        { "name": "key", "type": "Edm.String", "key": true },
        { "name": "albumTitle", "type": "Edm.String" },
        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
        { "name": "genre", "type": "Edm.String" },
        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
        { "name": "artistName", "type": "Edm.String" },
        { "name": "orderableOnline", "type": "Edm.Boolean" },
        { "name": "rating", "type": "Edm.Int32" },
        { "name": "tags", "type": "Collection(Edm.String)" },
        { "name": "price", "type": "Edm.Double", "filterable": false },
        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
        { "name": "inventory", "type": "Edm.Int32" },
        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
      ],
      "scoringProfiles": [
        {
          "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1.5,
              "genre": 5,
              "artistName": 2
            }
          }
        },
        {
          "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


## <a name="workflow"></a>Fluxo de trabalho

Para implementar o comportamento de pontuação personalizado, adicione um perfil de pontuação para o esquema que define o índice. Pode ter vários perfis pontuação dentro de um índice remissivo, mas só pode especificar um perfil momento qualquer consulta determinado.

Comece com o [modelo](#bkmk_template) fornecido neste tópico.

Forneça um nome. Os perfis de pontuação são opcionais, mas se adicionar um, é necessário o nome. Certifique-se de que siga as convenções de nomenclatura para campos (começa com uma carta, evita carateres especiais e palavras reservadas). Para mais informações, consulte [As regras de nomenclatura](http://msdn.microsoft.com/library/azure/dn857353.aspx) .

Corpo do perfil de pontuação fórmula foi criado a partir de campos ponderados e funções.

### <a name="weights"></a>Espessura ###

O `weights` propriedade de um perfil de pontuação especifica pares valor do nome que atribuir uma espessura de relativa a um campo. No [exemplo](#example), os campos albumTitle, adicionar nova e artistName são aumentado 1,5, 5 e 2, respetivamente. Por que motivo é género aumentado muito maior do que as outras? Se é realizada pesquisa sobre os dados que é um pouco homogéneos (tal como acontece com 'género' na `musicstoreindex`), poderá ter uma maior variância a espessura relativa. Por exemplo, na `musicstoreindex`, 'pedra' aparece como ambas as adicionar uma nova e em descrições género idêntico phrased. Se pretender adicionar nova para compensar descrição género, o campo género terão uma quantidade espessura relativa superior.

### <a name="functions"></a>Funções ###

Funções são utilizadas quando cálculos adicionais são necessários para o contextos específicos. Tipos de função válido são `freshness`, `magnitude`, `distance` e `tag`. Cada função tem parâmetros que sejam únicos ao mesmo.

  - `freshness`deverá ser utilizado quando pretender impulsionar por como novo ou antigo um item é. Esta função só pode ser utilizada com campos de data/hora (`Edm.DataTimeOffset`). Nota a `boostingDuration` atributo é utilizado apenas com a função de actualização.
  - `magnitude`deverá ser utilizado quando pretende impulsionar com base em como alta ou baixa é de um valor numérico. Cenários que utilizam para esta função incluem aumentar por margem de lucro, preços mais altos, mais baixo preço ou uma contagem das transferências. Pode reverter o intervalo, de cima para baixo, se pretender que o inverso padrão (por exemplo, para itens de intensidade preços mais baixos mais do que superior preços itens). Atribuído um intervalo de preços de $100 a $1, teria de definir `boostingRangeStart` a 100 e `boostingRangeEnd` em 1 para impulsionar os itens de preços mais baixos. Esta função só pode ser utilizada com campos duplo e o número inteiro.
  - `distance`deverá ser utilizado quando pretender impulsionar por proximidade ou localização geográfica. Esta função só pode ser utilizada com `Edm.GeographyPoint` campos.
  - `tag`deverá ser utilizado quando pretender impulsionar por etiquetas em comum entre documentos e consultas de pesquisa. Esta função só pode ser utilizada com `Edm.String` e `Collection(Edm.String)` campos.
  
#### <a name="rules-for-using-functions"></a>Regras para utilizando funções ####

  - Tipo de função (última modificação da, magnitude, distância, etiqueta) tem de ser minúsculas.
  - Funções não podem incluir valores nulos ou vazios. Especificamente, se incluir fieldname, tem de defini-la para algo.
  - Funções apenas podem ser aplicadas a campos filtráveis. Consulte o artigo [Criar índice](search-api-2015-02-28-preview.md#CreateIndex) para obter mais informações sobre os campos filtráveis.
  - Funções apenas podem ser aplicadas a campos que são definidos na coleção de campos de um índice remissivo.

Depois do índice está definido, construa o índice remissivo ao carregar o esquema de índice remissivo, seguido de documentos. Consulte o artigo [Criar índice](search-api-2015-02-28-preview.md#CreateIndex) e [Adicionar ou atualizar documentos](search-api-2015-02-28-preview.md#AddOrUpdateDocuments) para obter instruções sobre estas operações. Quando tiver criado o índice remissivo, deverá ter um perfil de pontuação funcional que funciona com os seus dados de pesquisa.

<a name="bkmk_template"></a>
## <a name="template"></a>Modelo
Esta secção mostra o modelo de perfis de pontuação e sintaxe. Consulte a [referência de atributo de índice remissivo](#bkmk_indexref) , na secção seguinte para as descrições dos atributos.

    ...
    "scoringProfiles": [
      {
        "name": "name of scoring profile",
        "text": (optional, only applies to searchable fields) {
          "weights": {
            "searchable_field_name": relative_weight_value (positive #'s),
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
## <a name="scoring-profile-property-reference"></a>Referência de propriedades de perfil de pontuação

**Nota** Uma função pontuação apenas pode ser aplicada a campos que são filtráveis.

| Propriedade | Descrição |
|----------|-------------|
| `name`   | Obrigatório. Este é o nome do perfil de pontuação. Segue-as mesmas convenções de nomenclatura de um campo. -Tem de começar com uma carta, não pode conter pontos, dois pontos ou @ símbolos e não pode começar com a expressão "azureSearch" (entre maiúsculas e minúsculas). |
| `text` | Contém a propriedade de espessura. |
| `weights` | Opcional. Um par de nome do valor que especifica um nome de campo e uma espessura relativa. Peso relativo tem de ser um número inteiro positivo ou o número de vírgula flutuante. Pode especificar o nome do campo sem uma espessura de correspondente. Espessura é utilizada para indicar a importância de um campo em relação a outra. |
| `functions` | Opcional. Tenha em atenção que uma função pontuação apenas pode ser aplicada a campos que são filtráveis. |
| `type` | É necessária para funções de pontuação. Indica o tipo de função a utilizar. Os valores válidos incluem `magnitude`, `freshness`, `distance` e `tag`. Pode incluir mais de uma função de cada perfil pontuação. O nome da função tem de ser minúsculas. |
| `boost` | É necessária para funções de pontuação. Um número positivo utilizado como multiplicador de pontuação não processada. Não pode ser igual a 1. |
| `fieldName` | É necessária para funções de pontuação. Uma função pontuação apenas pode ser aplicada a campos que fazem parte da coleção de campo do índice remissivo e que são filtráveis. Além disso, cada tipo de função apresenta restrições adicionais (última modificação da é utilizado com os campos de data/hora, magnitude com o número inteiro ou duplo campos, distância com campos de localização e etiquetas com a cadeia ou campos de coleção de cadeia). Só pode especificar um único campo por definição da função. Por exemplo, para utilizar a magnitude duas vezes no mesmo perfil, que precisa para incluir dois magnitude de definições, uma para cada campo. |
| `interpolation` | É necessária para funções de pontuação. Define o declive para o qual a pontuação aumentar aumentos desde o início do intervalo para o fim do intervalo. Os valores válidos incluem `linear` (predefinição), `constant`, `quadratic`, e `logarithmic`. Consulte o artigo [Definir interpolations](#bkmk_interpolation) para obter detalhes. |
| `magnitude` | A magnitude pontuação função é utilizada para alterar classificações com base num intervalo de valores para um campo numérico. Alguns dos exemplos da utilização mais comuns são:<ul><li>Classificações em estrelas: alterar a pontuação baseado no valor dentro do campo "Estrela classificação". Quando dois itens são relevantes, o item com a classificação superior será apresentado em primeiro lugar.</li><li>Margem: Quando são os dois documentos relevantes, num revendedor poderá pretender impulsionar a documentos que tenham as margens superiores pela primeira vez.</li><li>Clique em conta: para as aplicações que controlam, clique em através de acções a produtos ou páginas, é possível utilizar magnitude a intensidade itens que têm tendência para obter mais tráfego.</li><li>Transferir contagens: para as aplicações que transferências de registar, a permite de função magnitude impulsionar a que itens que tenham mais transferências.</li></ul> |
| `magnitude:boostingRangeStart` | Define o valor de início do intervalo relativamente à qual a magnitude é classificado. O valor tem de ser um número inteiro ou número de vírgula flutuante. Para classificações de 1 a 4, este seria 1. Para mais de 50% margens, este seria 50. |
| `magnitude:boostingRangeEnd` | Define o valor de fim do intervalo relativamente à qual a magnitude é classificado. O valor tem de ser um número inteiro ou número de vírgula flutuante. Para classificações de 1 a 4, este seria 4. |
| `magnitude:constantBoostBeyondRange` | Valores válidos são VERDADEIRO ou FALSO (predefinição). Quando definida como VERDADEIRO, a intensidade do completa irão continuar a aplicar a documentos que tenham um valor para o campo de destino que é mais elevado que extremidade superior do intervalo. Se for FALSO, a intensidade desta função não será aplicada à documentos com um valor para o campo de destino determinado fora do intervalo. |
| `freshness` | A última modificação da função de pontuação é utilizada para alterar as pontuações de classificação para itens com base em valores nos campos DateTimeOffset. Por exemplo, um item com uma data mais recente pode ser com classificação superior a itens mais antigos. (Tenha em atenção que é também possível classificação itens como eventos do calendário com datas futuras assim que os itens mais perto do presente podem ser com classificação superior a itens ainda mais no futuro.) Na versão atual do serviço, será corrigida uma extremidade do intervalo para a hora atual. A outra extremidade é um tempo no passado com base na `boostingDuration`. Para aumentar a um intervalo de horas no futuro utilize um negativo `boostingDuration`. A frequência com que a aumentar a alterações de um máximo e mínimo intervalo é determinado pela intercalação aplicada ao perfil de pontuação (consulte figura abaixo). Para inverter o factor boosting aplicado, selecione um fator de intensidade de menor que 1. |
| `freshness:boostingDuration` | Conjuntos de um período de expiração após que esse aumento deixarão de para um documento específico. Consulte o artigo [conjunto boostingDuration] [#bkmk_boostdur] na secção seguinte para a sintaxe e exemplos. |
| `distance` | A distância função pontuação é utilizada a afetar a pontuação de documentos baseados no como fecha ou extremidade são relativamente uma localização geográfica de referência. A localização de referência é dado como parte da consulta num parâmetro (utilizando o `scoringParameter` parâmetro de consulta) como um extenso, lat argumento. |
| `distance:referencePointParameter` | Um parâmetro para transmitir em consultas para utilizar como localização de referência. scoringParameter é um parâmetro de consulta. Consulte o artigo [Procurar documentos](search-api-2015-02-28-preview.md#SearchDocs) para descrições dos parâmetros de consulta. |
| `distance:boostingDistance` | Um número que indica a distância em quilómetros desde a localização de referência onde o intervalo boosting termina. |
| `tag` | A etiqueta pontuação função é utilizada a afetar a pontuação de documentos baseados no etiquetas em documentos e consultas de pesquisa. Documentos que tenham etiquetas comuns com a consulta de pesquisa irão ser aumentados. As etiquetas para a consulta de pesquisa é fornecida como um parâmetro pontuação em cada pedido de pesquisa (utilizando o `scoringParameter` parâmetro de consulta). |
| `tag:tagsParameter` | Um parâmetro para transmitir em consultas para especificar etiquetas para um determinado pedido. `scoringParameter`é um parâmetro de consulta. Consulte o artigo [Procurar documentos](search-api-2015-02-28-preview.md#SearchDocs) para descrições dos parâmetros de consulta. |
| `functionAggregation` | Opcional. Aplica-se apenas quando funções estão especificadas. Os valores válidos incluem: `sum` (predefinição), `average`, `minimum`, `maximum`, e `firstMatching`. Uma classificação de pesquisa é um único valor é calculado a partir de múltiplas variáveis, incluindo várias funções. Este atributos indica como aumenta de todas as funções é combinadas uma única intensidade agregação que, em seguida, é aplicada à pontuação documento base. A pontuação base é baseada no valor tf FIL calculado a partir do documento e a consulta de pesquisa. |
| `defaultScoringProfile` | Durante a execução de um pedido de pesquisa, se não for especificado nenhum perfil pontuação, em seguida, pontuação predefinido é utilizado (tf-FIL apenas). Uma predefinida nome do perfil de pontuação aqui, pode ser definida a causar o Azure a pesquisa para utilizar esse perfil quando não é indicado nenhum perfil específico no pedido de pesquisa. |

<a name="bkmk_interpolation"></a>
## <a name="set-interpolations"></a>Definir interpolations

Interpolations permitem-lhe definir o declive para o qual a pontuação aumentar aumentos desde o início do intervalo para o fim do intervalo. Podem ser utilizados interpolations os seguintes:

  - `Linear`: Para itens que estão no intervalo máximo e mínimo, um montante constantemente decrescente será realizada intensidade aplicada ao item. Linear é a intercalação predefinido de um perfil de pontuação.
  - `Constant`: Para itens que são utilizadas em Iniciar e terminar o intervalo, uma constante intensidade será aplicada aos resultados de classificação.
  - `Quadratic`: Na comparação para interpolação Linear que tem uma intensidade constantemente decrescente, quadrática num inicialmente diminuirá ao ritmo mais pequeno e, em seguida, ao aproxima-o intervalo de fim,-diminui um intervalo de muito superior. Esta opção interpolação não é permitida na etiqueta pontuação funções.
  - `Logarithmic`: Na comparação para interpolação Linear que tem uma intensidade constantemente decrescente, logarítmica inicialmente diminuirá ao ritmo superior e, em seguida, ao aproxima-o intervalo de fim,-diminui um intervalo muito mais pequeno. Esta opção interpolação não é permitida na etiqueta pontuação funções.

<a name="Figure1"></a>
 ![][1]

<a name="bkmk_boostdur"></a>
## <a name="set-boostingduration"></a>Definir boostingDuration

`boostingDuration`é um atributo da função actualização. Utilizá-la para definir uma expiração ponto após que esse aumento deixarão de para um documento específico. Por exemplo, para impulsionar uma linha de produto ou marca corporativa durante um período de promocional de 10 dias, teria de especificar o período de 10 dias como "P10D" para esses documentos. Ou para impulsionar eventos futuros na próxima semana especifique "-P7D".

`boostingDuration`tem de ser formatado como um valor de "dayTimeDuration" XSD (um subconjunto de um valor de duração ISO 8601 restrito). O padrão para este é: `[-]P[nD][T[nH][nM][nS]]`.

A tabela seguinte fornece vários exemplos.

| Duração | boostingDuration |
|----------|------------------|
| 1 dia | "P1D" |
| dois dias e 12 horas | "P2DT12H" |
| 15 minutos | "PT15M" |
| 30 dias, 5 horas, 10 minutos, e 6.334 segundos | "P30DT5H10M6.334S" |

Para obter mais exemplos, consulte o artigo [esquema XML: tipos de dados (W3.org web site)](http://www.w3.org/TR/xmlschema11-2/).

**Consulte também**
[REST API do serviço de pesquisa Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) no MSDN <br/>
[Criar o índice (Procurar Azure API)](http://msdn.microsoft.com/library/azure/dn798941.aspx) no MSDN<br/>
[Adicionar um perfil de pontuação a um índice de pesquisa](http://msdn.microsoft.com/library/azure/dn798928.aspx) no MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png
