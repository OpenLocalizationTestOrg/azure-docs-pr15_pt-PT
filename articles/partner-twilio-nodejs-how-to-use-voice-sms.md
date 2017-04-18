<properties 
    pageTitle="Utilizar o Twilio para voz, VoIP e mensagens SMS no Azure" 
    description="Saiba como efetuar uma chamada e enviar uma mensagem SMS com o serviço de Twilio API no Azure. Exemplos de código escritos Node.js." 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Utilizar o Twilio para voz, VoIP e mensagens SMS no Azure

Este guia demonstra como construir as aplicações que comunicam com Twilio e node.js no Azure.

<a id="whatis"/>
## <a name="what-is-twilio"></a>O que é Twilio?

Twilio é uma plataforma de API que torna mais fácil para os programadores fazer e receber chamadas telefónicas, enviar e receber mensagens de texto e incorporar chamadas VoIP para baseada no browser e nativas aplicações móveis.  Vamos brevemente abordar como isto funciona antes de consultar.

### <a name="receiving-calls-and-text-messages"></a>Receber chamadas e mensagens de texto

Twilio permite que os programadores para [comprar números de telefone programável] [ purchase_phone] que podem ser utilizado para enviar e receber chamadas e mensagens de texto.  Quando um número de Twilio recebe uma chamada de entrada ou texto, Twilio vai enviar a aplicação web uma mensagem de HTTP ou pedido GET, perguntar para obter instruções sobre como gerir a chamada ou texto.  O servidor irá responder ao pedido HTTP do Twilio com [TwiML][twiml], um conjunto de etiquetas XML que contêm instruções sobre como gerir uma chamada ou texto simple.  Podemos irá ver exemplos de TwiML no apenas um pouco a incorporá.

### <a name="making-calls-and-sending-text-messages"></a>Efetuar chamadas e enviar mensagens de texto

Ao tornar pedidos de HTTP para a API do serviço web Twilio, os programadores podem enviar mensagens de texto ou iniciar chamadas telefónicas saídas.  Para chamadas de saída, o programador também tem de especificar um URL que devolve TwiML sobre como gerir a chamada de saída quando for estabelecida.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Incorporação VoIP capacidades no código da IU (JavaScript, iOS ou Android)

Twilio fornece um SDK do lado do cliente que pode transformar qualquer browser web ambiente de trabalho, aplicação para iOS ou Android aplicação um telefone VoIP.  Neste artigo, iremos destacar sobre como utilizar VoIP chamar no browser.  Para além do SDK do JavaScript Twilio em execução no browser, uma aplicação do lado do servidor (nossa aplicação node.js) tem de ser utilizada para emitir um "token de capacidade" para o cliente de JavaScript.  Pode ler mais sobre como utilizar VoIP com node.js [no blogue Dev Center do Twilio][voipnode].

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>Inscrição para Twilio (Microsoft desconto)

Antes de utilizar os serviços de Twilio, tem primeiro [Inscrever-se para uma conta][signup].  Os clientes do Microsoft Azure recebem um desconto especial - [não se esqueça de se inscrever aqui][signup]!

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>Criar e implementar um site de Azure node.js

Em seguida, terá de criar um Web site node.js em execução no Azure.  [Documentação oficial para este procedimento se encontra aqui][azure_new_site].  De alto nível, que irão ser fazer o seguinte procedimento:

* Inscrever-se para uma conta Azure, se ainda não tiver um
* Utilizar consola do administrador Azure para criar um novo site
* Adicionar o suporte de controlo de origem (Recomendamos irá assumir que utilizou git)
* Criar um ficheiro `server.js` com uma aplicação web do node.js simples
* Implementar esta aplicação simples para Azure

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>Configurar o módulo Twilio

Em seguida, podemos vai começar a escrever uma aplicação de node.js simples que utiliza a API Twilio.  Antes de Vamos começar, precisamos de configurar o nossas Twilio as credenciais da conta.  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Configurar credenciais Twilio numa variáveis de sistema

Para poder efetuar pedidos autenticados contra Twilio back-end, precisamos de nossos SID da conta e auth token, qual é a função como o nome de utilizador e palavra-passe definida para a nossa conta Twilio. A forma mais segura de configurar estas para utilização com o módulo nó no Azure é através de variáveis de ambiente de sistema, pode definir diretamente na consola do administrador Azure.

Selecione o seu site node.js e clique na ligação "Configurar".  Se deslocar para baixo um pouco, irá ver uma área onde pode definir propriedades de configuração para a sua aplicação.  Introduza as suas credenciais de conta Twilio ([que se encontram no seu dashboard Twilio][twilio_dashboard]) conforme apresentado - certificar-se de que atribua o nome "TWILIO_ACCOUNT_SID" e "TWILIO_AUTH_TOKEN", respetivamente:

![Consola de administração do Azure][azure-admin-console]

Depois de ter configurado estas variáveis, reinicie a aplicação na consola do Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Declarar o módulo Twilio no package.json

Em seguida, precisamos de criar um package.json para gerir os nossos dependências de módulo nó através do [npm].  No mesmo nível, como o ficheiro de "server.js" que criou no Azure/node.js tutorial, crie um ficheiro com o nome "package.json".  Dentro neste ficheiro, coloque o seguinte procedimento:

  {"nome": "-nome da aplicação", "versão": "0.0.1", "privado": VERDADEIRO, "scripts": {"Iniciar": "servidor de nó"}, "dependências": {"express": "3.1.0", "ejs": "*", "twilio": "*"}}

Isto declara o módulo twilio como uma dependência, bem como o populares [framework express web] [ express] e o motor de modelo EJS.  Certo, agora podemos está pronto - vamos escrever algum código!

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>Efetuar uma chamada de saída

Vamos criar um formulário simples que irá colocar uma chamada para um número que optamos.  Abra o server.js e introduza o código seguinte.  Tenha em atenção onde diz "CHANGE_ME" - aí a colocar o nome do seu Web site azure:

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

Em seguida, criar um diretório denominado "vistas" - dentro este diretório, crie um ficheiro denominado "index.ejs" com o seguinte conteúdo:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

Agora, implementar o seu Web site em Azure e abra a sua casa.  Deverá conseguir introduzir o seu número de telefone no campo de texto e recebe uma chamada do seu número de Twilio!

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS

Agora, vamos configurar uma interface de utilizador e de lógica de processamento de formulários para enviar uma mensagem de texto.  Abra o "server.js" e adicione o código seguinte após a última chamada para "app.post":

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

No "views/index.ejs", adicione outro formulário na primeira parte para submeter um número e a uma mensagem de texto:

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Implementar a aplicação para Azure e agora deverá conseguir submeter que formam e enviar uma mensagem de texto si próprio (ou qualquer um dos seus amigos mais próximos)!

<a id="nextsteps"/>
## <a name="next-steps"></a>Próximos passos

Aprendeu os princípios básicos para utilizar node.js e Twilio para criar as aplicações que comuniquem agora.  Mas estes exemplos praticamente não a superfície do que é possível fazer com o Twilio e node.js de rascunho.  Para obter mais informações utilizar Twilio com node.js, consulte os seguintes recursos:

* [Documentos do módulo oficial][docs]
* [Tutorial no VoIP com node.js aplicações][voipnode]
* [Votr - um SMS em tempo real de voto aplicação com node.js e CouchDB (três partes)][votr]
* [Programação par no browser com node.js][pair]

Espero que adora a hackers node.js e Twilio no Azure!

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



