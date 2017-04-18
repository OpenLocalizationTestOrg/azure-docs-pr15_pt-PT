<properties 
    pageTitle="Web App com Express (Node.js) | Microsoft Azure" 
    description="Um tutorial que cria no tutorial de serviço de nuvem e demonstra como utilizar o módulo Express." 
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






# <a name="build-a-nodejs-web-application-using-express-on-an-azure-cloud-service"></a>Criar uma aplicação de web Node.js utilizando Express num serviço de nuvem Azure

NODE.js inclui um conjunto mínimo de funcionalidade no runtime do core.
Os programadores utilizam frequentemente 3º módulos de terceiros para fornecer funcionalidades adicionais quando desenvolver uma aplicação de Node.js. Neste tutorial vai criar uma nova aplicação de utilizar o módulo [Express][] , que fornece um quadro MVC para criar aplicações de web Node.js.

Uma captura de ecrã da aplicação concluída é abaixo:

![Um browser apresentar bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##<a name="create-a-cloud-service-project"></a>Criar um projeto de serviço de nuvem

Execute os seguintes passos para criar um novo projeto de serviço de nuvem denominado 'expressapp':

1. A partir do **Menu Iniciar** ou **Ecrã Iniciar**, procure o **Windows PowerShell**. Por fim, com o botão direito **Do Windows PowerShell** e selecione **Executar como administrador**.

    ![Ícone de PowerShell Azure](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

    [AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. Alterar directórios para o **c:\\nó** diretório e, em seguida, introduza os seguintes comandos para criar uma nova solução **expressapp** com o nome e uma função de web denominada **WebRole1**:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

    > [AZURE.NOTE] Por predefinição, **Adicionar AzureNodeWebRole** utiliza uma versão anterior do Node.js. A declaração de **Conjunto AzureServiceProjectRole** acima indica ao Azure utilizar v0.10.21 de nó.  Tenha em atenção que os parâmetros são entre maiúsculas e minúsculas.  Pode verificar que a versão correta do Node.js tiver sido seleccionada marcando a propriedade **motores** na **WebRole1\package.json**.

##<a name="install-express"></a>Instalar o Express

1. Instale o gerador Express ao emitir o seguinte comando:

        PS C:\node\expressapp> npm install express-generator -g

    O resultado do comando npm deverá ter um aspeto semelhante ao resultado abaixo. 

    ![Apresentar o resultado do npm do Windows PowerShell instalar o comando express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Alterar directórios ao diretório **WebRole1** e utilize o comando express para gerar uma nova aplicação:

        PS C:\node\expressapp\WebRole1> express

    Será pedido para substituir a aplicação anterior. Introduza **y** ou **Sim** para continuar. Express irá gerar o ficheiro de app.js e uma estrutura de pastas para criar a sua aplicação.

    ![O resultado do comando express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Para instalar dependências adicionais definidas no ficheiro package.json, introduza o seguinte comando:

        PS C:\node\expressapp\WebRole1> npm install

    ![O resultado do npm instalar o comando](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Utilize o seguinte comando para copiar o ficheiro de **Reciclagem/www** para **server.js**. Este é para que o serviço de nuvem possa localizar o ponto de entrada para esta aplicação.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    Depois de concluir este comando, deverá ter um ficheiro de **server.js** no diretório WebRole1.

7.  Modificar a **server.js** para remover um da '.' carateres a partir da linha seguinte.

        var app = require('../app');

    Depois de efetuar esta modificação, a linha deverá aparecer da seguinte forma.

        var app = require('./app');

    Esta alteração é necessária uma vez que é movido o ficheiro (anteriormente a **posição/www**,) para o mesmo directório como o ficheiro de aplicação que está a ser necessário. Depois de efetuar esta alteração, guarde o ficheiro **server.js** .

8.  Utilize o seguinte comando para executar a aplicação no Azure emulador:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![Uma página web que contém bem-vindo ao express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modificar a vista

Agora, modifique a vista para apresentar a mensagem "Bem-vindo ao Express no Azure".

1.  Introduza o seguinte comando para abrir o ficheiro index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![O conteúdo do ficheiro index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade é o motor de vista predefinido utilizado por aplicações Express. Para mais informações sobre o motor de vista Jade, consulte o artigo [http://jade-lang.com][].

2.  Modificar a última linha de texto por acrescentá **no Azure**.

    ![O ficheiro index.jade, a última linha lê: p bem-vindo ao \#{title} no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Guardar o ficheiro e saia do bloco de notas.

4.  Atualize o seu browser e irá ver as suas alterações.

    ![Numa janela do browser, a página contém bem-vindo ao Express no Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Depois de a testar a aplicação, utilize o cmdlet **AzureEmulator parar** para parar o emulador.

##<a name="publishing-the-application-to-azure"></a>A aplicação para Azure de publicação

Na janela do Azure PowerShell, utilize o cmdlet de **Publicar AzureServiceProject** para implementar a aplicação num serviço na nuvem

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Uma vez concluída a operação de implementação, o seu browser abra e apresentar a página web.

![Um browser a apresentar a página Express. O URL indica que está atualmente alojado no Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do Node.js](/develop/nodejs/).

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-Lang.com]: http://jade-lang.com

 
