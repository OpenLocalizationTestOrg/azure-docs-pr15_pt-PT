<properties
    pageTitle="Implementação de aplicações do Azure de aplicação de serviço"
    description="Saiba como implementar aplicações para o trabalho de aplicação de serviço"
    keywords="serviço de aplicação de serviço, azure de aplicação, implementar, implementação"
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="dariagrigoriu"/>

# <a name="azure-app-service-deployment-overview"></a>Descrição geral de implementação do Azure de aplicação de serviço

Azure de aplicação de serviço fornece uma funcionalidade avançada e integrada definida como suporta a criação de fluxos de trabalho de implementação poderosa e flexível. Implementação de aplicações pode tirar partido de opções que incluam integração contínua ou controlo de origem local de publicação, WebDeploy e FTP. O método recomendado para implementação da aplicação de produção é trocar ranhura de implementação. Faixas de implementação representam ambientes de transição e integração associados com aplicações de produção. Podem ser configuradas e direccionadas com tráfego da web para validação faixas de implementação e tráfego pode ser trocado a pedido para a implementação produção com sem hora para baixo e automatizado aquecimento. Os passos de um fluxo de trabalho de implementação podem ser facilmente automatizados através de produtos de gestão de lançamento, como o Visual Studio solte gestão. Isto é útil para coordená-lo com outros recursos de solução (por exemplo arquivo de dados), periodicidade e a replicação em múltiplas unidades de implementação. 

[AZURE.INCLUDE [app-service-blueprint-deployment](../../includes/app-service-blueprint-deployment.md)]
