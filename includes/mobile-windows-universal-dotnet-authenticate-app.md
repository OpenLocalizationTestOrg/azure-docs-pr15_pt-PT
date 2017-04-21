
1. Abra o ficheiro de projeto partilhada MainPage.cs e adicione o seguinte fragmento de código para a classe de MainPage:
    
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

3. Comentário saída ou eliminar a chamada para o método **RefreshTodoItems** a substituição de método **OnNavigatedTo** existente.

    Isto impede que os dados que está a ser carregados antes do utilizador é autenticado. Em seguida, irá adicionar um botão de **início de sessão** para a aplicação que accionadores autenticação.

4. Adicione o fragmento de código seguintes para a classe de MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. No project de aplicação da loja Windows, abra o ficheiro de projeto MainPage.xaml e adicione o seguinte elemento do **botão** apenas antes do elemento que define o botão **Guardar** :

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. No project de aplicação da loja Windows Phone, adicione o elemento **botão** seguinte na **ContentPanel**, após o elemento da **caixa de texto** :

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Abra o ficheiro de projeto App.xaml.cs partilhado e adicione o seguinte código:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    Se o método de **OnActivated** já existir, basta adicionar a `#if...#endif` bloco de código.

9. Prima a tecla F5 para executar a aplicação da loja Windows, clique no botão **início de sessão** e iniciar sessão na aplicação com o seu fornecedor de identidade que selecionou. 

    Quando estiver com sessão iniciada com êxito, a aplicação deverá ser possível executar sem erros e deverá conseguir consultar o seu back-end e efetuar atualizações aos dados.

10. Com o botão direito do projeto de aplicação da loja Windows Phone, clique em **Definir como projeto de arranque**e, em seguida, repita o passo anterior para verificar que a loja do Windows Phone aplicação também é executado corretamente.  

 