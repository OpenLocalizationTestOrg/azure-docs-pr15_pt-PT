<properties
    pageTitle="Tipos de compilação e qualidade de modelo: recomendações API | Microsoft Azure"
    description="Recomendações de aprendizagem máquina Azure – guia de introdução"
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
    ms.date="09/20/2016"
    ms.author="luisca"/>

#  <a name="build-types-and-model-quality"></a>Tipos de compilação e qualidade de modelo #

<a name="TypeofBuilds"></a>
## <a name="supported-build-types"></a>Tipos de compilação suportados ##

API recomendações atualmente suporta dois tipos de compilação: *Recomendação* e *FBT*. Cada é construída utilizando algoritmos diferentes e cada tem vantagens diferentes. Este documento descreve cada um dos seguintes compilações e técnicas para comparar a qualidade dos modelos gerado.

Se ainda não o tiver feito, recomendamos que conclua o [Guia de introdução](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### <a name="recommendation-build-type"></a>Tipo de compilação de recomendação ###

O tipo de compilação de recomendação utiliza factorization de matriz para fornecer recomendações. Gera vectores [funcionalidade latente](https://en.wikipedia.org/wiki/Latent_variable) com base nas suas transações para descrever cada item e, em seguida, utiliza essas vectores latentes para comparar os itens que são semelhantes.

Se formar o modelo com base em efetuadas no arquivo de electrónica de compras e fornecer um telefone Lumia 650 como a entrada para o modelo, o modelo irá devolver um conjunto de itens que têm tendência de ser comprados por pessoas que comprar um telefone Lumia 650. Os itens podem não ser complementares. Neste exemplo, é possível que o modelo irá devolver outros telefones, uma vez que as pessoas que gosta a 650 Lumia podem gostar outros telefones.

A compilação de recomendação tem duas funcionalidades que tornam apelativo:

* *Suporta a compilação de recomendação *fria item* posicionamento * *

Itens que não possui a utilização significativa chamam frias itens. Por exemplo, se receber um envio de um telemóvel que nunca tiver comprado antes, o sistema não é possível inferir recomendações para este produto no transações sozinho. Isto significa que o sistema deve obter informações sobre informações sobre o próprio produto.

Se pretender utilizar o posicionamento de item fria, terá de fornecer as informações de funcionalidades para cada um dos seus itens no catálogo de. Segue-se as primeiras linhas do catálogo poderão aspeto (nota a chave = valor formato para as funcionalidades).

>Pro2 6CX 00001, superfície, superfície, escreva = Hardware, armazenamento = 128 GB, memória = 4G, fabricante = Microsoft

>73H-00013, reactivar Xbox 360, jogos, tipo = Software, idioma = inglês, classificação = maduro

>WAH 0F05, Minecraft Xbox 360, jogos, * tipo = Software, idioma = espanhol, classificação = juventude

Também terá de definir os parâmetros de compilação seguintes:

| Criar parâmetro         | Notas
|------------------     |-----------
|*useFeaturesInModel*     | Defina como **Verdadeiro**.  Indica se as funcionalidades podem ser utilizadas para melhorar o modelo de recomendação.
|*allowColdItemPlacement*   | Defina como **Verdadeiro**. Indica se a recomendação deve também notificações push itens frias através do semelhança da funcionalidade.
| *modelingFeatureList*   | Lista de nomes de funcionalidades para ser utilizado na compilação recomendação para melhorar a recomendação separados por vírgulas. Por exemplo, "idioma, armazenamento" para que o exemplo anterior.

**A compilação de recomendação suporta recomendações de utilizador**

Construir uma recomendação suporta [recomendações de utilizador](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Isto significa que pode conferir recomendações personalizadas para os utilizadores com base na suas históricos da transação. Para obter recomendações de utilizador, poderá fornecer o ID de utilizador ou o histórico recente das transações para esse utilizador.

Um exemplo clássico de onde poderá pretender aplicar as recomendações de utilizador está no início de sessão na página de boas-vindas. Não existem pode promover conteúdos que se aplica ao utilizador específico.

Também poderá pretender aplicar um tipo de compilação recomendações quando o utilizador está prestes a dar saída. Nesse momento, tem a lista de itens que o utilizador está prestes a compra e pode fornecer recomendações com base nas cesto de mercado atual.

#### <a name="recommendations-build-parameters"></a>Recomendações construir parâmetros

| Nome  |   Descrição |    Escrever, <br>  valores válidos, <br> (valor predefinido)
|-------|-------------------|------------------
| *NumberOfModelIterations* |   O número de iterações que executa o modelo é refletido ao tempo de cluster global e a precisão do modelo. Quanto maior for o número, mais precisa o modelo, mas o tempo de cluster demora mais tempo.  |   Número inteiro, <br>  10 a 50 <br>(40)
| *NumberOfModelDimensions* |   O número de dimensões refere-se para o número de funcionalidades que o modelo será a tentar encontrar nos seus dados. Aumentar o número de dimensões irá permitir o melhor ajuste dos resultados para clusters mais pequenos. No entanto, demasiadas dimensões irão impedir o modelo de correlação entre os itens de localizar. |  Número inteiro, <br> 10 a 40 <br>(20) |
| *ItemCutOffLowerBound* |  Define o número mínimo de um item deve estar para a mesma sejam considerados parte do modelo de pontos de utilização. |        Número inteiro, <br> 2 ou mais <br> (2) |
| *ItemCutOffUpperBound* |  Define o número máximo de um item deve estar para a mesma sejam considerados parte do modelo de pontos de utilização. |  Número inteiro, <br>2 ou mais<br> (2147483647) |
|*UserCutOffLowerBound* |   Define o número mínimo de transações que um utilizador tem de ter executado sejam considerados parte do modelo. | Número inteiro, <br> 2 ou mais <br> (2)
| *UserCutOffUpperBound* |  Define o número máximo de transações que um utilizador tem de ter executado sejam considerados parte do modelo. | Número inteiro, <br>2 ou mais <br> (2147483647)|
| *UseFeaturesInModel* |    Indica se as funcionalidades podem ser utilizadas para melhorar o modelo de recomendação. |     Booleano<br> Predefinido: VERDADEIRO
|*ModelingFeatureList* |    Lista de nomes de funcionalidades para ser utilizado na compilação recomendação para melhorar a recomendação separados por vírgulas. Depende as funcionalidades que são importantes. |    Cadeia, até 512 caracteres
| *AllowColdItemPlacement* |    Indica se a recomendação deve também notificações push itens frias através do semelhança da funcionalidade. | Booleano <br> Predefinido: Falso
| *EnableFeatureCorrelation*    | Indica se as funcionalidades podem ser utilizadas na raciocínio. | Booleano <br> Predefinido: Falso
| *ReasoningFeatureList* |  Lista de nomes de funcionalidades para ser utilizado para argumentos frases, tal como explicações recomendação separados por vírgulas. Depende as funcionalidades que são importantes para clientes. | Cadeia, até 512 caracteres
| *EnableU2I* | Ative recomendações personalizadas, também denominadas utilizador para o item recomendações (U2I). | Booleano <br>Predefinido: VERDADEIRO
|*EnableModelingInsights* | Define se deve ser executada avaliação offline para recolher informações de modelação (ou seja, métricas precisão e diversidade). Se definida como VERDADEIRO, um subconjunto dos dados não será utilizada para treino, uma vez que terá de ser reservados para testar a ligação do modelo. Leia mais sobre [avaliações offline](#OfflineEvaluation). | Booleano <br> Predefinido: Falso
| *SplitterStrategy* | Se ativar modelação informações estiver definida como *Verdadeiro*, esta é a forma como os dados devem ser divididos para efeitos de avaliação.  | Cadeia, *RandomSplitter* ou *LastEventSplitter* <br>Predefinido: RandomSplitter


<a name="FBTBuild"></a>
### <a name="fbt-build-type"></a>Tipo de compilação FBT ###

Construir (FBT) em conjunto com frequência comprada faz uma análise que conta o número de vezes que dois ou três produtos diferentes cocriação ocorrerem em conjunto. Em seguida, ordena os conjuntos com base numa função semelhança (**ocorrências de cocriação**, **Jaccard**, **levantar**).

Pense **Jaccard** e **levantar** como formas de normalizar as ocorrências de cocriação.  Isto significa que os itens serão devolvidos apenas se estes onde comprou juntamente com o item de propagação.

No nosso exemplo Lumia 650 do telemóvel, telefone X será devolvido apenas se telefone X foi comprada na mesma sessão como o telefone Lumia 650. Uma vez que podem ser pouco, podemos seria esperado itens complementar para o 650 Lumia para ser devolvido; Por exemplo, um protector de ecrã ou uma placa de energia para o 650 Lumia.

Neste momento, dois itens são assumidos de ser comprados na mesma sessão se que aparecem numa transação com o mesmo ID de utilizador e a data/hora.

FBT compilações não suportam frias itens, porque por definição esperavam estes dois itens de ser comprados na mesma operação. Enquanto FBT compilações podem devolver conjuntos de itens (triplets), não suportam recomendações personalizadas porque aceitam um item de propagação única como a entrada.


#### <a name="fbt-build-parameters"></a>Parâmetros de compilação FBT

| Nome  |   Descrição |       Escrever,  <br> valores válidos, <br> (valor predefinido)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Quão conservador o modelo é. Número de ocorrências de cocriação de itens a ser consideradas para modelação. |  Número inteiro, <br> 3-50 <br> (6)
| *FbtMaxItemSetSize* | Liga o número de itens num conjunto frequentes.| Número inteiro  <br> 2-3 <br> (2)
| *FbtMinimalScore* | Pontuação mínimas que deverá ter um conjunto frequente devem ser incluídas dos resultados devolvidos. Quanto maior a melhor. | Dupla <br> 0 e superior <br> (0)
| *FbtSimilarityFunction* | Define a função semelhança para ser utilizado pela compilação. **Levantar** preferência por serendipity, **ocorrência cocriação** preferência por previsibilidade e **Jaccard** é um compromisso entre as duas. | Cadeia,  <br>  <i>cooccurrence elevação, jaccard</i><br> Predefinição: <i>jaccard</i>

<a name="SelectBuild"></a>
## <a name="build-evaluation-and-selection"></a>Construir a avaliação e selecção ##

Estas orientações poderão ajudá-lo a determinar se deve utilizar uma compilação recomendações ou uma compilação FBT, mas não fornece uma resposta definitiva em casos onde pode utilizar qualquer uma delas. Além disso, mesmo se souber que pretende utilizar um tipo de compilação FBT, ainda poderá escolher **Jaccard** ou **levantar** como a função de semelhança.

É a melhor forma para selecionar entre dois compilações diferentes para testá-las no mundo real (avaliação online) e controlar uma taxa de conversão para diferentes versões. A taxa de conversão poderia medida com base no recomendação cliques, o número real compras do recomendações apresentadas ou mesmo nos montantes de compra real quando as recomendações diferentes foram apresentadas. Poderá selecionar a métrica de taxa de conversão com base no seu objectivo de empresas.

Em alguns casos, poderá querer avaliar o modelo offline antes de colocá-lo de produção. Enquanto avaliação offline não é uma substituição para avaliação online, pode servir uma métrica.

<a name="OfflineEvaluation"></a>
## <a name="offline-evaluation"></a>Avaliação offline  ##

O objetivo de uma avaliação offline é prever precisão (o número de utilizadores que serão adquiridos dos itens recomendados) e a diversidade de recomendações (o número de itens que lhe foram recomendados).
Como parte da avaliação de métricas precisão e diversidade, o sistema de documentos localiza uma amostra de utilizadores e divide as transações para esses utilizadores em dois grupos: o conjunto de dados de formação e o conjunto de dados de teste.

> [AZURE.NOTE]Para utilizar métricas offline, tem de ter selos de tempo nos seus dados de utilização.
> Dados de tempo são necessários para dividir a utilização corretamente entre formação e teste conjuntos de dados.

> Além disso, avaliação offline não poderá lucro resultados para ficheiros de utilização pequenas. Para a avaliação ser aprofundado, deve ser um mínimo de 1.000 pontos a utilização o conjunto de dados de teste.

<a name="Precision"></a>
### <a name="precision-at-k"></a>Precisão na k ###
A tabela seguinte representa o resultado da avaliação precisão na k offline.

| K | 1 | 2 | 3 |   4 |     5
|---|---|---|---|---|---|
|Percentagem |   13.75 | 18.04   | 21 |  24.31 | 26.61
|Utilizadores no teste |    10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Utilizadores considerados | 10.000 |    10.000 |    10.000 |    10.000 |    10.000
|Utilizadores não são considerados | 0 | 0 | 0 | 0 | 0

#### <a name="k"></a>K
Na tabela anterior, *k* representa o número de recomendações apresentado para o cliente. A tabela lê da seguinte forma: "Se durante o período de teste, apenas uma recomendação foi apresentada aos clientes, 13.75 apenas dos utilizadores deverá ter adquirido essa recomendação." Esta declaração de baseia-se partem do princípio de que o modelo foi preparado com dados de compra. Outra forma de diga este é que a precisão em 1 é 13.75.

Vai notar que como mais itens são apresentadas para o cliente, a probabilidade de cliente compra de um item recomendado vai para cima. Para a experiência anterior, a probabilidade quase é duplicado para 26.61 percentagem quando 5 itens são recomendadas.

#### <a name="percentage"></a>Percentagem
A percentagem de utilizadores que os seus serviços contactou com pelo menos um das recomendações *k* é apresentada. A percentagem é calculada dividindo o número de utilizadores que os seus serviços contactou com pelo menos uma recomendação pelo número total de utilizadores considerado. Consulte o artigo utilizadores considerados para obter mais informações.

#### <a name="users-in-test"></a>Utilizadores no teste
Dados esta linha representam o número total de utilizadores no conjunto de dados de teste.

#### <a name="users-considered"></a>Utilizadores considerados
Um utilizador é considerado apenas se o sistema recomendado pelo menos *n* itens com base no modelo de gerado utilizando o conjunto de dados de formação.

#### <a name="users-not-considered"></a>Utilizadores não são considerados
Dados esta linha representam todos os utilizadores não são considerados. Os utilizadores que não recebeu, pelo menos, *k* recomendado itens.

O utilizador não são considerado = utilizadores no teste – utilizadores considerados

<a name="Diversity"></a>
### <a name="diversity"></a>Diversidade ###
Métricas diversidade medem o tipo de itens recomendado. A tabela seguinte representa o resultado da avaliação diversidade offline.

|Balde de percentil |    0-90|  90 99| 99-100
|------------------|--------|-------|---------
|Percentagem        | 34.258 | 55.127| 10.615


Total de itens recomendados: 100,000

Itens exclusivos recomendados: 954

#### <a name="percentile-buckets"></a>Grupos de percentil
Cada balde de percentil é representado por um intervalo (valores mínimos e máximos que o intervalo entre 0 e 100). Os itens perto 100 são os itens mais populares e os itens perto 0 são menos populares. Por exemplo, se o valor de percentagem para o balde de percentil de 99 100 é 10.6, significa que 10.6 percentagem das recomendações devolvido apenas a uma percentagem superior itens mais populares. O valor mínimo de balde de percentil é inclusive e o valor máximo é exclusivo, exceto 100.
#### <a name="unique-items-recommended"></a>Itens exclusivos recomendados
Os itens exclusivos recomendados métrica mostra o número de itens distintos que foram devolvidos para avaliação.
#### <a name="total-items-recommended"></a>Total de itens recomendados
O total de itens recomendado métrica mostra o número de itens recomendado. Algumas poderão ser duplicados.

<a name="ImplementingEvaluation"></a>
### <a name="offline-evaluation-metrics"></a>Métricas de avaliação offline ###
Métricas offline precisão e diversidade poderão ser útil quando selecionar que criar para utilizar. Ao tempo de compilação, como parte dos respetivos FBT ou recomendação crie parâmetros:

-   Defina o parâmetro de compilação *enableModelingInsights* **Verdadeiro**.
-   Em alternativa, selecione o *splitterStrategy* (quer *RandomSplitter* ou *LastEventSplitter*).
*RandomSplitter* divide os dados de utilização no comboio e conjuntos de teste com base na percentagem de teste determinado *randomSplitterParameters* e aleatório propagar valores.
*LastEventSplitter* divide-se os dados de utilização no comboio e testar conjuntos com base na última transação para cada utilizador.

Isto irá acionar uma compilação que utiliza apenas um subconjunto dos dados de cursos de formação e utiliza o resto dos dados para calcular métricas de avaliação.  Após a compilação está concluída, para obter o resultado da avaliação, é necessário chamar a [API de métricas de compilação de obter](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f), prisma os respetivos *modelId* e *buildId*.

 Segue-se a exportação JSON para a avaliação de exemplo.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }
