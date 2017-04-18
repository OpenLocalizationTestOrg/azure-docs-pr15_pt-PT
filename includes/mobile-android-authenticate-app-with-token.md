
No exemplo anterior mostrava um padrão de início de sessão no, que exige o cliente de contactar o fornecedor de identidade e o serviço Azure de back-end sempre que inicia a aplicação. Não só é este método ineficaz, que pode ocorrer problemas relacionados com a utilização devem muitos clientes tentar iniciar aplicação ao mesmo tempo. Uma abordagem melhor é o token de autorização devolvido pelo serviço Azure em cache e tentar utilizar este primeiro antes de utilizar uma com base no fornecedor de iniciar sessão. 

>[AZURE.NOTE]Pode a cache do token emitido pelo Azure service, independentemente de se estiver a utilizar o cliente- ou gerido pelo serviço de autenticação de back-end. Neste tutorial utiliza gerido de serviço de autenticação.


1. Abra o ficheiro ToDoActivity.java e adicione as seguintes instruções de importar:

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

2. Adicionar os membros que se seguem para o `ToDoActivity` escolares.

        public static final String SHAREDPREFFILE = "temp"; 
        public static final String USERIDPREF = "uid";  
        public static final String TOKENPREF = "tkn";   


3. No ficheiro ToDoActivity.java, adicione a seguinte definição para o `cacheUserToken` método.
 
        private void cacheUserToken(MobileServiceUser user)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            Editor editor = prefs.edit();
            editor.putString(USERIDPREF, user.getUserId());
            editor.putString(TOKENPREF, user.getAuthenticationToken());
            editor.commit();
        }   
  
    Este método armazena os id de utilizador e token num ficheiro de preferência que estiver marcado como privado. Isto deve proteger o acesso à cache para que outras aplicações num dispositivo não têm acesso ao token de uma vez que a preferência é em sandbox para a aplicação. No entanto, se alguém obtiver acesso ao dispositivo, é possível que eles podem aceder a cache de tokens através de outros meios. 

    >[AZURE.NOTE]Pode proteger ainda mais o token de encriptação se token acesso aos seus dados é considerado altamente sensível e alguém poderá ter acesso ao dispositivo. No entanto, uma solução totalmente segura está fora do âmbito deste tutorial e dependentes nos seus requisitos de segurança.


4. No ficheiro ToDoActivity.java, adicione a seguinte definição para o `loadUserTokenCache` método.

        private boolean loadUserTokenCache(MobileServiceClient client)
        {
            SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
            String userId = prefs.getString(USERIDPREF, null); 
            if (userId == null)
                return false;
            String token = prefs.getString(TOKENPREF, null); 
            if (token == null)
                return false;
                
            MobileServiceUser user = new MobileServiceUser(userId);
            user.setAuthenticationToken(token);
            client.setCurrentUser(user);
                
            return true;
        }



5. No ficheiro *ToDoActivity.java* , substitua a `authenticate` método com o seguinte método que utiliza uma cache de tokens. Altere o fornecedor de início de sessão, se pretender utilizar uma conta diferente Google.

        private void authenticate() {
            // We first try to load a token cache if one exists.
            if (loadUserTokenCache(mClient))
            {
                createTable();
            }
            // If we failed to load a token cache, login and create a token cache
            else
            {
                // Login using the Google provider.    
                ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
        
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        createAndShowDialog("You must log in. Login Required", "Error");
                    }           
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        createAndShowDialog(String.format(
                                "You are now logged in - %1$2s",
                                user.getUserId()), "Success");
                        cacheUserToken(mClient.getCurrentUser());
                        createTable();  
                    }
                });
            }
        }

6. Construa a autenticação de aplicação e teste utilizando uma conta válida. Executá-la, pelo menos, duas vezes. Durante a primeira execução, deverá receber um pedido para iniciar sessão e criar a cache de tokens. Após esta ação, cada execução irá tentar carregar a cache de tokens para autenticação e não devem ser necessários para o início de sessão.



