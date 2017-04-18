<properties
    pageTitle="Notificação Azure concentradores seguros de emissão"
    description="Saiba como enviar notificações push seguro no Azure. Exemplos de código escritos c# utilizando a API do .NET."
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs" 
    ms.workload="mobile"
    ms.tgt_pltfrm="windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Notificação Azure concentradores seguros de emissão

> [AZURE.SELECTOR]
- [Universal do Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Descrição geral

Suporte de notificações push no Microsoft Azure permite-lhe aceder a infraestrutura de push fáceis de utilizar múltiplas plataformas, dimensionada-out, o que simplifica significativamente a implementação de notificações push para aplicações do consumidor e enterprise para as plataformas de dispositivos móveis.

Devido ao regulamentação ou restrições de segurança, por vezes, uma aplicação poderá pretender incluir algo na notificação que não pode ser transmitida através de infraestrutura de notificação do push padrão. Neste tutorial descreve como alcançar a mesma experiência ao enviar informações confidenciais através de uma ligação segura, autenticada entre o dispositivo de cliente e o back-end de aplicação.

O fluxo de alto nível, é da seguinte forma:

1. Aplicação back-end:
    - Armazena seguro carga útil numa base de dados back-end.
    - Envia o ID desta notificação para o dispositivo (não seguras são enviadas informações).
2. A aplicação no dispositivo, quando receber a notificação:
    - O dispositivo de contactos a pedir a carga de útil segura back-end.
    - A aplicação pode mostrar a carga de útil como uma notificação no dispositivo.

É importante ter em atenção que no fluxo de anterior (e neste tutorial), podemos partem do princípio de que o dispositivo armazena token de autenticação em armazenamento local, depois do utilizador inicia sessão. Isto garante uma experiência totalmente totalmente integrada, tal como o dispositivo pode obter seguro carga útil a notificação utilizando este token. Se a sua aplicação não armazenar tokens de autenticação no dispositivo ou se estes tokens podem ser expirados, a aplicação de dispositivo, após receber a notificação deve mostrar uma notificação genérica perguntar ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra a carga útil notificação.

Neste tutorial seguro Push mostra como enviar uma notificação de emissão de forma segura. O tutorial constrói no tutorial [Notificar utilizadores](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , para que devem concluir os passos que tutorial pela primeira vez.

> [AZURE.NOTE] Neste tutorial assume que criou e configurado o seu centro de notificação, tal como descrito em [Introdução ao notificação concentradores (da loja Windows)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
Além disso, tenha em atenção que o Windows Phone 8.1 requer as credenciais do Windows (não Windows Phone), e que tarefas em segundo plano não funcionam no Windows Phone 8.0 ou Silverlight 8.1. Para as aplicações da loja Windows, pode receber notificações através de uma tarefa de fundo apenas se a aplicação é activado de ecrã de bloqueio (clique na caixa de verificação a Appmanifest).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o projecto do Windows Phone

1. No **NotifyUserWindowsPhone** project, adicione o seguinte código para App.xaml.cs para registar a tarefa de fundo push. Adicionar a linha seguinte de código no final da `OnLaunched()` método:

        RegisterBackgroundTask();

2. Ainda no App.xaml.cs, adicione o seguinte código imediatamente após o `OnLaunched()` método:

        private async void RegisterBackgroundTask()
        {
            if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
            {
                var result = await BackgroundExecutionManager.RequestAccessAsync();
                var builder = new BackgroundTaskBuilder();

                builder.Name = "PushBackgroundTask";
                builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
                builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
                BackgroundTaskRegistration task = builder.Register();
            }
        }

3. Adicione o seguinte `using` declarações na parte superior do ficheiro App.xaml.cs:

        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;

4. No menu **ficheiro** no Visual Studio, clique em **Guardar tudo**.

## <a name="create-the-push-background-component"></a>Criar o componente de fundo Push

O passo seguinte é criar o componente de fundo push.

1. No Explorador de solução, com o botão direito no nó de nível superior da solução (**solução SecurePush** neste caso), em seguida, clique em **Adicionar**, em seguida, clique em **Novo projeto**.

2. Expandir **Aplicações da loja**, em seguida, clique em **Aplicações do Windows Phone**e, em seguida, clique em **Componente do tempo de execução do Windows (Windows Phone)**. Nome do projeto **PushBackgroundComponent**e, em seguida, clique em **OK** para criar o projeto.

    ![][12]

3. No Explorador de solução, com o botão direito do projecto **PushBackgroundComponent (Windows Phone 8.1)** , clique em **Adicionar**e, em seguida clique em **classe**. Nome da nova classe **PushBackgroundTask.cs**. Clique em **Adicionar** para gerar a classe.

4. Substituir todo o conteúdo da definição de espaço de nomes de **PushBackgroundComponent** com o seguinte código, substituindo o marcador de posição `{back-end endpoint}` com o ponto final de back-end obtido ao implementar back-end:

        public sealed class Notification
            {
                public int Id { get; set; }
                public string Payload { get; set; }
                public bool Read { get; set; }
            }

            public sealed class PushBackgroundTask : IBackgroundTask
            {
                private string GET_URL = "{back-end endpoint}/api/notifications/";

                async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
                {
                    // Store the content received from the notification so it can be retrieved from the UI.
                    RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                    var notificationId = raw.Content;

                    // retrieve content
                    BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                    var httpClient = new HttpClient();
                    var settings = ApplicationData.Current.LocalSettings.Values;
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                    var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                    ShowToast(notification);

                    deferral.Complete();
                }

                private void ShowToast(Notification notification)
                {
                    ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                    XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                    XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                    toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                    ToastNotification toast = new ToastNotification(toastXml);
                    ToastNotificationManager.CreateToastNotifier().Show(toast);
                }
            }

5. No Explorador de solução, com o botão direito do projecto **PushBackgroundComponent (Windows Phone 8.1)** e, em seguida, clique em **Gerir pacotes de NuGet**.

6. No lado esquerdo, clique em **Online**.

7. Na caixa de **pesquisa** , escreva **Http cliente**.

8. Na lista de resultados, clique em **Bibliotecas de cliente do Microsoft HTTP**e, em seguida, clique em **instalar**. Conclua a instalação.

9. Novamente na caixa de NuGet **pesquisa** , escreva **Json.net**. Instale o pacote de **Json.NET** , em seguida, feche a janela Gestor de pacote NuGet.

10. Adicione o seguinte `using` declarações na parte superior do ficheiro **PushBackgroundTask.cs** :

        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;

11. No Explorador de solução, no project **NotifyUserWindowsPhone (Windows Phone 8.1)** , com o botão direito **referências**, em seguida, clique em **Adicionar referência...**. Na caixa de diálogo Gestor de referência, selecione a caixa junto **PushBackgroundComponent**e, em seguida, clique em **OK**.

12. No Explorador de solução, faça duplo clique **Package.appxmanifest** no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)** . Em **notificações**, defina **Capazes de alerta** para **Sim**.

    ![][3]

13. Ainda no **Package.appxmanifest**, clique no menu de **declarações** perto da parte superior. Na lista pendente **Declarações disponíveis** , clique em **Tarefas em segundo plano**e, em seguida, clique em **Adicionar**.

14. Na **Package.appxmanifest**, em **Propriedades**, selecione a **notificação de emissão**.

15. Na **Package.appxmanifest**, em **Definições de aplicação**, escreva **PushBackgroundComponent.PushBackgroundTask** no campo de **Ponto de entrada** .

    ![][13]

16. No menu **ficheiro** , clique em **Guardar tudo**.

## <a name="run-the-application"></a>Executar a aplicação

Para executar a aplicação, faça o seguinte:

1. No Visual Studio, execute a aplicação de Web API **AppBackend** . É apresentada uma página web do ASP.NET.

2. No Visual Studio, execute a aplicação do Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)** . O emulador do Windows Phone é executado e carrega automaticamente a aplicação.

3. Na aplicação **NotifyUserWindowsPhone** IU, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia, mas tem de ser o mesmo valor.

4. Na aplicação **NotifyUserWindowsPhone** IU, clique em **Iniciar sessão e registar**. Em seguida, clique em **Enviar push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
