<properties
    pageTitle="Recolha de dados para preparar o seu modelo: recomendações API de formação de máquina | Microsoft Azure"
    description="Azure máquina recomendações de formação - recolha de dados para preparar o seu modelo"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>Recolha de dados para preparar o seu modelo #

API recomendações aprende a partir das suas transações anteriores para encontrar os itens que devem ser recomenda-se um utilizador específico.

Depois de ter criado um modelo, terá de fornecer duas pedaço de informação antes que pode fazer qualquer formação: um ficheiro de catálogo e dados de utilização.

>   Se ainda não o tiver feito, aconselhamos para concluir o [Guia de introdução](cognitive-services-recommendations-quick-start.md).


## <a name="catalog-data"></a>Catálogo de dados ##

### <a name="catalog-file-format"></a>Formato de ficheiro de catálogo ###

O ficheiro de catálogo contém informações sobre os itens que está a oferta ao seu cliente.
Os dados do catálogo devem seguir o seguinte formato:

- Sem funcionalidades-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Com funcionalidades-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>Exemplo de linhas num ficheiro de catálogo

Sem funcionalidades:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Com funcionalidades:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>Formatar Detalhes

| Nome | Obrigatório | Tipo |  Descrição |
|:---|:---|:---|:---|
| Id do item |Sim | [A-z], [a-z], [0-9], [_] & #40; Caráter de sublinhado & #41; [-] & #40; traço & #41;<br> Comprimento máximo: 50 | Identificador exclusivo de um item. |
| Nome do item | Sim | Quaisquer carateres alfanuméricos<br> Comprimento máximo: 255 | Nome do item. |
| Categoria do produto | Sim | Quaisquer carateres alfanuméricos <br> Comprimento máximo: 255 | Categoria à qual este item pertence (por exemplo, cozinha livros, teatro...); pode estar vazio. |
| Descrição | Não, a menos que funcionalidades estão apresentar (mas pode estar em branco) | Quaisquer carateres alfanuméricos <br> Comprimento máximo: 4000 | Descrição do produto. |
| Lista de funcionalidades | N | Quaisquer carateres alfanuméricos <br> Comprimento máximo: 4000; Número máximo de funcionalidades: 20 | Lista separados por vírgulas do nome de funcionalidade = valor de funcionalidade que pode ser utilizado para melhorar recomendação de modelo.|

#### <a name="uploading-a-catalog-file"></a>Carregar um ficheiro de catálogo

Observe a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) para carregar um ficheiro de catálogo.  

Note que o conteúdo do ficheiro catálogo deve ser passado como o corpo do pedido.

Se carregar vários ficheiros de catálogo para o mesmo modelo com várias chamadas, podemos irá inserir apenas os novos itens de catálogo. Itens existentes irão permanecer com os valores originais. Não é possível atualizar dados do catálogo através deste método.

>   Nota: O tamanho máximo do ficheiro é 200MB.
>   O número máximo de itens no catálogo suportadas é 100.000 itens.


## <a name="why-add-features-to-the-catalog"></a>Porquê adicionar funcionalidades para o catálogo?

O motor de recomendações cria um modelo de estatístico mostra-lhe que itens provavelmente ser gostou ou comprado por um cliente. Quando tem um novo produto que nunca tenha sido processado com o mesmo não é possível criar um modelo no ocorrências cocriação só por si. Digamos que iniciar a perguntar se uma nova "violin das crianças" na sua loja, uma vez que nunca tiver comprado esse violin antes de não consegue indicar que outros itens para recomendar com essa violin.

Assim sendo, se o motor de conhecer informações sobre essa violin (ou seja, é um instrumentos musicais, é para crianças idades 7 a 10, não é uma violin dispendioso, etc.), em seguida, o motor pode obter a partir de outros produtos com funcionalidades semelhantes. Por exemplo, tiver comprado do violin no passado e normalmente pessoas comprar violinos tendem para comprar a música clássica CDs e bicicleta de folha de música.  O sistema pode encontrar estas ligações entre as funcionalidades e fornecer recomendações com base nas funcionalidades enquanto o seu novo violin tem pouca utilização.

Funcionalidades são importadas como parte dos dados catálogo e, em seguida, os respetivos classificação (ou a importância da funcionalidade no modelo de) é associada depois de criar uma classificação está concluído. Funcionalidade de classificação pode mudam de acordo com o padrão de dados de utilização e tipo de itens. Mas, para a utilização consistentes/itens, a classificação deve ter apenas os variações pequenas. A classificação de funcionalidades é um número não negativo. O número 0 significa que a funcionalidade não foi classificada (acontece se invocar esta API antes da conclusão da primeira construir classificação). A data em que foi atribuída a classificação chama-se a última modificação da pontuação.


###<a name="features-are-categorical"></a>Funcionalidades estão Categorical

Isto significa que deverá criar funcionalidades que se pareça com uma categoria. Por exemplo, preço = 9.34 não é uma funcionalidade de categorias. Por outro lado, uma funcionalidade como priceRange = Under5Dollars é uma funcionalidade de categorias. Outro erro comum é utilizar o nome do item como uma funcionalidade. Pretende fazer com que o nome de um item ser exclusivos, para que não-seria descrevem uma categoria. Certifique-se que as funcionalidades representam categorias de itens.


###<a name="how-manywhich-features-should-i-use"></a>Funcionalidades como muitos/qual devo utilizar?


Finalmente as recomendações construir suporta a criação de um modelo com funcionalidades até 20. Poderá atribuir mais de 20 funcionalidades para os itens no seu catálogo, mas são esperado para fazer uma compilação de classificação e escolha apenas as funcionalidades com a classificação alta. (Uma funcionalidade com uma ordem de 2.0 ou mais é uma funcionalidade muito bom a utilizar!). 


###<a name="when-are-features-actually-used"></a>Quando são realmente usadas funcionalidades?

Funcionalidades são utilizadas pelo modelo quando não é suficiente dados da transação para fornecer recomendações sobre as informações da transação sozinho. Por isso, funcionalidades terá o maior impacto em "itens frias" – itens com alguns transações. Se todos os seus itens tem informações da transação suficientes, não poderá ter de enriquecer o seu modelo com funcionalidades.


###<a name="using-product-features"></a>Utilizar funcionalidades do produto

Para utilizar funcionalidades como parte do seu compilação que terá de:

1. Certifique-se de que catálogo tem funcionalidades quando que carregá-lo.

2. Acionar uma compilação de classificação. Isto vai fazer a análise na importância classificação das funcionalidades.

3. Acionar uma compilação recomendações, definir o seguinte construir parâmetros: defina useFeaturesInModel para allowColdItemPlacement for VERDADEIRO, como VERDADEIRO, e modelingFeatureList deve ser definido para a lista de separados por ponto e de funcionalidades que pretende utilizar para melhorar o seu modelo. Para obter mais informações, consulte a [recomendações criar os parâmetros de tipo](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) .





## <a name="usage-data"></a>Dados de utilização ##
Um ficheiro de utilização contém informações sobre como são utilizadas esses itens ou as transações da sua empresa.

#### <a name="usage-format-details"></a>Detalhes de formato de utilização
Um ficheiro a utilização é um ficheiro de (de valores separados por vírgulas) CSV em que cada linha de um ficheiro de utilização representa uma interação entre um utilizador e um item. Cada linha é formatada da seguinte forma:<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| Nome  | Obrigatório | Tipo | Descrição
|-------|------------|------|---------------
|Id de utilizador|         Sim|[A-z], [a-z], [0-9], [_] & #40; Caráter de sublinhado & #41; [-] & #40; traço & #41;<br> Comprimento máximo: 255 |Identificador exclusivo de um utilizador.
|Id do item|Sim|[A-z], [a-z], [0-9], [& #95;] & #40; Caráter de sublinhado & #41; [-] & #40; traço & #41;<br> Comprimento máximo: 50|Identificador exclusivo de um item.
|Tempo|Sim|Data no formato: aaaa/MM/ddTHH (por exemplo, 2013/06/20T10:00:00)|Data/hora de dados.
|Evento|N | Um dos seguintes procedimentos:<br>• Clique em<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Compra| O tipo da transação. |

#### <a name="sample-rows-in-a-usage-file"></a>Exemplo de linhas num ficheiro de utilização

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>Carregar um ficheiro de utilização

Observe a [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) para carregar ficheiros de utilização.
Tenha em atenção que precisa de ser efetuada com o conteúdo do ficheiro a utilização, como o corpo da chamada HTTP.

>  Nota:

>  Tamanho máximo do ficheiro: 200MB. Pode carregar vários ficheiros de utilização.

>  Precisar de carregar um ficheiro de catálogo antes de começar a adicionar os dados de utilização ao seu modelo. Apenas os itens no ficheiro de catálogo serão utilizados durante a fase de formação. Todos os outros itens serão ignorados.

## <a name="how-much-data-do-you-need"></a>Quantidade de dados que precisa?

A qualidade do seu modelo é fortemente dependente qualidade e à quantidade dos seus dados.
O sistema aprende quando os utilizadores compram itens diferentes (chamamos este ocorrências cocriação). Para FBT compilações, também é importante saber quais os itens sejam comprados transações a mesma. 

É uma boa regra útil para que a maioria dos itens estar no 20 transações ou mais, para que se tinha 10.000 itens no seu catálogo, recomendamos que tem, pelo menos, 20 horas esse número de operações ou cerca 200.000 operações. Mais uma vez, esta é uma regra útil. Terá de experimentar com os seus dados.

Depois de ter criado um modelo, pode executar uma [avaliação offline](cognitive-services-recommendations-buildtypes.md) para verificar como bem o seu modelo é provável efetuar.
