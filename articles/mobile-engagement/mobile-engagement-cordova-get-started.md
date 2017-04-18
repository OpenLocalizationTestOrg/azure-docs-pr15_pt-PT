<properties
    pageTitle="Introdução ao Azure Cativação móvel para Cordova/Phonegap"
    description="Saiba como utilizar Azure Mobile Cativação com as notificações de emissão e de análise Cordova/Phonegap aplicações para."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introdução ao Azure Cativação móvel para Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e enviar as notificações push para os utilizadores segmentados para uma aplicação móvel desenvolvido com Cordova.

Neste tutorial, iremos criar uma aplicação Cordova em branco utilizando Mac e, em seguida, integrar Mobile Cativação SDK. -Recolhe dados analytics básicas e recebe as notificações push utilizar o sistema de notificações Push (APNS) da Apple para iOS e Google Cloud mensagens (GCM) para Android. Vamos implementar isto a um iOS ou dispositivo Android para testar a ligação. 

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).

Neste tutorial requer o seguinte:

+ XCode, que pode instalar a partir da Mac App Store (para implementar o iOS)
+ [Android SDK & emulador](http://developer.android.com/sdk/installing/index.html) (para implementar Android)
+ Certificado de notificações push (. p12) que pode obter a partir da Apple Dev Center para APNS
+ Número de projeto GCM que pode obter a partir do seu consola de programador do Google para GCM
+ [Plug-in Cordova de Cativação móvel](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] Pode localizar o código de origem e o ficheiro Leia-me para o plug-in de Cordova no [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Configuração móvel Cativação para a sua aplicação Cordova

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Ligar a aplicação Mobile Cativação back-end

Neste tutorial apresenta uma "integração básica" qual é o mínimas necessárias para recolher dados e enviar uma notificação de emissão do conjunto. 

Vamos irá criar uma aplicação básica com Cordova para demonstrar a integração:

###<a name="create-a-new-cordova-project"></a>Criar um novo projeto de Cordova

1. Inicie o *Terminal* janela no seu computador Mac e escreva o seguinte que irá criar um novo projeto de Cordova a partir do modelo predefinido. Certifique-se de que o perfil publicação que acaba por ser utilizado para implementar a sua aplicação iOS está a utilizar o 'com.mycompany.myapp' como o ID da aplicação. 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Execute o seguinte procedimento para configurar o seu projeto para **iOS** e executá-la no iOS Simulator:

        $ cordova platform add ios 
        $ cordova run ios

3. Execute o seguinte procedimento para configurar o seu projeto para **Android** e executá-la no emulador Android. Certifique-se de que as definições de Android SDK emulador têm o respetivo destino como Google APIs (Google Inc.) com a CPU / ABI como Google APIs processador.  

        $ cordova platform add android
        $ cordova run android

4.  Adicione o plug-in Consola de Cordova. 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a sua aplicação ao back-end de Cativação Mobile

1. Instale o plug-in do Azure Mobile Cativação Cordova enquanto fornecer nos valores variáveis para configurar o plug-in:

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Ícone de atingir Android* : tem de ser o nome do recurso sem qualquer extensão, nem o prefixo drawable (ex: mynotificationicon), e o ficheiro do ícone tem de ser copiado para o projeto android (plataformas/android/resoluções/drawable)

*iOS ícone atinja* : tem de ser o nome do recurso com respectiva extensão (ex: mynotificationicon.png), e o ficheiro do ícone tem de ser adicionado para o seu projeto iOS com XCode (utilizando o Menu para adicionar ficheiros)

##<a id="monitor"></a>Activar a monitorização em tempo real

1. No Cordova project - edite **www/js/index.js** para adicionar a chamada para o Mobile Cativação para declarar uma nova actividade de uma vez, o evento *deviceReady* for recebida.

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Execute a aplicação:
        
    - **Para iOS**
    
        No `Terminal` janela iniciação a sua aplicação numa nova instância Simulator executando o seguinte:

            cordova run ios

    - **Para Android**
        
        No `Terminal` janela iniciação a sua aplicação numa nova instância emulador executando o seguinte:

            cordova run android

3. Pode ver a seguinte nos registos do consola:

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações de emissão e mensagens de na aplicação

Cativação Mobile permite-lhe interagir com os seus utilizadores utilizando as notificações de emissão e mensagens no contexto das campanhas de na aplicação. Este módulo chama alcance no portal do Cativação Mobile.
As secções seguintes irão programa de configuração a aplicação recebê-las.

###<a name="configure-push-credentials-for-mobile-engagement"></a>Configurar as credenciais de Push para Mobile Cativação

Para permitir que o Mobile Cativação enviar notificações de emissão em seu nome, tem de conceder-lhe acesso ao seu certificado do Apple iOS ou GCM chave de API do servidor. 
    
1. Navegue até ao seu portal de Cativação Mobile. Certifique-se de que estiver na aplicação que está a utilizar para este projeto e, em seguida, clique no botão **captar** na parte inferior:
    
    ![][1]
    
2. Será direcionado na página de definições no seu Portal de Cativação. A partir do aí clique na secção de **Emissão nativo** :
    
    ![][2]

3. Configurar o iOS chave do certificado/GCM servidor API

    **[iOS]**

    um. Selecione o seu. p12, carregue-o e escreva a palavra-passe:
    
    ![][3]

    **[Android]**

    um. Clique no ícone de edição à frente **API chave** na secção definições GCM e no pop-up que é apresentado, cole a chave do servidor GCM e clique em **OK**. 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>Ativar as notificações push na aplicação Cordova

Edite **www/js/index.js** para adicionar a chamada para a atribuição de telemóvel para notificações push do pedido e declarar um processador de:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>Executar a aplicação

**[iOS]**

1. Irá utilizamos XCode criem e implementem a aplicação no dispositivo para testar as notificações push, uma vez que iOS permite apenas as notificações push para um dispositivo real. Vá para a localização onde o seu projeto Cordova é criado e navegue até à localização **...\platforms\ios** . Abrir o ficheiro .xcodeproj nativo no XCode. 
    
2. Criar e implementar a aplicação Cordova ao dispositivo iOS utilizando a conta que tem de aprovisionamento perfil que contém o certificado que acabou carregado para o portal de Cativação Mobile e o Id da aplicação que corresponde a fornecida ao criar a aplicação Cordova. Pode verificar o *identificador de pacote* no seu **recursos\*-info.plist** XCode para fazê-lo corresponder ficheiro. 

3. Irá ver o menu de contexto do iOS padrão no seu dispositivo a informar que a aplicação os pedidos de permissão para enviar notificações. Conceda a permissão. 

**[Android]**

Pode utilizar simplesmente o emulador para executar a aplicação Android, como notificações GCM são suportadas no emulador Android. 

    cordova run android

##<a id="send"></a>Enviar uma notificação para a sua aplicação

Vamos agora irá criar uma campanha de notificações Push simple que irá enviar um push para a sua aplicação em execução no dispositivo:

1. Navegue para o separador **atinja** no seu portal de Cativação Mobile

2. Clique em **Novo anúncio** para criar a sua campanha push

    ![][6]

3. Fornecer entradas para criar a sua campanha **[Android]**
    
    - Forneça um **nome** para a campanha. 
    - Selecione o **Tipo de entrega** como *notificação do sistema* *simples*
    - Selecione a **hora de entrega** como *"qualquer hora"*
    - Fornece um **título** para sua notificação de que vai ser a primeira linha a push.
    - Fornece uma **mensagem** para a sua notificação de que irá servir como o corpo da mensagem. 

    ![][11]

4. Fornecer entradas para criar o seu campanha **[iOS]**

    - Forneça um **nome** para a campanha. 
    - Selecione a **hora de entrega** como *"fora do aplicação"*
    - Fornece um **título** para sua notificação de que vai ser a primeira linha a push.
    - Fornece uma **mensagem** para a sua notificação de que irá servir como o corpo da mensagem. 
 
    ![][12]

5. Desloque-se para baixo e, na secção conteúdo selecione **notificação apenas**

    ![][8]

6. [Opcional] Também pode fornecer um URL de ação. Certifique-se de que utiliza um esquema de URL fornecido ao configurar o plug-in **AZME\_REDIRECIONAR\_URL** variável, por exemplo, *myapp://test*.  

7. Terminar a definição da campanha mais básica possíveis. Agora, desloque para baixo novamente e clique no botão **Criar** para guardar a sua campanha.

8. Por fim **Ativar** campanha
    
    ![][10]

9. Agora deverá ver uma notificação de emissão no seu dispositivo ou emulador como parte desta campanha. 

##<a id="next-steps"></a>Próximos passos
[Descrição geral de todos os métodos disponíveis com Cordova Mobile Cativação SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

