
1. No **Explorador de projecto** no Android Studio, abra o ficheiro ToDoActivity.java e adicione as seguintes instruções de importação.

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Adicione o seguinte método para a classe de **ToDoActivity** : 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    Esta ação cria um novo método para gerir o processo de autenticação. O utilizador é autenticado utilizando um início de sessão do Google. Uma caixa de diálogo é apresentada que apresenta o ID do utilizador autenticado. Não é possível continuar sem um método de autenticação positivo.

    > [AZURE.NOTE] Se estiver a utilizar um fornecedor de identidades que não seja o Google, altere o valor passado para o método de **início de sessão** acima para um dos seguintes: _MicrosoftAccount_, _Facebook_, _Twitter_ou _windowsazureactivedirectory_.

3. Método de **onCreate** , adicione a linha seguinte de código a seguir ao código que cria instâncias de `MobileServiceClient` objeto.

        authenticate();

    Esta chamada inicia o processo de autenticação.

4. Mova o código restante após `authenticate();` no método **onCreate** para um novo método de **createTable** , que tem este aspeto:

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. No menu **Executar** , em seguida, clique em **executar a aplicação** para iniciar a aplicação e inicie sessão com o seu fornecedor de identidade que selecionou. 

    Quando estiver com sessão iniciada com êxito, a aplicação deverá ser possível executar sem erros e deverá conseguir consultar o serviço de back-end e efetuar atualizações aos dados.