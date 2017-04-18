<properties 
    pageTitle="Una iOS vista Web com nativo Mobile Cativação iOS SDK" 
    description="Descreve como criar uma bridge entre a vista da Web que executem Javascript e o iOS Mobile Cativação nativo SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-ios" 
    ms.devlang="objective-c" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Una iOS vista Web com nativo Mobile Cativação iOS SDK

> [AZURE.SELECTOR]
- [Android Bridge](mobile-engagement-bridge-webview-native-android.md)
- [iOS Bridge](mobile-engagement-bridge-webview-native-ios.md)

Algumas aplicações móveis destinam-se como uma aplicação híbrida onde a própria aplicação está desenvolvida com iOS nativo objectivo-C desenvolvimento, mas algumas ou todas as filtragens de mesmo são processadas dentro de uma vista da Web iOS. Ainda pode consumir Mobile Cativação iOS SDK essas aplicações e este tutorial descreve como ir sobre como o fazer. 

Existem duas abordagens para alcançar isto apesar de ambas são não documentadas:

- Em primeiro lugar um é descrito nesta [ligação](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) que envolve registar um `UIWebViewDelegate` no seu vista da web e capturas-e-imediatamente-cancelar uma alteração de localização concluída no Javascript. 
- Segundo um é baseado nesta [WWDC 2013 sessão](https://developer.apple.com/videos/play/wwdc2013/615), uma abordagem de limpeza que o primeiro e que recomendamos vai ter de seguir para este guia. Tenha em atenção que esta abordagem apenas funciona no iOS7 e acima. 

Siga os passos abaixo para os iOS una exemplo:

1. Em primeiro lugar, tem de se certificar de que foram trocadas através do nosso [tutorial de introdução](mobile-engagement-ios-get-started.md) para integrar o iOS Mobile Cativação SDK na sua aplicação híbrida. Opcionalmente, também pode ativar teste registo da seguinte forma para que possam ver os métodos SDK como podemos acioná-los de uma vista a Web. 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. Agora, certifique-se de que a sua aplicação híbrida tem um ecrã com uma vista da Web no mesmo. Pode adicionar-o para a `Main.storyboard` da aplicação. 

3. Associar esta vista Web ao seu **ViewController** clicando e arrastando a vista da Web a partir da cena de controlador de vista para o `ViewController.h` editar ecrã, colocando-o imediatamente abaixo de `@interface` linha. 

4. Depois de fazer isto, será apresentada uma caixa de diálogo solicitar um nome. Fornece o nome como **vista da Web**. O `ViewController.h` ficheiro deverá ter um aspeto semelhante ao seguinte:

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. Vamos irão atualizar o `ViewController.m` ficheiros mais tarde, mas primeiro podemos irá criar o ficheiro de bridge que cria um dispositivo de moldagem através de alguns iOS Mobile Cativação utilizadas mais frequentemente métodos SDK. Criar um novo ficheiro de cabeçalho denominado **EngagementJsExports.h** que utiliza o `JSExport` mecanismo descrito na acima referidos [sessão](https://developer.apple.com/videos/play/wwdc2013/615) para expor os métodos de iOS nativa. 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. Em seguida, vamos criar a segunda parte do ficheiro bridge. Crie um ficheiro denominado **EngagementJsExports.m** irá conter a implementação de criar os wrappers reais ao contactar o suporte os métodos SDK Mobile Cativação iOS. Tenha também em atenção que recomendamos são de análise de `extras` a ser transmitida de javascript a vista da Web e colocar em que para um `NSMutableDictionary` objeto a transmitir com as chamadas de método de Cativação SDK.  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. Agora que regresse à **ViewController.m** e atualizá-lo com o seguinte código: 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. Tenha em atenção os seguintes pontos sobre o ficheiro **ViewController.m** :

    - No `loadWebView` método, podemos estão a ser carregados um ficheiro HTML local denominado **LocalPage.html** cujo código iremos rever seguinte. 
    - No `webViewDidFinishLoad` método, podemos são capturando o `JsContext` e associação a nossa classe bombom com o mesmo. Isto irá permitir chamar nosso bombom métodos SDK utilizando a alça de **EngagementJs** de uma vista a Web. 

7. Crie um ficheiro denominado **LocalPage.html** com o seguinte código:

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
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. Tenha em atenção os seguintes pontos sobre o ficheiro HTML acima:

    -   Contém um conjunto de caixas de entrada, onde pode fornecer dados a ser utilizados como nomes para o seu evento, tarefa, erro, AppInfo. Quando clica no botão junto ao mesmo, uma chamada é efetuada para o Javascript que acaba por ser chama os métodos do ficheiro bridge para passar esta chamada para o iOS Mobile Cativação SDK. 
    -   Vamos são etiquetagem no algumas informações extra estática ao eventos, tarefas e até mesmo erros para demonstrar como isto poderá ser feito. Este extra informações são enviada conforme um JSON de cadeia que, se procurar no `EngagementJsExports.m` de ficheiros, é analisado e transmitido juntamente com erros de eventos, tarefas, a enviar. 
    -   Uma tarefa de Cativação Mobile é pontapés com o nome que especificou na caixa de entrada, executar para 10 segundos e encerrar. 
    -   Um telemóvel Cativação appinfo ou etiqueta é transmitida com customer_name como a chave estática e o valor que introduziu na entrada de dados como o valor para a etiqueta. 
 
9. Executar a aplicação e verá o seguinte. Agora, fornecer algumas nome de um evento de teste como o seguinte e clique em **Enviar** junto ao mesmo. 

    ![][1]

10. Agora se aceder ao separador **Monitor** da sua aplicação e aspeto em **eventos -> detalhes**, verá este evento aparecem juntamente com a aplicação-info estática está a enviar. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
