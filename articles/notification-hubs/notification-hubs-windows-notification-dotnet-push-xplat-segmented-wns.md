<properties
    pageTitle="Utilizar concentradores de notificação para enviar notícias de última hora (Universal do Windows)"
    description="Utilize o Azure notificação concentradores com etiquetas no registo para enviar notícias de última hora para uma aplicação do Windows universal."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>


<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar concentradores de notificação para enviar notícias de última hora


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como utilizar o Azure notificação concentradores difundir força as notificações de notícias para da loja Windows ou a aplicação do Windows Phone 8.1 (que não sejam Silverlight). Se está a filtrar o Silverlight do Windows Phone 8.1, consulte para a versão do [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) . Quando estiver concluído, serão poderá para se registar no força de categorias de notícias que lhe interessam e receber apenas as notificações push para as categorias. Este cenário é um padrão comum para aplicações muitos onde notificações tem de ser enviadas para grupos de utilizadores que tenham declarados anteriormente interesse nos mesmos, por exemplo, leitor RSS, aplicações para ventoinhas música e assim sucessivamente. 

Cenários de difusão estão ativados para inclusão de um ou mais _etiquetas_ ao criar um registo no centro do notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que tenham registados para a etiqueta irão receber a notificação. Porque etiquetas são simplesmente cadeias, não tem de ser aprovisionada com antecedência. Para mais informações sobre etiquetas, consulte [notificação concentradores encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Pré-requisitos

Este tópico constrói a aplicação que criou no [artigo Introdução ao concentradores de notificação][get-started]. Antes de iniciar este tutorial, tem já concluiu [começar com notificação concentradores][get-started].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria à aplicação

É o primeiro passo adicionar os elementos da IU à sua página principal existente que permitem ao utilizador selecionar categorias para registar. As categorias seleccionadas por um utilizador são armazenadas no dispositivo. Quando inicia a aplicação, um registo de dispositivo é criado no seu centro de notificação com as categorias selecionados como etiquetas.

1. Abra o ficheiro de projeto MainPage.xaml, em seguida, copie o código seguinte no elemento de **grelha** :

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>


2. Clique com o botão direito do rato em projeto **partilhado** e adicionar uma nova classe com o nome de **notificações**, adicionar a modificadora **público** para a definição de classe, em seguida, adicione as seguintes instruções de **utilizar** para o novo ficheiro de código:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copie o código seguinte para a classe de **notificações de** nova:

        private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }

    Esta classe utiliza armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Tenha em atenção que em vez de chamar o método de *RegisterNativeAsync* chamamos *RegisterTemplateAsync* para registar para as categorias utilizando um registo de modelo. 
    
    Fornecemos também um nome para o modelo ("simpleWNSTemplateExample"), uma vez que poderá Queremos registar mais do que um modelo (por exemplo uma para notificações de alerta) e outra para mosaicos e precisamos de atribuir um nome-los para poder atualizar ou eliminá-los.

    Tenha em atenção que se um dispositivo regista vários modelos com a mesma etiqueta, uma entrada mensagem filtragem que etiqueta irá resultar em várias notificações entregue ao dispositivo (um para cada modelo). Este comportamento é útil quando a mesma mensagem lógica tem de resultar em várias notificações visuais, por exemplo que mostra um distintivo e uma alerta numa aplicação da loja Windows.

    Para mais informações sobre modelos, consulte o artigo [modelos](notification-hubs-templates-cross-platform-push-messages.md).




4. No ficheiro de projeto App.xaml.cs, adicione a propriedade seguinte para a classe de **aplicação** :

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    Esta propriedade é utilizada para criar e aceder a uma instância de **notificações** .

    No código acima, substitua a `<hub name>` e `<connection string with listen access>` marcadores de posição com o seu nome de concentrador de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anterior.

    > [AZURE.NOTE] Porque credenciais são distribuídas com uma aplicação de cliente não são geralmente seguras, deverá apenas distribuir a chave para ouvir acesso com a sua aplicação de cliente. Ouvir permite acesso a sua aplicação para se registar no notificações, mas os registos existentes não pode ser modificada e as notificações não podem ser enviadas. A tecla de acesso total é utilizada num serviço de back-end segura para envio de notificações e alterar os registos existentes.

5. No seu MainPage.xaml.cs, adicione a linha seguinte:

        using Windows.UI.Popups;

6. No ficheiro de projeto MainPage.xaml.cs, adicione o seguinte método:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Este método cria uma lista de categorias e utiliza a classe de **notificações** para armazenar a lista no armazenamento local e registar as tags correspondentes com o seu centro de notificação. Quando categorias são alteradas, o registo é recriado com novas categorias.

A aplicação é agora possível armazenar um conjunto de categorias no local armazenamento no dispositivo e registe-se com o concentrador de notificação sempre que o utilizador altera a seleção de categorias.

##<a name="register-for-notifications"></a>Registe-se para as notificações

Estes passos registe-se com o concentrador de notificação no arranque utilizando as categorias que tenham sido armazenadas no armazenamento local.

> [AZURE.NOTE] Porque o canal URI atribuída ao serviço de notificação do Windows (WNS) pode ser alteradas em qualquer altura, deverá registar para notificações com frequência evitar falhas de notificação. Este exemplo regista para notificação sempre que inicia a aplicação. Para as aplicações que são executadas com frequência, mais do que uma vez por dia, pode provavelmente ignorar registo para manter a largura de banda se menos de um dia passou desde o registo anterior.

1. Abra o ficheiro App.xaml.cs e atualizar o método de **InitNotificationsAsync** a utilizar o `notifications` escolares para subscrever baseiam em categorias.

        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
    
        var result = await notifications.SubscribeToCategories();

    Isto torna-se de que sempre que inicia a aplicação-obtém as categorias do armazenamento local e os pedidos de um registeration para estas categorias. O método **InitNotificationsAsync** foi criado como parte de [Introdução ao notificação concentradores] [ get-started] tutorial.

3. No ficheiro de projeto MainPage.xaml.cs, adicione o seguinte código para o método de *OnNavigatedTo* :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }

    Este procedimento actualiza página principal com o estado das categorias previamente guardadas.

A aplicação está agora concluída e pode armazenar um conjunto de categorias no armazenamento local dispositivo utilizado para registar o concentrador de notificação sempre que o utilizador altera a seleção de categorias. Em seguida, podemos definirá back-end que pode enviar notificações de categoria para esta aplicação.

##<a name="sending-tagged-notifications"></a>Enviar notificações com tags

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. No Visual Studio, prima F5 para compilar e inicie a aplicação.

    ![][1]

    Nota que a aplicação IU fornece um conjunto de alterna que lhe permite escolher as categorias para subscrever.

2. Ativar uma ou mais alterna categorias e, em seguida, clique em **subscrever**.

    A aplicação converte as categorias selecionadas etiquetas e os pedidos de um novo registo de dispositivo para as etiquetas selecionados a partir do hub a notificação. As categorias registadas são devolvidas e apresentadas numa caixa de diálogo.

    ![][19]

4. Envie uma notificação de novo a partir de back-end de uma das seguintes formas:

    + **Consola aplicação:** iniciar a aplicação de consola.

    + **Java/PHP:** executar a sua aplicação/script.

    Notificações para as categorias selecionadas são apresentados como notificações de alerta.

    ![][14]

##<a name="next-steps"></a>Próximos passos

Neste tutorial, vamos aprendeu como difundir notícias de última hora por categoria. Considere a concluir uma das seguintes tutoriais que destacam outros cenários de notificação concentradores avançados:

+ [Utilizar notificação concentradores difundir notícias de última hora localizados]

    Saiba como expandir a aplicação de notícias de divisão para ativar o envio de notificações localizadas.



<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Utilizar notificação concentradores difundir notícias de última hora localizados]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
