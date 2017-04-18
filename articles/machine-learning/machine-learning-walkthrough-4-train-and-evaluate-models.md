<properties
    pageTitle="Passo 4: Dar formação e avaliar os modelos de analíticos aspeto do Office | Microsoft Azure"
    description="Passo 4 da desenvolver instruções solução aspeto do Office: comboio, pontuação e avaliar vários modelos no Azure máquina aprendizagem Studio."
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
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Tutorial passo 4: Dar formação e avaliar os modelos de analíticos aspeto do Office

Este tópico contém o quarto passo a instruções passo a passo, [desenvolver uma solução de análise de aspeto do Office no Azure máquina aprendizagem](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar uma área de trabalho de aprendizagem automática](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)
4.  **Dar formação e avaliar os modelos**
5.  [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Aceder ao serviço web](machine-learning-walkthrough-6-access-web-service.md)

----------

Uma das vantagens da utilização Azure máquina de formação Studio para a criação de modelos de aprendizagem automática é a capacidade de experimentar mais do que um tipo de modelo de cada vez numa experiência e comparar os resultados. Este tipo de experimentação ajuda-na localizar a melhor solução para o seu problema.

Na experiência que podemos desenvolver neste tutorial, vamos criar dois tipos diferentes de modelos e, em seguida, comparar os respetivos resultados pontuação decidir qual o algoritmo queremos para utilizar no nossa experiência final.  

Existem vários modelos que poderia optamos a partir de. Para ver os modelos disponíveis, expanda o nó **Máquina formação** na paleta de módulo e, em seguida, expanda **O modelo de inicialização** e os nós abaixo da mesma. Para efeitos desta experiência, recomendamos selecione o suporte Vector máquina (SVM) e os módulos de classe duas aumentado as árvores de decisão.    

> [AZURE.TIP] Para obter o ajudar a decidir qual o algoritmo máquina aprendizagem melhor se adequa ao problema específico que está a tentar resolver, consulte o artigo [como escolher algoritmos para de formação do Microsoft Azure máquina](machine-learning-algorithm-choice.md).

##<a name="train-the-models"></a>Preparar os modelos
Primeiro, vamos configurar o modelo de árvore de decisões aumentado:  

1.  Localizar a [Árvore de decisões aumentado duas classe] [ two-class-boosted-decision-tree] módulo na paleta de módulo e arraste-o para a tela.
2.  Encontrar o [Modelo de comboio] [ train-model] módulo, arraste-o para a tela de e, em seguida, ligue o resultado do módulo de árvore de decisões aumentado para a porta de entrada para a esquerda ("modelo às") do [Modelo de comboio] [ train-model] módulo.
    
    A [Árvore de decisões aumentado duas classe] [ two-class-boosted-decision-tree] módulo inicia o modelo genérico e o [Modelo formar] [ train-model] utiliza dados de formação para formar o modelo. 
     
3.  Ligar a saída para a esquerda ("resultado conjunto de dados") do esquerdo [Executar scripts de R] [ execute-r-script] módulo à direita de entrada porta ("conjunto de dados") do [Modelo de comboio] [ train-model] módulo.

    > [AZURE.TIP] Não precisamos duas das entradas e um dos resultados do [Executar scripts de R] [ execute-r-script] módulo para esta experiência, para que recomendamos deixá-los não anexada. 

4.  Selecione o [Modelo de comboio] [ train-model] módulo. No painel de **Propriedades** , clique em **Iniciação selector de coluna**, selecione **Todos os tipos de** na lista pendente para baixo em **Colunas disponíveis** e introduza "Risco de crédito" no campo de texto. Selecione **Todos os tipos de** na lista pendente em **Colunas selecionadas**. Selecione "Risco de crédito" e clique no botão de seta realçado para movê-la para **Colunas selecionadas**. 
5.  Clique em **Guardar**.


Esta parte da experiência agora tenha este aspeto:  

![Formação um modelo][1]

Em seguida, vamos configurar o modelo de SVM.  

Em primeiro lugar, um pouco sobre explicação sobre SVM. Árvores de decisão aumentado funcionam bem com funcionalidades de qualquer tipo. No entanto, uma vez que o módulo SVM gera um classificador linear, o modelo que gera tem o erro de teste melhor quando todas as funcionalidades numéricas têm a mesma escala. Para converter todas as funcionalidades numéricas para a mesma escala, utilizamos uma transformação "Tanh" (com os [Dados normalizar] [ normalize-data] módulo.) Esta transforma os nossos números no intervalo [0,1]. Funcionalidades de cadeia são convertidas em pelo módulo SVM para funcionalidades categorias e, em seguida, para funcionalidades de 0/1 binárias, pelo que não necessita de transformar manualmente as funcionalidades de cadeia. Além disso, podemos não queira transformar a coluna de risco de crédito (coluna 21) - é numérico, mas é o valor que podemos está a formação sobre o modelo de prever, para que precisamos de deixá-la individualmente.  

Para configurar o modelo de SVM, faça o seguinte:

1.  Localizar a [Classe de duas suporte Vector máquina] [ two-class-support-vector-machine] módulo na paleta de módulo e arraste-o para a tela.
2.  Com o botão direito do [Modelo de comboio] [ train-model] módulo, selecione **Copiar**e, em seguida, com o botão direito da tela e selecione **Colar**. A cópia do [Modelo de comboio] [ train-model] módulo tem a mesma seleção de coluna, como o original.
3.  Ligar a saída do módulo SVM para a porta de entrada para a esquerda ("modelo às") do segundo [Modelo comboio] [ train-model] módulo.
4.  Localizar os [Dados normalizar] [ normalize-data] módulo e arraste-o para a tela.
5.  Ligar a entrada deste módulo para a esquerda saída do esquerdo [Executar scripts de R] [ execute-r-script] módulo (tenha em atenção que a porta de saída de um módulo poderá estar ligada ao mais do que um módulo de outros).
6.  Ligar a porta de saída para a esquerda ("transformados conjunto de dados") dos [Dados normalizar] [ normalize-data] módulo à direita de entrada porta ("conjunto de dados") do segundo [Modelo comboio] [ train-model] módulo.
7.  No painel de **Propriedades** para os [Dados normalizar] [ normalize-data] módulo, selecione **Tanh** para o parâmetro de **método de transformação** .
8.  Clique em **Iniciação selector de coluna**, selecione "Sem colunas" para **Começar com**, selecione **incluir** na primeira lista pendente, selecione o **tipo de coluna** na segundo lista pendente e selecione **numéricos** na lista pendente de terceiro. Especifica que todas as colunas numéricas (e apenas numérico) são transformados.
9.  Clique no sinal de adição (+) à direita desta linha - esta ação cria uma linha de dropdowns. Selecione **Excluir** na primeira lista pendente, selecione **os nomes das colunas** na segundo lista pendente e clique no campo de texto e selecione "Risco de crédito" a partir da lista de colunas. Isto indica que a coluna de risco de crédito deve ser ignorada (precisamos de fazer uma vez que esta coluna é numérica e para outro modo seria transformados).
10. Clique em **OK**.  


Os [Dados normalizar] [ normalize-data] módulo agora está definido para desempenhar uma transformação Tanh em todas as colunas numéricas exceto a coluna de risco de crédito.  

Esta parte do nossa experiência deve agora ter um aspeto semelhante ao seguinte:  

![O segundo modelo de formação][2]  

##<a name="score-and-evaluate-the-models"></a>Pontuação e avaliar os modelos

Utilizamos os dados de testes que foram separados pelos [Dados divididos] [ split] módulo pontuação os nossos modelos de formação. Em seguida, podemos pode comparar os resultados dos dois modelos para ver qual gerado melhores resultados.  

1.  Encontrar o [Modelo de pontuação] [ score-model] módulo e arraste-o para a tela.
2.  Ligar o [Modelo de comboio] [ train-model] módulo que está ligado à [Árvore de decisões aumentado duas classe] [ two-class-boosted-decision-tree] módulo para a esquerda de entrada porta do [Modelo de pontuação] [ score-model] módulo.
3.  Ligar a porta de entrada à direita do [Modelo de pontuação] [ score-model] módulo para a esquerda saída do direita [Executar scripts de R] [ execute-r-script] módulo.

    O [Modelo de pontuação] [ score-model] módulo agora pode ter as informações de crédito a partir dos dados de testes, executá-la através do modelo e comparar previsões o modelo gera com a coluna de risco de crédito real nos dados de testes.

4.  Copiar e colar o [Modelo de pontuação] [ score-model] módulo para criar uma segunda cópia ou arrastar um módulo novo para a tela.
5.  Ligar a porta de entrada para a esquerda do presente módulo ao modelo de SVM (ou seja, ligue à porta de saída do [Modelo de comboio] [ train-model] módulo que está ligado a [Duas classe suporte Vector máquina] [ two-class-support-vector-machine] módulo).
6.  Para o modelo SVM, temos a transformação mesmo para os dados de teste como fizemos para os dados de formação. Por isso, copie e cole os [Dados normalizar] [ normalize-data] módulo para criar uma segunda cópia e ligá-lo para a esquerda saída do direita [Executar scripts de R] [ execute-r-script] módulo.
7.  Ligar a porta de entrada à direita do [Modelo de pontuação] [ score-model] módulo para a esquerda saída dos [Dados normalizar] [ normalize-data] módulo.  

Para avaliar os dois resultados pontuação, utilizamos um [Modelo de avaliar] [ evaluate-model] módulo.  

1.  Encontrar o [Modelo de avaliar] [ evaluate-model] módulo e arraste-o para a tela.
2.  Ligar a porta de entrada para a esquerda para a porta de saída do [Modelo de pontuação] [ score-model] módulo associado com o modelo de árvore de decisões aumentado.
3.  Ligar a porta de entrada à direita para o [Modelo de pontuação] [ score-model] módulo.  

Para executar a experiência, clique no botão **Executar** por baixo da tela. Poderá demorar alguns minutos. Um indicador de girar em cada módulo para mostra que está a ser executado e, em seguida, uma marca de verificação verde quando estiver concluído o módulo. Quando todos os módulos têm uma marca de verificação, a experiência já terminou em execução.

A experiência deverá agora aspeto da seguinte forma:  

![Avaliar ambos os modelos][3]

Para verificar os resultados, clique na porta de saída do [Modelo de avaliar] [ evaluate-model] módulo e selecione **visualizar**.  

O [Modelo de avaliar] [ evaluate-model] módulo produz um par de curvas e métricas permitem-lhe comparar os resultados dos dois modelos de visita. Pode ver os resultados como curvas Recetor operador característica (ROC), precisão/resgate curvas ou elevação curvas. Dados adicionais apresentados incluem uma matriz de confusões, acumulados valores para a área sob a curva (AUC) e outras métricas. Pode alterar o valor limite ao mover o controlo de deslize para a esquerda ou para a direita e ver como-afeta o conjunto de métricas.  

À direita do gráfico, clique em **Scored conjunto de dados** ou **Scored conjunto de dados para comparar** para realçar a curva associada e para apresentar as métricas associadas abaixo. Na legenda para as curvas, "Classificados conjunto de dados" corresponde à porta de entrada para a esquerda do [Modelo de avaliar] [ evaluate-model] módulo - no nosso caso, este é o modelo de árvore de decisões aumentado. "Classificados conjunto de dados para comparar" corresponde à porta entrada direita - o modelo de SVM no nosso caso. Quando clica estas etiquetas, a curva para esse modelo está realçada e apresentar as métricas correspondentes, conforme mostrado no seguinte gráfico.  

![Curvas ROC para modelos de][4]

Ao examinar estes valores, pode decidir qual o modelo está mais próximo que lhe dá os resultados que procura. Pode voltar atrás e iteramos sobre a experiência alterando os valores nos diferentes modelos. 

> [AZURE.TIP] Sempre que executar a experiência um registo desse iteração é mantida no histórico de executar. Pode ver estas iterações e regressar a qualquer dos mesmos, ao clicar em **Ver histórico de executar** por baixo da tela. Também pode clicar em **Executar anterior** no painel de **Propriedades** para regressar à iteração imediatamente precedente no tema que tem abertos.
> 
Pode fazer uma cópia de qualquer iteração da sua experiência ao clicar em **Guardar como** , por baixo da tela. Utilize propriedades a experiência de **Resumo** e **Descrição** para manter um registo de o que experimentou no seu iterações experiência.

>  Para obter mais detalhes, consulte o artigo [Gerir experimentar iterações no Azure máquina aprendizagem Studio](machine-learning-manage-experiment-iterations.md).  


----------

**Seguinte: [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
