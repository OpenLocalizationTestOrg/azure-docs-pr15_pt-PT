<properties 
    pageTitle="Análise de sobrevivência com formação de máquina Azure | Microsoft Azure" 
    description="Probabilidade de ocorrência de evento de análise de sobrevivência" 
    services="machine-learning" 
    documentationCenter="" 
    authors="zhangya" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="zhangya"/> 


#<a name="survival-analysis"></a>Análise de sobrevivência 

Em muitos cenários, o resultado principal em avaliação é o tempo a um evento de interesse. Por outras palavras, a pergunta "quando este evento irá ocorrer?" é-lhe pedido. Como obter exemplos, considere situações em que os dados descreve o tempo decorrido (dias, anos, quilometragem, etc.) até que o evento de interesse (relapse doença, grau tese recebido, falha de marcação travão) ocorre. Cada instância nos dados representa um objeto específico (um paciente, um estudante, um carro, etc.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) responde a pergunta "qual é a probabilidade de que o evento de interesse irá apresentá-la por hora n para objeto x?" Ao fornecer um modelo de análise de sobrevivência, este serviço web permite aos utilizadores fornecer dados para formar o modelo e testá-la. O tema principal da experiência é modelar o comprimento do tempo decorrido até que ocorre o evento de interesse. 

>Este serviço web pode ser consumido por utilizadores – potencialmente através da aplicação móvel, através de um Web site ou mesmo num computador local, por exemplo. Mas o objetivo do serviço web é também servir como um exemplo de como Azure máquina formação pode ser utilizada para criar serviços web na parte superior de código R. Com apenas alguns linhas de código de R e cliques de um botão dentro Azure máquina aprendizagem Studio, uma experiência pode ser criada com o código de R e publicada como um serviço web. O serviço web, em seguida, pode ser publicado ao Azure Marketplace e média consumido por utilizadores e dispositivos em todo o mundo com a configuração não infraestrutura autor do serviço web.  

##<a name="consumption-of-web-service"></a>Consumo de serviço web

Esquema de dados de entrada do serviço web é apresentado na seguinte tabela. Seis partes de informações necessárias como a entrada: formação dados, testar os dados, hora de interesse, o índice de "hora" de dimensão, o índice de dimensão "evento" e os tipos de variáveis (contínua ou factor). Os dados de formação são representados por uma cadeia, onde as linhas são separadas por vírgulas e as colunas são separadas por ponto e vírgula. O número de funcionalidades dos dados é flexível. Todos os elementos a cadeia de entrada tem de ser numéricos. Os dados de formação, a dimensão de "hora" indica que o número de unidades de tempo (dias, anos, quilometragem, etc.) decorridos o ponto de partida do estudo (paciente receber programas de tratamento de contra, um estudo tese inicial estudantes, um carro comecem a ser pelo esforço, etc.) até que o evento de interesse (paciente voltar à utilização do contra, estudantes obter o grau de tese, carro tendo falha travão teclado de marcação etc.) ocorre. A dimensão "evento" indica se o evento de interesse ocorre no fim do estudo. Um valor de "evento = 1", significa que o evento de interesse ocorre ao tempo indicado por dimensão "hora"; "evento = 0", significa que o evento de interesse não ocorreu por hora indicada pela dimensão de "hora".

- trainingdata - numa cadeia de carateres. As linhas são separadas por vírgulas e as colunas são separadas por ponto e vírgula. Cada linha inclui dimensão de "hora", "evento" dimensões e variáveis predictor.
- testingdata - uma linha de dados que contém predictor variáveis para um objeto específico.
- time_of_interest - o tempo decorrido de juros n.
- index_time - o índice de coluna da dimensão "hora" (a começar em 1).
- index_event - o índice de coluna da dimensão "evento" (a começar em 1).
- variable_types - numa cadeia de carateres com ponto e vírgula como separadores no mesmo. 0 representa variáveis contínuas e 1 representa variáveis fator.


O resultado é a probabilidade de um evento que ocorram por uma hora específica. 

>Este serviço alojado no Azure Marketplace, é um serviço de OData; Estes podem ser chamados através de métodos de mensagem ou obter. 

Existem várias formas de consumir o serviço de forma automática (é uma aplicação de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)). 

###<a name="starting-c-code-for-web-service-consumption"></a>Inicial código c# para consumo de serviço web:
    public class Input
    {
            public string trainingdata;
            public string testingdata;
            public string timeofinterest;
            public string indextime;
            public string indexevent;
            public string variabletypes;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




A interpretação deste teste é da seguinte forma. Assumindo que o objetivo dos dados é modelar o tempo decorrido até o retorno para a utilização de contra para pacientes que receberam um dos programas do duas tratamento. O resultado do lê de serviço web: para doentes a ser 35 anos de idade, tendo anterior droga tratamento 2 vezes, o programa de tratamento de tempo local, a demorar e com a utilização de heroína e cocaína, a probabilidade de voltar à utilização de contra é 95.64% por dia 500.

##<a name="creation-of-web-service"></a>Criação de serviço web

>Este serviço web foi criado utilizando o Azure máquina aprendizagem. Para uma avaliação gratuita, bem como vídeos introdutórios sobre como criar experiências e [publicação serviços web](machine-learning-publish-a-machine-learning-web-service.md), consulte o artigo [azure.com/ml](http://azure.com/ml). Segue-se uma captura de ecrã da experiência que criou o código de exemplo e de serviço web para cada um dos módulos dentro da experiência.

A partir do Azure máquina formação, foi criada uma nova experiência em branco e duas [Executar scripts de R] [ execute-r-script] módulos foram movidos para a área de trabalho. O esquema de dados foi criado com um simples [Executar scripts de R][execute-r-script], que define o esquema de dados de entrada para o serviço web. Este módulo, em seguida, está ligado para o segundo [Executar scripts de R] [ execute-r-script] módulo, que principais trabalho. Este módulo faz pré-processamento de dados, criar um modelo e previsões. No passo dados pré-processamento, os dados de entrada representados por uma cadeia de texto longa são transformados e convertidos numa moldura de dados. No passo do edifício modelo, um pacote de R externo "survival_2.37 7.zip" pela primeira vez está instalado para realizar análise de sobrevivência. Em seguida, a função "coxph" é executada depois de um tarefas de processamento de dados da série. Podem ser lidos os detalhes da função "coxph" para uma análise de sobrevivência da documentação R. No passo previsão, uma instância de teste for fornecida para o modelo de qualificado com a função "surfit" e a curva de sobrevivência para esta instância de teste for produzida como variável "curva". Por fim, é obtida a probabilidade de tempo de interesse. 

###<a name="experiment-flow"></a>Fluxo de experiência:

![experimentar fluxo][1]

####<a name="module-1"></a>Módulo 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
    
####<a name="module-2"></a>Módulo 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##<a name="limitations"></a>Limitações

Este serviço web pode demorar apenas os valores numéricos como variáveis de funcionalidade (colunas). A coluna "evento" pode demorar apenas o valor 0 ou 1. A coluna "hora" tem de ser um número inteiro positivo.

##<a name="faq"></a>PERGUNTAS MAIS FREQUENTES
Para perguntas mais frequentes sobre o consumo do serviço web ou publicação ao Azure Marketplace, consulte o artigo [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
