<properties
    pageTitle="Introdução ao Azure aplicações móveis para Xamarin.Android aplicações"
    description="Siga este tutorial para começar a utilizar aplicações do Azure Mobile para o desenvolvimento de Xamarin Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>Criar uma aplicação de Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como adicionar um serviço baseado na nuvem back-end para uma aplicação de Xamarin.Android. Para mais informações, consulte o artigo [o que são aplicações Mobile](app-service-mobile-value-prop.md).

Uma captura de ecrã a partir da aplicação concluída é abaixo:

![][0]

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de aplicações móveis para Xamarin.Android aplicações.

##<a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário os pré-requisitos seguintes:

* Uma conta do Azure active. Se não tiver uma conta, inscrever-se para um avaliação do Azure e obter até 10 aplicações móveis gratuito. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Consulte o artigo [configurar e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções.

>[AZURE.NOTE]Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Serviço de aplicação tente](https://tryappservice.azure.com/?appServiceName=mobile).  Pode criar imediatamente um curto starter aplicação Mobile na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="create-an-azure-mobile-app-backend"></a>Criar um aplicação do Azure Mobile do back-end

Siga estes passos para criar a aplicação Mobile back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Tem agora aprovisionada um aplicação do Azure Mobile do back-end que pode ser utilizado pelas suas aplicações de cliente móvel. Em seguida, transferir um projeto de servidor para um simples "lista a fazer" back-end e publicá-lo Azure.

## <a name="configure-the-server-project"></a>Configurar o project server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Transferir e executar a aplicação Xamarin.Android

1. Em **Transferir e executa o seu projeto Xamarin.Android**, clique no botão **Transferir** .

    Guardar o ficheiro de projeto comprimido o computador local e tome nota do onde guardá-lo.

2. Prima a tecla **F5** para criar o projecto e iniciar a aplicação.

3. Na aplicação, escreva o texto relevante, tal como _concluída a iniciação_ e, em seguida, clique no botão **Adicionar** .

    ![][10]

    Dados a partir do pedido são inseridos na tabela TodoItem. Itens armazenados na tabela são devolvidos pela aplicação móvel back-end e os dados são apresentados na lista.

    > [AZURE.NOTE] Pode rever o código que acede a sua aplicação móvel do back-end para consultar e inserir dados, que se encontra no ficheiro ToDoActivity.cs c#.

##<a name="next-steps"></a>Próximos passos

* [Adicionar a Sincronização Offline para a sua aplicação](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Adicionar autenticação para a sua aplicação](app-service-mobile-xamarin-android-get-started-users.md)
* [Adicionar as notificações push para a sua aplicação Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md)
* [Como utilizar o cliente gerido para as aplicações móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
