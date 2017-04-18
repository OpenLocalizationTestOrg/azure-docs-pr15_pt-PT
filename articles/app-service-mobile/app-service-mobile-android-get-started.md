<properties
    pageTitle="Criar uma aplicação do Android em aplicações móveis do Azure de aplicação de serviço | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar com a utilização de back-ends do Azure aplicação móvel para o desenvolvimento Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#<a name="create-an-android-app"></a>Criar uma aplicação do Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como adicionar um serviço baseado na nuvem back-end a uma aplicação móvel Android utilizando um back-end Azure aplicação móvel.  Irá criar um novo back-end de aplicação móvel e uma simples _lista a fazer_ aplicação Android que armazena dados de aplicação no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Android sobre como utilizar a funcionalidade de aplicações móveis num serviço de aplicação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* [Ferramentas de programador android](https://developer.android.com/sdk/index.html), que inclui o ambiente de desenvolvimento integrado Android Studio e a plataforma Android mais recente.
* Azure Mobile Android SDK, que é referenciado automaticamente como parte do projeto que transferir Guia de introdução.
* Uma [conta Azure ativa](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um nova aplicação móvel Azure do back-end

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar o project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-android-app"></a>Transferir e executar a aplicação Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
