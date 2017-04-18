<properties
    pageTitle="Adicionar as notificações push para a sua aplicação Xamarin.Android | Azure de aplicação de serviço"
    description="Saiba como utilizar a aplicação de serviço de Azure e concentradores de notificação do Azure para enviar notificações push para a sua aplicação Xamarin.Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Adicionar as notificações push para a sua aplicação Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Descrição geral


Neste tutorial, adicione as notificações push ao projeto [Xamarin.Android rápida iniciar](app-service-mobile-windows-store-dotnet-get-started.md) para que uma notificação de emissão é enviada para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor do guia de introdução do transferido, é necessário o pacote de extensão de notificações push. Consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.


##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial requer o seguinte:

+ Uma conta do Google ativa. Pode inscrever-se para uma conta do Google em [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
+ [Componente do cliente de mensagens do Google Cloud](http://components.xamarin.com/view/GCMClient/).

##<a name="configure-hub"></a>Configurar um concentrador de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a id="register"></a>Ativar Firebase na nuvem mensagens

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

##<a name="configure-azure-to-send-push-requests"></a>Configurar o Azure para enviar pedidos de emissão

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

##<a id="update-server"></a>Actualizar o servidor de projecto para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a id="configure-app"></a>Configurar o projecto do cliente para as notificações push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Adicionar o código de notificações push para a sua aplicação

[AZURE.INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Teste as notificações push na sua aplicação

Pode testar a aplicação ao utilizar um dispositivo virtual no emulador. Existem passos de configuração adicionais necessários ao usar uma emulador.

1. Certifique-se de que está a implementar ou depuração num dispositivo virtual que tenha APIs Google definir como alvo, conforme apresentado abaixo no Gestor de dispositivo Virtual Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Adicionar uma conta do Google para o dispositivo Android ao clicar em **aplicações** > **Definições** > **Adicionar conta**, em seguida, siga os pedidos.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Executar a aplicação de fazer como antes e insira um novo item de todo. Desta vez, é apresentado um ícone de notificação na área de notificação. Pode abrir o nível de notificação para ver o texto completo da notificação.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)


<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
