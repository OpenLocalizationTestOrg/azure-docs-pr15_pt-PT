<properties 
    pageTitle="Depurar o seu modelo em aprender máquina Azure | Microsoft Azure" 
    description="Explica como como depurar o seu modelo no Azure máquina aprendizagem." 
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
    ms.date="09/09/2016" 
    ms.author="bradsev;garye" />

# <a name="debug-your-model-in-azure-machine-learning"></a>Depurar o seu modelo em aprender máquina Azure

Este artigo explica como depurar os modelos de formação do Microsoft Azure máquina. Especificamente, cobrir motivos possíveis porque é que um dos seguintes dois cenários de falha poderá deparar ao executar um modelo de:

* o [Modelo de comboio] [ train-model] módulo emitir um erro 
* o [Modelo de pontuação] [ score-model] módulo produz resultados incorretos 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-throws-an-error"></a>Módulo de modelo comboio emitir um erro

![image1](./media/machine-learning-debug-models/train_model-1.png)

O [Modelo de comboio] [ train-model] módulo espera as seguintes 2 entradas:

1. O tipo de modelo de classificação/regressão da coleção de modelos fornecida pela Azure máquina aprendizagem
2. Os dados de formação com uma coluna etiqueta especificada. A coluna etiqueta Especifica a variável de prever. O resto das colunas incluídas são considerada funcionalidades.

Este módulo emitir um erro nos casos seguintes:

1. A coluna etiqueta é especificada incorretamente porque mais de uma coluna está selecionada como etiqueta ou um índice de coluna incorretos está seleccionado. Por exemplo, o segundo caso seria aplicável se um índice de coluna de 30 foi utilizado com um conjunto de dados de entrada que tinha apenas 25 colunas.

2. O conjunto de dados não contém quaisquer colunas de funcionalidade. Por exemplo, se o conjunto de dados de entrada tem apenas 1 coluna, o que é assinalada como a coluna etiqueta, existem seria não existirem funcionalidades com a qual pretende criar o modelo. Neste caso, o [Modelo de comboio] [ train-model] módulo irá gerar um erro.

3. O conjunto de dados entrado (funcionalidades ou etiqueta) contêm infinito como um valor.


## <a name="score-model-module-does-not-produce-correct-results"></a>Módulo de modelo de pontuação não produzir os resultados corretos

![image2](./media/machine-learning-debug-models/train_test-2.png)

Num gráfico formação/testes típico para controlado de formação, os [Dados divididos] [ split] módulo divide o conjunto de dados original em duas partes: a peça que é utilizada para formar o modelo e a peça que está reservada a pontuação bem como o modelo de formação efetua com base em dados-não formar no. O modelo de formação, em seguida, é utilizado para os dados de teste após o qual os resultados são avaliados para determinar a precisão do modelo de pontuação.

O [Modelo de pontuação] [ score-model] módulo requer duas entradas:

1. Uma saída qualificados modelo a partir do [Modelo de comboio] [ train-model] módulo
2. Um conjunto de dados pontuação não que o modelo não foi formação em

Pode acontecer que, apesar da experiência é concluída com êxito, o [Modelo de pontuação] [ score-model] módulo produz resultados incorretos. Existem vários cenários podem fazer com que isto acontecer:

1. Se a etiqueta especificada é categorias e um modelo de regressão é formação nos dados, uma impressão incorreta seria ser produzido pelo do [Modelo de pontuação] [ score-model] módulo. Isto acontece porque regressão requer uma variável de resposta contínua. Neste caso deve ser mais adequada utilizar um modelo de classificação. 
2. Da mesma forma, se um modelo de classificação é formação num conjunto de dados está a ter números de vírgula flutuante na coluna etiqueta, pode produzir resultados indesejáveis. Isto acontece porque classificação requer uma variável de resposta descontínuos que permite apenas valores nesse intervalo através de um conjunto de classes finito e normalmente algo pequeno.
3. Se o conjunto de dados pontuação não contém todas as funcionalidades utilizadas para formar o modelo, o [Modelo de pontuação] [ score-model] vão produzir um erro.
4. O [Modelo de pontuação] [ score-model] não seria produzir qualquer saída correspondente a uma linha no conjunto de dados pontuação que contém um valor em falta ou um valor infinito para qualquer uma das suas funcionalidades.
5. O [Modelo de pontuação] [ score-model] pode produzir idênticas saídas para todas as linhas de pontuação o conjunto de dados. Isto pode acontecer, por exemplo, no quando tentar classificação através de decisão florestas se o número mínimo de amostras por nó folha optado por ser mais do que o número de exemplos de formação disponíveis.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 
