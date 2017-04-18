<properties
    pageTitle="Utilizar concentradores de notificação para enviar notícias de última hora (Windows Phone)"
    description="Utilize o Azure notificação concentradores para etiqueta no registos para enviar notícias de última hora para uma aplicação do Windows Phone."
    services="notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar concentradores de notificação para enviar notícias de última hora

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como utilizar o Azure notificação concentradores difundir força as notificações de notícias para uma aplicação do Windows Phone 8.0/8.1 Silverlight. Se está a filtrar o da loja Windows ou a aplicação do Windows Phone 8.1, consulte para a versão [Windows Universal](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) . Quando estiver concluído, serão poderá para se registar no força de categorias de notícias que lhe interessam e receber apenas as notificações push para as categorias. Este cenário é um padrão comum para aplicações muitos onde notificações tem de ser enviadas para grupos de utilizadores que tenham declarados anteriormente interesse nos mesmos, por exemplo, o leitor RSS, aplicações para ventoinhas música, etc.

Cenários de difusão estão ativados para inclusão de um ou mais _etiquetas_ ao criar um registo no centro do notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que tenham registados para a etiqueta irão receber a notificação. Porque etiquetas são simplesmente cadeias, não tem de ser aprovisionada com antecedência. Para mais informações sobre etiquetas, consulte [notificação concentradores encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).

##<a name="prerequisites"></a>Pré-requisitos

Este tópico constrói a aplicação que criou no [artigo Introdução ao concentradores de notificação]. Antes de iniciar este tutorial, tem já tiver concluído a [Introdução ao concentradores de notificação].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria à aplicação

É o primeiro passo adicionar os elementos da IU à sua página principal existente que permitem ao utilizador selecionar categorias para registar. As categorias seleccionadas por um utilizador são armazenadas no dispositivo. Quando inicia a aplicação, um registo de dispositivo é criado no seu centro de notificação com as categorias selecionados como etiquetas.

1. Abra o ficheiro de projeto MainPage.xaml, em seguida, substitua os elementos de **grelha** com o nome `TitlePanel` e `ContentPanel` com o seguinte código:

        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. No project, crie uma nova classe com o nome de **notificações**, adicionar a modificadora **público** para a definição de classe, em seguida, adicione as seguintes instruções de **utilizar** para o novo ficheiro de código:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;

3. Copie o código seguinte para a classe de **notificações de** nova:

        private NotificationHub hub;

        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }

        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();

            return await SubscribeToCategories();
        }

        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();

            var channel = HttpNotificationChannel.Find("MyPushChannel");

            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;

                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }

            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
            
            return await registrationTask.Task;
        }

        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }

        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.

            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";

            // The stored categories tags are passed with the template registration.

            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

            return await registrationTask.Task;
        }

        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;

            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }

            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }


    Esta classe utiliza o armazenamento isolado para armazenar as categorias de notícias que este dispositivo está a receber. Também contém métodos para se registar nestas categorias utilizando um registo de notificação do [modelo](notification-hubs-templates-cross-platform-push-messages.md) .


4. No ficheiro de projeto App.xaml.cs, adicione a propriedade seguinte para a classe de **aplicação** . Substituir o `<hub name>` e `<connection string with listen access>` marcadores de posição com o seu nome de concentrador de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anterior.

        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

    > [AZURE.NOTE] Porque credenciais são distribuídas com uma aplicação de cliente não são geralmente seguras, deverá apenas distribuir a chave para ouvir acesso com a sua aplicação de cliente. Ouvir permite acesso a sua aplicação para se registar no notificações, mas os registos existentes não pode ser modificada e as notificações não podem ser enviadas. A tecla de acesso total é utilizada num serviço de back-end segura para envio de notificações e alterar os registos existentes.

5. No seu MainPage.xaml.cs, adicione a linha seguinte:

        using Windows.UI.Popups;

6. No ficheiro de projeto MainPage.xaml.cs, adicione o seguinte método:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
    
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
    
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }

    Este método cria uma lista de categorias e utiliza a classe de **notificações** para armazenar a lista no armazenamento local e registar as tags correspondentes com o seu centro de notificação. Quando categorias são alteradas, o registo é recriado com novas categorias.

A aplicação é agora possível armazenar um conjunto de categorias no local armazenamento no dispositivo e registe-se com o concentrador de notificação sempre que o utilizador altera a seleção de categorias.

##<a name="register-for-notifications"></a>Registe-se para as notificações

Estes passos registe-se com o concentrador de notificação no arranque utilizando as categorias que tenham sido armazenadas no armazenamento local.

> [AZURE.NOTE] Uma vez que pode alterar o canal URI atribuída ao Microsoft emissão notificação de serviço (MPNS) em qualquer altura, deverá registar para notificações com frequência evitar falhas de notificação. Este exemplo regista para notificações sempre que inicia a aplicação. Para as aplicações que são executadas com frequência, mais do que uma vez por dia, pode provavelmente ignorar registo para manter a largura de banda se menos de um dia passou desde o registo anterior.


1. Abra o ficheiro App.xaml.cs e adicionar a modificadora **assíncrona** para o método de **Application_Launching** e substituir o código de registo de notificação concentradores que adicionou no [artigo Introdução ao concentradores de notificação] com o seguinte código:

        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();

            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }

    Isto torna-se de que sempre que inicia a aplicação-obtém as categorias do armazenamento local e os pedidos de um registo para estas categorias.

2. No ficheiro de projeto MainPage.xaml.cs, adicione o seguinte código que implementa o método de **OnNavigatedTo** :

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();

            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
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

    ![][2]

3. Depois de receber uma confirmação de que as categorias de foram subscrição concluída, execute a aplicação de consola para enviar notificações para cada categorias. Certifique-se de que só recebe uma notificação para as categorias que subscreveu.

    ![][3]

Concluiu este tópico.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Introdução ao concentradores de notificação]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

