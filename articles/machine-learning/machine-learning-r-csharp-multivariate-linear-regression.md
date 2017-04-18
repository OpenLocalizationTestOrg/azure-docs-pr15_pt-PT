<properties 
    pageTitle="Multivariate regressão Linear | Microsoft Azure" 
    description="Multivariate regressão Linear" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="multivariate-linear-regression"></a>Multivariate regressão Linear   
 

 
Imaginemos que tem um conjunto de dados e gostaria de prever rapidamente uma variável de dependente (y) para cada utilizador (i), com base em variáveis independentes. Regressão linear é uma técnica de estatística popular utilizada para essas previsões. Aqui y variável dependente é considerado um valor contínuo.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

Um cenário simples poderão ser onde investigador está a tentar prever a espessura de um indivíduo (y), com base na sua altura (x). Um cenário mais avançado pode dever onde investigador tem informações adicionais para individuais (tais como espessura, género, corrida) e tentativas de prever a espessura da pessoa. Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) se ajuste o modelo de regressão linear aos dados e devolve o valor previsto (y) para cada um dos observações dos dados.

>Este serviço web pode ser consumido por utilizadores – potencialmente através da aplicação móvel, através de um Web site ou mesmo num computador local, por exemplo. Mas o objetivo do serviço web é também servir como um exemplo de como Azure máquina formação pode ser utilizada para criar serviços web na parte superior de código R. Com apenas alguns linhas de código de R e cliques de um botão dentro Azure máquina aprendizagem Studio, uma experiência pode ser criada com o código de R e publicada como um serviço web. O serviço web, em seguida, pode ser publicado ao Azure Marketplace e média consumido por utilizadores e dispositivos em todo o mundo com a configuração não infraestrutura autor do serviço web.  

##<a name="consumption-of-web-service"></a>Consumo de serviço web  
Este serviço web dá-da variável dependente com base em variáveis independentes para todas as observações os valores previstos. O serviço web espera para a entrada de dados como uma cadeia de onde as linhas são separadas por vírgulas (,) e as colunas são separadas por ponto e vírgula (;)) do utilizador final. O serviço web espera 1 linha, uma vez e a primeira coluna para ser variável dependente de espera. Um conjunto de dados de exemplo poderia ter este aspeto:

![Dados de exemplo][1]

Observações sem uma variável dependente devem ser introduzidas como "Disp" para y. Os dados de entrada para o conjunto de dados acima seria a cadeia seguinte: "10; 5; 2,18; 1; 6,6; 5.3; 2.1,7; 5; 5,22; 3; 4,12; 2; 1, disp; 3; 4". O resultado é o valor previsto para cada uma das linhas com base em variáveis independentes. 

>Este serviço alojado no Azure Marketplace, é um serviço de OData; Estes podem ser chamados através de métodos de mensagem ou obter. 

Existem várias formas de consumir o serviço de forma automática (é uma aplicação de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Inicial código c# para consumo de serviço web:

    public class Input
    {
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text };
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


A partir do Azure máquina formação, foi criada uma nova experiência em branco e duas [Executar scripts de R] [ execute-r-script] módulos foram movidos para a área de trabalho. Este serviço web é executado uma experiência de aprendizagem do Azure máquina com um script R subjacente. Existem 2 peças para esta experiência: definição de esquema e o modelo de formação + pontuação. O primeiro módulo define a estrutura esperada do conjunto de dados entrada, onde a primeira variável é a variável de dependentes e as variáveis restantes são independentes. O segundo módulo ajusta-se um modelo de regressão linear genérico para os dados de entrada.  
  
![Fluxo de experiência][3]

####<a name="module-1"></a>Módulo 1:
 
####<a name="schema-definition"></a>Definição de esquema  
    data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####<a name="module-2"></a>Módulo 2:
####<a name="lm-modeling"></a>Modelação de LM   
    data <- maml.mapInputPort(1) # class: data.frame  
  
    data.split <- strsplit(data[1,1], ",")[[1]]  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
    data.split <- as.data.frame(t(data.split)) 
    data.split <- data.matrix(data.split) 
    data.split <- data.frame(data.split) 
    model <- lm(data.split)  

    out=data.frame(predict(model,data.split))  
    out <- data.frame(t(out))

    maml.mapOutputPort("out");  
 
##<a name="limitations"></a>Limitações
Este é um exemplo de um serviço de web de regressão linear vários muito simple. Como podem ser vistos a partir do código de exemplo acima, captura nenhum erro é implementada e o serviço assume tudo o que é uma variável de contínua (não categorias funcionalidades permitidas), como os valores numéricos serviço apenas entradas no momento da criação deste serviço web. Além disso, o serviço atualmente trata tamanho dos dados limitado, devido à natureza pedido/resposta de chamada de serviço web e o facto do modelo está a ser ajustar sempre que chama-se o serviço web. 

##<a name="faq"></a>PERGUNTAS MAIS FREQUENTES
Para perguntas mais frequentes sobre o consumo do serviço web ou publicação ao Azure Marketplace, consulte o artigo [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
