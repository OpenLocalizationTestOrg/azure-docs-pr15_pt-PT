<properties 
    pageTitle="Modelo de cluster | Microsoft Azure" 
    description="Modelo de cluster" 
    services="machine-learning" 
    documentationCenter="" 
    authors="FrancescaLazzeri" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="lazzeri"/> 


#<a name="cluster-model"></a>Modelo de cluster    

Como podemos prever grupos de comportamentos dos sistema de crédito para reduzir o risco desativar encargo de cartão de crédito grandes Como podemos definir grupos de traços personalidade dos empregados para melhorar o seu desempenho no trabalho? Como podem médicos classificar pacientes em grupos com base nas características da suas doenças? Em princípio, todas estas perguntas podem ser atendidas através de análise de cluster.   


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 
   
Análise de cluster classifica um conjunto de observações em duas ou mais grupos desconhecidos mutuamente com base em combinações de variáveis. O objetivo da análise do cluster é descobrir um sistema de organizar observações, normalmente, pessoas ou suas características, em grupos, onde os membros dos grupos partilham propriedades em comum. Este [serviço](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) utiliza a metodologia de K meios, uma técnica clusters utilizada mais frequentemente, para dados arbitrário cluster em grupos. Este serviço web coloca os dados e o número de k clusters como entrada e produz previsões dos quais dos grupos a que pertence cada observações k. 

>Este serviço web pode ser consumido por utilizadores – potencialmente através da aplicação móvel, através de um Web site ou mesmo num computador local, por exemplo. Mas o objetivo do serviço web é também servir como um exemplo de como Azure máquina formação pode ser utilizada para criar serviços web na parte superior de código R. Com apenas alguns linhas de código de R e cliques de um botão dentro Azure máquina aprendizagem Studio, uma experiência pode ser criada com o código de R e publicada como um serviço web. O serviço web, em seguida, pode ser publicado ao Azure Marketplace e média consumido por utilizadores e dispositivos em todo o mundo com a configuração não infraestrutura autor do serviço web.  

##<a name="consumption-of-web-service"></a>Consumo de serviço web   
Este serviço web agrupa os dados de um conjunto de grupos de k e exporta a atribuição de grupo para cada linha. O serviço web espera para a entrada de dados como uma cadeia de onde as linhas são separadas por vírgulas (,) e as colunas são separadas por ponto e vírgula (;)) do utilizador final. O serviço web espera 1 linha, uma vez. Um conjunto de dados de exemplo poderia ter este aspeto:

![Dados de exemplo][1]

Suponha que o utilizador pretendia separar estes dados em grupos mutuamente exclusivos 3. Os dados de entrada para o conjunto de dados acima seria o seguinte: valor = "10; 5; 2,18; 1; 6,7; 5; 5,22; 3; 4,12; 2; 1,10; 3; 4"; n = "3". O resultado é a associação a grupos previsto para cada uma das linhas.

>Este serviço alojado no Azure Marketplace, é um serviço de OData; Estes podem ser chamados através de métodos de mensagem ou obter. 

Existem várias formas de consumir o serviço de forma automática (é uma aplicação de exemplo [aqui](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

###<a name="starting-c-code-for-web-service-consumption"></a>Inicial código c# para consumo de serviço web:

    public class Input
    {
            public string value;
            public string k;
    }
    
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
    
    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

A partir do Azure máquina formação, foi criada uma nova experiência em branco e duas [Executar scripts de R] [ execute-r-script] módulos movido para a área de trabalho. O esquema de dados foi criado com um simples [Executar scripts de R][execute-r-script]. Em seguida, o esquema de dados estava ligado à secção de modelo de cluster, novamente criada com um [Script de R executar][execute-r-script]. No [Executar scripts de R] [ execute-r-script] utilizado para o modelo de cluster, o serviço web, em seguida, utiliza a função "k-significa", que é pré-concebidas para o [Executar scripts de R] [ execute-r-script] da Azure máquina aprendizagem.    
   

     
![Fluxo de experiência][3]

####<a name="module-1"></a>Módulo 1: 
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
    
    maml.mapOutputPort("mydata");     
    

####<a name="module-2"></a>Módulo 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");
   
 
##<a name="limitations"></a>Limitações
Este é um exemplo de um serviço web clusters muito simple. Como podem ser vistos a partir do código de exemplo acima, captura nenhum erro é implementada e o serviço assume tudo o que é uma variável de contínua (não categorias funcionalidades permitidas), como os valores numéricos serviço apenas entradas no momento da criação deste serviço web. Além disso, o serviço atualmente trata tamanho dos dados limitado, devido à natureza pedido/resposta de chamada de serviço web e o facto do modelo está a ser ajustar sempre que chama-se o serviço web. 

##<a name="faq"></a>PERGUNTAS MAIS FREQUENTES
Para perguntas mais frequentes sobre o consumo do serviço web ou publicação ao Azure Marketplace, consulte o artigo [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 
