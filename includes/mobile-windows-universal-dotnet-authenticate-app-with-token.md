
1. No ficheiro de projeto MainPage.xaml.cs, adicione as seguintes instruções **utilizando** :

        using System.Linq;      
        using Windows.Security.Credentials;

2. Substitua o método de **AuthenticateAsync** com o seguinte código:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

    Nesta versão do **AuthenticateAsync**, a aplicação tentará utilizar credenciais armazenadas na **PasswordVault** para aceder ao serviço. Uma sessão normal é também realizado quando não existe nenhuma credencial armazenada.

    >[AZURE.NOTE]Poderá ser expirado um token em cache e expiração do token também pode ocorrer após a autenticação quando a aplicação está a ser utilizado. Para saber como determinar se um token tiver expirado, consulte o artigo [verificar os tokens de autenticação expirados](http://aka.ms/jww5vp). Para uma solução para processamento de erros de autorização relacionados com o tokens prestes a expirar, consulte a mensagem [em cache e que processam tokens expirados nos serviços do Azure Mobile gerido SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 

3. Reinicie a aplicação duas vezes.

    Repare que no arranque a primeira, iniciar sessão com o fornecedor novamente é necessário. No entanto, no segundo reinício são utilizadas as credenciais em cache e início de sessão no será ignorada. 
