<properties
    pageTitle="Adicionar autenticação no Android com aplicações Mobile | Azure de aplicação de serviço"
    description="Saiba como utilizar aplicações móveis no serviço de aplicação do Azure para autenticar os utilizadores da sua aplicação Android através de uma variedade de fornecedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>Adicionar autenticação para a sua aplicação Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo

Neste tutorial, adicione autenticação ao projeto fazer guia de introdução no Android utilizando um fornecedor de identidades suportados. Neste tutorial é baseado no tutorial [começar a trabalhar com aplicações Mobile] , que tem de concluir primeiro.

##<a name="register"></a>Registe-se a sua aplicação para a autenticação e configurar o serviço de aplicação

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a utilizadores autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ No Android Studio, abra o projeto tiver concluído previsto com o tutorial [começar a trabalhar com aplicações móveis]. A partir do menu **Executar** clique em **executar a aplicação** e certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é elevada após a aplicação é iniciada.

     Esta exceção acontece porque a aplicação tentativas para aceder a back-end como um utilizador não autenticado, mas a tabela _TodoItem_ agora requer autenticação.

Em seguida, pode atualizar a aplicação para autenticar os utilizadores antes de pedir recursos a partir da aplicação Mobile back-end.

## <a name="add-authentication-to-the-app"></a>Adicionar a autenticação à aplicação

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Tokens de autenticação de cache no cliente

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Próximos passos

Agora que se tiver concluído a este tutorial de autenticação básica, considere continuar para um dos seguintes tutoriais:

+ [Adicionar as notificações push para a sua aplicação Android](app-service-mobile-android-get-started-push.md) Saiba como configurar a sua aplicação Mobile do back-end a utilizar o Azure notificação concentradores para enviar notificações push.

+ [Ativar a sincronização offline para a sua aplicação Android](app-service-mobile-android-get-started-offline-data.md) Saiba como adicionar a sua aplicação utilizando a aplicação Mobile back-end suporte offline. Sincronização offline permite aos utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Começar a trabalhar com aplicações Mobile]: app-service-mobile-android-get-started.md
