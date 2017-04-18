<properties
    pageTitle="Começar a trabalhar com autenticação para aplicações móveis no Xamarin iOS"
    description="Saiba como utilizar aplicações móveis para autenticar os utilizadores da sua aplicação de iOS Xamarin através de uma variedade de fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
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
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>Adicionar autenticação para a sua aplicação Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra-lhe como autenticar os utilizadores de uma aplicação de Mobile de serviço de aplicação a partir da sua aplicação de cliente. Neste tutorial, adicione autenticação para o projecto de guia de introdução de Xamarin.iOS utilizando um fornecedor de identidades que é suportado pelo serviço de aplicação. Depois de a ser e com êxito autenticados autorizados pela sua aplicação móvel, o valor de ID de utilizador é apresentado e poderão aceder aos dados de tabela restrito.

Primeiro tem de concluir o tutorial [criar uma aplicação de Xamarin.iOS]. Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registe-se a sua aplicação para a autenticação e configurar os serviços de aplicação

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a utilizadores autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. na Visual Studio ou Xamarin Studio, execute o projecto de cliente num dispositivo ou emulador. Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é elevada após a aplicação é iniciada. A falha é registada na consola do depurador. Por isso, no Visual Studio, deverá ver a falha na janela de saída.

&nbsp;&nbsp;Esta não autorizada falha acontece porque a aplicação tentativas para aceder a sua aplicação Mobile do back-end como um utilizador não autenticado. A tabela *TodoItem* agora requer autenticação.

Em seguida, irá atualizar a aplicação de cliente para recursos de pedido a partir do back-end a aplicação Mobile com um utilizador autenticado.

##<a name="add-authentication-to-the-app"></a>Adicionar a autenticação à aplicação

Nesta secção, irá modificar a aplicação para apresentar um ecrã de início de sessão antes de apresentar dados. Quando inicia a aplicação, não irá não ligar a sua aplicação de serviço e não serão apresentados quaisquer dados. Depois de pela primeira vez que o utilizador executa o gesto de atualização, irá aparecer no ecrã de início de sessão; Depois de início de sessão bem sucedido vai ser apresentada a lista de itens de todo.

1. No projeto de cliente, abra o ficheiro **QSTodoService.cs** e adicione o seguinte utilizando instrução e `MobileServiceUser` com acessor para a classe de QSTodoService:

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. Adicione o novo método denominado **autenticar** para **QSTodoService** com a seguinte definição:


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Se estiver a utilizar um fornecedor de identidades que não seja uma Facebook, altere o valor transmitido para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**. Modificar a definição de método de **ViewDidLoad** remover a chamada para **RefreshAsync()** junto ao fim:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. Modificar o método **RefreshAsync** para autenticar se a propriedade de **utilizador** é nula. Adicione o seguinte código na parte superior da definição do método:

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. No Visual Studio ou Xamarin Studio ligado ao seu anfitrião de construir Xamarin no seu Mac, execute o projecto de cliente filtragem de um dispositivo ou emulador. Certifique-se de que a aplicação apresenta sem dados.

    Execute o gesto de atualização solicitando-se para baixo na lista de produtos, que irão provocar o ecrã de início de sessão que seja apresentado. Assim que introduziu credenciais válidas com êxito, a aplicação irá apresentar a lista de itens de todo e pode fazer atualizações aos dados.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Criar uma aplicação de Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
