<properties
    pageTitle="Notificação concentradores notícias de última hora Tutorial - Android"
    description="Saiba como utilizar o Azure Service Bus notificação concentradores para enviar notificações de notícias de divisão para dispositivos Android."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>Utilizar concentradores de notificação para enviar notícias de última hora

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>Descrição geral

Este tópico mostra-lhe como utilizar o Azure notificação concentradores difundir força as notificações de notícias para uma aplicação do Android. Quando estiver concluído, serão poderá para se registar no força de categorias de notícias que lhe interessam e receber apenas as notificações push para as categorias. Este cenário é um padrão comum para aplicações muitos onde notificações tem de ser enviadas para grupos de utilizadores que tenham declarados anteriormente interesse nos mesmos, por exemplo, o leitor RSS, aplicações para ventoinhas música, etc.

Cenários de difusão estão ativados para inclusão de um ou mais _etiquetas_ ao criar um registo no centro do notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que tenham registados para a etiqueta irão receber a notificação. Porque etiquetas são simplesmente cadeias, não tem de ser aprovisionada com antecedência. Para mais informações sobre etiquetas, consulte [notificação concentradores encaminhamento e expressões de etiqueta](notification-hubs-tags-segment-push-message.md).


##<a name="prerequisites"></a>Pré-requisitos

Este tópico constrói a aplicação que criou no [artigo Introdução ao concentradores de notificação][get-started]. Antes de iniciar este tutorial, tem já concluiu [começar com notificação concentradores][get-started].

##<a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categoria à aplicação

É o primeiro passo adicionar os elementos da IU a sua atividade principal existente que permitem ao utilizador selecionar categorias para registar. As categorias seleccionadas por um utilizador são armazenadas no dispositivo. Quando inicia a aplicação, um registo de dispositivo é criado no seu centro de notificação com as categorias selecionados como etiquetas.

1. Abra o seu ficheiro res/layout/activity_main.xml e substituir o conteúdo com o seguinte:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. Abra o seu ficheiro res/values/strings.xml e adicione as linhas seguintes:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    O esquema de gráfico main_activity.xml deverá agora o seguinte aspeto:

    ![][A1]

3. Agora, crie uma classe **notificações** no pacote do mesmo como a sua aula **MainActivity** .

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Esta classe utiliza armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Também contém métodos para se registar nestas categorias.


4. Na sua aula **MainActivity** remover os campos privados para **NotificationHub** e **GoogleCloudMessaging**e adicionar um campo para **notificações**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. Em seguida, no método de **onCreate** , remova a inicialização do **concentrador de tipos de** campo e o método de **registerWithNotificationHubs** . Em seguida, adicione as seguintes linhas que iniciar uma instância da classe **notificações** . 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`e `HubListenConnectionString` já deve ser definido com o `<hub name>` e `<connection string with listen access>` marcadores de posição com o seu nome de concentrador de notificação e a cadeia de ligação para *DefaultListenSharedAccessSignature* que obteve anterior.

    > [AZURE.NOTE] Porque credenciais são distribuídas com uma aplicação de cliente não são geralmente seguras, deverá apenas distribuir a chave para ouvir acesso com a sua aplicação de cliente. Ouvir permite acesso a sua aplicação para se registar no notificações, mas os registos existentes não pode ser modificada e as notificações não podem ser enviadas. A tecla de acesso total é utilizada num serviço de back-end segura para envio de notificações e alterar os registos existentes.


6. Em seguida, adicione as seguintes importações e `subscribe` método para processar no botão subscrever clique em evento:
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Este método cria uma lista de categorias e utiliza a classe de **notificações** para armazenar a lista no armazenamento local e registar as tags correspondentes com o seu centro de notificação. Quando categorias são alteradas, o registo é recriado com novas categorias.

A aplicação é agora possível armazenar um conjunto de categorias no local armazenamento no dispositivo e registe-se com o concentrador de notificação sempre que o utilizador altera a seleção de categorias.

##<a name="register-for-notifications"></a>Registe-se para as notificações

Estes passos registe-se com o concentrador de notificação no arranque utilizando as categorias que tenham sido armazenadas no armazenamento local.

> [AZURE.NOTE] Uma vez que pode alterar o atributos registrationId atribuída ao Google Cloud mensagens (GCM) em qualquer altura, deverá registar para notificações com frequência evitar falhas de notificação. Este exemplo regista para notificação sempre que inicia a aplicação. Para as aplicações que são executadas com frequência, mais do que uma vez por dia, pode provavelmente ignorar registo para manter a largura de banda se menos de um dia passou desde o registo anterior.


1. Adicione o código seguinte no final do método **onCreate** na classe **MainActivity** :

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Isto torna-se de que sempre que inicia a aplicação-obtém as categorias do armazenamento local e os pedidos de um registeration para estas categorias. 

2. Em seguida, atualize o `onStart()` método a `MainActivity` classe da seguinte forma:

    @OverrideonStart() void protegida {super.onStart();      isVisible = verdadeiro;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    Este procedimento actualiza a atividade principal com o estado das categorias previamente guardadas.

A aplicação está agora concluída e pode armazenar um conjunto de categorias no armazenamento local dispositivo utilizado para registar o concentrador de notificação sempre que o utilizador altera a seleção de categorias. Em seguida, podemos definirá back-end que pode enviar notificações de categoria para esta aplicação.

##<a name="sending-tagged-notifications"></a>Enviar notificações com tags

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. No Android Studio, criar a aplicação e iniciá-lo num dispositivo ou emulador.

    Nota que a aplicação IU fornece um conjunto de alterna que lhe permite escolher as categorias para subscrever.

2. Ativar uma ou mais alterna categorias e, em seguida, clique em **subscrever**.

    A aplicação converte as categorias selecionadas etiquetas e os pedidos de um novo registo de dispositivo para as etiquetas selecionados a partir do hub a notificação. As categorias registadas são devolvidas e apresentadas numa notificação de alerta.

4. Envie uma notificação de novo ao executar a aplicação de consola do .NET.  Em alternativa, pode enviar notificações de modelo com tags através do separador de depuração do seu centro de notificação do [Azure clássica Portal].

    Notificações para as categorias selecionadas são apresentados como notificações de alerta.

##<a name="next-steps"></a>Próximos passos

Neste tutorial, vamos aprendeu como difundir notícias de última hora por categoria. Considere a concluir uma das seguintes tutoriais que destacam outros cenários de notificação concentradores avançados:

+ [Utilizar notificação concentradores difundir notícias de última hora localizados]

    Saiba como expandir a aplicação de notícias de divisão para ativar o envio de notificações localizadas.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Utilizar notificação concentradores difundir notícias de última hora localizados]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure Portal clássico]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
