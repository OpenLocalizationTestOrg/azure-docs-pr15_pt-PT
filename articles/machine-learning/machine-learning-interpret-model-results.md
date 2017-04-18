<properties
    pageTitle="Interpretar os resultados de modelo em aprender máquina | Microsoft Azure"
    description="Como escolher o parâmetro ideal definido para um algoritmo de utilizar e visualizar os resultados de modelo de classificação."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />


# <a name="interpret-model-results-in-azure-machine-learning"></a>Interpretar os resultados de modelo na formação de máquina do Azure

Este tópico explica como visualizar e interpretar os resultados de previsão no Azure máquina aprendizagem Studio. Depois de ter preparado um modelo e concluído previsões sobre a mesma ("classificados o modelo de"), tem de compreender e interpretar o resultado da previsão.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Existem quatro tipos principais de modelos de aprendizagem do Azure máquina de aprendizagem automática:

* Classificação
* Clusters
* Regressão
* Sistemas de recomendação

Os módulos utilizados para previsão na parte superior destes modelos são:

* [Modelo de pontuação] [ score-model] módulo para a classificação e regressão
* [Atribuir a Clusters] [ assign-to-clusters] módulo para clusters
* [Pontuação Matchbox recomendação] [ score-matchbox-recommender] para sistemas de recomendação

Este documento explica como interpretar os resultados de previsão para cada um destes módulos. Para obter uma descrição geral destes módulos, consulte o artigo [como escolher parâmetros para otimizar o seu algoritmos no Azure máquina aprendizagem](machine-learning-algorithm-parameters-optimize.md).

Este tópico aborda interpretação de previsão mas não avaliação de modelo. Para obter mais informações sobre como avaliar o seu modelo, consulte o artigo [como avalie o desempenho do modelo no Azure máquina aprendizagem](machine-learning-evaluate-model-performance.md).

Se estiver familiarizado com o Azure máquina aprendizagem e precisa de ajuda para criar uma experiência simple para começar a utilizar, consulte o artigo [criar uma experiência simple no Azure máquina aprendizagem Studio](machine-learning-create-experiment.md) no Azure máquina aprendizagem Studio.

## <a name="classification"></a>Classificação ##
Existem duas subcategorias de problemas de classificação:

* Problemas com apenas dois classes (classificação de classe de duas ou binária)
* Problemas com mais de duas classes (classificação de classe com várias)

Formação do Azure máquina tem módulos diferentes para lidar com cada um destes tipos de classificação, mas os métodos para interpretar os respetivos resultados de previsão são semelhantes.

### <a name="two-class-classification"></a>Classificação de duas classe###
**Experiência de exemplo**

Um exemplo de um problema de classificação de classe duas é a classificação de flores iris. A tarefa é classificar flores iris com base nas suas funcionalidades. O conjunto de dados Iris fornecido no Azure máquina formação é um subconjunto do [conjunto de dados Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) populares que contém instâncias de apenas dois flor-de-rio (classes 0 e 1). Existem quatro funcionalidades para cada flor (comprimento sepal, largura sepal, comprimento Pétala e largura Pétala).

![Captura de ecrã da experiência de iris](./media/machine-learning-interpret-model-results/1.png)

Figura 1. Experiência de problema de classificação de classe duas IRIS

Foi realizada uma experiência para resolver este problema, conforme apresentado na figura 1. Um modelo de árvore de classe duas aumentado decisão foi formação e classificado. Agora pode visualizar os resultados de previsão do [Modelo de pontuação] [ score-model] módulo ao clicar na porta de saída do [Modelo de pontuação] [ score-model] módulo e, em seguida, clicando em **visualizar**.

![Módulo de modelo de pontuação](./media/machine-learning-interpret-model-results/1_1.png)

Isto reúne os resultados de pontuação conforme apresentado na figura 2.

![Resultados de experiência de classificação de classe duas iris](./media/machine-learning-interpret-model-results/2.png)

Figura 2. Visualizar um resultado de pontuação modelo de classificação de classe de dois

**Interpretação de resultado**

Existem seis colunas da tabela de resultados. As quatro colunas à esquerda são as funcionalidades de quatro. Para a direita duas colunas classificados etiquetas e classificados probabilidades, são os resultados de previsão. A coluna classificados probabilidades mostra a probabilidade que pertence uma flor para a classe de positiva (classe 1). Por exemplo, o primeiro número a coluna (0.028571) significa que existem é probabilidade 0.028571 que a primeira flor pertence à classe 1. A coluna de etiquetas classificados mostra a classe de previsto para cada flor. Isto é baseado na coluna probabilidades classificados. Se a probabilidade de uma flor visita for maior que 0,5, são previsto como classe 1. Caso contrário, são previsto como classe 0.

**Publicação de serviço Web**

Depois dos resultados de previsão foram compreendidos e avaliados som, a experiência pode ser publicada como um serviço web para que possa implementá-los em várias aplicações e chame-obter previsões de aula em qualquer flor iris novo. Para saber como alterar uma experiência de formação para uma experiência de pontuação e publicá-lo como um serviço web, consulte [publicar o serviço web de aprendizagem do Azure máquina](machine-learning-walkthrough-5-publish-web-service.md). Este procedimento fornece uma experiência de pontuação conforme apresentado na figura 3.

![Captura de ecrã da experiência de pontuação](./media/machine-learning-interpret-model-results/3.png)

Figura 3. A experiência de problema de classificação de classe duas iris de pontuação

Agora, precisa de configurar a entrada e saída para o serviço web. A entrada é a porta de entrada à direita do [Modelo de pontuação][score-model], qual é a flor Iris funcionalidades de entrada. A opção do resultado depende se se estiver interessado na classe prevista (visita etiqueta), a probabilidade de visita ou ambas. Neste exemplo, é assumido que se estiver interessado em ambos. Para selecionar as colunas de saída pretendido, utilize um [Selecionar colunas de conjunto de dados] [ select-columns] módulo. Clique em [Selecionar colunas de conjunto de dados][select-columns], clique em **Iniciação selector de coluna**e selecione **Classificados etiquetas** e **Probabilidades classificados**. Depois de configurar a porta de saída de [Selecionar colunas de conjunto de dados] [ select-columns] e executá-la novamente, deverá pronto para publicar a experiência de pontuação como um serviço web ao clicar em **Publicar serviço WEB**. A experiência final aspeto figura 4.

![A experiência de classificação de classe duas iris](./media/machine-learning-interpret-model-results/4.png)

Figura 4. Experiência de pontuação final de um problema de classificação de classe duas iris

Depois de executar o serviço web e introduza alguns valores de funcionalidade de uma instância de teste, o resultado devolve dois números. O número da primeira é a etiqueta de visita e o segundo é a probabilidade de visita. Este flor é previsto como 1 de classe com probabilidade 0.9655.

![Modelo de pontuação interpretar de teste](./media/machine-learning-interpret-model-results/4_1.png)

![Os resultados dos testes de pontuação](./media/machine-learning-interpret-model-results/5.png)

Figura 5. Resultado de serviço Web de classificação de classe duas iris

### <a name="multi-class-classification"></a>Classificação de classe com várias
**Experiência de exemplo**

Nesta experiência, executar uma tarefa de reconhecimento de letra como um exemplo de classificação multiclass. O classificador tenta prever uma letra específica (classe) com base em alguns valores de escrita à mão atributo extraídas as imagens de escrita à mão.

![Exemplo de reconhecimento de carta](./media/machine-learning-interpret-model-results/5_1.png)

Os dados de formação, existem 16 funcionalidades extraídas de imagens de escrita à mão letra. As 26 letras formam os nossos 26 classes. Figura 6 mostra uma experiência que irá formar um modelo de classificação multiclass para o reconhecimento de carta e prever a mesma funcionalidade configurar num conjunto de dados de teste.

![Experiência de classificação multiclass de reconhecimento de carta](./media/machine-learning-interpret-model-results/6.png)

Figura 6. Experiência de problema de classificação multiclass de reconhecimento de carta

Visualizar os resultados do [Modelo de pontuação] [ score-model] módulo ao clicar na porta de saída de [Modelo de pontuação] [ score-model] módulo e, em seguida, clicando em **visualizar**, deverá ver conteúdo conforme apresentado na figura 7.

![Resultados de modelo de pontuação](./media/machine-learning-interpret-model-results/7.png)

Figura 7. Visualizar os resultados do modelo de pontuação numa classificação de classe com várias

**Interpretação de resultado**

As colunas de 16 para a esquerda representam os valores de funcionalidade do conjunto de teste. As colunas com nomes como classificados probabilidades para a classe "XX" estão apenas como a coluna classificados probabilidades no caso de classe de duas. Que sejam a probabilidade a entrada correspondente determinado para uma determinada categoria. Por exemplo, para a primeira entrada, existe probabilidade 0.003571 que é uma "A," probabilidade 0.000451 que é um "B" e assim sucessivamente. Na última coluna (classificados etiquetas) é igual classificados etiquetas no caso de classe de duas. Seleciona a classe com a maior probabilidade visita como a classe prevista da entrada correspondente. Por exemplo, para a primeira entrada, a visita etiqueta é "F" uma vez que tem a maior probabilidade de ser um "F" (0.916995).

**Publicação de serviço Web**

Também pode aceder a etiqueta de visita para cada entrada e a probabilidade da etiqueta de visita. A lógica básica é localizar a maior probabilidade entre as probabilidades visita. Para executar esta tarefa, tem de utilizar o [Script de R executar] [ execute-r-script] módulo. O código de R é apresentado na figura 8 e o resultado da experiência é apresentado na figura 9.

![Exemplo de código R](./media/machine-learning-interpret-model-results/8.png)

Figura 8. Código de R para extrair classificados etiquetas e associadas probabilidades de etiquetas

![Resultado de experiência](./media/machine-learning-interpret-model-results/9.png)

Figura 9. Experiência de pontuação final do problema de classificação multiclass reconhecimento de carta

Depois de publicar e execute o serviço web e introduza alguns valores de entrada de funcionalidade, o resultado devolvido parece figura 10. Esta carta escrita à mão, com as suas 16 funcionalidades extraídas, é prevista para ser "T" com probabilidade 0.9715.

![Módulo de pontuação interpretar de teste](./media/machine-learning-interpret-model-results/9_1.png)

![Resultado de teste](./media/machine-learning-interpret-model-results/10.png)

Figura 10. Resultado de serviço Web de classificação multiclass

## <a name="regression"></a>Regressão

Problemas de regressão são diferentes dos problemas de classificação. Um problema de classificação, que está a tentar prever descontínuos classes, tal como que classe uma flor iris pertence. Mas como pode ver no exemplo seguinte de um problema de regressão, que está a tentar prever uma variável contínua, tal como o preço de um carro.

**Experiência de exemplo**

Utilize predição do preço automóveis como o exemplo para regressão. Está a tentar prever o preço de um carro com base nas suas funcionalidades, incluindo efetuar, tipo de combustível, tipo de corpo e roda de unidade. A experiência é apresentada na figura 11.

![Experiência de regressão automóveis preço](./media/machine-learning-interpret-model-results/11.png)

Figura 11. Experiência de problema de regressão automóveis preço

Visualizar o [Modelo de pontuação] [ score-model] módulo, o resultado aspeto figura 12.

![Pontuação resultados para o problema de previsão de automóveis preço](./media/machine-learning-interpret-model-results/12.png)

Figura 12. Resultado de pontuação para o problema de previsão automóveis preço

**Interpretação de resultado**

Etiquetas visita é a coluna resultado neste resultado pontuação. Os números são o preço previsto para cada carro.

**Publicação de serviço Web**

Pode publicar a experiência de regressão para um serviço web e chame-o de previsão de automóveis preço da mesma forma, tal como no caso de utilização de classificação de classe de duas.

![Experiência de pontuação para problema do preço automóveis regressão](./media/machine-learning-interpret-model-results/13.png)

Figura 13. Experiência de um problema de regressão automóveis preço de pontuação

Executar o serviço web, o resultado devolvido aspeto figura 14. O preço previsto para este carro é $15,085.52.

![Módulo de pontuação interpretar de teste](./media/machine-learning-interpret-model-results/13_1.png)

![Resultados de módulo de pontuação](./media/machine-learning-interpret-model-results/14.png)

Figura 14. Resultado de serviço Web de um problema de regressão automóveis preço

## <a name="clustering"></a>Clusters

**Experiência de exemplo**

Vamos utilizar o conjunto de dados Iris novamente para criar uma experiência clusters. Aqui pode filtrar saída as etiquetas de classe no conjunto de dados para que apenas tem funcionalidades e podem ser utilizado para clusters. Este iris utilizar maiúsculas/minúsculas, especifique o número de clusters para ser dois durante o processo de formação, o que significa que iria cluster de flores em duas categorias. A experiência é apresentada na figura 15.

![Problema clusters IRIS experimentar](./media/machine-learning-interpret-model-results/15.png)

Figura 15. Problema clusters IRIS experimentar

Clusters difere da classificação no conjunto de dados de formação não têm rótulos de terra verdade por si. Grupos clusters as instâncias do conjunto de dados de formação em clusters distintos. Durante o processo de formação, o modelo de etiquetas as entradas pelas diferenças entre as suas funcionalidades de aprendizagem. Após esta ação, o modelo de formação pode ser utilizado para ainda mais classificar nas entradas futuras. Existem duas partes do resultado que podemos estiver interessados em dentro de um problema clusters. A primeira parte é rotular o conjunto de dados de formação e o segundo está a classificar um novo conjunto de dados com o modelo de formação.

A primeira parte do resultado pode ser visualizada ao clicar na porta de saída para a esquerda de [Modelo de clusters comboio] [ train-clustering-model] e, em seguida, clicando em **visualizar**. A visualização é apresentada na figura 16.

![Resultado clusters](./media/machine-learning-interpret-model-results/16.png)

Figura 16. Visualizar clusters resultado para o conjunto de dados de formação

O resultado da segunda parte, clusters novas entradas com o modelo clusters qualificado, é apresentado na figura 17.

![Visualizar clusters resultado](./media/machine-learning-interpret-model-results/17.png)

Figura 17. Visualizar clusters resultado num novo conjunto de dados

**Interpretação de resultado**

Apesar dos resultados de duas partes haste de experiência diferentes fases, eles apresentar o mesmo aspeto e são interpretados da mesma forma. As primeiras quatro colunas são funcionalidades. Na última coluna, atribuições, é o resultado da previsão. As entradas atribuídas o mesmo número são previstas para ser no mesmo cluster, ou seja, partilharem semelhanças algumas forma (esta experiência utiliza a métrica do Euclidean distância predefinida). Uma vez que especificou o número de clusters para ser 2, as entradas atribuições estão identificados como 0 ou 1.

**Publicação de serviço Web**

Pode publicar a experiência clusters para um serviço web e chame-o para clusters previsões caso de utilização da mesma forma, tal como a classificação de classe de duas.

![Experiência de pontuação para problema clusters iris](./media/machine-learning-interpret-model-results/18.png)

Figura 18. Experiência de um problema de clusters iris de pontuação

Depois de executar o serviço web, o resultado devolvido aspeto figura 19. Este flor é previsto para ser num cluster de 0.

![Teste interpretar módulo pontuação](./media/machine-learning-interpret-model-results/18_1.png)

![Resultado do módulo de pontuação](./media/machine-learning-interpret-model-results/19.png)

Figura 19. Resultado de serviço Web de classificação de classe duas iris

## <a name="recommender-system"></a>Sistema de recomendação
**Experiência de exemplo**

Para os sistemas de recomendação, pode utilizar o problema de recomendação restaurante como um exemplo: pode recomendar restaurantes para clientes com base no respetivo histórico de classificação. Os dados de entrada é constituído por três partes:

* Classificações de restaurante de clientes
* Dados de funcionalidade do cliente
* Dados da funcionalidade de restaurante

Existem várias coisas que pode fazer com de [Comboio Matchbox recomendação] [ train-matchbox-recommender] módulo no Azure máquina formação:

- Prever classificações para um determinado utilizador e um item
- Recomendamos itens para um determinado utilizador
- Localizar utilizadores relacionados com um determinado utilizador
- Localizar itens relacionados a um determinado item

Pode escolher o pretende fazer ao selecionar a partir das quatro opções no menu **tipo de previsão recomendação** . Aqui pode percorrer todas as quatro cenários.

![Recomendação matchbox](./media/machine-learning-interpret-model-results/19_1.png)

Uma experiência de aprendizagem do Azure máquina típica para um sistema de recomendação aspeto figura 20. Para obter informações sobre como utilizar esses módulos do sistema de recomendação, consulte o artigo [Recomendação de matchbox comboio] [ train-matchbox-recommender] e [Recomendação de matchbox pontuação][score-matchbox-recommender].

![Experiência de sistema de recomendação](./media/machine-learning-interpret-model-results/20.png)

Figura 20. Experiência de sistema de recomendação

**Interpretação de resultado**

**Prever classificações para um determinado utilizador e um item**

Ao selecionar a **Previsão de classificação** em **tipo de previsão de recomendação**, são perguntar o sistema de recomendação para prever a classificação de um determinado utilizador e um item. A visualização dos de [Pontuação Matchbox recomendação] [ score-matchbox-recommender] saída aspeto figura 21.

![Resultado do sistema recomendação – previsão de classificação de pontuação](./media/machine-learning-interpret-model-results/21.png)

Figura 21. Visualizar o resultado de pontuação do sistema recomendação – previsão de classificação

As primeiras duas colunas são os pares de item de utilizador fornecidos pelos dados de entrada. A terceira coluna é a classificação prevista de um utilizador para um determinado tipo de item. Por exemplo, na primeira linha, o cliente U1048 é previsto para restaurante taxa 135026 como 2.

**Recomendamos itens para um determinado utilizador**

Ao selecionar o **Item recomendação** em **tipo de previsão de recomendação**, apresentamos o sistema de recomendação recomendar itens para um determinado utilizador. Para escolher neste cenário, o último parâmetro é *recomendado item seleção*. A opção **a partir da classificados de itens (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de formação. Nesta fase de previsão, vamos escolher **a partir de todos os itens**. A visualização dos de [Pontuação Matchbox recomendação] [ score-matchbox-recommender] saída aspeto figura 22.

![Resultado do sistema de recomendação – recomendação de item de pontuação](./media/machine-learning-interpret-model-results/22.png)

Figura 22. Visualizar o resultado de pontuação do sistema recomendação – recomendação de item

O primeiro das seis colunas representa o utilizador determinado IDs recomendar itens, tal como fornecido pelos dados de entrada. As outras cinco colunas representam os itens recomenda-se o utilizador por ordem descendente de relevância. Por exemplo, na primeira linha, o restaurante mais recomendado para o cliente U1048 é 134986, seguido de 135018, 134975, 135021 e 132862.

**Localizar utilizadores relacionados com um determinado utilizador**

Ao selecionar **Utilizadores relacionados** em **tipo de previsão de recomendação**, apresentamos o sistema de recomendação para encontrar utilizadores relacionados a um determinado utilizador. Utilizadores relacionados são os utilizadores que têm preferências semelhantes. O último parâmetro para escolher neste cenário é *selecção de utilizador em questão*. A opção **De utilizadores que classificados itens (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de formação. Escolha **a partir de todos os utilizadores** para nesta fase de previsão. A visualização dos de [Pontuação Matchbox recomendação] [ score-matchbox-recommender] saída aspeto figura 23.

![Resultado de pontuação do sistema de recomendação – utilizadores relacionados](./media/machine-learning-interpret-model-results/23.png)

Figura 23. Visualizar os resultados de pontuação do sistema recomendação – utilizadores relacionados

O primeiro das seis colunas mostra o utilizador determinado que IDs necessário para encontrar utilizadores relacionados, tal como fornecido pela dados de entrada. As outras cinco colunas armazenam os utilizadores relacionados previstos do utilizador por ordem descendente de relevância. Por exemplo, na primeira linha, o cliente são mais relevante para o cliente U1048 é U1051, seguido de U1066, U1044, U1017 e U1072.

**Localizar itens relacionados a um determinado item**

Ao selecionar **Itens relacionados** em **tipo de previsão de recomendação**, são perguntar o sistema de recomendação para localizar itens relacionados a um determinado item. Itens relacionados são os itens mais provável que gostou pelo mesmo utilizador. Para escolher neste cenário, o último parâmetro é *selecção de item relacionado*. A opção **a partir da classificados de itens (para avaliação de modelo)** é principalmente para avaliação de modelo durante o processo de formação. Vamos escolher **a partir de todos os itens** para nesta fase de previsão. A visualização dos de [Pontuação Matchbox recomendação] [ score-matchbox-recommender] saída aspeto figura 24.

![Resultado de pontuação do sistema de recomendação – itens relacionados](./media/machine-learning-interpret-model-results/24.png)

Figura 24. Visualizar os resultados de pontuação do sistema recomendação – itens relacionados

O primeiro das seis colunas representa o item determinado IDs necessários para localizar itens relacionados, tal como fornecido pelos dados de entrada. As outras cinco colunas armazenam os itens relacionados previstos do item por ordem em termos de relevância descendente. Por exemplo, na primeira linha, o item mais relevante para o item 135026 é 135074, seguido de 135035, 132875, 135055 e 134992.

**Publicação de serviço Web**

O processo de publicar estes experiências como serviços web para obter previsões é semelhante para cada uma das quatro cenários. Aqui vamos pegar em segundo cenário (recomendar itens para um determinado utilizador) como um exemplo. Pode seguir o mesmo procedimento com as outras três.

Guardar o sistema de recomendação qualificados como um modelo de formação e filtrar os dados de entrada a uma coluna de ID do utilizador única conforme pedido, pode ligar a experiência vistos figura 25 e publicá-lo como um serviço web.

![Experiência do problema de recomendação restaurante de pontuação](./media/machine-learning-interpret-model-results/25.png)

Figura 25. Experiência do problema de recomendação restaurante de pontuação

Executar o serviço web, o resultado devolvido aspeto figura 26. Os cinco restaurantes recomendadas para o utilizador U1048 são 134986, 135018, 134975, 135021 e 132862.

![Exemplo de serviço de recomendação do sistema](./media/machine-learning-interpret-model-results/25_1.png)

![Resultados da experiência de exemplo](./media/machine-learning-interpret-model-results/26.png)

Figura 26. Resultado de serviço Web de problema de recomendação restaurante


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
