<properties
    pageTitle="Começar a trabalhar com autenticação para aplicações móveis no Xamarin Android"
    description="Saiba como utilizar aplicações móveis para autenticar os utilizadores da sua aplicação Xamarin Android através de uma variedade de fornecedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adicionar autenticação para a sua aplicação Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra-lhe como autenticar os utilizadores de uma aplicação móvel da aplicação de cliente. Neste tutorial, adicione autenticação ao projeto com um fornecedor de identidades que seja suportado pelo aplicações do Azure Mobile guia de introdução. Depois de a ser e com êxito autenticados autorizados na aplicação móvel, o valor de ID de utilizador é apresentado.

Neste tutorial é baseado no guia de introdução aplicação Mobile. Também primeiro tem de concluir o tutorial [criar uma aplicação de Xamarin.Android]. Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Registe-se a sua aplicação para a autenticação e configurar os serviços de aplicação

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a utilizadores autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou Xamarin Studio, execute o projecto de cliente num dispositivo ou emulador. Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é elevada após a aplicação é iniciada. Isto acontece porque a aplicação tentativas para aceder a sua aplicação Mobile do back-end como um utilizador não autenticado. A tabela *TodoItem* agora requer autenticação.

Em seguida, irá atualizar a aplicação de cliente para recursos de pedido a partir do back-end a aplicação Mobile com um utilizador autenticado.

##<a name="add-authentication"></a>Adicionar a autenticação à aplicação

A aplicação é atualizada para exigir que os utilizadores toque no botão **início de sessão** e autenticar antes dos dados são apresentados.

1. Adicione o seguinte código para a classe de **TodoActivity** :

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Esta ação cria um novo método para autenticar um utilizador e um processador de método para um novo botão de **início de sessão** . O utilizador no código de exemplo acima é autenticado utilizando um início de sessão do Facebook. Uma caixa de diálogo é utilizada para apresentar o ID de utilizador, uma vez autenticado.

    > [AZURE.NOTE] Se estiver a utilizar um fornecedor de identidades diferente de Facebook, altere o valor transmitido para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Método de **OnCreate** , eliminar ou comentário-out a linha seguinte de código:

        OnRefreshItemsSelected ();

4. No ficheiro Activity_To_Do.axml, adicione a definição de botão de *LoginUser* seguinte antes do botão *AddItem* existente:

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Adicione o elemento seguinte para o ficheiro de recursos Strings.xml:

        <string name="login_button_text">Sign in</string>

6. No Visual Studio ou Xamarin Studio, execute o projecto de cliente num dispositivo ou emulador e inicie sessão com o seu fornecedor de identidade que selecionou.

    Quando estiver com sessão iniciada com êxito, a aplicação irá apresentar o seu ID de início de sessão e a lista de itens de todo e pode fazer atualizações aos dados.


<!-- URLs. -->
[Criar uma aplicação de Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
