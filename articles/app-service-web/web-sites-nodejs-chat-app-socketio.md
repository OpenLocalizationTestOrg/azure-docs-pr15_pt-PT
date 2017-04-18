<properties
    pageTitle="Criar uma aplicação do chat Node.js com Socket.IO na aplicação de serviço do Azure"
    description="Um tutorial demonstra utilizar socket.io numa aplicação web node.js alojada no Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Criar uma aplicação do chat Node.js com Socket.IO na aplicação de serviço do Azure

Socket.IO fornece em tempo real comunicação entre o seu servidor de node.js e clientes utilizando WebSockets. Também suporta contingência a outros transportes (como longo inquéritos) que funcionam com browsers mais antigos. Neste tutorial irá guiá-lo alojamento uma aplicação do chat Socket.IO com base como uma aplicação web do Azure e mostrar-lhe como dimensionar a aplicação utilizando [Azure Redis Cache]. Para mais informações sobre Socket.IO, consulte o artigo <http://socket.io/>.

> [AZURE.NOTE] Os procedimentos nesta tarefa aplicam-se a [Aplicação de serviço Web Apps]; para serviços em nuvem, consulte o artigo [criar uma aplicação de Chat para Node.js com Socket.IO num serviço de nuvem Azure].

## <a name="download-the-chat-example"></a>Transferir o exemplo de chat

Para este projeto, vamos utilizar o exemplo de chat do [repositório de Socket.IO GitHub]. Execute os passos seguintes para transferir o exemplo e adicioná-lo ao projeto que criou anteriormente.

1.  Transferir um [postal ou GZ arquivado lançamento] do projeto Socket.IO (versão 1.3.5 foi utilizado para este documento)

1.  Extrair do arquivo e copie o **exemplos\\chat** directório para uma nova localização. Por exemplo, ** \\nó\\chat**.

## <a name="modify-appjs-and-install-modules"></a>Modificar app.js e instalar módulos

1.  Mudar o nome de ficheiro de **index.js** para **app.js**. Esta opção permite-Azure detetar que se trata de uma aplicação de Node.js.

1.  Abra o ficheiro **app.js** num editor de texto. Alterar a linha que contém `var io = require('../..')(server);` conforme apresentado abaixo:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. Abra o ficheiro **package.json** e adicione uma referência a socket.io em `dependencies`, conforme apresentado abaixo:

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. A partir da linha de comandos, mude para o ** \\nó\\chat** npm diretório e a utilização para instalar módulos necessários por esta aplicação:

        npm install

    Isto irá instalar os módulos para uma subpasta denominada **node_modules**.

## <a name="create-an-azure-web-app"></a>Criar uma aplicação Web do Azure

Siga estes passos para criar uma aplicação Azure web, ativar Git publicação e, em seguida, ativar o suporte do WebSocket para a aplicação web.

> [AZURE.NOTE] Para concluir este tutorial, é necessária uma conta Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Versão de avaliação gratuita do Azure</a>.

1. Instalar a Interface da linha de comandos do Azure (Azure CLI) e ligue à sua subscrição do Azure. Consulte o artigo [instalar e configurar o clip Azure](../xplat-cli-install.md).

1. Se esta é a sua definição de horas primeira para cima um repositório no Azure, terá de criar de credenciais de início de sessão. A partir do clip Azure, introduza o seguinte comando:

        azure site deployment user set [username] [password]

1. Alterar para o ** \\node\chat** diretório e utilize o seguinte comando para criar um novo Azure web app e um repositório de Git local. Este comando também cria um Git remote com nome 'azure'.

        azure site create mysitename --git

    Tem de substituir 'mysitename' com um nome exclusivo para a sua aplicação web.

1. Consolide os ficheiros existentes para o repositório local através dos comandos seguintes:

        git add .
        git commit -m "Initial commit"

1. Notificações push os ficheiros para o repositório Azure Web Apps com o seguinte comando:

        git push azure master

    Quando lhe for pedido, introduza as suas credenciais do passo 2. Irá receber mensagens de estado como módulos são importados no servidor. Assim que este processo terminar, a aplicação vai ser alojada em sua aplicação Azure web.

    > [AZURE.NOTE] Durante a instalação do módulo, é possível que existam erros que ' o projecto importado... não foi encontrado '. Estes podem ser ignoradas.

1. Socket.IO utiliza WebSockets, que não se encontram ativados por predefinição no Azure. Para ativar web sockets, utilize o seguinte comando:

        azure site set -w

    Se lhe for pedido, introduza o nome da aplicação web.

    >[AZURE.NOTE]
    >O 'azure site set -w' comando irá funcionam apenas com a versão 0.7.4 ou superior da Interface de comandos Azure. Também pode ativar o suporte de WebSocket utilizando o [Portal do Azure](https://portal.azure.com).
    >
    >Para ativar WebSockets utilizando o Portal do Azure, clique na aplicação web a partir do pá aplicações Web, clique em **todas as definições de** > **definições da aplicação**. Em **Web Sockets**, clique **no**. Em seguida, clique em **Guardar**.

1. Para ver a aplicação web no Azure, utilize o seguinte comando para iniciar o seu browser e navegue para a aplicação web alojado:

        azure site browse

A sua aplicação está a ser executado no Azure e pode reencaminhar mensagens de conversação entre diferentes clientes utilizando Socket.IO.

## <a name="scale-out"></a>Dimensionar saída

Aplicações de Socket.IO podem ser dimensionadas saída ao utilizar um __adaptador__ para distribuir mensagens e eventos entre várias instâncias da aplicação. Apesar de existirem várias placas disponível, o adaptador [socket.io redis] pode ser utilizado facilmente com a funcionalidade de Azure Redis Cache.

> [AZURE.NOTE] Um requisito adicional para escalar para fora de uma solução Socket.IO é o suporte para sessões de autocolantes. Sessões autocolantes estão activadas por predefinição para aplicações Web do Azure através do Azure Pedir encaminhamento. Para mais informações, consulte o artigo [Instância afinidade no Azure Web Sites].

### <a name="create-a-redis-cache"></a>Criar uma cache Redis

Execute os passos no artigo [criar uma cache no Azure Redis Cache] para criar uma nova cache.

> [AZURE.NOTE] Guarde o __nome do anfitrião__ e a __chave primária__ para a cache, tal como estes serão necessárias nos próximos passos.

### <a name="add-the-redis-and-socketio-redis-modules"></a>Adicionar a redis e socket.io redis módulos

1. A partir de uma linha de comandos, mude para o __ \\nó\\chat__ diretório e utilize o comando seguinte.

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] As versões especificadas neste comando são as versões utilizadas durante o teste deste artigo.

1. Modificar o ficheiro de __app.js__ para adicionar o seguinte imediatamente após as linhas`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Substitua __redishostname__ e __rediskey__ o nome do anfitrião e a chave para a sua cache Redis.

    Isto irá criar uma publicação e subscrever cliente para a cache de Redis criada anteriormente. Os clientes, em seguida, são utilizados com a placa para configurar Socket.IO para utilizar a cache de Redis para transmitir mensagens e eventos entre instâncias da aplicação

    > [AZURE.NOTE] Enquanto o adaptador __socket.io redis__ pode comunicar diretamente para Redis, a versão atual não suporta a autenticação de uma ferramenta necessária ao Azure Redis cache. Por isso, é criada a ligação inicial utilizar o módulo __redis__ , em seguida, o cliente é transmitido para o adaptador __socket.io redis__ .
    >
    > Enquanto a Cache de Redis Azure suportar ligações seguras utilizando porta 6380, os módulos utilizados neste exemplo não suportam ligações seguras partir 14/7/2014. O código acima utiliza o predefinido e não segura porta do 6379.

1. Guardar a modificados __app.js__

### <a name="commit-changes-and-redeploy"></a>Consolidar alterações e voltar a implementar

A partir da linha de comandos na __ \\nó\\chat__ diretório, utilize os seguintes comandos para consolidar alterações e voltar a implementar a aplicação.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Assim que as alterações tiverem sido enviadas para o servidor, pode dimensionar o seu site em várias instâncias de vários utilizando o comando seguinte.

    azure site scale instances --instances #

Onde __#__ é o número de instâncias para criar.

Pode ligar a sua aplicação web a partir de vários browsers ou computadores para confirmar que as mensagens são enviadas corretamente para todos os clientes.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="connection-limits"></a>Limites de ligação

Azure Web Apps está disponível no múltiplas SKUs determinam os recursos disponíveis para o seu site. Isto inclui o número de ligações de WebSocket permitidas. Para mais informações, consulte a [página de preços de aplicações Web].

### <a name="messages-arent-being-sent-using-websockets"></a>As mensagens não estão a ser enviadas utilizando WebSockets

Se browsers cliente manterem fiquem novamente tempo procura em vez de utilizar WebSockets, pode ser devido a um dos seguintes.

* **Tente limitar o transporte para apenas WebSockets**

    Ordem para Socket.IO utilizar WebSockets como o transporte mensagens, o servidor e o cliente tem de suportar WebSockets. Se uma ou de outra não, Socket.IO irá negoceie transporte outra, tal como inquéritos por extenso. A lista de transportes utilizado pelo Socket.IO predefinida é ` websocket, htmlfile, xhr-polling, jsonp-polling`. Pode forçar-o para utilizar apenas WebSockets, adicionando o código seguinte para o ficheiro **app.js** , após a linha que contém `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] Tenha em atenção que browsers mais antigos que não suportam WebSockets não conseguir ligar-se para o site enquanto o código acima está ativo, tal como-restringe a comunicação para WebSockets apenas.

* **Utilizar SSL**

    WebSockets baseia-se em algumas menor utilizados cabeçalhos de HTTP, tal como o cabeçalho **de atualização** . Alguns dispositivos de rede intermédio, tal como web proxies, podem remover estes cabeçalhos. Para evitar este problema, pode estabelecer a ligação WebSocket através de SSL.

    Uma forma fácil para realizar esta tarefa é configurar Socket.IO para `match origin protocol`. Isto indica ao Socket.IO para assegurar a comunicação de WebSockets o mesmo que o pedido HTTP/HTTPS origem para a página web. Se a um browser utiliza um URL de HTTPS para visitar o seu Web site, comunicações WebSocket subsequentes através de Socket.IO serão protegidas através de SSL.

    Para modificar neste exemplo para ativar esta configuração, adicione o código seguinte para o ficheiro **app.js** após a linha que contém `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **Verifique se as definições de Web. config**

    Aplicações Azure web que alojam Node.js aplicações utilizam o ficheiro da **Web. config** para encaminhar pedidos recebidos para a aplicação Node.js. Para WebSockets para funcionar corretamente com Node.js aplicações, a **Web. config** tem de conter a seguinte entrada.

        <webSocket enabled="false"/>

    Isto desativa o módulo do IIS WebSockets, que inclui a sua própria implementação do WebSockets e está em conflito com Node.js módulos WebSocket específicos, tal como Socket.IO. Se esta linha não se encontra presente ou está definida para `true`, tal pode dever-a razão pela qual o transporte WebSocket não está a funcionar para a sua aplicação.

    Normalmente, Node.js aplicações não inclua um ficheiro de **Web. config** para que sites públicos do Azure irá gerar automaticamente um para aplicações Node.js quando estão implementados. Uma vez que este ficheiro é gerado automaticamente no servidor, tem de utilizar FTP ou ftps:// o URL do Web site para ver este ficheiro. Pode encontrar FTP e ftps:// URLs para o seu site no portal do clássico selecionando a sua aplicação web e, em seguida, a ligação do **Dashboard** . Os URLs são apresentados na secção **vista rápida** .

    > [AZURE.NOTE] O ficheiro da **Web. config** apenas é gerado pelo sites públicos do Azure se a aplicação não fornecer uma. Se fornecer um ficheiro de **Web. config** na raiz do seu projeto de aplicação, será utilizada por Azure Web Apps.

    Se a entrada não se encontra presente ou está definida para um valor de `true`, em seguida, deve criar um **Web. config** na raiz da sua aplicação Node.js e especificar um valor de `false`.  Para sua referência, o segue-se uma predefinição de **Web. config** para uma aplicação que utiliza **app.js** como o ponto de entrada.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    Se a sua aplicação utiliza um ponto de entrada que não seja **app.js**, tem de substituir todas as ocorrências dos **app.js** com o ponto de entrada correto. Por exemplo, substituindo **app.js** **server.js**.

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço], onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como criar uma aplicação de chat alojada numa aplicação Azure web. Também pode alojar esta aplicação como um serviço de nuvem Azure. Para obter passos sobre como realizar esta tarefa, consulte o artigo [criar uma aplicação de Chat para Node.js com Socket.IO num serviço de nuvem Azure].

Para obter mais informações, consulte também o [Centro de programadores do Node.js].

## <a name="whats-changed"></a>O que é alterado

* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto dos serviços do Azure existente].

<!-- URL List -->

[Cache de Azure Redis]: /documentation/services/redis-cache/
[Aplicação de serviço Web Apps]: http://go.microsoft.com/fwlink/?LinkId=529714
[Página de preços de aplicações Web]: http://go.microsoft.com/fwlink/?LinkId=511643
[Criar uma aplicação de Node.js Chat com Socket.IO num serviço de nuvem Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Azure de aplicação de serviço e o seu impacto nos serviços de Azure existentes]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro de programadores do node.js]: /develop/nodejs/
[Experimente a aplicação de serviço]: http://go.microsoft.com/fwlink/?LinkId=523751
[Instância afinidade no Azure Web Sites]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Criar uma cache na Cache Redis do Azure]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[socket.IO redis]: https://github.com/socketio/socket.io-redis
[Repositório de Socket.IO GitHub]: https://github.com/socketio/socket.io
[Postal ou GZ arquivado lançamento]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
