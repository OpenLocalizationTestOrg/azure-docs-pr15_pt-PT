<properties
    pageTitle="Escolha os parâmetros para optimizar os algoritmos na aprendizagem de máquinas Azure | Microsoft Azure"
    description="Explica como escolher o parâmetro ideal definido para um algoritmo de aprendizagem de máquinas Azure."
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


# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Escolha os parâmetros para optimizar os algoritmos na aprendizagem de máquinas Azure

Este tópico descreve como escolher o hyperparameter direito definido para um algoritmo de aprendizagem de máquinas Azure. A maior parte dos algoritmos de aprendizagem da máquina tem parâmetros a definir. Quando preparar um modelo, tem de fornecer valores para esses parâmetros. A eficácia do modelo formada depende os parâmetros de modelo que escolher. O processo de localizar o conjunto de parâmetros ideal denomina-se a *selecção do modelo*.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Existem várias formas para modelar a selecção. Na aprendizagem de máquinas, validação cruzada é um dos métodos mais amplamente utilizados para a selecção de modelo e é o mecanismo de selecção de modelo predefinido em Azure máquina saber. Uma vez que suporta aprendizagem de máquinas Azure R e o Python, pode implementar sempre seus próprios mecanismos de selecção de modelo utilizando um R ou o Python.

Existem quatro passos no processo de encontrar o melhor conjunto de parâmetro:

1.  **Definir o espaço de parâmetro**: para o algoritmo, decida primeiro os valores de parâmetro exacta que pretende considerar.
2.  **Definições de definir a validação cruzada**: decidir como escolher dobras de validação cruzada para o conjunto de dados.
3.  **Definir a métrica**: decidir qual métrica a utilizar para determinar o melhor conjunto de parâmetros, tais como a exactidão, a média de raiz ao quadrado erro, precisão, recuperar ou f-pontuação.
4.  **Comboio, avaliar e comparar**: para cada combinação única dos valores dos parâmetros, validação cruzada é efectuada por e baseia a métrica de erro que definir. Depois de avaliação e comparação, pode escolher o modelo de melhor desempenho.

A seguinte imagem ilustra mostra como este pode ser alcançado na aprendizagem de máquinas Azure.

![Localizar o melhor conjunto de parâmetro](./media/machine-learning-algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir o espaço de parâmetro
Pode definir o parâmetro definido no passo de inicialização de modelo. O painel de parâmetros de todos os algoritmos de aprendizagem da máquina tem dois modos de formador: *Parâmetro único* e *Intervalo de parâmetro*. Escolha o modo de intervalo de parâmetro. No modo de intervalo de parâmetro, pode introduzir vários valores para cada parâmetro. Pode introduzir valores separados por vírgula na caixa de texto.

![Árvore de decisão de aumentado de dois classe, único parâmetro](./media/machine-learning-algorithm-parameters-optimize/fig2.png)

 Em alternativa, pode definir os pontos máximos e mínimos de grelha e o número total de pontos a serem gerados com o **Construtor de intervalo de utilização**. Por predefinição, os valores dos parâmetros são gerados numa escala linear. Mas se estiver seleccionada a **Escala logarítmica** , os valores são gerados na escala do registo (ou seja, a relação entre os pontos adjacentes for constante em vez de sua diferença). Para os parâmetros de número inteiro, pode definir um intervalo utilizando um hífen. Por exemplo, "1-10" meios de que todos os números inteiros entre 1 e 10 (ambos inclusive) formam o conjunto de parâmetro. Também é suportado o modo misto. Por exemplo, o parâmetro definido "1-10, 20, 50" incluiria números inteiros 1-10, 20. o e 50.

![Árvore de decisão de aumentado de dois classe, o intervalo de parâmetro](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir validação cruzada dobras
A [partição e amostra] [ partition-and-sample] módulo pode ser utilizado para atribuir aleatoriamente dobras aos dados. A seguinte configuração de exemplo para o módulo, vamos definir cinco dobras e aleatoriamente atribuir um número de dobra as instâncias de amostra.

![Partição e exemplo](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## <a name="define-the-metric"></a>Defina a métrica
A [Optimizar o modelo de Hyperparameters] [ tune-model-hyperparameters] módulo fornece suporte para modo empírico escolher o melhor conjunto de parâmetros para um determinado algoritmo e dataset. Para além de outras informações relativas à formação do modelo, o painel de **Propriedades** do presente módulo inclui a métrica para determinar o melhor conjunto de parâmetro. Tem duas caixas de lista pendente diferente para algoritmos de classificação e de regressão, respectivamente. Se o algoritmo em causa é um algoritmo de classificação, a métrica de regressão é ignorada e vice versa. Neste exemplo específico, a métrica é de **precisão**.   

![Parâmetros de varrimento](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Preparar, avaliar e comparar  
O mesmo [Sintonizar o modelo de Hyperparameters] [ tune-model-hyperparameters] módulo prepara todos os modelos que correspondem ao conjunto de parâmetros, avalia métricas diferentes e, em seguida, cria o modelo mais bem formado com base na métrica que escolher. Este módulo tem duas entradas obrigatórias:

* O aprendiz às
* O conjunto de dados

O módulo também tem um conjunto de dados opcional de entrada. Ligar o conjunto de dados com dobra de informações para a entrada de conjunto de dados obrigatórios. Se o conjunto de dados não estiver atribuído a quaisquer informações de dobragem, em seguida, uma validação cruzada 10-fold é executada automaticamente por predefinição. Se não é efectuada a atribuição de dobra e é fornecido um conjunto de dados de validação do porto de um conjunto de dados opcionais, é escolhido de um modo de teste do comboio e o primeiro conjunto de dados é utilizado para preparar o modelo para cada combinação de parâmetros.

![Classificador de árvore de decisão aumentada](./media/machine-learning-algorithm-parameters-optimize/fig6a.png)

O modelo, em seguida, é avaliado num conjunto de dados de validação. A porta de saída para a esquerda do módulo mostra métricas diferentes como funções de valores de parâmetros. A porta de saída direito dá formado modelo correspondente ao modelo que melhor efectuar em conformidade com a métrica escolhida (**precisão** neste caso).  

![Conjunto de dados de validação](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)

Pode ver os parâmetros exactos escolhidos pelo visualização a porta de saída à direita. Este modelo pode ser utilizado um conjunto de ensaio de pontuação ou num serviço web operacionalizada depois de guardar como um modelo de formação.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
