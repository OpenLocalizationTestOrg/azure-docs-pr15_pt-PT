<properties
    pageTitle="Adicionar as notificações Push à aplicação Android com aplicações móveis do Azure"
    description="Saiba como utilizar aplicações do Azure Mobile para enviar notificações push para a sua aplicação Android."
    services="app-service\mobile"
    documentationCenter="android"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-android-app"></a>Adicionar as notificações Push para a sua aplicação Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, adicione as notificações push para o projeto de [Guia de introdução do Android] para que uma notificação de emissão é enviada para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor do guia de introdução do transferido, terá do pacote de extensão de notificações push. Para mais informações, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte:

* Um IDE dependendo back-end do seu projeto:

    * [Android Studio](https://developer.android.com/sdk/index.html) se esta aplicação tem Node.js back-end.

    * [Visual Studio Comunidade 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou posterior se esta aplicação tem .net back-end.

* Android 2.3 ou superior, revisão do Google repositório 27 ou superior e serviços do Google Play 9.0.2 ou superior para mensagens de nuvem Firebase.

* Conclua o [Guia de introdução do Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Criar um projeto que suporte Firebase nuvem mensagens

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um concentrador de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Ativar notificações push para o project server

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar as notificações push para a sua aplicação

Nesta secção, pode atualizar a aplicação Android do cliente para processar as notificações push.

### <a name="verify-android-sdk-version"></a>Verificar a versão do Android SDK

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

O passo seguinte é instalar os serviços do Google Play. Google Cloud mensagens tem algumas API nível requisitos mínimos para desenvolvimento e teste, qual a propriedade **minSdkVersion** no manifesto está em conformidade com.

Se estiver a testar com um dispositivo mais antigo, em seguida, consulte [Configurar o Google reproduzir SDK Services] para determinar como baixa pode defina este valor e defini-lo corretamente.

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play ao projeto

[AZURE.INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Adicionar código

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testar a aplicação em comparação com o serviço móvel publicado

Pode testar a aplicação ao diretamente anexar telemóvel Android com um cabo USB, ou ao utilizar um dispositivo virtual no emulador.

## <a name="more"></a>Mais

<!-- URLs -->
[Guia de introdução do Android]: app-service-mobile-android-get-started.md

[Configurar serviços do Google Play SDK]:https://developers.google.com/android/guides/setup
