<properties
    pageTitle="Começar a trabalhar com autenticação para aplicações Mobile na aplicação Xamarin.Forms | Microsoft Azure"
    description="Saiba como utilizar aplicações móveis para autenticar os utilizadores da sua aplicação Xamarin formulários através de uma variedade de fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinforms-app"></a>Adicionar autenticação para a sua aplicação Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como autenticar os utilizadores de uma aplicação de Mobile de serviço de aplicação a partir da sua aplicação de cliente. Neste tutorial, adicione autenticação para o projecto de guia de introdução de Xamarin.Forms utilizando um fornecedor de identidades que é suportado pelo serviço de aplicação. Depois de a ser e com êxito autenticados autorizados pela sua aplicação móvel, o valor de ID de utilizador é apresentado, e poderão aceder aos dados de tabela restrito.

##<a name="prerequisites"></a>Pré-requisitos

Para o resultado melhor com este tutorial, recomendamos que conclua o tutorial de [criar uma aplicação Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md) pela primeira vez. Depois de concluir este tutorial, terá de um projeto de Xamarin.Forms que é uma aplicação para fazer com várias plataforma.

Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registe-se a sua aplicação para a autenticação e configurar os serviços de aplicação

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a utilizadores autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##<a name="add-authentication-to-the-portable-class-library"></a>Adicionar autenticação para a biblioteca de classes portátil

Aplicações móveis utiliza o método de extensão [LoginAsync] na [MobileServiceClient] para iniciar sessão um utilizador com autenticação de aplicação de serviço. Este exemplo utiliza um fluxo de autenticação de servidor gerido que apresenta início de sessão no interface o fornecedor na aplicação. Para mais informações, consulte o artigo [autenticação de servidor gerido](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Para fornecer uma melhor experiência de utilizador na sua aplicação de produção, pode considerar em vez disso, utilizando a [autenticação do cliente com gestão](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow). 

Para autenticar com um projeto Xamarin.Forms, pode definir uma interface **IAuthenticate** na biblioteca de classe portáteis para a aplicação. Também a atualizar a interface de utilizador definida na biblioteca de classe portáteis para adicionar um **início de sessão no** botão que o utilizador clica para começar a autenticação. Após a autenticação efetuada com êxito, são carregados dados a partir da aplicação móvel back-end.

Tem de implementar a interface **IAuthenticate** para cada plataforma suportada pela sua aplicação.


1. No Visual Studio ou Xamarin Studio, abrir App.cs do projeto com **portátil** em nome, o que é o projeto de biblioteca de classes portátil, em seguida, adicione o seguinte `using` declaração:

        using System.Threading.Tasks;

2. No App.cs, adicione o seguinte `IAuthenticate` imediatamente antes da interface definição a `App` classe definição.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }

3. Adicione membros estáticos que se seguem para a classe de **aplicação** para iniciar a interface de com uma implementação específica de plataforma.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Abra TodoList.xaml do projeto a biblioteca de classes portátil, adicione o elemento seguinte do **botão** no elemento de esquema de *buttonsPanel* , após o botão existente: 

        <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>

    Este botão accionadores gerido pelo servidor autenticação com a aplicação móvel do back-end.

5. Abrir TodoList.xaml.cs do projeto a biblioteca de classes portátil, em seguida, adicione o campo seguinte para o `TodoList` classe:

        // Track whether the user has authenticated. 
        bool authenticated = false;


6. Substitua o método de **OnAppearing** com o seguinte código:

        protected override async void OnAppearing()
        {
            base.OnAppearing();
    
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Isto torna-se de que os dados só são atualizados de serviço depois do utilizador autenticado.

7. Adicione o processador seguinte para o evento **Clicked** para a classe de **fazer** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Guardar as suas alterações e reconstruirá o projeto de biblioteca de classes Portable verificar sem erros.


##<a name="add-authentication-to-the-android-app"></a>Adicionar autenticação à aplicação do Android

Esta secção mostra como implementar a interface **IAuthenticate** no projeto aplicação Android. Ignore esta secção se não estiver a suportar dispositivos Android.

1. No Visual Studio ou Xamarin Studio, com o botão direito do projecto **droid** , em seguida, **Definir como projeto de arranque**.

2. Prima F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é elevada após a aplicação é iniciada. Isto acontece porque acesso no back-end está limitado a apenas os utilizadores autorizados.

3. Abra MainActivity.cs no projeto Android e adicione o seguinte `using` declarações:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Atualize a classe de **MainActivity** para implementar a interface **IAuthenticate** , da seguinte forma:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Atualize a classe de **MainActivity** ao adicionar um campo de **MobileServiceUser** e um método de **autenticação** , uma ferramenta necessária ao **IAuthenticate** interface, da seguinte forma:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


    Se estiver a utilizar um fornecedor de identidades diferente de Facebook, selecione um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione o seguinte código para o método de **OnCreate** a classe de **MainActivity** antes das chamadas para de `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Isto torna-se de que o autenticador está inicializado antes das cargas de aplicação.

7. Reconstruir a aplicação, executá-la, em seguida, iniciar sessão com o fornecedor de autenticação que escolheu e certifique-se de que é podem aceder aos dados como um utilizador autenticado.

##<a name="add-authentication-to-the-ios-app"></a>Adicionar autenticação à aplicação do iOS

Esta secção mostra como implementar a interface **IAuthenticate** no projeto de aplicação iOS. Ignore esta secção se não estiver a suportar dispositivos iOS.

1. No Visual Studio ou Xamarin Studio, com o botão direito do projecto **iOS** , em seguida, **Definir como projeto de arranque**.

2. Prima F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é elevada após a aplicação é iniciada. Isto acontece porque acesso no back-end está limitado a apenas os utilizadores autorizados.

4. Abra AppDelegate.cs no projeto iOS e adicione o seguinte `using` declarações:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Atualize a classe de **AppDelegate** para implementar a interface **IAuthenticate** , da seguinte forma:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Atualize a classe de **AppDelegate** ao adicionar um campo de **MobileServiceUser** e um método de **autenticação** , uma ferramenta necessária ao **IAuthenticate** interface, da seguinte forma:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

    Se estiver a utilizar um fornecedor de identidades diferente de Facebook, selecione um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicionar a linha seguinte de código para o método de **FinishedLaunching** antes das chamadas para `LoadApplication()`: 

        App.Init(this);

    Isto torna-se de que o autenticador está inicializado antes da aplicação é carregada.

7. Reconstruir a aplicação, executá-la, em seguida, iniciar sessão com o fornecedor de autenticação que escolheu e certifique-se de que é podem aceder aos dados como um utilizador autenticado.


##<a name="add-authentication-to-windows-app-projects"></a>Adicionar autenticação a projetos de aplicação do Windows

Esta secção mostra como implementar a interface **IAuthenticate** no Windows 8.1 ou Windows Phone 8.1 projetos de aplicação. Aplicam os mesmos passos para projetos Universal Windows plataforma (UWP). Ignore esta secção se não estiver a suportar dispositivos com Windows.

1. No Visual Studio, com o botão direito no **WinApp** ou projeto de **WinPhone81** , em seguida, **Definir como projeto de arranque**.

2. Prima F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é elevada após a aplicação é iniciada. Isto acontece porque acesso no back-end está limitado a apenas os utilizadores autorizados.

3. Abra o MainPage.xaml.cs para o projeto de aplicação do Windows e adicione o seguinte `using` declarações:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Substituir `<your_Portable_Class_Library_namespace>` com o espaço de nomes para a biblioteca de classe portátil.

4. Atualize a classe de **MainPage** para implementar a interface **IAuthenticate** , da seguinte forma:

        public sealed partial class MainPage : IAuthenticate


5. Atualize a classe de **MainPage** ao adicionar um campo de **MobileServiceUser** e um método de **autenticação** , uma ferramenta necessária ao **IAuthenticate** interface, da seguinte forma:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


    Se estiver a utilizar um fornecedor de identidades diferente de Facebook, selecione um valor diferente para [MobileServiceAuthenticationProvider].

6. Adicione a linha seguinte de código no construtor para a classe de **MainPage** antes das chamadas para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Substituir `<your_Portable_Class_Library_namespace>` com o espaço de nomes para a biblioteca de classe portátil.  
    Se este for o projeto WinApp, pode ignorar para baixo para o passo 8. O passo seguinte aplica-se apenas ao projecto WinPhone81, onde precisa de concluir a chamada de retorno do início de sessão.

7. (Opcional) No project de aplicação **WinPhone81** , abra App.xaml.cs e adicione o seguinte `using` declarações:

        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;

    Substituir `<your_Portable_Class_Library_namespace>` com o espaço de nomes para a biblioteca de classe portátil.

8.  Adicione a substituição de método **OnActivated** seguinte para a classe de **aplicação** :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            // We just need to handle activation that occurs after web authentication. 
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Get the client and call the LoginComplete method to complete authentication.
                var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
                client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
        }

    Quando já existe a substituição de método, basta adicione o código condicional a partir do fragmento de acima.

7. Reconstruir a aplicação, executá-la, em seguida, iniciar sessão com o fornecedor de autenticação que escolheu e certifique-se de que é podem aceder aos dados como um utilizador autenticado.

##<a name="next-steps"></a>Próximos passos

Agora que se tiver concluído a este tutorial de autenticação básica, considere continuar para um dos seguintes tutoriais:

+ [Adicionar as notificações push para a sua aplicação](app-service-mobile-xamarin-forms-get-started-push.md)  
  Saiba como adicionar push notificações de suporte para a sua aplicação e configurar a sua aplicação Mobile do back-end a utilizar o Azure notificação concentradores para enviar notificações push.

+ [Ativar a sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar a sua aplicação utilizando um back-end aplicação Mobile suporte offline. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

