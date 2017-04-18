<properties
    pageTitle="Adicionar autenticação para a sua aplicação Universal Windows plataforma (UWP) | Aplicações móveis Azure"
    description="Saiba como utilizar aplicações do Azure aplicação serviço Mobile para autenticar os utilizadores da sua aplicação Universal Windows plataforma (UWP) utilizando uma variedade de fornecedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>Adicionar autenticação para a sua aplicação do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra-lhe como adicionar uma autenticação baseada na nuvem para a sua aplicação móvel. Neste tutorial, adicione autenticação para o projeto de guia de introdução Universal Windows plataforma (UWP) para as aplicações móveis utilizando um fornecedor de identidades que é suportado pelo serviço de aplicação do Azure. Depois de a ser com êxito autenticado e autorizado pelo seu back-end aplicação Mobile, o valor de ID de utilizador é apresentado.

Neste tutorial baseia-se a aplicações Mobile guia. Primeiro tem de concluir o tutorial [começar a trabalhar com aplicações móveis](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registe-se a sua aplicação para a autenticação e configurar o serviço de aplicação

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a utilizadores autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, pode verificar que tenha sido desativado acesso anónimo ao seu back-end. Com o projeto de aplicação UWP definir como o projeto de arranque, implementar e executa a aplicação; Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é elevada após a aplicação é iniciada. Isto acontece porque a aplicação tentativas para aceder ao seu código de aplicação móvel como um utilizador não autenticado, mas a tabela *TodoItem* agora requer autenticação.

Em seguida, irá atualizar a aplicação para autenticar os utilizadores antes de pedir recursos a partir do seu serviço de aplicação.

##<a name="add-authentication"></a>Adicionar a autenticação à aplicação

1. Na UWP aplicação ficheiro MainPage.cs de projecto e adicione o seguinte fragmento de código para a classe de MainPage:
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Este código autentica o utilizador com um início de sessão do Facebook. Se estiver a utilizar um fornecedor de identidades diferente de Facebook, altere o valor de **MobileServiceAuthenticationProvider** acima para o valor para o seu fornecedor.

3. Comentário saída ou eliminar a chamada para o método de **ButtonRefresh_Click** (ou o método de **InitLocalStoreAsync** ) na substituição de método **OnNavigatedTo** existente. Isto impede que os dados que está a ser carregados antes do utilizador é autenticado. Em seguida, irá adicionar um botão de **início de sessão** para a aplicação que accionadores autenticação.

4. Adicione o fragmento de código seguintes para a classe de MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Abra o ficheiro de projeto MainPage.xaml, localize o elemento que define o botão **Guardar** e substituí-lo com o seguinte código:

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Prima a tecla F5 para executar a aplicação, clique no botão **início de sessão** e iniciar sessão na aplicação com o seu fornecedor de identidade que selecionou. Após o início de sessão no é efetuada com êxito, a aplicação é executado sem erros e podem consultar o seu back-end e efetuar atualizações aos dados.


##<a name="tokens"></a>Armazenar o token de autenticação do cliente

No exemplo anterior mostrava um padrão de início de sessão no, que exige o cliente de contactar o fornecedor de identidade e o serviço de aplicação sempre que inicia a aplicação. Não só é ineficaz, pode executar este método para utilização-se refere problemas devem muitos clientes tentar iniciar aplicação ao mesmo tempo. Uma abordagem melhor é o token de autorização devolvido pela sua aplicação de serviço de cache e tentar utilizar este primeiro antes de utilizar uma com base no fornecedor de iniciar sessão.

>[AZURE.NOTE]Pode a cache do token emitido por serviços de aplicação, independentemente de se estiver a utilizar o cliente- ou gerido pelo serviço de autenticação. Neste tutorial utiliza gerido de serviço de autenticação.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Próximos passos

Agora que se tiver concluído a este tutorial de autenticação básica, considere continuar para um dos seguintes tutoriais:

+ [Adicionar as notificações push para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar push notificações de suporte para a sua aplicação e configurar a sua aplicação Mobile do back-end a utilizar o Azure notificação concentradores para enviar notificações push.

+ [Ativar a sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar a sua aplicação utilizando um back-end aplicação Mobile suporte offline. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

