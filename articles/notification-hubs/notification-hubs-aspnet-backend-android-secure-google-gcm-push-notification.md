<properties
    pageTitle="Enviar notificações Push seguro com concentradores notificação Azure"
    description="Saiba como enviar notificações push seguro para uma aplicação do Android a partir do Azure. Exemplos de código escritos Java e c#."
    documentationCenter="android"
    keywords="notificações push notificação, as notificações push, mensagens, as notificações android push de emissão"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

#<a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Enviar notificações Push seguro com concentradores notificação Azure

> [AZURE.SELECTOR]
- [Universal do Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

##<a name="overview"></a>Descrição geral

> [AZURE.IMPORTANT] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Suporte de notificações push no Microsoft Azure permite-lhe aceder a uma infraestrutura de mensagem push fáceis de utilizar múltiplas plataformas, dimensionada-out, que simplifica significativamente a implementação de notificações push para aplicações do consumidor e enterprise para as plataformas de dispositivos móveis.

Devido ao regulamentação ou restrições de segurança, por vezes, uma aplicação poderá pretender incluir algo na notificação que não pode ser transmitida através de infraestrutura de notificação do push padrão. Neste tutorial descreve como alcançar a mesma experiência ao enviar informações confidenciais através de uma ligação segura, autenticada entre o dispositivo Android cliente e o back-end de aplicação.

O fluxo de alto nível, é da seguinte forma:

1. Aplicação back-end:
    - Armazena seguro carga útil numa base de dados back-end.
    - Envia o ID desta notificação para o dispositivo Android (não seguras são enviadas informações).
2. A aplicação no dispositivo, quando receber a notificação:
    - O dispositivo Android contacta back-end pedir a carga de útil segura.
    - A aplicação pode mostrar a carga de útil como uma notificação no dispositivo.

É importante ter em atenção que no fluxo de anterior (e neste tutorial), podemos partem do princípio de que o dispositivo armazena token de autenticação em armazenamento local, depois do utilizador inicia sessão. Isto garante uma experiência totalmente totalmente integrada, tal como o dispositivo pode obter seguro carga útil a notificação utilizando este token. Se a sua aplicação não armazenar tokens de autenticação no dispositivo ou se estes tokens podem ser expirados, a aplicação de dispositivo, após receber a notificação de push deve mostrar uma notificação genérica perguntar ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra a carga útil notificação.

Este tutorial mostra como enviar notificações push seguro. Constrói no tutorial [Notificar utilizadores](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , para que devem concluir os passos que tutorial pela primeira vez se ainda não o fez.

> [AZURE.NOTE] Neste tutorial assume que criou e configurado o seu centro de notificação, tal como descrito em [Introdução ao notificação concentradores (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Modificar o projecto Android

Agora que modificado sua aplicação de segurança para enviar apenas o *id* de uma notificação de emissão, tem de alterar a sua aplicação Android para processar essa notificação e uma chamada de retorno seu back-end para obter a mensagem segura para ser apresentado.
Para realizar este objetivo, tem de Certifique-se de que a sua aplicação Android sabe como para se autenticar com back-end quando recebe as notificações push.

Vamos agora irá modificar o fluxo de *início de sessão* para guardar o valor de cabeçalho de autenticação nas preferências da sua aplicação partilhadas. Mecanismos análogos podem ser utilizados para armazenar qualquer token de autenticação (por exemplo, OAuth tokens) com a aplicação irá utilizar sem necessidade de credenciais de utilizador.

1. No seu projeto de aplicação para Android, adicione as seguintes constantes na parte superior da classe **MainActivity** :

        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Ainda na classe **MainActivity** , atualize o `getAuthorizationHeader()` método para incluir o seguinte código:

        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

            return basicAuthHeader;
        }

3. Adicione o seguinte `import` declarações na parte superior do ficheiro **MainActivity** :

        import android.content.SharedPreferences;

Agora iremos alterar o processador denominado quando for recebida a notificação.

4. Na **MyHandler** classe alterar o `OnReceive()` método para conter:

        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }

5. Em seguida, adicione o `retrieveNotification()` método, substituindo o marcador de posição `{back-end endpoint}` com o ponto final de back-end obtido ao implementar back-end:

        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }


Este método liga para a aplicação de segurança para obter o conteúdo de notificação utilizando as credenciais armazenadas nas preferências de partilhada e apresenta-o como uma notificação normal. A notificação de aspeto para o utilizador aplicação exatamente como qualquer outra notificação push.

Tenha em atenção que é preferível para processar dos casos de propriedade de cabeçalho de autenticação em falta ou rejeição ao back-end. O processamento específico de nestes casos dependem principalmente em sua experiência de utilizador de destino. É uma opção apresentar uma notificação com um pedido genérico para o utilizador autenticar para obter a notificação real.

## <a name="run-the-application"></a>Executar a aplicação

Para executar a aplicação, faça o seguinte:

1. Certifique-se de **que appbackend** é implementada no Azure. Se utilizar o Visual Studio, execute a aplicação de Web API **AppBackend** . É apresentada uma página web do ASP.NET.

2. No Eclipse, execute a aplicação de um dispositivo Android físico ou o emulador.

3. Na aplicação Android IU, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia, mas tem de ser o mesmo valor.

4. Na aplicação Android IU, clique em **Iniciar sessão**. Em seguida, clique em **Enviar push**.
