<properties
    pageTitle="Ligar a um serviço de Web de aprendizagem máquina | Microsoft Azure"
    description="Com c# ou Python, ligar a um serviço Web de aprendizagem do Azure máquina com uma chave de autorização."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016" 
    ms.author="garye" />


# <a name="connect-to-an-azure-machine-learning-web-service"></a>Ligar a um serviço de Web de aprendizagem máquina Azure

A experiência de programador do Azure máquina formação é uma API do serviço Web para efetuar previsões a partir de dados de entrada em tempo real ou no modo do lote. Utilize o Azure máquina aprendizagem Studio para criar previsões e implementar um serviço Web de aprendizagem do Azure máquina.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para saber mais sobre como criar e implementar um serviço Web de aprendizagem de máquina utilizando Studio de aprendizagem automática:

- Para obter um tutorial sobre como criar uma experiência no máquina aprendizagem Studio, consulte o artigo [criar a sua primeira experiência](machine-learning-create-experiment.md).
- Para obter detalhes sobre como implementar um serviço Web, consulte o artigo [Implementar um serviço Web de aprendizagem do computador](machine-learning-publish-a-machine-learning-web-service.md).
- Para mais informações sobre a aprendizagem máquina em geral, visite o [Centro de formação documentação do computador](https://azure.microsoft.com/documentation/services/machine-learning/).

## <a name="azure-machine-learning-web-service"></a>Serviço de máquina aprendizagem Web Azure ##

Com o serviço Web de aprendizagem do Azure máquina, uma aplicação externa comunica com um modelo de pontuação de fluxo de trabalho de máquina formação em tempo real. Uma chamada de serviço Web de aprendizagem máquina devolve resultados de previsão para uma aplicação externa. Para tornar um serviço Web de aprendizagem máquina de chamadas, ser efetuada com uma chave de API que é criada quando implementar uma previsão. O serviço Web de aprendizagem automática é baseado resto, uma escolha de arquitectura populares para projetos de programação na web.

Formação do Azure máquina tem dois tipos de serviços:

- Resposta de pedido de serviço (RRS) – uma latência baixa, altamente dimensionável serviço que fornece uma interface para os modelos de sem estado criado e implementado a partir do Studio de aprendizagem máquina.
- Lote execução serviço (BES) – um assíncrono esse pontuações de um lote de registos de dados de serviço.

Para mais informações sobre os serviços Web de aprendizagem automática, consulte o artigo [Implementar um serviço Web de aprendizagem do computador](machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obter uma chave de autorização de formação de máquina do Azure ##

Quando implementar a sua experiência, são geradas chaves de API para o serviço Web. Pode obter as teclas a partir de várias localizações.

## <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>A partir do portal do Microsoft Azure máquina aprendizagem Web Services

Inicie sessão no portal do [Microsoft Azure máquina aprendizagem Web Services](https://services.azureml.net) .

Para obter a chave de API para um serviço Web de aprendizagem do novo computador:

1. No portal do Azure máquina aprendizagem Web Services, clique em **Serviços Web** no menu superior.
2. Clique no serviço Web para a qual pretende obter a chave.
3. No menu superior, clique em **consumir**.
4. Copiar e guarde a **Chave primária**.


Para obter a chave de API para um serviço Web de aprendizagem máquina clássico:

1. No portal do Azure máquina aprendizagem Web Services, clique em **Serviços Web clássica** menu superior.
2. Clique no serviço Web com a qual está a trabalhar.
3. Clique no ponto final para o qual pretende obter a chave.
3. No menu superior, clique em **consumir**.
4. Copiar e guarde a **Chave primária**.

## <a name="classic-web-service"></a>Serviço Web clássico ##

 Também pode obter uma chave para um serviço Web clássico de máquina aprendizagem Studio ou o portal do Azure.

### <a name="machine-learning-studio"></a>Máquina aprendizagem Studio ###

1. No computador aprendizagem Studio, clique em **Serviços WEB** à esquerda.
2. Clique num serviço Web. A **chave de API** está no separador **DASHBOARD** .

### <a name="azure-portal"></a>Portal do Azure ###

1. Clique em **Máquina aprendizagem** à esquerda.
2. Clique em área de trabalho na qual está localizado o serviço Web.
3. Clique em **Serviços WEB**.
4. Clique num serviço Web.
5. Clique num ponto final. A "API chave" for para baixo no canto inferior direito.

## <a id="connect"></a>Ligar a um serviço Web de aprendizagem automática

Pode ligar para um serviço Web de aprendizagem de máquina utilizando qualquer linguagem de programação compatível com o pedido de HTTP e resposta. Pode visualizar exemplos no c#, Python e R a partir de uma página de ajuda do serviço Web de aprendizagem do computador.

**Ajudar a API de aprendizagem automática** Ajuda de aprendizagem API máquina é criada quando implementar um serviço Web. Consulte o artigo [formação de máquina Azure tutorial - implementar o serviço Web](machine-learning-walkthrough-5-publish-web-service.md).
A ajuda de máquina aprendizagem API contém detalhes sobre uma previsão de serviço Web.

2. Clique no serviço Web com a qual está a trabalhar.
3. Clique no ponto final para o qual pretende ver a página de ajuda de API.
3. No menu superior, clique em **consumir**.
3. Clique em **página de ajuda a API** ao abrigo do pedido de resposta ou pontos finais de execução de lote.

**Para a vista de aprendizagem máquina API ajuda para um serviço Web novo**

Na máquina Azure Portal de serviços Web de aprendizagem:

1. Clique em **Serviços WEB** no menu superior.
2. Clique no serviço Web para a qual pretende obter a chave.

Clique em **consumir** para obter o URIs Reposonse pedido e serviços de execução de lote código de exemplo no c#, R e Python.

Clique em **Swagger API** para obter a documentação de Swagger com base para APIs chamadas a partir do URIs fornecido.

### <a name="c-sample"></a>Exemplo c# ###

Para ligar a um serviço Web de aprendizagem máquina, utilize uma **HttpClient** prisma ScoreData. ScoreData contém um FeatureVector, um vector n dimensionais das funcionalidades numéricos que representa o ScoreData. Autenticar para o serviço de aprendizagem máquina com uma chave de API.

Para ligar a um serviço Web de aprendizagem máquina, tem de estar instalado o pacote de NuGet **Microsoft.AspNet.WebApi.Client** .

**Instalar Microsoft.AspNet.WebApi.Client NuGet no Visual Studio**

1. Publicar o conjunto de dados de transferência do UCI: conjunto de dados de classe 2 adultos serviço Web.
2. Clique em **Ferramentas** > **Gestor de pacotes NuGet** > **Consola do Gestor de pacote**.
2. Selecione o **pacote de instalação Microsoft.AspNet.WebApi.Client**.

**Para executar o código de exemplo**

1. Publicar "exemplo 1: Transferir o conjunto de dados a partir do UCI: conjunto de dados de classe de adulto 2" experiência, a parte da coleção de exemplo de máquina aprendizagem.
2. Atribua apiKey com a chave de um serviço Web. Consulte o artigo **obter uma chave de autorização de formação do Azure máquina** acima.
3. Atribua serviceUri com o URI pedido.


### <a name="python-sample"></a>Exemplo de Python ###

Para ligar a um serviço Web de aprendizagem máquina, utilize a biblioteca de **urllib2** prisma ScoreData. ScoreData contém um FeatureVector, um vector n dimensionais das funcionalidades numéricos que representa o ScoreData. Autenticar para o serviço de aprendizagem máquina com uma chave de API.


**Para executar o código de exemplo**

1. Implementar "exemplo 1: Transferir o conjunto de dados a partir do UCI: conjunto de dados de classe de adulto 2" experiência, a parte da coleção de exemplo de máquina aprendizagem.
2. Atribua apiKey com a chave de um serviço Web. Consulte a secção **obter uma chave de autorização de formação do Azure máquina** perto do início deste artigo.
3. Atribua serviceUri com o URI pedido.
