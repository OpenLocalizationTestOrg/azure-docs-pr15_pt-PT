<properties
    pageTitle="Introdução ao Node.js web apps no serviço de aplicação do Azure"
    description="Saiba como implementar uma aplicação de Node.js para uma aplicação web na aplicação de serviço de Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Introdução ao Node.js web apps no serviço de aplicação do Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Este tutorial mostra como criar uma aplicação de [Node.js] simple e implementá-lo para a [Aplicação de serviço de Azure] a partir de um ambiente da linha de comandos, tal como cmd.exe ou festa. As instruções neste tutorial, podem ser seguidas em qualquer sistema operativo que podem ser executados Node.js.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>Pré-requisitos

- [NODE.js]
- [Bower]
- [Yeoman]
- [Git]
- [Clip Azure]
- Uma conta do Microsoft Azure. Se não tiver uma conta, pode [Inscrever-se para uma avaliação gratuita] ou [Ativar os benefícios da sua Visual Studio subscritor].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Criar e implementar uma aplicação de web Node.js simple

1. Abra o terminal da linha de comandos da sua preferência e instalar [Express gerador para Yeoman].

        npm install -g generator-express

2. `CD`para um diretório de trabalho e gerar uma aplicação express utilizando a seguinte sintaxe:

        yo express
        
    Escolha as seguintes opções quando lhe for pedido:  

    `? Would you like to create a new directory for your project?`**Sim**  
    `? Enter directory name`**{nome}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**Nenhum**  
    `? Select a database to use:`**Nenhum**  
    `? Select a build tool to use:`**Roncador**

3. `CD`para o diretório de raiz da sua nova aplicação e iniciá-lo para se certificar de que é executada no seu ambiente de desenvolvimento:

        npm start

    No seu browser, navegue para <http://localhost:3000> para se certificar de que pode ver a home page do Express. Assim que tiver confirmado as sequências de aplicação corretamente, utilizar `Ctrl-C` para pará-la.
    
1. Alterar o modo de ASM e iniciar sessão no Azure (ter [Azure clip](#prereq)):

        azure config mode asm
        azure login

    Siga a linha de comandos para continuar o início de sessão num browser com uma conta Microsoft que tem a sua subscrição do Azure.

2. Certifique-se de que ainda se encontra o diretório de raiz da sua aplicação, em seguida, criar o recurso de aplicação de serviço de aplicação no Azure com um nome de aplicação exclusiva com o comando seguinte. Por exemplo: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Siga a linha de comandos para selecionar uma região Azure para implementar. Se nunca configurou credenciais de implementação de Git/FTP para a sua subscrição Azure, também será solicitado criá-los.

3. Abra o ficheiro de./config/config.js a partir da raiz da sua aplicação e altere a porta de produção para `process.env.port`; o `production` propriedade na `config` objeto deverá ter um aspeto semelhante ao exemplo seguinte:

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Permite a aplicação de Node.js responder Web pedidos a porta predefinida que iisnode recebe.
    
4. Abra./package.json e adicione o `engines` propriedade para [especificar a versão Node.js pretendida](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Guardar as suas alterações e, em seguida, utilizar git para implementar a aplicação Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    O gerador Express já fornece um ficheiro de .gitignore, por isso o `git push` não consumir a largura de banda tentar carregar o node_modules / directório.

5. Por fim, inicie a sua aplicação Azure direto no browser:

        azure site browse

    Agora deverá ver a aplicação web do Node.js a executar o live na aplicação de serviço de Azure.
    
    ![Exemplo de navegar para a aplicação implementada.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Atualizar a aplicação web do Node.js

Para efetuar atualizações a sua aplicação web do Node.js em execução no serviço de aplicação, basta executar `git add`, `git commit`, e `git push` como fazer quando a aplicação web implementado pela primeira vez.
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>Como aplicação de serviço de ser implementada a sua aplicação Node.js

Aplicação de serviço de Azure utiliza [iisnode] para executar Node.js aplicações. O clip do Azure e o motor de Kudu (Git implementação) funcionam em conjunto para dar-lhe uma experiência simplificada quando desenvolver e implementar Node.js aplicações da linha de comandos. 

- `azure site create --git`reconhece o padrão de Node.js comuns de server.js ou app.js e cria uma iisnode.yml no seu diretório de raiz. Pode utilizar este ficheiro para personalizar iisnode.
- Em `git push azure master`, Kudu automatiza as seguintes tarefas de implementação:

    - Se não estiver na raiz da repositório package.json, execute `npm install --production`.
    - Gera um Web. config para iisnode que aponta para o script iniciar package.json (por exemplo, server.js ou app.js).
    - Personalize Web. config para prontos a aplicação para depuração com o Inspector de nó.
    
## <a name="use-a-nodejs-framework"></a>Utilizar um quadro Node.js

Se utilizar um quadro Node.js popular, tal como [Sails.js] [ SAILSJS] ou [MEAN.js] [ MEANJS] desenvolver aplicações, pode implementar os para a aplicação de serviço. Populares Node.js quadros tem os respetivos quirks específicas e manterem introdução atualizadas respectivas dependências de pacote. No entanto, o serviço de aplicação torna os registos de stdout e stderr disponível para si, para que possa saber exatamente o que se passa com a sua aplicação e efetuar alterações em conformidade. Para mais informações, consulte o artigo [obter registos stdout e stderr a partir do iisnode](#iisnodelog).

Os seguintes tutoriais mostrar-lhe como trabalhar com um quadro específico na aplicação de serviço:

- [Implementar uma aplicação web do Sails.js ao serviço de aplicação do Azure]
- [Criar uma aplicação do chat Node.js com Socket.IO na aplicação de serviço do Azure]
- [Como utilizar io.js Azure aplicação de serviço Web Apps]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>Utilizar um motor de Node.js específico

No seu fluxo de trabalho normal, informe aplicação de serviço para utilizar um motor de Node.js específico como faria normalmente no package.json.
Por exemplo:

    "engines": {
        "node": "6.6.0"
    }, 

O motor de implementação Kudu determina qual motor Node.js para utilizar pela seguinte ordem:

- Em primeiro lugar, observe iisnode.yml para ver se `nodeProcessCommandLine` for especificado. Se Sim, em seguida, utilizá-la.
- Em seguida, veja package.json para ver se `"node": "..."` especificado na `engines` objeto. Se Sim, em seguida, utilizá-la.
- Selecione uma versão de Node.js predefinida por predefinição.

>[AZURE.NOTE] Recomenda-se definir explicitamente o motor de Node.js pretendido. Pode alterar a versão de Node.js predefinida e poderá obter erros na sua aplicação Azure web porque a versão de Node.js predefinida não é adequada para a sua aplicação.

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Obter registos stdout e stderr de iisnode

Para ler iisnode registos, siga estes passos.

> [AZURE.NOTE] Depois de concluir estes passos, os ficheiros de registo podem não existir até ocorre um erro.

1. Abra o ficheiro de iisnode.yml que fornece o clip do Azure.

2. Defina os dois parâmetros seguintes: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Em conjunto, indicam iisnode na aplicação de serviço para colocar respectiva saída stdout e stderror a D:\home\site\wwwroot\**iisnode** diretório.

3. Guardar as suas alterações e, em seguida, transmitir as suas alterações para Azure com os seguintes comandos Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode agora está configurado. Próximos passos mostram-lhe como aceder a estes registos.
     
4. No seu browser, aceder a consola de depuração de Kudu para a sua aplicação, que se encontra no:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Este URL é diferente do URL do web app através da adição de "*.scm.*" para o nome DNS. Se omitir essa adição para o URL, obterá um erro 404.

5. Navegue para D:\home\site\wwwroot\iisnode

    ![Navegar para a localização dos ficheiros de registo iisnode.][iislog-kudu-console-find]

6. Clique no ícone **Editar** para o registo que pretende ler. Também pode clicar em **Transferir** ou **Eliminar** se pretender.

    ![Abrir um ficheiro de registo de iisnode.][iislog-kudu-console-open]

    Agora pode ver o registo para o ajudar a sua implementação da aplicação de serviço de depuração.
    
    ![Examinar um ficheiro de registo iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Depurar a sua aplicação com o nó do Inspector

Se utilizar o Inspetor de nó para depurar as suas aplicações Node.js, pode utilizá-lo para a sua aplicação de serviço de aplicação direto. Inspetor de nó está pré-instalado na instalação iisnode para a aplicação de serviço. E se implementar através de Git, a Web. config gerados automaticamente a partir do Kudu já contenha todos os configuração que ter de ativar o Inspector de nó.

Para ativar o nó do Inspector, siga estes passos:

1. Abra iisnode.yml na sua raiz repositório e especificar os parâmetros seguintes: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Guardar as suas alterações e, em seguida, transmitir as suas alterações para Azure com os seguintes comandos Git:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Agora, apenas navegue até ao ficheiro de iniciar a aplicação conforme especificado pelo script iniciar no seu package.json, com /debug adicionada para o URL. Por exemplo,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Ou,
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Mais recursos

- [Especificar uma versão Node.js numa aplicação do Azure](../nodejs-specify-node-version-azure-apps.md)
- [Melhores práticas e guia de resolução de problemas para aplicações Node.js no Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Como depurar uma aplicação web do Node.js na aplicação de serviço do Azure](web-sites-nodejs-debug.md)
- [Utilização Node.js módulos com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
- [Azure de aplicação de serviço Web Apps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centro de programadores do node.js](/develop/nodejs/)
- [Introdução ao web apps no serviço de aplicação do Azure](app-service-web-get-started.md)
- [Explorar a consola de depuração Kudu Super secreta]

<!-- URL List -->

[Clip Azure]: ../xplat-cli-install.md
[Azure de aplicação de serviço]: ../app-service/app-service-value-prop-what-is.md
[Ativar os benefícios da sua subscritor Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Criar uma aplicação do chat Node.js com Socket.IO na aplicação de serviço do Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Implementar uma aplicação web do Sails.js ao serviço de aplicação do Azure]: ./app-service-web-nodejs-sails.md
[Explorar a consola de depuração Kudu Super secreta]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express gerador para Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Como utilizar io.js Azure aplicação de serviço Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[NODE.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[inscrever-se para uma avaliação gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
