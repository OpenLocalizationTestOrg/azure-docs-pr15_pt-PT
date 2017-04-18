<properties
    pageTitle="Adicionar as notificações de emissão a aplicação com aplicações do Azure Mobile iOS"
    description="Saiba como utilizar aplicações do Azure Mobile para enviar notificações push para a sua aplicação iOS."
    services="app-service\mobile"
    documentationCenter="ios"
    manager="yochayk"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="yuaxu"/>


# <a name="add-push-notifications-to-your-ios-app"></a>Adicionar as notificações de emissão a sua aplicação do iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, adicione as notificações push ao projeto [iOS rápidas iniciar] para que uma notificação de emissão é enviada para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor do guia de introdução do transferido, é necessário o pacote de extensão de notificações push. Consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

[IOS simulator não suporta as notificações push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Precisa de um [programa de programador do Apple associação](https://developer.apple.com/programs/ios/)e um dispositivo iOS física.

##<a name="configure-hub"></a>Configurar o concentrador de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Registe-se a aplicação para as notificações push

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Atualizar back-end para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a id="add-push"></a>Adicionar as notificações push à aplicação

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Teste as notificações push

[AZURE.INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>Mais

* Modelos de dar-lhe flexibilidade de poder enviar em diferentes plataformas puxa e puxa localizadas. [Como utilizar iOS biblioteca do cliente para as aplicações móveis do Azure](app-service-mobile-ios-how-to-use-client-library.md#templates) mostra-lhe como registar modelos.

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[Guia de introdução do iOS]: app-service-mobile-ios-get-started.md
