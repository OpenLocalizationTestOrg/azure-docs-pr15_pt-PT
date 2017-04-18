<properties 
    pageTitle="Exemplos criados com R os serviços web de aprendizagem máquina | Microsoft Azure" 
    description="Localize um conjunto úteis do web exemplos de serviços criados com o código de R e formação de máquina e publicados no Azure Marketplace." 
    keywords="csharp código r, exemplos de serviços web"
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


#<a name="web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>Web dos serviços de exemplos de como utilizar o código de R no Azure máquina aprendizagem e publicados no Microsoft Azure Marketplace

Neste artigo são web exemplo serviços foram criados utilizando o Azure máquina aprendizagem e publicados Azure Marketplace. Cada exemplo de serviço web tem um documento completo anexado, incorporação de conjuntos de dados de exemplo para testar os serviços e que explica como o utilizador pode criar um serviço semelhante próprios. 

No Azure máquina aprendizagem Studio, os utilizadores podem escrever código R e com alguns cliques, publicá-lo como um serviço web para aplicações e dispositivos consumir em todo o mundo. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


A partir do produzir calculadoras simples que fornecem funcionalidades estatística para a criação de um predictor de análise de sentimento extração de texto personalizado, novos e mais experientes utilizadores R beneficiar facilidade com a qual os utilizadores do Azure máquina aprendizagem podem operationalize código R. Enquanto está estes web services podem ser consumido por utilizadores, potencialmente através da aplicação móvel ou um Web site, o objetivo destes exemplos de serviços web Mostrar como formação de máquina pode operationalize R scripts para fins analíticos e ser utilizado para criar serviços web na parte superior de código R.

Cada exemplo inclui um exemplo c# para o consumo de serviço web.


![Diagrama de código de R no Azure máquina formação: soluções de R para especialidades utilize ou publicados no Azure Marketplace.][1]

Considere os cenários seguintes.

##<a name="scenario-1-generic-model"></a>Cenário 1: Modelo de genérico 
Um utilizador funciona com um modelo de genérico que pode ser aplicado a dados de um novo utilizador, como uma previsão base de dados da série de tempo ou um método de R com análise avançadas. Este utilizador publica o modelo, como um serviço web para que outras pessoas consumir com os seus dados.



* [Classificador binário](machine-learning-r-csharp-binary-classifier.md)
* [Modelo de cluster](machine-learning-r-csharp-cluster-model.md)
* [Multivariate regressão Linear](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Previsão - suavização exponencial](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [ETS + STL previsão](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Previsão - Autoregressive integrada mover média (ARIMA)](machine-learning-r-csharp-arima.md)
* [Análise de sobrevivência](machine-learning-r-csharp-survival-analysis.md)


##<a name="scenario-2-trained-model--specific-data"></a>Cenário 2: Formação modelo – dados específicos 
Um utilizador contém os dados que fornece previsões útil através de código R, tal como um grandes de amostras dos questionários personalidade agrupadas através de um algoritmo de k meios para prever do utilizador personalidade tipo ou dados de inquérito de estado de funcionamento que podem ser utilizados para prever risco de uma pessoa para cancro pulmonar com um pacote de análise R sobrevivência. O utilizador publica os dados através de um serviço web que prevê, com um resultado de um novo utilizador.

##<a name="scenario-3-trained-model--generic-data"></a>Cenário 3: Formação modelo – dados genéricos 
Um utilizador tem dados genéricos (tal como um corpo de texto), que permite que um serviço web ser criadas e aplicados forma genérica através de diferentes tipos de casos de utilização e cenários.

* [Léxico com base sentimento análise](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##<a name="scenario-4-advanced-calculator"></a>Cenário 4: Calculadora de avançadas 
Um utilizador fornece cálculos avançados ou simulações não exigem início de qualquer modelo com formação ou montagem de um modelo de dados do utilizador.

* [Diferença no proporções teste](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Distribuição normal conjunto de aplicações](machine-learning-r-csharp-normal-distribution.md)
* [Distribuição binomial conjunto de aplicações](machine-learning-r-csharp-binomial-distribution.md)

##<a name="faq"></a>PERGUNTAS MAIS FREQUENTES
Para perguntas mais frequentes sobre o consumo do serviço web ou publicar no mercado, consulte o artigo [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 
