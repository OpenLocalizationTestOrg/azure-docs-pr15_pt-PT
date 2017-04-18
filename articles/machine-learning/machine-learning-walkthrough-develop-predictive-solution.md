<properties
    pageTitle="Uma solução para o risco de crédito com formação de máquina de aspeto do Office | Microsoft Azure"
    description="Obter instruções detalhadas sobre que mostra como criar uma solução de análise de aspeto do Office para avaliação de riscos de crédito no Azure máquina aprendizagem Studio."
    keywords="risco de crédito, a solução de análise de aspeto do Office, a avaliação de riscos"
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
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Tutorial: Desenvolver uma solução de análise de aspeto do Office para avaliação de riscos de crédito no Azure máquina aprendizagem

Suponha que necessita prever risco de crédito de um indivíduo com base na informação derem num pedido de crédito.  

Avaliação de riscos de crédito é um problema complexo, claro, mas vamos um pouco a simplificar os parâmetros da pergunta. Em seguida, podemos pode utilizá-lo como um exemplo de como pode utilizar o Microsoft Azure máquina Learning com Studio de aprendizagem automática e o serviço web de aprendizagem automática para criar uma solução de análise de aspeto do Office.  

Neste tutorial detalhadas, podemos terá de seguir o processo de desenvolver um modelo de análise de aspeto do Office no computador aprendizagem Studio e, em seguida, implementá-lo como um serviço web de aprendizagem de máquina Azure. Vamos começar com dados de risco de crédito publicamente disponível, desenvolver e formar um modelo de aspeto do Office com base em que os dados e, em seguida, implementar o modelo como um serviço web que pode ser utilizado por outras pessoas para avaliação de riscos de crédito.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

>[AZURE.TIP] Para transferir e imprimir um diagrama que apresenta uma visão geral das capacidades do máquina aprendizagem Studio, consulte o [diagrama de descrição geral das capacidades do Azure máquina aprendizagem Studio](machine-learning-studio-overview-diagram.md).

Para criar uma solução de avaliação de riscos de crédito, podemos irá siga estes passos:  

1.  [Criar uma área de trabalho de aprendizagem automática](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carregar dados existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Criar uma nova experiência](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Dar formação e avaliar os modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [Implementar o serviço web](machine-learning-walkthrough-5-publish-web-service.md)
6.  [Aceder ao serviço web](machine-learning-walkthrough-6-access-web-service.md)

Este tutorial baseia-se uma versão simplificada da [Classfication binário: previsão de risco de crédito](http://go.microsoft.com/fwlink/?LinkID=525270) experiência de exemplo na [Galeria de informações da empresa Cortana](http://gallery.cortanaintelligence.com/).
