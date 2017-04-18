<properties 
    pageTitle="Ciência de dados no Máquina Virtual Linux dados ciência | Microsoft Azure" 
    description="Como realizar várias tarefas de ciências dados comuns com a VM de ciências dados Linux." 
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
    ms.author="bradsev;paulsh" />


# <a name="data-science-on-the-linux-data-science-virtual-machine"></a>Ciência de dados no Máquina Virtual Linux dados ciência

Este tutorial mostra-lhe como realizar várias tarefas de ciências dados comuns com a VM de ciências dados Linux. O Linux dados ciência Máquina Virtual (DSVM) é uma imagem de máquina virtual disponível no Azure que se encontra instalado com um conjunto de ferramentas mais utilizadas em formação do computador e de análise de dados. Os componentes de software de chave são listados no tópico de [aprovisionar o Linux dados ciência Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md) . A imagem VM torna mais fácil começar a efetuar ciência de dados em minutos, sem ter de instalar e configurar cada uma das ferramentas individualmente. Pode facilmente dimensionar o VM, se necessário e pará-lo quando não estiver em utilização. Por isso, este recurso é flexível e eficiente de custo. 

As tarefas de ciências dados demonstrado na seguir tutorial os passos descritos no [Processo de ciências de dados de equipa](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Este processo fornece uma abordagem sistemática à ciência de dados que permite que as equipas de cientistas de dados para colaborar eficazmente sobre o ciclo de vida de criação de aplicações do inteligentes. O processo de ciências dados também fornece um quadro ou iterativo para ciência de dados que pode ser seguida por um indivíduo.

Vamos analisar o conjunto de dados [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) neste tutorial. Este é um conjunto de mensagens de correio eletrónico que estejam marcados como spam ou presunto (o que significa que não estão spam), e também contém algumas estatísticas sobre o conteúdo dos e-mails. As estatísticas de incluído são explicadas a próxima mas uma secção. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar uma máquina de Virtual Linux dados ciência, tem de ter o seguinte procedimento:

- Uma **subscrição do Azure**. Se não já possui um, consulte o artigo [Criar Azure conta gratuita hoje](https://azure.microsoft.com/free/).
- [**Ciência de dados Linux VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Para obter informações sobre este VM de aprovisionamento, consulte o artigo [aprovisionar o Linux dados ciência Virtual Machine](machine-learning-data-science-linux-dsvm-intro.md). 
- [X2Go](http://wiki.x2go.org/doku.php) instalado no seu computador e abertos uma sessão XFCE. Para obter informações sobre como instalar e configurar um **cliente X2Go**, consulte o artigo [instalar e configurar o cliente de X2Go](machine-learning-data-science-linux-dsvm-intro.md#Installing-and-configuring-X2Go-client). 
- Uma **conta de AzureML**. Se ainda não tiver uma, inscrever-se para um novo na [Home page do AzureML](https://studio.azureml.net/). Existe uma camada de utilização gratuito para o ajudar a começar a utilizar.


## <a name="download-the-spambase-dataset"></a>Transferir o conjunto de dados spambase

O conjunto de dados [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) é um conjunto de dados que contém apenas 4601 exemplos relativamente pequeno. Este é um tamanho conveniente para utilizar quando demonstrar que algumas das principais funcionalidades da ciência VM dados tal como é mantém os requisitos dos recursos conhecimentos básicos.

>[AZURE.NOTE] Este tutorial foi criado numa D2 dimensão v2 Linux dados ciência Máquina Virtual. Este tamanho DSVM é capaz de processamento os procedimentos neste tutorial.

Se precisar de mais espaço de armazenamento, pode criar discos adicionais e ligá-los ao seu VM. Estes discos utilizam armazenamento Azure persistente, para que os respetivos dados são preservados mesmo quando o servidor é reprovisioned devido a redimensionar ou é encerrar. Para adicionar um disco e anexe-o ao seu VM, siga as instruções em [Adicionar um disco para uma VM Linux](../virtual-machines/virtual-machines-linux-add-disk.md). Estes passos que utilizam a Interface de linha de comandos do Azure (Azure CLI), que já está instalado o DSVM. Por isso, estes procedimentos podem ser feitos totalmente a VM própria. Outra opção para aumentar o armazenamento é de utilizar os [ficheiros do Azure](../storage/storage-how-to-use-files-linux.md).

Para transferir os dados, abra uma janela de terminal e executa este comando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

O ficheiro transferido não tem uma linha de cabeçalho, vamos criar outro ficheiro que têm um cabeçalho. Execute este comando para criar um ficheiro com os cabeçalhos adequados:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Em seguida, concatenar dois ficheiros juntamente com o comando:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

O conjunto de dados tem vários tipos de estatísticas sobre cada e-mail: 

- Gostar de colunas ***word\_freq\_WORD*** indicar a percentagem de palavras no e-mail que correspondem ao *WORD*. Por exemplo, se *word\_freq\_tornar* for 1, em seguida, 1% de todas as palavras numa mensagem de correio electrónico foram *efetuar*. 
- Gostar de colunas ***CARÁCT\_freq\_CARÁCT*** indicar a percentagem de todos os carateres no e-mail que foram *CARÁCT*. 
- ***maiúscula\_executar\_comprimento\_mais longa*** é o mais longo comprimento de uma sequência de letras maiúsculas. 
- ***maiúscula\_executar\_comprimento\_média*** é o comprimento médio de todas as sequências de letras maiúsculas. 
- ***maiúscula\_executar\_comprimento\_total*** é o comprimento total de todas as sequências de letras maiúsculas. 
- ***spam*** indica se o e-mail foi considerado spam ou não (1 = spam, 0 = não correio publicitário não solicitado).


## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorar o conjunto de dados com o Microsoft R aberta

Vamos analisar os dados e efetue algumas máquina básica de formação com R. A VM de ciências dados vem com [Microsoft R abrir](https://mran.revolutionanalytics.com/open/) pré-instalado. As bibliotecas de matemática multithreaded nesta versão do R oferecem um melhor desempenho que várias versões por tópicos único. Microsoft R abrir também fornece reprodutibilidade utilizando um instantâneo do repositório de pacote CRAN.

Para obter cópias das amostras de código utilizadas neste tutorial, clonar do repositório de **Azure-máquina-aprendizagem-dados-ciência** utilizando git, que se encontra instalado na VM. A partir da linha de comandos git, execute:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Abra uma janela de terminal e iniciar uma nova sessão de R com a consola interativa R.

>[AZURE.NOTE] Também pode utilizar RStudio para os seguintes procedimentos. Para instalar RStudio, execute este comando num terminal:`./Desktop/DSVM\ tools/installRStudio.sh`

Para importar os dados e configurar o ambiente, execute:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Para ver as estatísticas de resumo sobre cada coluna:

    summary(data)

Para obter uma vista diferente dos dados:

    str(data)

Isto mostra-lhe o tipo de cada variável e os primeira alguns valores no conjunto de dados. 

A coluna de *spam* foi lida como um número inteiro, mas é realmente um categorias variável (ou fator). Para definir o tipo:

    data$spam <- as.factor(data$spam)

Para executar alguns análise exploratória, utilize o pacote de [ggplot2](http://ggplot2.org/) , uma biblioteca de gráficos popular para R que já esteja instalada na VM. Tenha em atenção dos dados de resumo apresentados em versões anteriores, temos resumo estatísticas sobre a frequência de um caráter de ponto de exclamação. Vamos Traçar esse frequência com os seguintes comandos:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Uma vez que a barra de zero é torcer o desenho, vamos eliminá-lo:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Existe uma densidade não comum acima 1 com um aspeto interessante. Vamos ver apenas os dados:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Em seguida, divididos por presunto vs de spam:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Estes exemplos devem permitem-lhe efetuar representações semelhantes das outras colunas para explorar os dados neles contidas.


## <a name="train-and-test-an-ml-model"></a>Dar formação e testar a um modelo de ML

Agora vamos dar formação de modelos de aprendizagem automática para classificar os e-mails no conjunto de dados como contendo intervalo específico ou presunto duas. Vamos formar um modelo de árvore de decisões e um modelo de floresta aleatório nesta secção e, em seguida, teste sua precisão das suas previsões. 

>[AZURE.NOTE] O pacote de rpart (a partições recursiva e árvores de regressão) utilizado no seguinte código já estiver instalado na VM de ciências de dados.


Primeiro, vamos dividir o conjunto de dados em conjuntos de formação e teste:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

E, em seguida, criar uma árvore de decisões para classificar os e-mails.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Eis o resultado:

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

Para determinar como bem que executa o conjunto de formação, utilize o seguinte código:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Para determinar como bem que executa o conjunto de teste:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Vamos experimentar também um modelo de floresta aleatório. Aleatórias florestas formar inúmeros árvores de decisão e de saída de uma classe que é o modo de classificações de todas as árvores de decisão individual. Fornecem uma máquina mais eficazes abordagem de formação corrigir para a tendência de um modelo de árvore de decisões para overfit um conjunto de dados de formação. 

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Implementar um modelo no Azure ML

[Azure máquina Studio de formação](https://studio.azureml.net/) (AzureML) é um serviço na nuvem que torna mais fácil criar e implementar modelos de análise de aspeto do Office. Uma das funcionalidades totalmente do AzureML é a sua capacidade para publicar qualquer função R como um serviço web. O pacote de AzureML R facilita a implementação efetuar a partir do nossa sessão a partir do R o DSVM. 

Para implementar o código de árvore de decisões da secção anterior, é necessário iniciar sessão no Azure máquina aprendizagem Studio. Precisa do seu ID de área de trabalho e um token de autorização para sigh no. Para localizar estes valores e iniciar as variáveis AzureML com os mesmos:

Selecione **Definições** no menu à esquerda. Tenha em atenção o seu **ID de área de trabalho**. ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

Selecione **Autorização Tokens** a partir do menu custos geral e tenha em atenção o **Token de autorização principal**. ![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

Carregar o pacote de **AzureML** e em seguida, defina valores de variáveis com o seu ID de token e área de trabalho na sessão R o DSVM:


    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Vamos simplificar o modelo para facilitar a esta sequência de demonstração implementar. Escolha as três variáveis na árvore de decisão mais próxima da raiz e crie uma nova árvore utilizando apenas as três variáveis:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Precisamos de uma função de previsão que recebe as funcionalidades como uma entrada e devolve os valores previstos:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publica a função predictSpam AzureML utilizando a função **publishWebService** : 

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Esta função leva-o até a função **predictSpam** , cria um serviço web com o nome **spamWebService** com entradas definidas e saídas e devolve informações sobre o ponto final de novo.

Ver detalhes do serviço web publicada, incluindo o respectivo ponto final API e aceder a chaves com o comando:

    spamWebService[[2]]

Para experimentar sobre o primeiro conjunto de 10 linhas do teste:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Utilizar outras ferramentas disponíveis

As secções restantes mostram como utilizar algumas das ferramentas instaladas na VM de ciências dados Linux. Eis a lista de ferramentas de outros fabricantes referidos:

- XGBoost
- Python
- Jupyterhub
- Roca
- PostgreSQL & exemplo SQL
- Armazém de dados do SQL Server


## <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) é uma ferramenta que fornece uma implementação rápidas e precisas aumentado da árvore.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost também pode chamar a partir do python ou uma linha de comandos.

## <a name="python"></a>Python

Para o desenvolvimento utilizando Python, as distribuições Anaconda Python 2.7 e 3.5 tem sido instaladas na DSVM. 

>[AZURE.NOTE] A distribuição Anaconda inclui [Condas](http://conda.pydata.org/docs/index.html), que podem ser utilizados para criar ambientes personalizados para Python que tenham versões diferentes e/ou pacotes instalados nas mesmas.

Vamos ler em alguns do conjunto de dados spambase e classificar os e-mails com máquinas de vetor de suporte no scikit-Saiba mais:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para efetuar previsões:

    clf.predict(X.ix[0:20, :])

Para mostrar como publicar um ponto final AzureML, vamos tornar a um modelo de mais simples as três variáveis como fizemos quando podemos publicada anteriormente o modelo de R. 

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para publicar o modelo de AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

>[AZURE.NOTE] Isto só está disponível para python 2.7 e ainda não é suportado no 3.5. Execute com **/anaconda/bin/python2.7**.


## <a name="jupyterhub"></a>Jupyterhub

A distribuição de Anaconda na DSVM vem com um bloco de notas Jupyter, um ambiente em diferentes plataformas para partilhar código Python, R ou Paula e análise. O bloco de notas Jupyter é acedido através de JupyterHub. Iniciar sessão com o seu nome de utilizador Linux local e a palavra-passe em ***https://\<VM DNS nome ou endereço IP\>: 8000 /***. Todos os ficheiros de configuração para JupyterHub encontram-se no diretório **/etc/jupyterhub**.

Vários blocos de notas do exemplo já estão instalados na VM:

- Consulte o artigo o [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) para um bloco de notas do exemplo Python.
- Consulte o artigo [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) para um bloco de notas do exemplo **R** .
- Consulte o artigo o [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) para outro bloco de notas de **Python** de exemplo.

>[AZURE.NOTE] O idioma de Paula também está disponível na linha de comandos na VM de ciências dados Linux.


## <a name="rattle"></a>Roca

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (o R Analytical ferramenta para saber facilmente) é uma ferramenta de R gráfica para extração de dados. Possui uma interface intuitiva que torna mais fácil carregar, explore e transformar os dados e criar e avaliar modelos.  O artigo [Rattle: A dados interface gráfica Data Mining para R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece instruções que demonstrará suas funcionalidades.

Instalar e iniciar roca com os seguintes comandos:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

>[AZURE.NOTE] Instalação não é necessária na DSVM. Mas roca poderá pedir-lhe instalar pacotes adicionais quando é carregado.

Roca utiliza uma interface com base no separador. A maior parte dos separadores corresponde aos passos do [Processo de ciências de dados](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), como o carregamento de dados ou explorá-la. O processo de ciências dados flui da esquerda para a direita através de separadores. Mas o último separador contém um registo dos comandos R execute ao roca. 


Para carregar e configurar o conjunto de dados:

- Para carregar o ficheiro, selecione o separador **dados** , em seguida 
- Selecione no Seletor de junto ao **nome do ficheiro** e selecione **spambaseHeaders.data**. 
- Para carregar o ficheiro. Selecione **Executar** na linha superior dos botões. Deverá ver um resumo de cada coluna, incluindo o tipo de dados identificados, quer seja uma entrada, um alvo ou outro tipo de variável e o número de valores exclusivos.
- Roca corretamente identificado com a coluna de **spam** como o destino. Selecione a coluna de spam e, em seguida, defina o **Tipo de dados de destino** para **Categoric**.

Explorar os dados: 

- Selecione o separador **explorar** . 
- Clique em **Resumo**, em seguida, **Executar**, para ver algumas informações sobre os tipos de variáveis e algumas estatísticas de resumo. 
- Para ver outros tipos de estatísticas sobre cada variável, selecione outras opções como **descrever** ou **Noções básicas**.

No separador **explorar** também permite-lhe gerar representações inteligentes muitos. Para desenhar um histograma dos dados:


- Selecione **as distribuições**.
- Procure **histograma** **word_freq_remove** e **word_freq_you**.
- Selecione **Executar**. Deverá ver ambas as representações de densidade numa janela única graph, onde é que a palavra "que" é apresentado muito mais frequentemente em e-mails que "Remover" Limpar.

As representações de correlação também são interessantes. Para criar uma:


- Escolher **correlação** como o **tipo**, em seguida 
- Selecione **Executar**. 
- Roca avisa recomenda um máximo de 40 variáveis. Selecione **Sim** para ver o desenho. 

Existem algumas correlação interessante que surgem: "tecnologias" vivamente relacionada para "HP" e "labs", por exemplo. É também vivamente associado para "650", uma vez que o código da área de legais conjunto de dados é 650.

Os valores numéricos de correlação entre as palavras estão disponíveis na janela do explorar. É interessante para nota, por exemplo, uma relacionado negativa "tecnologia" com "a" e "dinheiro".

Roca pode transformar o conjunto de dados para processar alguns problemas comuns. Por exemplo,-permite-lhe redimensionar funcionalidades, imputar valores em falta, processar aberrantes e remover as variáveis ou observações com dados em falta. Roca também pode identificar regras de associação entre observações e/ou variáveis. Estes separadores são fora do âmbito para este tutorial introdutório.

Roca também pode executar a análise de cluster. Vamos excluir algumas funcionalidades para facilitar a leitura o resultado. No separador **dados** , selecione **Ignorar** ao lado de cada uma das variáveis exceto estes dez itens principais:

- word_freq_hp
- word_freq_technology
- word_freq_george
- word_freq_remove
- word_freq_your
- word_freq_dollar
- word_freq_money
- capital_run_length_longest
- word_freq_business
- spam

Em seguida, aceda novamente para o separador de **Cluster** , selecione **KMeans**e defina o *número de clusters* para 4. Em seguida, em **Executar**. Os resultados são apresentados na janela de saída. Um cluster tem alta frequência de "Jorge" e "hp" e provavelmente é um e-mail da empresa legítimas.

Para criar um modelo de formação de máquina de árvore de decisão simples: 

- Selecione o separador de **modelo** 
- Selecione **árvore** como o **tipo**. 
- Selecione **Executar** para apresentar a árvore em forma de texto na janela de saída. 
- Selecione o botão **Desenhar** para ver uma versão de gráfica. Isto é bastante semelhante a árvore de que podemos obtidos anterior utilizando *rpart*.

Uma das funcionalidades totalmente do roca é a sua capacidade de executar vários métodos de aprendizagem automática e avaliá-las rapidamente. Eis o procedimento:

- Selecione **todos os** para o **tipo**. 
- Selecione **Executar**. 
- Depois de terminar pode clique em qualquer único **tipo**, como **SVM**e ver os resultados. 
- Pode também comparar o desempenho dos modelos de validação definir utilizando o separador **avaliar** . Por exemplo, a seleção de **Erro matriz** mostra-lhe a ambiguidade matriz, erro global e erro de classe médio para cada modelo no conjunto de validação. 
- Também pode desenhar curvas ROC, efetuar uma análise de sensibilidade e fazer outros tipos de avaliações de modelo.

Assim que tiver terminado de construir modelos, selecione o separador de **registo** para ver o código de R ser executado por roca durante a sessão. Pode selecionar o botão **Exportar** para guardá-lo. 

>[AZURE.NOTE] Existe um erro na versão atual do roca. Para modificar o script ou utilizá-la para repetir os passos mais tarde, tem de inserir um caráter # à frente de *exportar este registo …* no texto do registo. 


## <a name="postgresql--squirrel-sql"></a>PostgreSQL & exemplo SQL

O DSVM vem com PostgreSQL instalado. PostgreSQL é uma base de dados relacional sofisticado, abrir origem. Esta secção mostra como carregar o nosso conjunto de dados de spam no PostgreSQL e, em seguida, inicie a consulta-lo.

Antes de poder carregar os dados, é necessário permitir uma autenticação a partir do anfitriãolocal palavra-passe. Numa linha de comandos:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Perto da parte inferior do ficheiro de configuração são várias linhas de detalhe as ligações permitidas:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Altere a linha "IPv4 local ligações" para utilizar md5 em vez de travessão, para que recomendamos pode iniciar sessão utilizando um nome de utilizador e palavra-passe:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

E reinicie o serviço de postgres:

    sudo systemctl restart postgresql

Para iniciar o psql, um terminal interativo para PostgreSQL, como o utilizador postgres incorporados, execute o seguinte comando a partir de uma linha de comandos:

    sudo -u postgres psql

Crie uma nova conta de utilizador, utilizando o mesmo nome de utilizador como a conta do Linux atualmente sessão iniciada como e dar-lhe uma palavra-passe:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Em seguida, inicie sessão no psql como o utilizador:

    psql

E importar os dados para uma nova base de dados:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Agora, vamos explorar os dados e executar algumas consultas através do **SQL de exemplo**, uma ferramenta gráfica que lhe permite interagem com bases de dados através de um controlador JDBC.

Para começar, inicie SQL de exemplo a partir do menu aplicações. Para configurar o controlador da:

- Selecione **Windows**, em seguida, **controladores de vista**. 
- Com o botão direito no **PostgreSQL** e selecione **Modificar controlador**. 
- Selecione a **categoria Extra caminho**, em seguida, **Adicionar**. 
- Introduza ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** para o **nome do ficheiro** e 
- Selecione **Abrir**.
- Selecione controladores de lista, em seguida, selecione **org.postgresql.Driver** no **Nome de classe**e selecione **OK**.

Para configurar a ligação para o servidor local:
 
- Selecione **Windows**, em seguida, **Ver Aliases.** 
- Selecione o **+** botão para fazer com um novo alias. 
- O nome *base de dados de Spam*, selecione **PostgreSQL** no menu pendente **controlador** .
- Defina o URL para *jdbc:postgresql://localhost/spam*. 
- Introduza o *nome de utilizador* e *palavra-passe*. 
- Clique em **OK**. 
- Para abrir a janela de **ligação** , faça duplo clique sobre o alias de ***base de dados de Spam*** . 
- Selecione **Ligar**.

Para executar algumas consultas:

- Selecione o separador **SQL** .
- Introduzir uma consulta simples, tal como `SELECT * from data;` na caixa de consulta texto na parte superior do separador SQL. 
- Prima **Ctrl-introduza** executá-la. Por predefinição SQL de exemplo devolve as primeiros 100 linhas da sua consulta. 

Existem muitas mais consultas que podem ocorrer para explorar estes dados. Por exemplo, como é que a frequência do *fazer* diferem entre o spam e presunto?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou, quais são as características de e-mail que contenham frequentemente *3d*?

    SELECT * from data order by word_freq_3d desc;

A maior parte dos e-mails que tenham uma ocorrência alta *3d* são aparentemente de spam, para que dever-se uma funcionalidade útil para criar um modelo de aspeto do Office para classificar os e-mails.

Se pretender executar formação de máquina com dados armazenados numa base de dados PostgreSQL, considere utilizar [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Armazém de dados do SQL Server

Armazém de dados SQL Azure é uma baseado na nuvem, escala de saída base de dados capaz de processar grandes volumes de dados, não relacionais e relacionais. Para obter mais informações, consulte o artigo [o que é armazém de dados do SQL Azure?](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para ligar para o armazém de dados e criar a tabela, execute o seguinte comando a partir de uma linha de comandos:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Em seguida, na linha de comandos sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copie os dados com bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

>[AZURE.NOTE] Respectivo linha no ficheiro transferido é estilo do Windows, mas bcp espera estilo UNIX, para que precisamos de indicar ao bcp que com o sinalizador - r.

E consulta com sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Também pode consultar com SQL de exemplo. Siga passos semelhantes para PostgreSQL, utilizando MSSQL servidor JDBC o controlador da Microsoft, que podem ser encontradas no ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Próximos passos

Para obter uma descrição geral de tópicos guiá-lo as tarefas que compõem o processo de ciências de dados no Azure, consulte o artigo [O processo de ciências de dados de equipa](http://aka.ms/datascienceprocess).

Para obter uma descrição de outros tutoriais de ponto a ponto que demonstram os passos do processo de ciências de dados de equipa para cenários específicos, consulte o artigo [Tutoriais do processo de ciências de dados de equipa](data-science-process-walkthroughs.md). Os tutoriais ilustram também como combinar as ferramentas na nuvem e no local e serviços num fluxo de trabalho ou em curso para criar uma aplicação inteligente.

