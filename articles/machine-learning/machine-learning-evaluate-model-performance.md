<properties 
    pageTitle="Avalie o desempenho do modelo em aprender máquina | Microsoft Azure" 
    description="Explica como avalie o desempenho do modelo no Azure máquina aprendizagem." 
    services="machine-learning"
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="bradsev;garye" />


# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>Como avalie o desempenho do modelo no Azure máquina aprendizagem

Este tópico demonstra como avaliar o desempenho de um modelo no Azure máquina aprendizagem Studio e fornece uma breve explicação sobre as métricas disponíveis para esta tarefa. Três cenários comuns de aprendizagem controlados são apresentados: 

* regressão
* classificação binária 
* classificação multiclass

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Avaliar o desempenho de um modelo é um das fases principais no processo de ciências de dados. Indica como bem sucedida pontuação (previsões) de um conjunto de dados foi por um modelo de formação. 

Formação de máquina Azure suporta a avaliação de modelo através de dois dos seu máquina principal módulos de formação: [Modelo de avaliar] [ evaluate-model] e o [Modelo de validar cruzada][cross-validate-model]. Estes módulos permitem-lhe ver como o seu modelo executa em termos de um número de métricas de que são utilizados frequentemente em formação do computador e estatísticas.

##<a name="evaluation-vs-cross-validation"></a>Validação de avaliação vs. Cruz##
Avaliação e validação cruzada são formas padrão de medir o desempenho do seu modelo. Ambas as geram métricas de avaliação que pode inspecionar ou comparar com os outros modelos.

[Avaliar modelo] [ evaluate-model] espera um conjunto de dados visita como entrada (ou 2, no caso que pretende comparar o desempenho de 2 diferentes modelos). Isto significa que necessita preparar o seu modelo de utilizar o [Modelo de formar] [ train-model] módulo e efetuar previsões no algumas conjunto de dados utilizando o [Modelo de pontuação] [ score-model] módulo, antes de pode avaliar os resultados. A avaliação é com base nas etiquetas/probabilidades visita juntamente com as etiquetas verdadeiras, as quais são o resultado pelo [Modelo de pontuação] [ score-model] módulo.

Em alternativa, pode utilizar validação cruzada para executar um número de operações comboio pontuação avaliar (10 dobras) automaticamente no diferentes subconjuntos de dados de entrada. Os dados de entrada são divididos em 10 partes, onde um reservada para testar a ligação, e a outros 9 de cursos de formação. Este processo é repetido 10 vezes e as métricas de avaliação são média. Isto ajuda para determinar bem como um modelo de seria generalizar para conjuntos de dados novos. O [Modelo de validar cruzada] [ cross-validate-model] módulo leva num modelo de às e algumas conjunto de dados com nome e exporta os resultados de avaliação de cada um das 10 dobras, para além dos resultados médio.

Nas secções seguintes, iremos construir modelos de regressão e classificação simples e avaliar o respetivo desempenho, utilizando o [Modelo de avaliar] de[ evaluate-model] e o [Modelo de validar cruzada] [ cross-validate-model] módulos.

##<a name="evaluating-a-regression-model"></a>Avaliação de um modelo de regressão##
Partem do pressuposto que queremos prever preço de um carro utilizando algumas funcionalidades como as dimensões, potência, especificações motor e assim sucessivamente. Este é um problema de regressão típica, onde a variável de destino (*preço*) é um valor numérico contínuo. Vamos pode ajustar um modelo de regressão linear simples que, tendo em conta os valores da funcionalidade de um determinado carro, pode prever o preço desse carro. Este modelo de regressão pode ser utilizado para pontuação o mesmo conjunto de dados que pedimos formação em. Assim que temos os preços previstos para todos os carros, podemos pode avaliar o desempenho do modelo de verificando quanto as previsões desviar-se dos preços reais em média. Para ilustrar isto, utilizamos o *conjunto de dados do automóveis preço dados (matéria-prima)* disponíveis na secção de **Conjuntos de dados guardado** no Azure máquina aprendizagem Studio.
 
###<a name="creating-the-experiment"></a>Criar a experiência###
Adicione os seguintes módulos para a área de trabalho no Azure máquina aprendizagem Studio:

- Dados de automóveis preço (matéria-prima)
- [Regressão linear][linear-regression]
- [Modelo de comboio][train-model]
- [Modelo de pontuação][score-model]
- [Avaliar modelo][evaluate-model]


Ligar as portas, conforme apresentado abaixo na figura 1 e defina a coluna de etiqueta do [Modelo de comboio] [ train-model] módulo ao *preço*.
 
![Avaliação de um modelo de regressão](media/machine-learning-evaluate-model-performance/1.png)

Figura 1. Avaliar a um modelo de regressão.

###<a name="inspecting-the-evaluation-results"></a>Inspecionar os resultados de avaliação###
Depois de executar a experiência, pode clicar na porta de saída do [Modelo de avaliar] [ evaluate-model] módulo e selecione *visualizar* para ver os resultados de avaliação. As avaliação métricas disponíveis para os modelos de regressão são: *Vermelha erro absoluta*, *Raiz vermelha erro absoluta*, *Relativa erro absoluta*, *Relativa de erro ao quadrado*e o *Coeficiente de imagem*.

O termo "erro" aqui representa a diferença entre o valor VERDADEIRO e o valor previsto. O valor absoluto ou o quadrado desta diferença normalmente são processados para capturar a magnitude total de erro ao longo de todas as instâncias, tal como a diferença entre o valor previsto e verdadeiro dever negativa em alguns casos. A métrica do erro medir o desempenho aspeto do Office de um modelo de regressão em termos o desvio da média da suas previsões dos valores verdadeiros. Valores de erro mais baixos significam que o modelo é mais exato no efetuar previsões. Uma métrica do erro geral de 0 significa que o modelo se ajusta dados perfeitamente.

O coeficiente de imagem, o que é também conhecido como R ao quadrado, também é uma forma padrão de medição bem como o modelo se ajusta dados. Pode ser interpretada como a proporção de variação explicada pelo modelo. A mais alto proporção é melhor neste caso, onde 1 indica um ajuste perfeito.
 
![Métricas de avaliação de regressão linear](media/machine-learning-evaluate-model-performance/2.png)

Figura 2. Métricas de avaliação de regressão linear.

###<a name="using-cross-validation"></a>Utilizar cruzada validação###
Como mencionado anteriormente, pode efetuar repetida de formação, pontuação e avaliações automaticamente, utilizando o [Modelo de validar cruzada] [ cross-validate-model] módulo. Tudo o que precisa neste caso é um conjunto de dados, um modelo de às e um [Modelo de validar cruzada] [ cross-validate-model] módulo (ver figura abaixo). Nota que precisa definir a coluna etiqueta ao *preço* no [Modelo de validar cruzada] [ cross-validate-model] propriedades do módulo.

![Publicação em-Validar um modelo de regressão](media/machine-learning-evaluate-model-performance/3.png)

Figura 3. Publicação em-Validar um modelo de regressão.

Depois de executar a experiência, pode inspecionar os resultados de avaliação ao clicar na porta de saída à direita do [Modelo de validar cruzada] [ cross-validate-model] módulo. Isto irá fornecer uma vista detalhada das métricas para cada iteração (Dobra) e os resultados médio de cada um dos métricas (figura 4).
 
![Resultados de validação cruzada de um modelo de regressão](media/machine-learning-evaluate-model-performance/4.png)

Figura 4. Resultados de validação cruzada de um modelo de regressão.

##<a name="evaluating-a-binary-classification-model"></a>Avaliação de um modelo de classificação binário##
Num cenário de classificação binário, a variável de destino tem apenas dois resultados possíveis, por exemplo: {0, 1} ou {false e true}, {positivos, negativos}. Partem do princípio de que é tendo em conta um conjunto de dados dos colaboradores adultos com algumas demográficas e emprego variáveis, e que lhe for pedido para o nível de rendimento, uma variável binário com os valores de previsão {"< = 50K", "> 50K"}. Por outras palavras, a classe negativa representa os empregados que efetuar menor ou igual a 50K por ano e a classe positiva representa todos os outros empregados. Tal como o cenário de regressão, podemos formar um modelo, pontuação alguns dados e, avaliar os resultados. A diferença principal aqui é a escolha de métricas de que fórmula calcula Azure máquina aprendizagem e saídas. Para ilustrar o cenário de previsão nível rendimento, irá utilizamos o conjunto de dados [para adultos](http://archive.ics.uci.edu/ml/datasets/Adult) para criar uma experiência de formação de máquina Azure e avaliar o desempenho de um modelo de regressão logística duas escolares, um classificador binário utilizado mais frequentemente.

###<a name="creating-the-experiment"></a>Criar a experiência###
Adicione os seguintes módulos para a área de trabalho no Azure máquina aprendizagem Studio:

- Conjunto de dados de contagem rendimento binário classificação adultos
- [Duas classe logística regressão][two-class-logistic-regression]
- [Modelo de comboio][train-model]
- [Modelo de pontuação][score-model]
- [Avaliar modelo][evaluate-model]

Ligar as portas, conforme apresentado abaixo na figura 5 e defina a coluna de etiqueta do [Modelo de comboio] [ train-model] módulo *rendimento*.

![Avaliação de um modelo de classificação binário](media/machine-learning-evaluate-model-performance/5.png)

Figura 5. Avaliar a um modelo de classificação binário.

###<a name="inspecting-the-evaluation-results"></a>Inspecionar os resultados de avaliação###
Depois de executar a experiência, pode clicar na porta de saída do [Modelo de avaliar] [ evaluate-model] módulo e selecione *visualizar* para ver os resultados de avaliação (figura 7). As avaliação métricas disponíveis para os modelos de classificação binário são: *precisão*, *precisão*, *recuperar*, *Pontuação F1*e *AUC*. Além disso, o módulo exporta uma matriz de confusões a mostrar o número de positivos verdadeiros, falsos negativos, falsos e negativas verdadeiras, bem como *ROC*, *Precisão/resgate*e *levantar* curvas.

Precisão é simplesmente a proporção de instâncias classificadas corretamente. É normalmente a métrica do primeira que olhar ao avaliar um classificador. No entanto, quando os dados de teste estiver desequilibradas (onde a maior parte das instâncias pertença a uma das categorias), ou estiver mais interessado no desempenho um das classes, precisão não captura realmente a eficácia de um classificador. Cenário de classificação nível rendimento, partem do princípio de que está a testar alguns dados onde 99% das instâncias representam as pessoas que ganhar menor ou igual a 50K por ano. É possível alcançar uma precisão de 0.99 por as previsões a classe "< = 50K" para todas as instâncias. Classificador parece, neste caso, ser efetuar um bom trabalho geral, mas na realidade, falha classificar qualquer um dos indivíduos elevado rendimento (1%) corretamente.

Por que razão, é útil calcular métricas adicionais que capturar aspetos mais específicos da avaliação. Antes de ser entregues os detalhes dessas métricas, é importante compreender a matriz confusões de uma avaliação de classificação binário. As etiquetas de classe do conjunto de formação podem tirar no apenas 2 valores possíveis, normalmente, a referência é feita como positivo ou negativo. As instâncias positivas e negativas que um classificador prevê, com um corretamente são denominadas positivos verdadeiros (TP) e negativas verdadeiras (TN), respetivamente. Da mesma forma, as instâncias incorretamente classificadas são denominadas falsos (FP) e falsos negativos (FN). A matriz confusões é simplesmente uma tabela que mostra o número de ocorrências de se situar em cada uma das seguintes 4 categorias. Formação de máquina Azure automaticamente decidir qual das duas classes no conjunto de dados é a classe de positiva. Se as etiquetas de classe são booleano ou de números inteiros, as instâncias com nome 'true' ou '1' são atribuídas a classe positiva. Se as etiquetas forem cadeias, tal como no caso do conjunto de dados de rendimento, as etiquetas são ordenadas por ordem alfabética e o primeiro nível optado por estar a classe negativa enquanto o segundo nível é a classe positiva.

![Classificação binário confusões matriz](media/machine-learning-evaluate-model-performance/6a.png)

Figura 6. Classificação binário confusões matriz.

Vai novamente para o problema de classificação de rendimento, teria queremos pedir a várias perguntas de avaliação que ajudam-nos compreender o desempenho do classificador utilizado. É uma pergunta muito natural: ' Terminar indivíduos a quem o modelo de previstos para com lucros de ser > 50 K (TP + FP), quantas classificadas corretamente (TP)?' Esta pergunta pode ser respondida verificando a **precisão** do modelo, que é a proporção de positivos são classificados corretamente: TP/(TP+FP). Outra pergunta comum é "terminar a mais alta com lucros de empregados com rendimento > 50 k (TP + FN), quantas classificador classificar corretamente (TP)". Este é realmente a **recuperar**ou a taxa de positiva verdadeira: TP/(TP+FN) do classificador. Poderá reparar em que existe um compromisso óbvio entre precisão e resgate. Por exemplo, tendo em conta um conjunto de dados relativamente desequilibrado, um classificador que prevê, com principalmente positivas instâncias, teria de um resgate alta, mas uma precisão de preferir baixa tantas as instâncias negativas seria mal classificada que resulta num grande número de falsos. Para ver um desenho do como estes dois métricas variam, pode clicar na curva de precisão/RESGATE na página de saída de resultado de avaliação (parte superior esquerda parte da figura 7).

![Resultados de avaliação de classificação binário](media/machine-learning-evaluate-model-performance/7.png) figura 7. Resultados de avaliação de classificação binário.

Outra métrica relacionada que é utilizada frequentemente é o **F1 pontuação**, que entra em precisão e resgate em consideração. É a média harmónica nestas 2 métricas e é calculado como tal: F1 = 2 (resgate precisão x) / (precisão + resgate). Pontuação F1 é uma boa forma para resumir da avaliação de um número único, mas sempre é aconselhável para precisão e resgate em conjunto para compreender melhor como um classificador comporta-se.

Além disso, uma pode inspecionar a taxa positiva verdadeira vs. a taxa falsa positiva na curva **Recetor operativo característica (ROC)** e o valor de **Área na curva (AUC)** correspondente. Quanto mais próximo desta curva ao canto superior esquerdo, melhor desempenho o classificador é (que é maximizar a velocidade de positiva verdadeira enquanto minimizar a taxa falsa positiva). Curvas que estão perto da diagonal do desenho, resultado de classificadores tendência para efetuar previsões que estão perto adivinhar aleatório.

###<a name="using-cross-validation"></a>Utilizar cruzada validação###
Tal como no exemplo de regressão, podemos pode executar validação cruzada repetidamente formar, pontuação e avaliar diferentes subconjuntos dos dados automaticamente. Da mesma forma, podemos pode utilizar o [Modelo de validar cruzada] [ cross-validate-model] módulo, um modelo de regressão logística às e um conjunto de dados. A coluna etiqueta tem de estar definida para *rendimento* no [Modelo de validar cruzada] [ cross-validate-model] propriedades do módulo. Depois de executar a experiência e clicar na porta de saída à direita do [Modelo de validar cruzada] [ cross-validate-model] módulo, é possível ver os valores de métricas de classificação binário para cada dobra, para além para a média e o desvio-padrão de cada. 
 
![Publicação em-Validar um modelo de classificação binário](media/machine-learning-evaluate-model-performance/8.png)

Figura 8. Publicação em-Validar um modelo de classificação binário.

![Resultados de validação cruzada de um classificador binário](media/machine-learning-evaluate-model-performance/9.png)

Figura 9. Resultados de validação cruzada de um classificador binário.

##<a name="evaluating-a-multiclass-classification-model"></a>Avaliação de um modelo de classificação Multiclass##
Nesta experiência utilizamos o popular [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") conjunto de dados que contém as instâncias de 3 tipos diferentes (classes) da planta iris. Existem 4 funcionalidade valores (sepal comprimento/largura e Pétala comprimento/largura) para cada instância. Nas experiências anterior formação e testado os modelos de utilizar os mesma conjuntos de dados. Aqui, vamos utilizar os [Dados divididos] [ split] módulo para criar 2 subconjuntos de dados, dar formação sobre o primeiro e pontuação e avaliar na segunda. O conjunto de dados Iris publicamente disponível no [Repositório de aprendizagem UCI máquina](http://archive.ics.uci.edu/ml/index.html)e pode ser transferido com um [Importar dados] de[ import-data] módulo.

###<a name="creating-the-experiment"></a>Criar a experiência###
Adicione os seguintes módulos para a área de trabalho no Azure máquina aprendizagem Studio:

- [Importar dados][import-data]
- [Multiclass decisão floresta][multiclass-decision-forest]
- [Dividir dados][split]
- [Modelo de comboio][train-model]
- [Modelo de pontuação][score-model]
- [Avaliar modelo][evaluate-model]

Ligue as portas conforme apresentado abaixo na figura 10.

Definir o índice de coluna etiqueta do [Modelo de comboio] [ train-model] módulo a 5. O conjunto de dados tem sem linha de cabeçalho mas recomendamos certeza de que as etiquetas de classe na quinta coluna.

Clique em [Importar dados] [ import-data] módulo e defina a propriedade de *origem de dados* para o *URL da Web através de HTTP*e o *URL* para http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Definir a fração de instâncias a ser utilizado para formação nos [Dados divididos] [ split] módulo (0.7 por exemplo).
 
![Avaliar um classificador Multiclass](media/machine-learning-evaluate-model-performance/10.png)

Figura 10. Avaliar um classificador Multiclass

###<a name="inspecting-the-evaluation-results"></a>Inspecionar os resultados de avaliação###
Execute a experiência e clique na porta de saída de [Modelo de avaliar][evaluate-model]. Os resultados de avaliação são apresentados sob a forma de uma matriz de confusões, neste caso. A matriz mostra o valor real vs. instâncias previstas para todas as 3 classes.
 
![Resultados de avaliação de classificação multiclass](media/machine-learning-evaluate-model-performance/11.png)

Figura 11. Resultados de avaliação de classificação multiclass.

###<a name="using-cross-validation"></a>Utilizar cruzada validação###
Como mencionado anteriormente, pode efetuar repetida de formação, pontuação e avaliações automaticamente, utilizando o [Modelo de validar cruzada] [ cross-validate-model] módulo. Seria necessário um conjunto de dados, um modelo de às e um [Modelo de validar cruzada] [ cross-validate-model] módulo (ver figura abaixo). Novamente, tem de defini a coluna etiqueta do [Modelo de validar cruzada] [ cross-validate-model] módulo (índice de coluna de 5 neste caso). Depois de executar a experiência e clicar em à direita de saída de porta do [Modelo de validar cruzada][cross-validate-model], pode inspecionar os valores métricos para cada dobra, bem como o desvio padrão e médio. Métricas apresentadas aqui são semelhantes para aqueles abordado no caso de classificação binário. No entanto, tenha em atenção que no multiclass classificação, computação a positivos/negativas verdadeiras e falsas positivos/negativas está concluída, contando por classe base em, porque não existe nenhuma classe global positivo ou negativo. Por exemplo, quando computação a precisão ou tenta resgatar a classe de 'Iris setosa', é assumido que está a classe de positiva e todas as outras pessoas como negativo.
 
![Publicação em-Validar um modelo de classificação Multiclass](media/machine-learning-evaluate-model-performance/12.png)

Figura 12. Publicação em-Validar um modelo de classificação Multiclass.


![Resultados de validação cruzada de um modelo de classificação Multiclass](media/machine-learning-evaluate-model-performance/13.png)

Figura 13. Resultados de validação cruzada de um modelo de classificação Multiclass.


<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
 
