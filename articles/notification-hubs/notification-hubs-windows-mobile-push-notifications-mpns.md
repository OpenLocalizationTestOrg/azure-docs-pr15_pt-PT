<properties
    pageTitle="Enviar as notificações push com Azure notificação concentradores no Windows Phone | Microsoft Azure"
    description="Neste tutorial, saiba como utilizar o Azure notificação concentradores para as notificações push para uma aplicação do Windows Phone 8 ou Windows Phone 8.1 Silverlight."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notificações de emissão, notificação, de notificações push do windows phone"
    authors="ysxu"
    manager="erikre"
    editor="erikre"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Enviar as notificações push com Azure notificação concentradores no Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

Este tutorial mostra-lhe como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação do Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se está a filtrar o Windows Phone 8.1 (que não sejam Silverlight), em seguida, consulte para a versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .
Neste tutorial, criar uma aplicação vazia do Windows Phone 8 que recebe as notificações push utilizando o serviço de notificação de Push Microsoft (MPNS). Quando tiver terminado, poderá utilizar o seu centro de notificação para difundir notificações push para todos os dispositivos de executar a sua aplicação.

> [AZURE.NOTE] Notificação concentradores Windows Phone SDK não suporta a utilizar o Windows emissão notificação de serviço (WNS) com aplicações do Windows Phone 8.1 Silverlight. Para utilizar WNS (em vez de MPNS) com aplicações do Windows Phone 8.1 Silverlight, siga a [Notificação concentradores - Windows Phone Silverlight tutorial], que utiliza REST APIs.

O tutorial demonstra o cenário de difusão simple concentradores notificação ao utilizar.

##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial requer o seguinte:

+ [Visual Studio 2012 Express para Windows Phone], ou uma versão posterior.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais concentradores de notificação para as aplicações do Windows Phone 8.

##<a name="create-your-notification-hub"></a>Criar o seu centro de notificação

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Clique na secção <b>Serviços de notificação</b> (dentro do <i>Definições</i>), clique no <b>Windows Phone (MPNS)</b> e, em seguida, clique na caixa de verificação <b>Ativar push não autenticado</b> .</p>
</li>
</ol>

&emsp;&emsp;![Portal Azure - unathenticated ativar as notificações push](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

O concentrador agora é criado e configurado para enviar uma notificação não autenticada para Windows Phone.

> [AZURE.NOTE] Neste tutorial utiliza MPNS no modo não autenticado. Modo não autenticado MPNS vem com restrições de notificações que pode enviar a cada canal. Notificação concentradores suporta [MPNS autenticados modo](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) , permitindo-lhe carregar o certificado.

##<a name="connecting-your-app-to-the-notification-hub"></a>Ligar a sua aplicação para o concentrador de notificação

1. No Visual Studio, crie uma nova aplicação do Windows Phone 8.

    ![Aplicação do Visual Studio - novo projeto - Windows Phone][13]

    No Visual Studio 2013 Update 2 ou posterior, em vez disso, criar uma aplicação do Windows Phone Silverlight.

    ![Visual Studio - novo projeto - em branco aplicação - Silverlight do Windows Phone][11]

2. No Visual Studio, a solução com o botão direito e, em seguida, clique em **Gerir pacotes de NuGet**.

    Isto apresenta a caixa de diálogo **Gerir pacotes de NuGet** .

3. Procurar `WindowsAzure.Messaging.Managed` e clique em **instalar**e, em seguida, aceite os termos de utilização.

    ![Visual Studio - Gestor de pacotes NuGet][20]

    Isto transfere, instalações e adiciona uma referência para a biblioteca do Azure mensagens para o Windows utilizando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacote de WindowsAzure.Messaging.Managed NuGet</a>.

4. Abra o ficheiro App.xaml.cs e adicione o seguinte `using` declarações:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Adicione o seguinte código na parte superior do método **Application_Launching** no App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] O valor **MyPushChannel** é um índice remissivo que é utilizado para pesquisar um canal existente na coleção de [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) . Se não houver um existir, crie uma nova entrada com esse nome.
    
    Certifique-se inserir o nome do seu centro e a cadeia de ligação denominado **DefaultListenSharedAccessSignature** que obteve na secção anterior.
    Este código obtém o canal URI para a aplicação a partir de MPNS e, em seguida, regista desse canal URI com o seu centro de notificação. Também garante que o canal que URI é registada no seu centro de notificação sempre que a aplicação é iniciada.

    >[AZURE.NOTE]Neste tutorial envia uma notificação de alerta para o dispositivo. Quando envia uma notificação de mosaico, tem de chamar em vez disso, o método de **BindToShellTile** no canal. Para suportar ambos os alerta e dispor em mosaico notificações, **BindToShellTile** e **BindToShellToast**de chamadas.

6. No Explorador de solução, expanda **Propriedades**, abra o `WMAppManifest.xml` de ficheiro, clique no separador de **capacidades** e certifique-se de que a funcionalidade **ID_CAP_PUSH_NOTIFICATION** está selecionada.

    ![Visual Studio - capacidades de aplicação do Windows Phone][14]

    Este procedimento garante que a sua aplicação pode receber notificações push. Sem ele, qualquer tentativa para enviar uma notificação de emissão para a aplicação irá falhar.

7. Prima a `F5` tecla para executar a aplicação.

    É apresentada uma mensagem de registo na aplicação.

8. Feche a aplicação.  

   >[AZURE.NOTE] Para receber uma notificação de push alerta, a aplicação não tem de executar em primeiro plano.

##<a name="send-push-notifications-from-your-backend"></a>Enviar notificações push a partir do seu back-end

Pode enviar notificações push ao utilizar concentradores de notificação de qualquer back-end através da <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface do resto</a>de público. Neste tutorial, enviar notificações push utilizando uma aplicação de consola do .NET. 

Para um exemplo sobre como enviar notificações push a partir de um back-end de ASP.NET WebAPI que está integrado com o concentradores de notificação, consulte o artigo [Azure notificação concentradores notificar os utilizadores com .NET do back-end](./notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md).  

Para obter um exemplo de como enviar notificações push ao utilizar os [REST APIs](https://msdn.microsoft.com/library/azure/dn223264.aspx), consulte o artigo [como utilizar concentradores de notificação de Java](./notification-hubs-java-push-notification-tutorial.md) e [como utilizar concentradores de notificação de PHP](./notification-hubs-php-push-notification-tutorial.md).

1. A solução de contexto, selecione **Adicionar** e o **Novo projeto...**e, em seguida, em **Visual c#**, clique em **Windows** e a **Aplicação de consola**e clique em **OK**.

    ![Aplicação de consola do Visual Studio - novo projeto-][6]

    Esta ação adiciona uma nova Visual c# consola aplicação para a solução. Pode também fazê-lo numa solução em separado.

4. Clique em **Ferramentas**, clique em **Gestor de pacote biblioteca**e, em seguida, clique em **Consola do Gestor de pacote**.

    Esta ação apresentará a consola do Gestor de pacote.

5.  Na janela da **Consola do Gestor de pacote** , definir **predefinidos do project** para o novo projeto de aplicação de consola e, em seguida, na janela da consola, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esta ação adiciona uma referência para o SDK do Azure notificação concentradores utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote de Microsoft.Azure.Notification concentradores NuGet</a>.

6. Abrir o `Program.cs` ficheiro e adicione o seguinte `using` declaração:

        using Microsoft.Azure.NotificationHubs;

6. No `Program` classe, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Certifique-se substituir o `<hub name>` marcador de posição com o nome do concentrador de notificação que é apresentado no portal. Além disso, substitua o marcador de posição de cadeia de ligação com a cadeia de ligação chamada **DefaultFullSharedAccessSignature** que obteve na secção "Configurar seu centro de notificação".

    >[AZURE.NOTE]Certifique-se de que utiliza a cadeia de ligação com o acesso **completo** , não o access **ouvir** . A cadeia de ouvir o access não tem permissões para enviar notificações push.

4. Adicione a linha seguinte no seu `Main` método:

         SendNotificationAsync();
         Console.ReadLine();

5. Com emulador seu Windows Phone em execução e a aplicação fechado, defina o projeto de aplicação de consola como o projeto de arranque predefinido e, em seguida, prima a `F5` tecla para executar a aplicação.

    Receberá uma notificação de push alerta. Tocar na faixa de alerta carrega a aplicação.

Pode encontrar os possíveis payloads os tópicos do [catálogo de alerta] e [Dispor em mosaico catálogo] no MSDN.

##<a name="next-steps"></a>Próximos passos

Neste exemplo simples, difundida notificações push para todos os seus dispositivos Windows Phone 8. 

Para utilizadores específicos de destino, consulte o tutorial [Concentradores de notificação de utilização para as notificações push para os utilizadores] . 

Se pretender segmentar os seus utilizadores por grupos de interesse, pode ser lido [Concentradores de notificação de utilização para enviar notícias de última hora]. 

Saiba mais sobre como utilizar concentradores de notificação na [Notificação concentradores orientação].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Express de 2012 Studio para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Notificação concentradores orientações]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utilizar concentradores de notificação para as notificações push para os utilizadores]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Utilizar concentradores de notificação para enviar notícias de última hora]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[catálogo de alerta]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[Mosaico do catálogo]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notificação concentradores - tutorial do Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

