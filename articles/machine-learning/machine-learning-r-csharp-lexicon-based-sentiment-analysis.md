<properties 
    pageTitle="Léxico com base sentimento análise | Microsoft Azure" 
    description="Léxico com base sentimento análise" 
    services="machine-learning" 
    documentationCenter="" 
    authors="pengxia" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="pengxia"/> 



#<a name="lexicon-based-sentiment-analysis"></a>Léxico com base sentimento análise 

Como pode medir dos utilizadores opiniões e atitudes por defeito até marcas ou tópicos onlinehttps redes sociais, tal como o Facebook publicar, tweets, revisões, etc.? Análise de sentimento fornece um método para analisar esses perguntas.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Normalmente, existem dois métodos para uma análise sentimento. Um está a utilizar um algoritmo de aprendizagem controlados e o outro pode ser tratado como deficiente aprendizagem. Um algoritmo de aprendizagem controlados geralmente cria um modelo de classificação num grande corpo anotado. Sua precisão é principalmente com base na qualidade da anotação e normalmente o processo de formação irá demorar muito tempo. Para além de que, quando o algoritmo é aplicada para outro domínio, o resultado, normalmente, não é recomendável. Em comparação com controlados aprendizagem, com base em léxico utilizações de aprendizagem deficiente um dicionário sentimento, que não necessita de armazenar um corpo de dados grandes e formação - que faz com que todo o processo muito mais rápido. 

O nosso [serviço](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) é criada com base em léxico de elemento subjectivo de MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), que é uma das léxicos elemento subjectivo utilizadas com mais frequência. Existem 5097 negativas e 2533 positivas palavras MPQA. E todas estas palavras são anotadas como polaridade forte ou fraca. O corpo inteiro está em licença pública GNU geral. O serviço web pode ser aplicado a todas as frases breves, tais como tweets e mensagens do Facebook. 

>Por exemplo este serviço web poderia consumido por utilizadores – potencialmente através de uma aplicação móvel, através de um Web site ou mesmo num computador local. Mas o objetivo do serviço web é também servir como um exemplo de como Azure máquina formação pode ser utilizada para criar serviços web na parte superior de código R. Com apenas alguns linhas de código de R e cliques de um botão dentro Azure máquina aprendizagem Studio, uma experiência pode ser criada com o código de R e publicada como um serviço web. O serviço web, em seguida, pode ser publicado ao Azure Marketplace e média consumido por utilizadores e dispositivos em todo o mundo com a configuração não infraestrutura autor do serviço web.

##<a name="consumption-of-web-service"></a>Consumo de serviço web

Os dados de entrada podem ser qualquer texto, mas o serviço web funciona melhor com frases breves. O resultado é um valor numérico entre -1 e 1. Qualquer valor inferior a 0 indica que o sentimento do texto for negativo; Se estiver positivo superior a 0. O valor absoluto do resultado indica a intensidade do sentimento associado. 

>Este serviço alojado no Azure Marketplace, é um serviço de OData; Estes podem ser chamados através de métodos de mensagem ou obter. 

Existem várias formas de consumir o serviço de forma automática (é uma aplicação de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/)).

###<a name="starting-c-code-for-web-service-consumption"></a>Inicial código c# para consumo de serviço web:

    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();
    
                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



A entrada é "Hoje é um bom dia". O resultado é "1", que indica um sentimento positivo associado a frase de entrada. 

##<a name="creation-of-web-service"></a>Criação de serviço web
>Este serviço web foi criado utilizando o Azure máquina aprendizagem. Para uma avaliação gratuita, bem como vídeos introdutórios sobre como criar experiências e [publicação serviços web](machine-learning-publish-a-machine-learning-web-service.md), consulte o artigo [azure.com/ml](http://azure.com/ml). Segue-se uma captura de ecrã da experiência que criou o código de exemplo e de serviço web para cada um dos módulos dentro da experiência.


A partir da dentro de aprendizagem do Azure máquina, foi criada uma nova experiência em branco. A figura seguinte mostra o fluxo de experiência de análise de sentimento com base em léxico. O ficheiro "sent_dict.csv" é o léxico de elemento subjectivo MPQA e está definido como uma das entradas de [Executar scripts de R][execute-r-script]. Outra entrada é uma amostra rever a partir do conjunto de dados de rever Amazon para teste, onde é executado seleção, alteração de nome de coluna e dividir operações. Utilizamos um pacote de hash para armazenar o léxico elemento subjectivo na memória e acelerar o processo de pontuação no cálculo. O texto todo serão com token por pacote "tm" e em comparação com a palavra no dicionário sentimento. Por fim, uma pontuação será calculada ao adicionar a espessura de cada palavra subjectiva no texto. 

###<a name="experiment-flow"></a>Fluxo de experiência:

![experimentar fluxo][2]


####<a name="module-1"></a>Módulo 1:
    
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # <a name="install-hash-package"></a>Instalar o pacote de hash
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }
          
        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
    


##<a name="limitations"></a>Limitações

A partir de uma perspetiva algoritmo, análise com base em léxico sentimento é uma ferramenta de análise sentimento geral, que não poderá ter um melhor do que o método de classificação para os campos específicos. O problema de negação não é bem Tratado. Vamos hardcode negação várias palavras no nosso programa, mas a melhor forma de é utilizar um dicionário negação e criar algumas regras. O serviço web executa melhor no frases curtas e simples, tais como tweets e mensagens de Facebook, que no longos e complexos frases como revisões Amazon. 

##<a name="faq"></a>PERGUNTAS MAIS FREQUENTES
Para perguntas mais frequentes sobre o consumo do serviço web ou publicação ao Azure Marketplace, consulte o artigo [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 
