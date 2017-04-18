1. No seu projeto de **aplicação** , abra o ficheiro `AndroidManifest.xml`. No código nos dois passos, substituir _`**my_app_package**`_ com o nome do pacote de aplicações para o seu projeto, que é o valor da `package` atributo da `manifest` etiqueta.

2. Adicione as seguintes novas permissões após a existente `uses-permission` elemento:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Adicione o código seguinte após a `application` tag de abertura:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Abra o ficheiro *ToDoActivity.java*e adicione a seguinte instrução importar:

        import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Adicionar a seguinte variável privada à classe: substituir _`<PROJECT_NUMBER>`_ com o número de projeto atribuído pelo Google para a sua aplicação no procedimento anterior:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. Altere a definição do *MobileServiceClient* de **privado** para **público estático**, para que-lo agora este aspeto:

        public static MobileServiceClient mClient;

7. Seguinte que precisamos de adicionar uma nova classe para processar as notificações. No Project Explorer, abra o **src** => **principal** => **java** nós e o botão direito do rato o nó do nome do pacote: clique em **Novo**, em seguida, clique em **Classe Java**.

8. Em **nome** escreva `MyHandler`, em seguida, clique em **OK**.


    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


9. No ficheiro MyHandler, substitua a declaração de classe

        public class MyHandler extends NotificationsHandler {


10. Adicione as seguintes instruções de importar para o `MyHandler` escolares:

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;


11. Em seguida, adicione este membro para o `MyHandler` classe:

        public static final int NOTIFICATION_ID = 1;


12. No `MyHandler` classe, adicione o seguinte código para substituir o método de **onRegistered** , que regista o seu dispositivo com o serviço móvel concentrador de notificação.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            new AsyncTask<Void, Void, Void>() {

                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                        return null;
                    }
                    catch(Exception e) {
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }


13. No `MyHandler` classe, adicione o seguinte código para substituir o método de **onReceive** , o que faz com que a notificação apresentar quando for recebida.

        @Override
        public void onReceive(Context context, Bundle bundle) {
                String msg = bundle.getString("message");

                PendingIntent contentIntent = PendingIntent.getActivity(context,
                        0, // requestCode
                        new Intent(context, ToDoActivity.class),
                        0); // flags

                Notification notification = new NotificationCompat.Builder(context)
                        .setSmallIcon(R.drawable.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                        .setContentText(msg)
                        .setContentIntent(contentIntent)
                        .build();

                NotificationManager notificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);
                notificationManager.notify(NOTIFICATION_ID, notification);
        }


14. Novamente no ficheiro de TodoActivity.java, atualize o método de **onCreate** a classe de *ToDoActivity* para registar a classe de processador de notificação de. Certifique-se adicionar este código depois do *MobileServiceClient* é criada uma instância.


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Agora, a aplicação é atualizada para suportar as notificações push.
