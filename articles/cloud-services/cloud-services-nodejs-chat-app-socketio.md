<properties 
    pageTitle="Aplicação de node.js utilizando Socket.io | Microsoft Azure" 
    description="Saiba como utilizar socket.io numa aplicação node.js alojada no Azure." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Criar uma aplicação de Node.js Chat com Socket.IO num serviço de nuvem Azure

Socket.IO fornece tempo real comunicação entre o seu servidor de node.js e clientes. Neste tutorial irá guiá-lo através de um socket de alojamento. Aplicação de chat no Azure baseada em es. Para mais informações sobre Socket.IO, consulte o artigo <http://socket.io/>.

Uma captura de ecrã da aplicação concluída é abaixo:

![Numa janela do browser apresentando o serviço alojado no Azure][completed-app]  

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os produtos e versões seguintes estão instaladas para concluir com êxito o exemplo neste artigo:

* Instalar o [Visual Studio 2013](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Instalar [Node.js](https://nodejs.org/download/)
* Instalar [Python versão 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Criar um projeto de serviço de nuvem

Os passos seguintes criam o projeto de serviço de nuvem que irá alojar a aplicação Socket.IO.

1. A partir do **Menu Iniciar** ou **Ecrã Iniciar**, procure o **Windows PowerShell**. Por fim, com o botão direito **Do Windows PowerShell** e selecione **Executar como administrador**.

    ![Ícone de PowerShell Azure][powershell-menu]

2. Criar um diretório chamado **c:\\nó**. 
 
        PS C:\> md node

3. Alterar directórios para o **c:\\nó** diretório
 
        PS C:\> cd node

4. Introduza os seguintes comandos para criar uma nova solução **chatapp** com o nome e uma função de trabalho denominada **WorkerRole1**:

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Irá ver a seguinte resposta:

    ![O resultado das novas azureservice e azurenodeworkerrolecmdlets adicionar](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Transferir o exemplo de Chat

Para este projeto, vamos utilizar o exemplo de chat do [repositório de Socket.IO GitHub]. Execute os passos seguintes para transferir o exemplo e adicioná-lo ao projeto que criou anteriormente.

1.  Crie uma cópia local do repositório utilizando o botão **clonar** . Também pode utilizar o botão de **código postal** para transferir o projeto.

    ![Numa janela do browser ver https://github.com/LearnBoost/socket.io/tree/master/examples/chat, com o ícone de transferência do postal realçado][chat-example-view]

3.  Navegue até a estrutura do directório do repositório local até chegar à **exemplos\\chat** diretório. Copiar o conteúdo deste directório para o **c:\\nó\\chatapp\\WorkerRole1** diretório criado anteriormente.

    ![Explorer, apresentar o conteúdo dos exemplos\\extraído do arquivo de diretório de chat][chat-contents]

    Os itens realçados na captura de ecrã acima são os ficheiros que copiou da **exemplos\\chat** diretório

4.  Na **c:\\nó\\chatapp\\WorkerRole1** diretório, elimine o ficheiro de **server.js** e, em seguida, mude o nome **app.js** ficheiro para **server.js**. Isto remove o ficheiro de **server.js** predefinido criado anteriormente pelo cmdlet **Adicionar AzureNodeWorkerRole** e substitui-la com o ficheiro de aplicação do exemplo chat.

### <a name="modify-serverjs-and-install-modules"></a>Modificar Server.js e instalar módulos

Oferecemos, antes de testar a aplicação no Azure emulador, algumas modificações secundárias. Execute os seguintes passos para o ficheiro server.js:

1.  Abra o ficheiro **server.js** no Visual Studio ou editores de texto.

2.  Localizar a secção de **dependências de módulo** no início do server.js e altere a linha que contém **sio = require('.. //.. lib//socket.IO')** para **sio = require('socket.io')** conforme apresentado abaixo:

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Para assegurar que a aplicação recebe a porta correta, abra server.js no bloco de notas ou o editor de favorito e, em seguida, altere a linha seguinte substituindo **3000** por **process.env.port** conforme apresentado abaixo:

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Depois de guardar as alterações para **server.js**, utilize os passos seguintes para instalar módulos necessários e, em seguida, testar a aplicação no Azure emulador:

1.  Através do **Azure PowerShell**, altere a directórios para o **c:\\nó\\chatapp\\WorkerRole1** diretório e utilize o seguinte comando para instalar módulos necessários por esta aplicação:

        PS C:\node\chatapp\WorkerRole1> npm install

    Isto irá instalar os módulos listados no ficheiro package.json. Após completa o comando, deverá ver o resultado semelhante ao seguinte:

    ![O resultado do npm instalar o comando][The-output-of-the-npm-install-command]

4.  Uma vez que este exemplo foi originalmente uma parte do repositório Socket.IO GitHub e diretamente referenciadas a biblioteca de Socket.IO por caminho relativo, Socket.IO não foi referenciado no ficheiro de package.json, para que podemos tem instalá-la através da emissão o seguinte comando:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testar e implementar

1.  Inicie o emulador ao emitir o seguinte comando:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Abra um browser e navegue para **http://127.0.0.1**.

3.  Quando abre a janela do browser, introduza uma alcunha e, em seguida, premir enter.
    Isto irá que publica mensagens nas como uma alcunha específica. Para testar a funcionalidade de vários utilizador, abra janelas do browser adicionais com o mesmo URL e introduza alcunhas diferentes.

    ![Apresentar a mensagens de conversação de Utilizador1 e Utilizador2 duas janelas do browser](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Depois de a testar a aplicação, interrompa o emulador emitir o seguinte comando:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Para implementar a aplicação para Azure, utilize o cmdlet **AzureServiceProject publicar** . Por exemplo:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    > [AZURE.IMPORTANT] Certifique-se de que utiliza um nome exclusivo, caso contrário, o processo de publicar irá falhar. Quando tiver concluído a implementação, o browser abra e navegue para o serviço implementado.
    > 
    > Se receber um erro a indicar que o nome da subscrição fornecido não existe no perfil de publicar importados, tem de transferir e importar o perfil de publicação para a sua subscrição antes de implementar para Azure. Consulte a secção **Implementar a aplicação para Azure** [criar e implementar uma aplicação de Node.js num serviço de nuvem do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

    ![Numa janela do browser apresentando o serviço alojado no Azure][completed-app]

    > [AZURE.NOTE] Se receber um erro a indicar que o nome da subscrição fornecido não existe no perfil de publicar importados, tem de transferir e importar o perfil de publicação para a sua subscrição antes de implementar para Azure. Consulte a secção **Implementar a aplicação para Azure** [criar e implementar uma aplicação de Node.js num serviço de nuvem do Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)

A aplicação está a ser executado no Azure e pode reencaminhar mensagens de conversação entre diferentes clientes utilizando Socket.IO.

> [AZURE.NOTE] Para simplificar, este exemplo está limitado a conversar entre os utilizadores ligados na mesma instância. Isto significa que se o serviço de nuvem cria duas instâncias de função de trabalho, os utilizadores apenas poderão conversar com outras pessoas ligado na mesma instância de função de trabalho. Para dimensionar a aplicação para trabalhar com várias instâncias de papel, pode utilizar uma tecnologia como Bus de serviço para partilhar o estado do arquivo de Socket.IO em várias instâncias. Para obter exemplos, consulte os exemplos de utilização serviço Bus filas e tópicos no [Azure SDK para o repositório de Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).

##<a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como criar uma aplicação de chat básicas alojada num serviço na nuvem Azure. Para saber como pode alojar esta aplicação de um Web site Azure, consulte o artigo [criar uma aplicação de Chat para Node.js com Socket.IO um Web Site da Azure][chatwebsite].

Para obter mais informações, consulte também o [Centro de programadores do Node.js](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Repositório de Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 
