<properties
    pageTitle="Criar uma aplicação de Cordova no Azure de aplicação de serviço aplicações móveis | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar com a utilização de back-ends do Azure aplicação móvel para o desenvolvimento de Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""
    tags=""
    keywords="cordova, cliente móvel, javascript," />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-an-apache-cordova-app"></a>Criar um Apache Cordova aplicação

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como adicionar um serviço baseado na nuvem back-end a uma aplicação móvel Apache Cordova utilizando um back-end Azure aplicação móvel.  Irá criar um novo back-end de aplicação móvel e uma simples _lista a fazer_ aplicação Apache Cordova que armazena dados de aplicação no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Apache Cordova sobre como utilizar a funcionalidade de aplicações móveis num serviço de aplicação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Um PC com [Visual Studio Comunidade 2015] ou mais recente.
* [Ferramentas do visual Studio para Apache Cordova].
* Uma [conta Azure ativa](https://azure.microsoft.com/pricing/free-trial/).

Poderá também ignorar Visual Studio e utilizar a linha de comandos Apache Cordova diretamente.  Isto é útil quando concluir o tutorial num computador Mac.  A compilar aplicações de cliente Apache Cordova utilizando a linha de comandos não é abrangida por este tutorial.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um nova aplicação móvel Azure do back-end

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurar o project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Transferir e executar a aplicação Apache Cordova

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Próximos passos

Agora que se tiver concluído a este tutorial de guia de introdução, avance para um dos seguintes tutoriais:

* [Adicionar a autenticação] para sua Apache Cordova aplicação.
* [Adicionar as notificações Push] para o seu Apache Cordova aplicação.

Saiba mais sobre os conceitos chave com a aplicação de serviço de Azure.

* [Autenticação]
* [Notificações de emissão]

Saiba como utilizar os SDK.

* [Apache Cordova SDK]
* [Servidor de ASP.NET SDK]
* [Servidor de node.js SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Comunidade do Visual Studio 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Adicionar autenticação]: app-service-mobile-cordova-get-started-users.md
[Adicionar as notificações Push]: app-service-mobile-cordova-get-started-push.md
[Autenticação]: app-service-mobile-auth.md
[Notificações de emissão]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[Servidor de ASP.NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Servidor de node.js SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
