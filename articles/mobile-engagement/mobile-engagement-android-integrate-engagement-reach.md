<properties
    pageTitle="Integração do Azure Cativação móvel Android SDK"
    description="Atualizações e procedimentos para Android SDK para Azure Mobile Cativação mais recentes"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-reach-on-android"></a>Como integrar o atingir compromisso no Android

> [AZURE.IMPORTANT] Tem de seguir o integração com o procedimento descrito na secção de como integrar Cativação no Android documento antes de seguir este guia.

##<a name="standard-integration"></a>Integração do padrão

O SDK atinja requer a **biblioteca de suporte Android (v4)**.

A forma mais rápida de adicionar a biblioteca ao seu projeto no **Eclipse** é `Right click on your project -> Android Tools -> Add Support Library...`.

Se não utiliza o Eclipse, pode ler as instruções [aqui].

Copie os ficheiros de recursos alcance do SDK do projeto:

-   Copiar ficheiros a partir do `res/layout` pasta fornecido com o SDK para o `res/layout` pasta da sua aplicação.
-   Copiar ficheiros a partir do `res/drawable` pasta fornecido com o SDK para o `res/drawable` pasta da sua aplicação.

Editar o `AndroidManifest.xml` ficheiro:

-   Adicione a seguinte secção (entre o `<application>` e `</application>` etiquetas):

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
              <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
              </intent-filter>
            </receiver>

-   É necessário esta permissão para reproduzir as notificações do sistema que não clicou durante o arranque (caso contrário, serão mantidos no disco, mas não serão apresentadas deixem, na verdade tem de incluir este).

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Especificar um ícone utilizado para notificações (ambos na aplicação e sistema aqueles) ao copiar e edição a secção seguinte (entre o `<application>` e `</application>` etiquetas):

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] Esta secção é **obrigatória** se planeia utilizar notificações do sistema quando criar campanhas de atingir. Android impede que as notificações do sistema sem ícones a ser apresentado. Por isso, se omitir nesta secção, os utilizadores finais não poderá recebê-los.

-   Se criar campanhas com as notificações do sistema com imagem grande, tem de adicionar as seguintes permissões (depois do `</application>` etiqueta) se:

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   No Android M e se a aplicação destinos nível de Android API 23 ou superior, ``WRITE_EXTERNAL_STORAGE`` permissão requer a aprovação de utilizador. Leia [Esta secção](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   Para as notificações do sistema também pode especificar na campanha alcance se o dispositivo deve simultaneamente e/ou vibração. Para que funcione, tem de Certifique-se de que declarados a permissão seguinte (depois do `</application>` etiqueta):

            <uses-permission android:name="android.permission.VIBRATE" />

    Sem esta permissão, Android impede que as notificações do sistema sejam apresentadas se verificada tocar ou para a opção vibrate no Gestor de campanha atinja.

-   Se construir a sua aplicação utilizando **ProGuard** e tiver erros relacionados com a biblioteca de suporte Android ou para caixa de Cativação, adicione as seguintes linhas para sua `proguard.cfg` ficheiro:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Push nativo

Agora que tiver configurado o módulo alcance, tem de configurar push nativo possam receber campanhas no dispositivo.

Suportamos dois serviços no Android:

  - Google Play dispositivos: utilizar [Mensagens do Google Cloud] seguindo o guia de [como integrar GCM com o guia de Cativação](mobile-engagement-android-gcm-integrate.md) .
  - Dispositivos Amazon: utilizar [Mensagens de dispositivo Amazon] seguindo o guia de [como integrar ADM com o guia de Cativação](mobile-engagement-android-adm-integrate.md) .

Se pretender dispositivos Amazon e Google Play, respetivo possível tem tudo dentro 1 AndroidManifest.xml/APK para o desenvolvimento de destino. Mas ao submeter para Amazon, poderá rejeitar a aplicação de se encontrarem código GCM.

Deve utilizar vários APKs nesse caso.

**A aplicação está agora pronta para receber e apresentar campanhas de atingir!**

##<a name="how-to-handle-data-push"></a>Como lidar push de dados

### <a name="integration"></a>Integração

Se pretender que a sua aplicação possam receber alcance puxa de dados, tem de criar uma sub-classe de `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` e faça-o na referência a `AndroidManifest.xml` ficheiro (entre o `<application>` e/ou `</application>` etiquetas):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Em seguida, pode substituir o `onDataPushStringReceived` e `onDataPushBase64Received` chamadas de retorno. Eis um exemplo:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Categoria

Parâmetro de categoria é opcional quando criar uma campanha de emissão de dados e permite-lhe filtrar dados emite. Isto é útil se tiver vários destinatários difusão processamento diferentes tipos de dados puxa, ou se pretender emissão diferentes tipos de `Base64` dados e pretende identificar o respetivo tipo antes da análise-los.

### <a name="callbacks-return-parameter"></a>Parâmetro de retorno dos chamadas de retorno

Eis algumas diretrizes para processar corretamente o parâmetro de retorno `onDataPushStringReceived` e `onDataPushBase64Received`:

-   Deve devolver uma difusão recetor `null` na chamada de retorno se não sabe como gerir um push de dados. Deve utilizar a categoria para determinar se o seu auscultador difusão deve processar push os dados ou não.
-   Uma no auscultador difusão deve devolver `true` na chamada de retorno se aceita o push de dados.
-   Uma no auscultador difusão deve devolver `false` na chamada de retorno se reconhece push os dados, mas rejeita-la por qualquer motivo. Por exemplo, devolver `false` quando os dados recebidos são inválidos.
-   Se um difundir o recetor devolve `true` enquanto outro um devolve `false` para o mesmo push de dados, o comportamento é indefinido, nunca deverá fazê-lo.

O tipo devolvido é utilizado apenas para as estatísticas de atingir:

-   `Replied`é incrementado se um dos destinatários a difusão devolvido quer `true` ou `false`.
-   `Actioned`é incrementado apenas se um dos destinatários a difusão devolvido `true`.

##<a name="how-to-customize-campaigns"></a>Como personalizar campanhas

Para personalizar campanhas, pode modificar os esquemas fornecidos no SDK atinja.

Devo manter todos os identificadores de utilizados nos esquemas de e manter os tipos das vistas que utilizam um identificador, especialmente para vistas de texto e vistas de imagem. Algumas vistas apenas são utilizadas para ocultar ou mostrar áreas para que os respetivos tipo pode ser alterado. Verifique o código de origem, se pretender alterar o tipo de uma vista nos esquemas de fornecido.

### <a name="notifications"></a>Notificações

Existem dois tipos de notificações: notificações de sistema e na aplicação que utilizar ficheiros de esquema diferente.

#### <a name="system-notifications"></a>Notificações de sistema

Para personalizar as notificações do sistema que tem de utilizar as **categorias**. Pode ir para [as categorias](#categories).

#### <a name="in-app-notifications"></a>Notificações de na aplicação

Por predefinição, uma notificação de na aplicação é uma vista que é adicionada dinamicamente para a interface de utilizador de atividade atual graças ao método Android `addContentView()`. Esta opção é denominada uma sobreposição de notificação. As Sobreposições de notificação são perfeitas para a integração do fast porque não necessitam que modificar qualquer esquema na sua aplicação.

Para modificar o aspeto do seu sobreposições de notificação, pode simplesmente modificar o ficheiro `engagement_notification_area.xml` às suas necessidades.

> [AZURE.NOTE] O ficheiro `engagement_notification_overlay.xml` é aquele que é utilizado para criar uma sobreposição de notificação, inclui o ficheiro `engagement_notification_area.xml`. Também pode personalizá-lo conforme as suas necessidades (tais como o posicionamento de área de notificação dentro de sobreposição de).

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Incluir o esquema de notificação como parte de um esquema de atividade

As Sobreposições de são perfeitas para uma rápida integração, mas podem ser uma tarefa morosa ou ter efeitos colaterais em casos especiais. O sistema de sobreposição pode ser personalizado a um nível de atividade, tornando mais fácil impedir que os efeitos de lado para atividades especiais.

Pode decidir incluir os nossos esquema notificação no seu esquema existente graças à declaração de Android **incluir** . Segue-se um exemplo de um modificados `ListActivity` esquema que contém apenas uma `ListView`.

**Antes da integração de Cativação:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Depois da integração de Cativação:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

Neste exemplo adicionámos um contentor principal de uma vez ao esquema original utilizado uma vista de lista como o elemento de nível superior. Também adicionámos `android:layout_weight="1"` possam adicionar uma vista abaixo configurada com uma vista de lista `android:layout_height="fill_parent"`.

O SDK do atinja Cativação Deteta automaticamente que o esquema da notificação está incluído nesta actividade e não irá acrescentar uma sobreposição para esta atividade.

> [AZURE.TIP] Se utilizar um ListActivity na sua aplicação, uma sobreposição alcance visível irá impedir a reagindo para clicado deixem itens na vista de lista. Este é um problema conhecido. Para contornar este problema sugerimos que para incorporar o esquema de notificação no seu próprio esquema de atividade de lista como no exemplo anterior.

##### <a name="disabling-application-notification-per-activity"></a>Desativar a notificação de aplicação por actividade

Se não quiser que a sobreposição para ser adicionada à sua atividade, e se não inclui o esquema de notificação no seu próprio esquema, pode desativar a sobreposição para esta atividade na `AndroidManifest.xml` adicionando um `meta-data` secção como no exemplo seguinte:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Categorias

Quando modificar os esquemas fornecidos, é modificar o aspeto de todas as notificações. Categorias permitem-lhe definir várias alvo o seguinte aspeto (possivelmente comportamentos) de notificações. Pode ser especificada uma categoria quando criar uma campanha de atingir. Tenha em atenção que categorias também permitem-lhe personalizar anúncios e consultas, que é descrita mais tarde neste documento.

Para registar um processador de categoria para as suas notificações, é necessário adicionar uma chamada quando a aplicação é inicializada.

> [AZURE.IMPORTANT] Leia o aviso sobre o atributo android: processo \<android-sdk-Cativação-process\> de como integrar Cativação no tópico Android antes de continuar.

O exemplo seguinte assume que confirmado o aviso anterior e utilizar uma sub-classe de `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

O `MyNotifier` objeto é a implementação da alça de categoria de notificação. É um de uma implementação da `EngagementNotifier` interface ou de uma classe de sub da execução predefinido: `EngagementDefaultNotifier`.

Tenha em atenção que o mesmo notificador pode lidar com várias categorias, pode registá-los da seguinte forma:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Para substituir a implementação de categoria predefinida, pode registar a sua implementação como no exemplo seguinte:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

Na categoria atual utilizada num processador de lhe é transmitida como um parâmetro na maioria dos métodos que pode substituem as respetivas `EngagementDefaultNotifier`.

Esta é passou quer como um `String` parâmetro ou indiretamente num `EngagementReachContent` objeto que tem um `getCategory()` método.

Pode alterar a maior parte do processo de criação da notificação ao redefinir métodos no `EngagementDefaultNotifier`, para a personalização mais avançada à vontade para explorá na documentação técnica e o código de origem.

##### <a name="in-app-notifications"></a>Notificações de na aplicação

Se pretende apenas utilizar esquemas alternativas para uma categoria específica, pode implementar isto tal como no exemplo seguinte:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exemplo de `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Como pode ver, o identificador de vista de sobreposição é diferente do padrão. É importante que cada esquema utilize um identificador exclusivo para sobreposições.

**Exemplo de `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Como pode ver, o identificador de vista de área de notificação é diferente do padrão. É importante que cada esquema utiliza um identificador exclusivo para áreas de notificação.

Este exemplo simple de categoria faz com que as notificações de aplicação (ou na aplicação) apresentadas na parte superior do ecrã. Vamos não alterou os identificadores de padrão utilizados na área de notificação propriamente dita.

Se pretender alterar, tem de redefinir a `EngagementDefaultNotifier.prepareInAppArea` método. É recomendado para procurar na documentação técnica e no código fonte de `EngagementNotifier` e `EngagementDefaultNotifier` se quiser que este nível de personalização avançada.

##### <a name="system-notifications"></a>Notificações de sistema

Através do prolongamento `EngagementDefaultNotifier`, pode substituir `onNotificationPrepared` para alterar a notificação de que tenha sido preparada pela implementação predefinida.

Por exemplo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Este exemplo faz com que uma notificação de sistema para uma conteúdo a ser apresentada como um evento em curso quando é utilizada na categoria "em curso".

Se pretender criar a `Notification` objeto de raiz, poderá regressar `false` para o método e chamada `notify` -se na `NotificationManager`. Nesse caso é importante que mantém um `contentIntent`, um `deleteIntent` e o identificador de notificação utilizado pelo `EngagementReachReceiver`.

Eis um exemplo correto de uma execução:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Anúncios apenas de notificação

A gestão dos clicar numa notificação de anúncio só pode ser personalizado substituindo `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` para modificar preparada `Intent`. Utilizar este método permite-lhe sintonizar facilmente os sinalizadores.

Por exemplo adicionar o `SINGLE_TOP` sinalizador:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Para utilizadores de Cativação legados, tenha em atenção que as notificações do sistema sem ação URL agora inicia a aplicação se tiver sido em segundo plano, para que este método pode ser chamado com um anúncio sem URL de ação. Pode deverá tomar em consideração que quando personaliza a intenção.

Também pode implementar `EngagementNotifier.executeNotifAnnouncementAction` de raiz.

##### <a name="notification-life-cycle"></a>Ciclo de vida de notificação

Ao utilizar a categoria predefinida, alguns métodos de ciclo de vida são designado pelo `EngagementReachInteractiveContent` objeto para estatísticas do relatório e actualizar o estado de campanha:

-   Quando a notificação é apresentada na aplicação ou colocar na barra de estado, a `displayNotification` método denomina (que relatórios estatísticas) por `EngagementReachAgent` se `handleNotification` devolve `true`.
-   Se a notificação é fechada, a `exitNotification` método é chamado, é comunicada estatística e campanhas seguintes agora podem ser processadas.
-   Se a notificação é clicada, `actionNotification` é chamado estatística for comunicada e a intenção associada é iniciada.

Se a implementação do `EngagementNotifier` ignora o comportamento predefinido, tem de chamar estes métodos de ciclo de vida por si. Os exemplos seguintes mostram alguns casos onde o comportamento predefinido é ignorado:

-   Não a expandir `EngagementDefaultNotifier`, por exemplo, o processamento de categoria de raiz implementada.
-   Para as notificações de sistema, overrode a `onNotificationPrepared` e modificada `contentIntent` ou `deleteIntent` na `Notification` objeto.
-   Para as notificações de na aplicação, overrode `prepareInAppArea`, certifique-se de que pelo menos mapear `actionNotification` para um dos seus U.I controlos.

> [AZURE.NOTE] Se `handleNotification` inicia uma exceção, o conteúdo é eliminada e `dropContent` chama-se. Isto é comunicado nas estatísticas e agora podem ser processadas campanhas seguintes.

### <a name="announcements-and-polls"></a>Anúncios e consultas

#### <a name="layouts"></a>Esquemas

Pode modificar a `engagement_text_announcement.xml`, `engagement_web_announcement.xml` e `engagement_poll.xml` ficheiros para personalizar os anúncios de texto, anúncios de web e consultas.

Estes ficheiros partilham dois esquemas comuns para a área de título e a área do botão. O esquema para o título é `engagement_content_title.xml` e utiliza o ficheiro de drawable eponymous para o fundo. O esquema para os botões de ação e de saída é `engagement_button_bar.xml` e utiliza o ficheiro de drawable eponymous para o fundo.

Num inquérito, o esquema de pergunta e as suas escolhas são dinamicamente cheios utilizar várias vezes a `engagement_question.xml` ficheiro de esquema para as perguntas e a `engagement_choice.xml` ficheiro para as escolhas.

#### <a name="categories"></a>Categorias

##### <a name="alternate-layouts"></a>Esquemas alternativas

Como as notificações, categoria da campanha pode ser utilizada para têm esquemas alternativos para o seu anúncios e inquéritos.

Por exemplo, para criar uma categoria para um anúncio de nascimento de texto, pode expandir `EngagementTextAnnouncementActivity` e de referência-lo a `AndroidManifest.xml` ficheiro:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Tenha em atenção que a categoria na intenção de filtro é utilizada para tornar a diferença com a atividade de anúncio predefinido.

O SDK atinja utiliza o sistema intenção para resolver a atividade à direita para uma categoria específica e reverte na categoria predefinição se a resolução falhou.

Em seguida, tem de implementar `MyCustomTextAnnouncementActivity`, se apenas quiser alterar o esquema (mas manter os identificadores de vista mesmo), que só tem de definir a classe como no exemplo seguinte:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Para substituir a categoria de predefinido de anúncios de texto, basta substituí `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` pela sua implementação.

Anúncios de Web e consultas podem ser personalizadas, de forma semelhante.

Para anúncios de web é possível expandir `EngagementWebAnnouncementActivity` e declarar a sua atividade na `AndroidManifest.xml` como no exemplo seguinte:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Para inquéritos pode expandir `EngagementPollActivity` e declarar seu na `AndroidManifest.xml` como no exemplo seguinte:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementação de raiz

Pode implementar categorias para as suas atividades anúncio (e de inquérito) sem expandir um da `Engagement*Activity` classes fornecidas pelo SDK atinja. Isto é útil por exemplo se pretende definir um esquema que não utilize as vistas do mesmo como os esquemas de padrão.

Como para a personalização de notificação avançadas, recomenda-se para ver o código fonte de implementação padrão.

Eis alguns aspetos a ter em conta: alcance iniciará a atividade com uma intenção específica (correspondente para o filtro intenção) plus um parâmetro adicional que é o identificador de conteúdo.

Para obter o objeto de conteúdo que contêm os campos que especificou quando criação da campanha no web site pode fazê-lo:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Estatísticas, deverá comunicar o conteúdo é apresentado na `onResume` evento:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Em seguida, não se esqueça ligar a um `actionContent(this)` ou `exitContent(this)` no objeto conteúdo antes da atividade entra em segundo plano.

Se não chamar quer `actionContent` ou `exitContent`, estatísticas não serão enviadas (ou seja, não analytics da campanha) e ainda mais importante, campanhas de seguintes não serão notificadas até que seja reiniciado o processo de aplicação.

Orientação ou outras alterações de configuração podem fazer com o código de difícil determinar se a atividade compõem fundo ou não, a aplicação padrão assegura que o conteúdo é comunicado como saído se o utilizador sai da atividade (quer premindo `HOME` ou `BACK`), mas não se altera a orientação.

Eis a peça da aplicação interessante:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Como pode ver, se lhe chamado `actionContent(this)` , em seguida, terminar a atividade `exitContent(this)` pode ser chamado com segurança sem ter qualquer efeito.

[Aqui]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud mensagens]:http://developer.android.com/guide/google/gcm/index.html
[Mensagens de dispositivo Amazon]:https://developer.amazon.com/sdk/adm.html
