<properties 
    pageTitle="Diferença no teste proporções | Microsoft Azure" 
    description="Diferença no proporções teste" 
    services="machine-learning" 
    documentationCenter="" 
    authors="aniedea" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="aniedea"/> 


#<a name="difference-in-proportions-test"></a>Diferença no proporções teste


Duas proporções são estatisticamente diferentes? Suponha que pretende que um utilizador comparar duas filmes para determinar se um filme tem uma proporção significativamente superior das 'gostos' quando em comparação com o outro. Com grandes de amostras, podem existir uma diferença entre as proporções 0,50 e 0.51 estatisticamente significativa. Com uma pequena amostra, não poderá dados suficientes para determinar se estes proporções são realmente diferentes. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) realize um teste hipótese da diferença em duas proporções com base na entrada do utilizador do número de sucessos e o número total de avaliações para os grupos de comparação de 2. Um cenário possíveis, este serviço web poderá chamar-se a partir de uma aplicação de comparação de filme que o informa o utilizador se um dos filmes é realmente 'Gosto' com mais frequência do que outra, com base em classificações de filme.

>Este serviço web pode ser consumido por utilizadores – potencialmente através da aplicação móvel, através de um Web site ou mesmo num computador local, por exemplo. Mas o objetivo do serviço web é também servir como um exemplo de como Azure máquina formação pode ser utilizada para criar serviços web na parte superior de código R. Com apenas alguns linhas de código de R e cliques de um botão dentro Azure máquina aprendizagem Studio, uma experiência pode ser criada com o código de R e publicada como um serviço web. O serviço web, em seguida, pode ser publicado ao Azure Marketplace e média consumido por utilizadores e dispositivos em todo o mundo com a configuração não infraestrutura autor do serviço web.


##<a name="consumption-of-web-service"></a>Consumo de serviço web

Este serviço aceita 4 argumentos e uma hipótese teste de proporções.

Sejam os argumentos de entrada:

* Successes1 - número de eventos de sucesso em amostra 1.
* Successes2 - número de eventos de sucesso em amostra 2.
* Total1 - tamanho da amostra 1.
* Total2 - tamanho da amostra 2.

O resultado do serviço é o resultado da hipótese de teste juntamente com o chi-square estatística, df, valor-p e proporção no limites de 1/2 e intervalo de confiança de exemplo.

>Este serviço alojado no Azure Marketplace, é um serviço de OData; Estes podem ser chamados através de métodos de mensagem ou obter. 

Existem várias formas de consumir o serviço de forma automática (é uma aplicação de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Inicial código c# para consumo de serviço web:

    public class Input
    {
            public string successes1;
            public string successes2;
            public string total1;
            public string total2;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();
    
            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
    
            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


##<a name="creation-of-web-service"></a>Criação de serviço web

>Este serviço web foi criado utilizando o Azure máquina aprendizagem. Para uma avaliação gratuita, bem como vídeos introdutórios sobre como criar experiências e [publicação serviços web](machine-learning-publish-a-machine-learning-web-service.md), consulte o artigo [azure.com/ml](http://azure.com/ml). Segue-se uma captura de ecrã da experiência que criou o código de exemplo e de serviço web para cada um dos módulos dentro da experiência.

A partir do Azure máquina formação, uma nova experiência em branco foi criada com duas [Executar scripts de R] [ execute-r-script] módulos. No módulo primeiro que o esquema de dados é definido, enquanto a segunda, módulo utiliza o comando prop.test do R para executar o teste de hipótese para 2 proporções. 


###<a name="experiment-flow"></a>Fluxo de experiência:

![Fluxo de experiência][2]


####<a name="module-1"></a>Módulo 1:
    ####Schema definition  
    data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
    maml.mapOutputPort("data.set"); #send data to output port
    dataset1 = maml.mapInputPort(1) #read data from input port
    

####<a name="module-2"></a>Módulo 2:

    test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

    result=data.frame(
    result=ifelse(test$p.value<0.05,"The proportions are different!",
    "The proportions aren't different statistically."),
    ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
    pvalue=round(test$p.value,4),
    proportion1=round(test$estimate[1],4),
    proportion2=round(test$estimate[2],4),
    confintlow=round(test$conf.int[1],4),
    confinthigh=round(test$conf.int[2],4)) 

    maml.mapOutputPort("result"); #output port
    

##<a name="limitations"></a>Limitações 

Este é um exemplo de um teste de diferença em 2 proporções muito simple. Como podem ser vistos a partir do código de exemplo acima, captura nenhum erro é implementada e o serviço assume que todas as variáveis são contínuas.

##<a name="faq"></a>PERGUNTAS MAIS FREQUENTES
Para perguntas mais frequentes sobre o consumo do serviço web ou publicação ao Azure Marketplace, consulte o artigo [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
