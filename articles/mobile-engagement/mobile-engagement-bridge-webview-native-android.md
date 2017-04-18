<properties 
    pageTitle="Bridge vista da Web Android com nativo Mobile Cativação SDK Android" 
    description="Descreve como criar uma bridge entre a vista da Web que executem Javascript e nativo Mobile Cativação Android SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Bridge vista da Web Android com nativo Mobile Cativação SDK Android

> [AZURE.SELECTOR]
- [Android Bridge](mobile-engagement-bridge-webview-native-android.md)
- [iOS Bridge](mobile-engagement-bridge-webview-native-ios.md)

Algumas aplicações móveis destinam-se como uma aplicação híbrida onde a própria aplicação é desenvolvida utilizando nativo desenvolvimento Android, mas algumas ou todas as filtragens de mesmo são processadas dentro de uma vista da Web Android. Ainda pode consumir Mobile Cativação Android SDK essas aplicações e este tutorial descreve como ir sobre como o fazer. O código de exemplo abaixo baseia-se a documentação Android [aqui](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Descreve como esta abordagem documentada pode ser utilizada para implementar o mesmo para métodos utilizados mais frequentemente do Mobile Cativação Android SDK assim que uma vista da Web a partir de uma aplicação híbrida também pode iniciar pedidos para controlar eventos, tarefas, erros, informações de app enquanto tubagens-los através do nosso SDK Android. 

1. Em primeiro lugar, tem de se certificar de que foram trocadas através do nosso [tutorial de introdução](mobile-engagement-android-get-started.md) para integrar o Cativação de telemóvel Android SDK na sua aplicação híbrida. Assim que fazê-lo, o `OnCreate` método será o aspeto semelhante ao seguinte.  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. Agora, certifique-se de que a sua aplicação híbrida tem um ecrã com uma vista da Web no mesmo. O código para a mesma será semelhante para os seguintes onde podemos estão a ser carregados um local HTML ficheiro **Sample.html** na vista a Web na `onCreate` método do seu ecrã. 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. Agora crie um ficheiro de bridge chamado **WebAppInterface** cria um dispositivo de moldagem através de alguns métodos Mobile Cativação Android SDK utilizados mais frequentemente, utilizando o `@JavascriptInterface` abordagem descrita na [documentação Android](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. Assim que criámos ficheiro bridge acima, é necessário para se certificar de que está associado a nossa vista da Web. Para isto acontecer, tem de editar o `SetWebview` método para que a mesma tenha um aspeto semelhante ao seguinte:

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. No fragmento de acima, denominado `addJavascriptInterface` para associar a nossa classe bridge nossa vista da Web e também criada uma alça de denominado **EngagementJs** para ligar os métodos do ficheiro bridge. 

6. Agora crie o seguinte ficheiro denominado **Sample.html** no seu projeto numa pasta denominada **elementos** que é carregado para a vista da Web e onde serão chamamos os métodos do ficheiro bridge.

        <!doctype html>
        <html>
            <head>
                <style type='text/css'>
                    html { font-family:Helvetica; color:#222; }
                    h1 { color:steelblue; font-size:22px; margin-top:16px; }
                    h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
                </style>
        
                <script type="text/javascript">
        
                    window.onerror = function(err)
                    {
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. Tenha em atenção os seguintes pontos sobre o ficheiro HTML acima:

    -   Contém um conjunto de caixas de entrada, onde pode fornecer dados a ser utilizados como nomes para o seu evento, tarefa, erro, AppInfo. Quando clica no botão junto ao mesmo, uma chamada é efetuada para o Javascript que acaba por ser chama os métodos do ficheiro bridge para passar esta chamada o SDK do Android Cativação Mobile. 
    -   Vamos são etiquetagem no algumas informações extra estática ao eventos, tarefas e até mesmo erros para demonstrar como isto poderá ser feito. Este extra informações são enviada conforme um JSON de cadeia que, se procurar no `WebAppInterface` de ficheiros, é analisado e colocar num Android `Bundle` e lhe é transmitido juntamente com erros de eventos, tarefas, a enviar. 
    -   Uma tarefa de Cativação Mobile é pontapés com o nome que especificou na caixa de entrada, executar para 10 segundos e encerrar. 
    -   Um telemóvel Cativação appinfo ou etiqueta é transmitida com customer_name como a chave estática e o valor que introduziu na entrada de dados como o valor para a etiqueta. 
 
9. Executar a aplicação e verá o seguinte. Agora, fornecer algumas nome de um evento de teste como o seguinte e clique em **Enviar** abaixo do mesmo. 

    ![][1]

10. Agora se aceder ao separador **Monitor** da sua aplicação e aspeto em **eventos -> detalhes**, verá este evento aparecem juntamente com a aplicação-info estática está a enviar. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
