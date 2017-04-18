<properties 
    pageTitle="Criar e implementar uma aplicação web do Node.js para Azure utilizando WebMatrix" 
    description="Tutorial que lhe ensina utilizar WebMatrix para desenvolver uma aplicação de Node.js e implemente-o Azure aplicação de serviço Web Apps." 
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


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Criar e implementar uma aplicação web do Node.js para Azure utilizando WebMatrix

Este tutorial mostra-lhe como utilizar WebMatrix para desenvolver uma aplicação de Node.js e implementá-la ao [Azure aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. WebMatrix é uma ferramenta de desenvolvimento web gratuito da Microsoft que inclui tudo o que precisa para o desenvolvimento de aplicação Web site ou na web. WebMatrix inclui várias funcionalidades que tornam Node.js fáceis de utilizar, incluindo a conclusão de código, modelos previamente concebidos e suporte do editor para Jade, menos e CoffeeScript. Saiba mais sobre [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Após concluir este guia, terá de uma aplicação de web Node.js em execução no Azure aplicação de serviço.
 
Uma captura de ecrã da aplicação concluída é abaixo:

![Web site da nó Azure][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="sign-into-azure"></a>Inicie sessão no Azure

Siga estes passos para criar uma aplicação web na aplicação de serviço de Azure.

1. Iniciação WebMatrix
2. Se esta for a primeira vez que utilizou WebMatrix, ser-lhe-á pedido para iniciar sessão no Azure.  Caso contrário, pode clicar no botão **Iniciar sessão** e selecione **Adicionar conta**.  Selecione para **Iniciar sessão** utilizando a sua Account da Microsoft.

    ![Adicionar conta][addaccount]

3. Se tiver inscrito para uma conta do Azure, que pode iniciar sessão com a sua Account da Microsoft:

    ![Inicie sessão no Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Criar um site utilizando um modelo incorporado para o Azure

1. No ecrã Iniciar, clique no botão **Novo** e selecione a **Galeria de modelos** para criar um novo site a partir da Galeria de modelos:

    ![Novo site a partir da Galeria de modelos][sitefromtemplate]

2. Na caixa de diálogo **Site a partir de modelo** , selecione **nó** e, em seguida, selecione **Express Site**. Por fim, clique em **seguinte**. Se faltar qualquer pré-requisitos para o modelo de **Express Site** , vai ser-lhe instalá-los.

    ![Selecione o modelo express][webmatrix-templates]

3. Se tiver iniciado sessão no Azure, agora tem a opção para criar um aplicação de serviço web app para o site local.  Selecione um nome exclusivo e selecione o Centro de dados onde pretende que a aplicação web do serviço de aplicação para ser criado: 

    ![Criar o site no Azure][nodesitefromtemplateazure]
    
4. Depois de concluir WebMatrix construir o site local e a criação do serviço de aplicação web app, é apresentado o IDE WebMatrix.

    ![webmatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>Publicar a sua aplicação ao Azure

1. No WebMatrix, clique em **Publicar** no friso **base** para apresentar a caixa de diálogo **Publicar pré-visualização** para o site.

    ![publicar a pré-visualização][webmatrix-node-publishpreview]

2. Clique em **continuar**. Quando a publicação estiver concluída, o URL para o aplicação de serviço web app é apresentado na parte inferior do WebMatrix IDE

    ![publicar concluída][webmatrix-publish-complete]

3. Clique na ligação para abrir o aplicação de serviço web app no seu browser.

    ![Express do web app][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>Modificar e voltar a publicar a aplicação

Pode facilmente modificar e voltar a publicar a aplicação. Aqui, irá tornar uma alteração simples alterar para o título no ficheiro **index.jade** e voltar a publicar a aplicação.

1. Na WebMatrix, selecione **ficheiros**e, em seguida, expanda a pasta de **vistas** . Abra o ficheiro **index.jade** fazendo duplo clique na mesma.

    ![webmatrix index.jade de visualização][webmatrix-modify-index]

2. Altere a linha do parágrafo para o seguinte:

        p Welcome to #{title} with WebMatrix on Azure!

3. Guardar as suas alterações e, em seguida, clique no ícone de publicar. Por fim, clique em **continuar** na caixa de diálogo **Publicar pré-visualizar** e aguarde que a atualização para ser publicado.

    ![publicar a pré-visualização][webmatrix-republish]

4. Quando tiver concluído a publicação, utilize a hiperligação devolvida quando o processo de publicar estiver concluído para ver a aplicação web do serviço de aplicação atualizada.

    ![Nó Azure web app][webmatrix-node-completed]

##<a name="next-steps"></a>Próximos passos

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão para ser utilizado com a aplicação, consulte o artigo [especificando uma versão Node.js numa aplicação do Azure](../nodejs-specify-node-version-azure-apps.md).

Se encontrar problemas com a sua aplicação depois de ter sido implementada para Azure, consulte o artigo [como depurar uma aplicação web do Node.js na aplicação de serviço de Azure](web-sites-nodejs-debug.md) para obter informações sobre diagnosticar o problema.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 