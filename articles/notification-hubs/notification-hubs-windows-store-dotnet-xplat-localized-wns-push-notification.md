<properties
    pageTitle="Notificação concentradores localizadas força News Tutorial"
    description="Saiba como utilizar o Azure notificação concentradores enviar notificações de notícias força localizados."
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

# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>Utilizar concentradores de notificação para enviar notícias de última hora localizados

> [AZURE.SELECTOR]
- [Da loja Windows c#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como utilizar a funcionalidade de **modelo** do Azure notificação concentradores difundir notificações de notícias recentes que foram localizadas por idioma e ao dispositivo. Neste tutorial, irá iniciar com a aplicação da loja Windows criada no [Concentradores de notificação de utilização para enviar notícias de última hora]. Quando estiver concluído, que poderá registe-se para as categorias que lhe interessam, especifique um idioma no qual pretende receber as notificações e receber apenas as notificações push para as categorias selecionadas nesse idioma.


Existem duas partes para este cenário:

- a aplicação da loja Windows permite aos dispositivos cliente para especificar um idioma e para subscrever força diferentes categorias de notícias;

- back-end emite as notificações, utilizando o **modelo** e **etiquetas** feautres do Azure notificação concentradores.



##<a name="prerequisites"></a>Pré-requisitos

Tem já tiver concluído o tutorial [Concentradores de notificação de utilização para enviar notícias de última hora] e ter o código disponível, uma vez que este tutorial baseia-se diretamente nesse código.

Também precisa de Visual Studio 2012 ou posterior.


##<a name="template-concepts"></a>Conceitos de modelo

Na [Notificação de utilização concentradores para enviar notícias de última hora] criadas uma aplicação que utilizado **etiquetas** para subscrever notificações para categorias de notícias diferente.
Muitas aplicações, no entanto, vários mercados de destino e necessitam de localização. Isto significa que o conteúdo das notificações deles próprios tem de ser localizadas e entregue para o conjunto de dispositivos correto.
Este tópico iremos mostrar como utilizar a funcionalidade de **modelo** de notificação concentradores para entregar facilmente as notificações de notícias força localizados.

Nota: uma forma de enviar notificações localizadas é criar várias versões de cada etiqueta. Por exemplo, para suportar o inglês, francês e Mandarim, teria precisamos três etiquetas diferentes para obter notícias do mundo: "world_en", "world_fr," e "world_ch". Em seguida, podemos teria que enviar uma versão localizada de notícias a nível mundial para cada um dos seguintes tags. Este tópico utilizamos modelos para evitar a multiplicação de etiquetas e o requisito de várias mensagens a enviar.

De alto nível, os modelos são uma forma de especificar como um dispositivo específico deve receber uma notificação. O modelo especifica o formato de carga útil exata ao referir propriedades que fazem parte da mensagem que foi enviada por sua aplicação back-end. No nosso caso, podemos irá enviar uma mensagem de região desconhecido que contém todos os idiomas suportados:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Em seguida, vamos assegurar que dispositivos registe-se com um modelo que refere-se para a propriedade correta. Por exemplo, uma aplicação da loja Windows que pretenda receber uma mensagem de alerta simples irá registe-se para o modelo com quaisquer etiquetas correspondentes seguinte:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



Os modelos são uma funcionalidade muito poderosa que pode saber mais sobre o nosso artigo de [modelos](notification-hubs-templates-cross-platform-push-messages.md) . 


##<a name="the-app-user-interface"></a>Interface de utilizador da aplicação

Vamos agora irá modificar a aplicação de força de notícias que criou no tópico [Concentradores de notificação de utilização para enviar notícias de última hora] para enviar notícias força localizados utilizar modelos.

Na sua aplicação da loja Windows:

Altere o seu MainPage.xaml para incluir uma caixa de combinação de região:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>

##<a name="building-the-windows-store-client-app"></a>Criar a aplicação de cliente da loja Windows

1. Na sua aula notificações, adicione um parâmetro de região aos seus métodos *StoreCategoriesAndSubscribe* e *SubscribeToCateories* .

        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
        }

        public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                categories = RetrieveCategories();
            }

            // Using a template registration. This makes supporting notifications across other platforms much easier.
            // Using the localized tags based on locale selected.
            string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
        }

    Tenha em atenção que em vez de chamar o método de *RegisterNativeAsync* chamamos *RegisterTemplateAsync*: Recomendamos está a registar um formato de notificação específico na qual o modelo depende da região. Fornecemos também um nome para o modelo ("localizedWNSTemplateExample"), uma vez que poderá Queremos registar mais do que um modelo (por exemplo uma para notificações de alerta) e outra para mosaicos e precisamos de atribuir um nome-los para poder atualizar ou eliminá-los.

    Tenha em atenção que se um dispositivo regista vários modelos com a mesma etiqueta, uma entrada mensagem filtragem que etiqueta irá resultar em várias notificações entregue ao dispositivo (um para cada modelo). Este comportamento é útil quando a mesma mensagem lógica tem de resultar em várias notificações visuais, por exemplo que mostra um distintivo e uma alerta numa aplicação da loja Windows.

2. Adicione o seguinte método para obter a região armazenada:

        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. No seu MainPage.xaml.cs, atualize o seu botão clique processador a obter o valor atual da caixa de combinação de região e fornecendo-lo para a chamada para a classe de notificações, conforme apresentado:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var locale = (string)Locale.SelectedItem;

            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                 categories);

            var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
                string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }


4. Por fim, no ficheiro App.xaml.cs, certifique-se atualizar o seu `InitNotificationsAsync` método para obter a região e utilizá-la quando subscrever:

        private async void InitNotificationsAsync()
        {
            var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


##<a name="send-localized-notifications-from-your-back-end"></a>Enviar localizadas notificações a partir do seu back-end

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]






<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Utilizar concentradores de notificação para enviar notícias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
