<properties
    pageTitle="Adicionar autenticação IOS com aplicações móveis do Azure"
    description="Saiba como utilizar as aplicações móveis do Azure para autenticar os utilizadores da sua aplicação iOS através de uma variedade de fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>Adicionar autenticação para a sua aplicação iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Neste tutorial, adicione autenticação ao projeto [iOS rápidas começar] com um fornecedor de identidades suportados. Neste tutorial baseia-se a iniciação [rápido do iOS iniciar] , que tem de concluir primeiro.

##<a name="register"></a>Registe-se a sua aplicação para a autenticação e configurar o serviço de aplicação

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a utilizadores autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Na Xcode, prima **Executar** para iniciar a aplicação. Uma exceção será elevada porque a aplicação tentativas para aceder a back-end como um utilizador não autenticado, mas _TodoItem_ tabela agora requer autenticação.

##<a name="add-authentication"></a>Adicionar a autenticação à aplicação

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[Guia de introdução do iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
