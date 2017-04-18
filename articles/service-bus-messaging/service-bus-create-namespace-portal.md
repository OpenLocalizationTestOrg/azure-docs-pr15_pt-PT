<properties
    pageTitle="Criar um espaço de nomes de serviço Bus através do portal Azure | Microsoft Azure"
    description="Para poder começar com o serviço Bus, terá de um espaço de nomes. Eis como criar um através do portal Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>

# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Criar um espaço de nomes de serviço Bus através do portal Azure

Um espaço de nomes é um contentor comuns para todos os componentes de mensagens. Várias filas e tópicos podem residem num único espaço de nomes e espaços de nomes servem frequentemente contentores de aplicação. Existem duas formas para criar um espaço de nomes de serviço Bus.

1.  Portal Azure (Este artigo)

2.  [Modelos de Gestor de recursos][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Criar um espaço de nomes no portal do Azure

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Agora ter criado um espaço de nomes de serviço Bus mensagens.

## <a name="next-steps"></a>Próximos passos

Veja os nossos [GitHub amostras] [ github-samples] que mostrar algumas das funcionalidades mais avançadas do Azure Service Bus Messaging.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples