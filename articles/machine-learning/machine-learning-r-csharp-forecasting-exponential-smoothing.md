<properties 
    pageTitle="Suavização previsão-exponencial | Microsoft Azure" 
    description="Serviço Web: suavização previsão-exponencial" 
    services="machine-learning" 
    documentationCenter="" 
    authors="xueshanz" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="xueshzha"/> 


#<a name="forecasting---exponential-smoothing"></a>Previsão - suavização exponencial 

Este [serviço web]( https://datamarket.azure.com/dataset/aml_labs/ets) implementa o modelo de suavização exponencial (ETS) para produzir previsões com base em dados de histórico fornecidos pelo utilizador. A procura de um produto específico aumentará este ano? Pode posso prever meu vendas de produtos para a época de boas festas, para que posso pode planear eficazmente meu inventário? Modelos de previsão são provável que essas questões de endereços. Estes modelos tendo em conta os últimos dados, examinam tendências ocultas e sazonalidade para prever tendências futuras.  


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
>Este serviço web pode ser consumido por utilizadores – potencialmente através da aplicação móvel, através de um Web site ou mesmo num computador local, por exemplo. Mas o objetivo do serviço web é também servir como um exemplo de como Azure máquina formação pode ser utilizada para criar serviços web na parte superior de código R. Com apenas alguns linhas de código de R e cliques de um botão dentro Azure máquina aprendizagem Studio, uma experiência pode ser criada com o código de R e publicada como um serviço web. O serviço web, em seguida, pode ser publicado ao Azure Marketplace e média consumido por utilizadores e dispositivos em todo o mundo com a configuração não infraestrutura autor do serviço web.
 
##<a name="consumption-of-web-service"></a>Consumo de serviço web 
 
Este serviço aceita 4 argumentos e calcula as previsões ETS.
Sejam os argumentos de entrada:

* Frequência - indica a frequência dos dados não processados (Diária/Semanal/Mensal/trimestral/anual).
* Horizonte - futuro previsão período de tempo.
* Data - adicionar os novos dados da série de tempo para o tempo.
* Valor - adicionar os novos valores de dados de série de tempo.

O resultado do serviço é os valores calculados previsão.

Entrada de exemplo pode ser: 

* Frequência - 12
* Horizonte - 12
* Data - 1/15/2012 15/2/2012; 3/15/2012; 4/15/2012; 5/15/2012; 15/6/2012; 15/7/2012; 8 / 15/2012; 9/15/2012 15/10/2012; 15/11/2012; 12/15/2012; 1/15/2013 2/15/2013; 3/15/2013; 15/4/2013; 5/15/2013; 6/15/2013; 15/7/2013; 8 / 15/2013 9/15/2013; 10/15/2013; 11/15/2013; 12/15/2013; 15/1/2014; 15/2/2014; 3/15/2014; 15/4/2014; 15/5/2014; 6/15/2014; 15/7/2014; 8 / 15/2014; 15/9/2014
* Valor - 3.479; 3.68; 3.832; 3.941; 3.797; 3.586; 3.508; 3.731; 3.915; 3.844; 3.634; 3.549; 3.557; 3.785; 3.782; 3.601; 3.544; 3.556; 3.65; 3.709; 3.682; 3.511; 3.429 3.51; 3.523; 3.525; 3.626; 3.695; 3.711; 3.711; 3.693; 3.571; 3.509
 
>Este serviço alojado no Azure Marketplace, é um serviço de OData; Estes podem ser chamados através de métodos de mensagem ou obter. 

Existem várias formas de consumir o serviço de forma automática (é uma aplicação de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx)).

###<a name="starting-c-code-for-web-service-consumption"></a>Inicial código c# para consumo de serviço web:
    public class Input
    {
            public string frequency;
            public string horizon;
            public string date;
            public string value;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
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

A partir da dentro de aprendizagem do Azure máquina, foi criada uma nova experiência em branco. Dados de entrada de exemplo foram carregados com um esquema de dados predefinido. Ligadas para o esquema de dados é uma [Executar scripts de R] [ execute-r-script] módulo que gera ETS previsão modelo utilizando funções 'ets' e 'de previsão' a partir de R. 


![Fluxo de experiência][2]

####<a name="module-1"></a>Módulo 1:
 
    # Add in the CSV file with the data in the format shown below 
![Exemplo de dados][3]   

####<a name="module-2"></a>Módulo 2:
    # Data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)
    
    # Preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]
    
    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)
    
    # Fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- ets(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)
    
    # Produce forcasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
    
    # Data output
    maml.mapOutputPort("data.forecast");

 
##<a name="limitations"></a>Limitações 

Este é um exemplo muito simple para ETS previsão. À medida que podem ser vistos a partir do código de exemplo acima, captura nenhum erro é implementado e o serviço assume que todas as variáveis são valores de contínua/positivo e a frequência deve ser um número inteiro maior do que 1. O comprimento de vectores de data e o valor deve ser o mesmo. Deve aderir a variável de data no formato de ' dd/mm/aaaa'.

##<a name="faq"></a>PERGUNTAS MAIS FREQUENTES
Para perguntas mais frequentes sobre o consumo do serviço web ou publicação ao Azure Marketplace, consulte o artigo [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
