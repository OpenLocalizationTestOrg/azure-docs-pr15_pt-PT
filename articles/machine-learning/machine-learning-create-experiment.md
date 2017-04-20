<properties
    pageTitle="Uma experiência simple na máquina aprendizagem Studio | Microsoft Azure"
    description="Este tutorial de aprendizagem máquina orienta-o através de uma experiência de ciência fácil de dados. A Microsoft vai prever o preço de um automóvel utilizando um algoritmo de regressão."
    keywords="experiências, a regressão linear, os algoritmos de aprendizagem de máquina, iniciação de aprendizagem de máquina, técnicas de previsão de modelação, experiência de ciências de dados"
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
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="garye"/>

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Iniciação de aprendizagem de máquinas: criar a primeira experiência de ciência de dados no Azure máquina aprendizagem Studio

Este tutorial de aprendizagem máquina orienta-o através de uma experiência de ciência fácil de dados. Vamos criar um modelo de regressão linear, que prevê o preço de um automóvel com base nas variáveis diferentes tais como efectuar e as especificações técnicas. Para tal, utilizaremos Azure máquina aprendizagem Studio para desenvolver e iterar num simples preditiva analytics experiências.

*Analytics previsão* é um tipo de ciências de dados que utiliza dados actuais para prever futuros resultados. Para obter um exemplo muito simple de previsão analytics, veja dados ciência para principiantes 4 vídeo: [prever uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (tempo de execução: 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Como ajuda Studio de aprendizagem do computador?

Máquina aprendizagem Studio torna mais fácil configurar uma experiência utilizando módulos de arrastar e largar pré-programados com técnicas de previsão de modelação. Para executar a experiência e prever uma resposta, vai utilizar máquina aprendizagem Studio para *criar um modelo*, *o modelo do comboio*e *pontuação e teste o modelo*.

Introduza Studio de aprendizagem do computador: [https://studio.azureml.net](https://studio.azureml.net). Se tiver iniciado sessão na máquina aprendizagem Studio antes, clique em **Iniciar sessão aqui**. Caso contrário, clique em **Inscrever-se** e escolha entre opções livres e pagas.

Para mais informações gerais sobre Studio de aprendizagem do computador, consulte [o que é o computador aprendizagem Studio?](machine-learning-what-is-ml-studio.md)

## <a name="five-steps-to-create-an-experiment"></a>Cinco passos para criar uma experiência

Nesta máquina aprendizagem tutorial, irá siga cinco passos básicos para criar uma experiência na máquina de aprendizagem Studio para criar, comboio e o modelo de pontuação:

- Criar um modelo
    - [Passo 1: Dados de obter]
    - [Passo 2: Pré-processamento dados]
    - [Passo 3: Definir funcionalidades]
- O modelo do comboio
    - [Passo 4: Escolher e aplicar um algoritmo de aprendizagem]
- Pontuação e testar o modelo
    - [Passo 5: Preços de automóveis novos de previsão]

[Passo 1: Dados de obter]: #step-1-get-data
[Passo 2: Pré-processamento dados]: #step-2-preprocess-data
[Passo 3: Definir funcionalidades]: #step-3-define-features
[Passo 4: Escolher e aplicar um algoritmo de aprendizagem]: #step-4-choose-and-apply-a-learning-algorithm
[Passo 5: Preços de automóveis novos de previsão]: #step-5-predict-new-automobile-prices


## <a name="step-1-get-data"></a>Passo 1: Dados de obter

Existem um número de conjuntos de dados de exemplo incluídos com o computador aprendizagem Studio que pode escolher e pode importar dados a partir de várias origens. Neste exemplo, utilizaremos o conjunto de dados de exemplo incluída, **dados a preços automóvel (Raw)**.
Este dataset inclui entradas para um número de automóveis individuais, incluindo informações como marca, modelo, as especificações técnicas e preço.

1. Iniciar uma nova experiência clicando **+ Novo** na parte inferior da janela da máquina aprendizagem Studio, seleccione a **EXPERIMENTAR**e, em seguida, seleccione a **Experiências em branco**. Seleccione o nome de experiência predefinido na parte superior da tela de e mude-o para um com mais significado, por exemplo, a **previsão de preço do automóvel**.

2. Para a esquerda da experiência tela é uma paleta de conjuntos de dados e módulos. Tipo **automóvel** na caixa de pesquisa no topo da paleta para localizar o conjunto de dados identificados como **dados de preços do automóvel (Raw)**.

    ![Procura de paleta][screen1a]

3. Arraste o conjunto de dados para a tela de experiência.

    ![Conjunto de dados][screen1]

Para ver o aspecto de dados, clique na porta de saída na parte inferior do dataset automóvel e, em seguida, seleccione **visualizar**.

![Porta de saída do módulo][screen1c]

As variáveis no conjunto de dados apareçam como colunas e cada instância de um automóvel aparece como uma linha. A coluna da extremidade direita (coluna 26 e com o título "preço") é a variável-alvo, vamos tentar prever.

![Conjunto de dados visualização][screen1b]

Feche a janela de visualização clicando no "**x**" no canto superior direito.

## <a name="step-2-preprocess-data"></a>Passo 2: Pré-processamento dados

Um conjunto de dados requer normalmente alguns pré-processamento antes de pode ser analisada. Poderá ter reparado os valores em falta presentes nas colunas de várias linhas. Estes valores em falta necessitam de limpeza para que o modelo pode analisar os dados correctamente. No nosso caso, iremos remover quaisquer linhas que têm valores em falta. Além disso, a coluna de **perdas normalizada** tem uma grande fracção de valores em falta, pelo que iremos irá excluir totalmente nessa coluna do modelo.

> [AZURE.TIP] Limpar os valores em falta de dados de entrada é um pré-requisito para utilizar a maior parte dos módulos.

Primeiro Iremos remover a coluna de **perdas normalizado** e, em seguida, iremos remover todas as linhas que tem dados em falta.

1. Escreva **Seleccionar colunas** na caixa de procura na parte superior da paleta de módulo para o [Seleccionar colunas no conjunto de dados] de localização[ select-columns] módulo, em seguida, arraste-o para a experiência da tela e ligá-lo para a porta de saída do dataset **dados a preços automóvel (Raw)** . Este módulo permite-nos seleccionar as colunas de dados que deseja incluir ou excluir no modelo.

2. Seleccione as [Seleccionar colunas no conjunto de dados] [ select-columns] módulo e clique em **Iniciar o selector de colunas** no painel **Propriedades** .

    - No lado esquerdo, clique **com regras**
    - Em **Começar com**, clique em **todas as colunas**. Isto indica ao [Seleccionar colunas no conjunto de dados] [ select-columns] a passagem de todas as colunas (excepto as estamos prestes a excluir).
    - As listas pendentes, seleccione **os nomes das colunas**e de **Excluir** e, em seguida, clique dentro da caixa de texto. É apresentada uma lista de colunas. Seleccione **perdas normalizada**e serão adicionado à caixa de texto.
    - Clique no botão de marca de verificação (OK) para fechar o selector de coluna.

    ![Seleccionar colunas][screen3]

    O painel de propriedades para **Seleccionar colunas no conjunto de dados** indica que passem através de todas as colunas do dataset que a excepto **perdas normalizada**.

    ![Seleccionar colunas nas propriedades do conjunto de dados][screen4]

    > [AZURE.TIP] Pode adicionar um comentário a um módulo fazendo duplo clique no módulo e introdução de texto. Isto pode ajudar a ver rapidamente o que o módulo está a fazer na sua experiência. Neste caso, faça duplo clique em [Seleccionar colunas no conjunto de dados] [ select-columns] módulo e escreva o comentário "excluem perdas normalizada."

3. Arraste os [Dados em falta limpo] [ clean-missing-data] módulo para a experiência da tela e ligue-o a [Seleccionar colunas no conjunto de dados] [ select-columns] módulo. No painel **Propriedades** , seleccione a **remover a linha inteira** de mensagens em fila em **modo de limpeza** para limpar os dados removendo linhas que têm valores em falta. Faça duplo clique sobre o módulo e escreva o comentário "Remover linhas de valor em falta".

    ![Propriedades de dados em falta limpas][screen4a]

4. Execute a experiência clicando em **Executar** em tela de experiência.

Quando tiver terminado o ensaio, todos os módulos têm uma marca de verificação verde para indicar que estas concluída com êxito. Repare também o estado de **terminada a ser executado** no canto superior direito.

![Experiência de primeira executada][screen5]

Tudo fizemos na experiência a este ponto é limpar os dados. Se pretender ver o conjunto de dados limpo, clique na porta de saída para a esquerda dos [Dados em falta limpo] [ clean-missing-data] módulo ("dataset limpo") e seleccione **visualizar**. Repare que a coluna de **perdas normalizada** já não está incluída e não existem valores em falta.

Agora que os dados estiverem limpos, estamos prontos para especificar quais as funcionalidades que vamos utilizar no modelo de previsão.

## <a name="step-3-define-features"></a>Passo 3: Definir funcionalidades

Máquina de aprendizagem, *funcionalidades* são propriedades mensuráveis individuais de algo que lhe interessam. Na nossa base de dados, cada linha representa um automóvel e cada coluna é uma funcionalidade do que automóvel.

Localizar um bom conjunto de funcionalidades para criar um modelo de previsão requer experimentação e conhecimentos sobre o problema que pretende resolver. Algumas funcionalidades são melhores para prever o destino que outros. Além disso, algumas funcionalidades têm uma forte correlação com outras funcionalidades (por exemplo, Localidade-mpg versus via mpg), para que não irão adicionar muitas informações de novas o modelo e podem ser removidas.

Vamos criar um modelo que utiliza um subconjunto das funcionalidades do nosso conjunto de dados. Pode voltar e seleccionar funcionalidades diferentes, execute novamente a experiência e ver se obtém melhores resultados. Mas para iniciar, tente as seguintes funcionalidades:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Arraste outra [Seleccionar colunas no conjunto de dados] [ select-columns] módulo a experiência da tela e ligue-o para a porta de saída para a esquerda dos [Dados em falta limpo] [ clean-missing-data] módulo. Faça duplo clique sobre o módulo e escreva "Seleccionar funcionalidades para previsão".

2. Clique em **Iniciação selector de colunas** no painel **Propriedades** .

3. Clique em **regras**.

4. Em **Iniciar com** clique em **nenhuma coluna**e, em seguida, seleccione **incluir** e **os nomes das colunas** na linha de filtro. Introduza a nossa lista de nomes de colunas. Isto indica o módulo passar apenas as colunas especificar.

    > [AZURE.TIP] Ao executar o ensaio, tiver assegurámos que as definições de coluna para os nossos dados passam do dataset que a [Limpeza de dados em falta] [ clean-missing-data] módulo. Isto significa que os outros módulos de que estabelecer ligação também terá informações sobre a partir do conjunto de dados.

5. Clique no botão de marca de verificação (OK).

![Seleccionar colunas][screen6]

Deste modo, o conjunto de dados que será utilizado no algoritmo de aprendizagem nos próximos passos. Mais tarde, pode regressar e tentar novamente com uma selecção diferente de funcionalidades.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Passo 4: Escolher e aplicar um algoritmo de aprendizagem

Agora que os dados estão prontos, construir um modelo de previsão consiste em formação e de teste. Utilizaremos os nossos dados para o modelo do comboio e, em seguida, teste o modelo para ver a como fechar é capaz de predizer a preços. Por agora, não se preocupe porque é que é necessário para preparar e, em seguida, testar um modelo.

*Classificação* e de *regressão* são dois tipos de computador controlado técnicas de aprendizagem. Classificação prevê uma resposta de um conjunto definido de categorias, como uma cor (vermelha, azul ou verde). Regressão é utilizada para prever um número.

Uma vez que o queremos prever preço, que é um número, vamos utilizar um modelo de regressão. Para este exemplo, vamos vai preparar um modelo de *regressão linear* de simples e, no passo seguinte, vamos testá-lo.

1. Utilizamos os nossos dados para a formação e dividindo-o em separado de formação e testar conjuntos de teste. Seleccione e arraste os [Dados divididos] [ split] módulo a experiência da tela e ligue-o para a saída da última [Seleccionar colunas no conjunto de dados] [ select-columns] módulo. Defina a **fracção de linhas no conjunto de dados de saída primeiro** para 0,75. Desta forma, vamos utilizar 75 por cento dos dados para o modelo do comboio e contrárias 25 por cento para o ensaio.

    > [AZURE.TIP] Ao alterar o parâmetro de **semente aleatória** , pode produzir diferentes amostras aleatórias para a formação e de ensaio. Este parâmetro controla a sementeira do gerador de números pseudo-aleatórios.

2. Execute o ensaio. Isto permite [Seleccionar colunas no conjunto de dados] [ select-columns] e [Dividir dados] [ split] módulos para transferir definições de coluna para os módulos continuaremos a adicionar em seguida.  

3. Para seleccionar o algoritmo de aprendizagem, a categoria **Máquina de aprendizagem** na paleta de módulo de expansão à esquerda da tela de e, em seguida, expanda **Inicializar o modelo**. Isto apresenta várias categorias de módulos que podem ser utilizadas para inicializar algoritmos de aprendizagem do computador.

    Para esta experiência, seleccione a [Regressão Linear] [ linear-regression] módulo sob a categoria de **regressão** (também pode encontrar o módulo escrevendo "regressão linear" na caixa de pesquisa de paleta) e arraste-o para a tela de experiência.

4. Localize e arraste o [Modelo de comboio] [ train-model] módulo à tela de experiência. Ligar a porta de entrada para a esquerda para a saída da [Regressão Linear] [ linear-regression] módulo. Ligar a porta de entrada direita para a saída de dados de formação (porta para a esquerda) dos [Dados divididos] [ split] módulo.

5. Seleccione o [Modelo de comboio] [ train-model] módulo, clique em **Iniciar o selector de colunas** no painel **Propriedades** e, em seguida, seleccione a coluna do **preço** . Este é o valor que o nosso modelo vai para previsão.

    ![Seleccione a coluna "preço"][screen7]

6. Execute o ensaio.

O resultado é um modelo de regressão formada que pode ser utilizado para pontuar novas amostras para fazer previsões.

![Aplicar o algoritmo de aprendizagem do computador][screen8]

## <a name="step-5-predict-new-automobile-prices"></a>Passo 5: Preços de automóveis novos de previsão

Agora que estamos tiver preparado o modelo utilizando 75 por cento dos nossos dados, pode ser utilizado para os outros 25 porcento dos dados para ver como bem de pontuação nosso funções de modelo.

1. Localize e arraste o [Modelo de pontuação] [ score-model] módulo para a experiência da tela e ligue a porta de entrada para a esquerda para a saída do [Modelo de comboio] [ train-model] módulo. Ligar a porta de entrada direita para a saída de dados teste (porta à direita) dos [Dados divididos] [ split] módulo.  

    ![Módulo de modelo de pontuação][screen8a]

2. Para executar a experiência e visualizar a saída do [Modelo de pontuação] de[ score-model] módulo, clique na porta de saída e, em seguida, seleccione **visualizar**. A saída mostra os valores previstos para preços e os valores conhecidos dos dados de teste.  

3. Finalmente, para testar a qualidade dos resultados, seleccione e arraste o [Modelo avaliar] [ evaluate-model] módulo a experiência da tela e ligue a porta de entrada para a esquerda para a saída do [Modelo de pontuação] [ score-model] módulo. (Existem duas portas de entrada porque o [Modelo avaliar] [ evaluate-model] módulo pode ser utilizado para comparar dois modelos.)

4. Execute o ensaio.

Para visualizar a saída do [Modelo avaliar] [ evaluate-model] módulo, clique na porta de saída e, em seguida, seleccione **visualizar**. As seguintes estatísticas são mostradas para o nosso modelo:

- **Erro absoluto médio** (MAE): A média dos erros absolutos (um *erro* é a diferença entre o valor previsto e o valor real).
- **Erro ao quadrado médio de raiz** (RMSE): A raiz quadrada da média dos quadrados erros de previsões efectuadas no conjunto de dados de ensaio.
- **Erro absoluto relativo**: A média dos erros absolutas relativamente a diferença absoluta entre os valores reais e a média de todos os valores reais.
- **Erro ao quadrado relativo**: A média dos quadrados erros relativamente a diferença de quadrados entre os valores reais e a média de todos os valores reais.
- **Coeficiente de determinação**: também conhecido como o **valor de R ao quadrado**, esta é uma métrica estatística indicando a forma como um modelo se ajusta a dados.

Para cada do erro é melhor estatísticas, mais pequena. Um valor inferior indica que as previsões mais se aproximam dos valores reais. **Coeficiente de determinação**, quanto mais próximo respectivo valor é um (1.0), melhor será as previsões.

![Resultados das avaliações][screen9]

A experiência final deverá ter este aspecto:

![Iniciação de aprendizagem de máquinas: concluir a experiência de regressão linear que utiliza técnicas de previsão de modelação.][screen10]

## <a name="next-steps"></a>Passos seguintes

Agora que tiver concluído uma primeira aprendizagem iniciação da máquina e ter a experiência configurada, pode efectuar iterações para tentar melhorar o modelo. Por exemplo, pode alterar as funcionalidades que utiliza na sua previsão. Ou pode modificar as propriedades da [Regressão Linear] [ linear-regression] algoritmo ou tente um algoritmo diferente completamente. Pode até adicionar máquina vários algoritmos para a experiência de aprendizagem de uma só vez e comparar dois, utilizando o [Modelo avaliar] [ evaluate-model] módulo.

> [AZURE.TIP] Utilize o botão **Guardar como** em tela de experiência para copiar qualquer iteração da experiência. Pode ver todas as iterações de sua experiência, clicando em **Ver histórico de executar** ao abrigo da tela. Consulte [gerir experiências iterações no Azure máquina aprendizagem Studio] [ runhistory] para obter mais detalhes.

[runhistory]: machine-learning-manage-experiment-iterations.md

Quando estiver satisfeito com o modelo, pode implementá-lo como um serviço web a ser utilizado para calcular preços automóveis através da utilização de novos dados. Consulte [Implementar um serviço web de aprendizagem de máquinas Azure] [ publish] para obter mais detalhes.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Para instruções mais extensa e detalhada das técnicas de modelação de previsão para a criação, formação, pontuação e implementação de um modelo, consulte [desenvolver uma solução preditiva através da utilização de aprendizagem de máquinas Azure][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
