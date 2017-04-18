<properties
    pageTitle="Passo 6: Aceder ao serviço de web de aprendizagem máquina | Microsoft Azure"
    description="Passo 6 da desenvolver instruções solução aspeto do Office: aceder a um serviço de web formação de máquina do Azure active."
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


# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Tutorial passo 6: Aceder ao serviço de web de aprendizagem de máquina do Azure

Este é o último passo do tutorial, [desenvolver uma solução de análise de aspeto do Office no Azure máquina aprendizagem](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Criar uma área de trabalho de aprendizagem automática](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Dar formação e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)
6.  **Aceder ao serviço web**

----------

No passo anterior neste tutorial, vamos implementado um serviço web que utiliza o nosso modelo de previsão de risco de crédito. Agora os utilizadores têm de ser possível enviar dados para o mesmo e receber resultados. 

O serviço web é um serviço Azure web que possa receber e devolver dados utilizando REST APIs numa de duas formas:  

-   **Pedido/resposta** - o utilizador envia um ou mais linhas de dados de crédito para o serviço utilizando um protocolo HTTP e o serviço responde com um ou mais conjuntos de resultados.
-   **Execução lote** - o utilizador armazena um ou mais linhas de dados de crédito num BLOBs do Azure e, em seguida, envia a localização de BLOBs para o serviço. O serviço ganha todas as linhas de dados no blob entrado, armazena os resultados na outra blob e devolve o URL do recipiente.  

A forma mais rápida e fácil de aceder ao serviço web é entre os modelos de aplicação Web disponíveis no [Mercado de aplicações Web Azure](https://azure.microsoft.com/marketplace/web-applications/all/).
Estes modelos de aplicação web podem criar uma aplicação web personalizada que sabe o serviço web dados de entrada e o que irá devolver. Tudo o que precisa de fazer é fornecer acesso aos seus dados e o serviço web e o modelo é que os restantes.

Para mais informações sobre como utilizar os modelos de aplicação web, consulte o artigo [consumir um serviço web de aprendizagem do Azure máquina com um modelo de aplicação web](machine-learning-consume-web-service-with-web-app-template.md).

Também pode desenvolver uma aplicação personalizada para aceder ao serviço web utilizando o código de starter fornecido por si no R, c# e linguagens de programação Python.
Encontrará detalhes completos [como consumir um serviço web de aprendizagem do Azure máquina que foi publicado a partir de uma experiência de aprendizagem automática](machine-learning-consume-web-services.md).
