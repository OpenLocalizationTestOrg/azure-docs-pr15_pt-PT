
1. Na solução vista (ou **Solução Explorador** no Visual Studio), com o botão direito na pasta de **componentes** , clique em **Obter mais componentes...**, procure o componente do **Cliente de mensagens do Google Cloud** e adicioná-lo ao projeto.

2. Abra o ficheiro de projeto ToDoActivity.cs e adicione o seguinte instrução para a classe de a utilizar:

        using Gcm.Client;

3. Aula **ToDoActivity** , adicione o seguinte código novo: 

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

    Isto permite-lhe aceder a instância do cliente móvel do processo de serviço de processador push.

4.  Adicione o seguinte código para o método de **OnCreate** , depois do **MobileServiceClient** é criado:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

O **ToDoActivity** agora está preparado para adicionar as notificações push.