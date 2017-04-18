4. Criar uma nova classe no projeto denominado `ToDoBroadcastReceiver`.

5. Adicione o seguinte utilizando instruções para a classe de **ToDoBroadcastReceiver** :

        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

6. Adicione os seguintes pedidos de permissão entre as declarações de **utilização** e a declaração de **espaço de nomes** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. Substitua a definição de classe **ToDoBroadcastReceiver** existente com o seguinte:
 
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
        public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            // Set the Google app ID.
            public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
        }

    No código acima, tem de substituir _`<PROJECT_NUMBER>`_ com o número de projeto atribuído pelo Google quando a sua aplicação no portal de programador do Google aprovisionado. 

8. No ficheiro de projeto ToDoBroadcastReceiver.cs, adicione o seguinte código que define a classe de **PushHandlerService** :
 
        // The ServiceAttribute must be applied to the class.
        [Service] 
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
 
            public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
        }

    Note que esta classe deriva da **GcmServiceBase** e que o atributo **serviço** deve ser aplicado a esta classe.

    >[AZURE.NOTE]A classe de **GcmServiceBase** implementa o **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** e **OnError()** métodos. Tem de substituir estes métodos de aula **PushHandlerService** .

5. Adicione o seguinte código para a classe de **PushHandlerService** que substitui o processador de eventos **OnRegistered** . 

        protected override void OnRegistered(Context context, string registrationId)
        {
            System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");

            // Get the MobileServiceClient from the current activity instance.
            MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;
            var push = client.GetPush();

            // Define a message body for GCM.
            const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

            // Define the template registration as JSON.
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
              {"body", templateBodyGCM }
            };

            try
            {
                // Make sure we run the registration on the same thread as the activity, 
                // to avoid threading errors.
                ToDoActivity.CurrentActivity.RunOnUiThread(

                    // Register the template with Notification Hubs.
                    async () => await push.RegisterAsync(registrationId, templates));
                
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Push Installation Id", push.InstallationId.ToString()));
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Error with Azure push registration: {0}", ex.Message));
            }
        }

    Este método utiliza o ID de registo GCM devolvido para registar com Azure para notificações push. Etiquetas só podem ser adicionadas ao registo após ter sido criada. Para obter mais informações, consulte o artigo [como: Adicionar etiquetas a uma instalação do dispositivo para ativar etiquetas de push](../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

10. Substitua o método de **OnMessage** no **PushHandlerService** com o seguinte código:

        protected override void OnMessage(Context context, Intent intent)
        {          
            string message = string.Empty;

            // Extract the push notification message from the intent.
            if (intent.Extras.ContainsKey("message"))
            {
                message = intent.Extras.Get("message").ToString();
                var title = "New item added:";

                // Create a notification manager to send the notification.
                var notificationManager = 
                    GetSystemService(Context.NotificationService) as NotificationManager;

                // Create a new intent to show the notification in the UI. 
                PendingIntent contentIntent = 
                    PendingIntent.GetActivity(context, 0, 
                    new Intent(this, typeof(ToDoActivity)), 0);           

                // Create the notification using the builder.
                var builder = new Notification.Builder(context);
                builder.SetAutoCancel(true);
                builder.SetContentTitle(title);
                builder.SetContentText(message);
                builder.SetSmallIcon(Resource.Drawable.ic_launcher);
                builder.SetContentIntent(contentIntent);
                var notification = builder.Build();

                // Display the notification in the Notifications Area.
                notificationManager.Notify(1, notification);

            }
        }

12. Substitui os métodos de **OnUnRegistered()** e **OnError()** com o código seguinte.

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            throw new NotImplementedException();
        }

        protected override void OnError(Context context, string errorId)
        {
            System.Diagnostics.Debug.WriteLine(
                string.Format("Error occurred in the notification: {0}.", errorId));
        }