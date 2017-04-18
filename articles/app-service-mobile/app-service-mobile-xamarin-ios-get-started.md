<properties
    pageTitle="Introdução ao Azure aplicação do serviço móvel aplicações para aplicações Xamarin.iOS | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar com a utilização de aplicações móveis para o desenvolvimento de Xamarin.iOS."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>Criar uma aplicação de Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como adicionar um serviço baseado na nuvem back-end a aplicação móvel Xamarin.iOS utilizando um back-end Azure aplicação móvel.  Criar um novo back-end de aplicação móvel e uma simples _lista a fazer_ Xamarin.iOS aplicação que armazena dados de aplicação no Azure.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais Xamarin.iOS sobre como utilizar a funcionalidade de aplicações móveis num serviço de aplicação do Azure.

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário os pré-requisitos seguintes:

* Uma conta do Azure active. Se não tiver uma conta, se inscrever para uma avaliação do Azure e obtenha até 10 aplicações móveis gratuitas que pode continuar a utilizar mesmo após a sua avaliação extremidades. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Consulte o artigo [configurar e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções.

* Um Mac com o v Xcode 7.0 ou posterior e Xamarin Studio Comunidade instalado. Consulte o artigo [configurar e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [o programa de configuração, instalar e verificações para os utilizadores de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE]Se pretender começar com a aplicação de serviço de Azure antes de se inscrever para uma conta do Azure, aceda ao [Serviço de aplicação tente](https://tryappservice.azure.com/?appServiceName=mobile). Pode criar uma aplicação móvel starter curto imediatamente na aplicação de serviço — sem cartão de crédito obrigatório e sem compromissos.

## <a name="create-an-azure-mobile-app-backend"></a>Criar um aplicação do Azure Mobile do back-end

Siga estes passos para criar a aplicação Mobile back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar o project server

Tem agora aprovisionada um aplicação do Azure Mobile do back-end que pode ser utilizado pelas suas aplicações de cliente móvel. Em seguida, transferir um projeto de servidor para um simples "lista a fazer" back-end e publicá-lo Azure.

Siga os seguintes passos para configurar o project server para utilizar o Node.js ou .NET back-end.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Transferir e executar a aplicação Xamarin.iOS

1. Abra o [Azure portal] numa janela do browser.

2. No pá definições para a sua aplicação móvel, clique em **Começar** > **Xamarin.iOS**. Em passo 3, clique em **criar uma nova aplicação** se ainda não estiver a ser selecionado.  Em seguida, clique no botão **Transferir** .

    Uma aplicação de cliente liga-se ao seu back-end móvel é transferida. Guardar o ficheiro de projeto comprimido o computador local e tome nota do onde guardá-lo.

3. Extrair o projeto que transferiu e, em seguida, abra-o no Xamarin Studio (ou Visual Studio).

    ![][9]

    ![][8]

4. Prima a tecla F5 para criar o projecto e inicie a aplicação no iPhone emulador.

5. Na aplicação, escreva texto relevante, tal como _Saber Xamarin_e, em seguida, clique na **+** botão.

    ![][10]

    Dados a partir do pedido são inseridos na tabela TodoItem. Itens armazenados na tabela são devolvidos pela aplicação móvel back-end e os dados são apresentados na lista.

>[AZURE.NOTE]Pode rever o código que acede a sua aplicação móvel do back-end para consultar e inserir dados no ficheiro QSTodoService.cs c#.

##<a name="next-steps"></a>Próximos passos

* [Adicionar a Sincronização Offline para a sua aplicação](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Adicionar autenticação para a sua aplicação](app-service-mobile-xamarin-ios-get-started-users.md)
* [Adicionar as notificações push para a sua aplicação Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Como utilizar o cliente gerido para as aplicações móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/
