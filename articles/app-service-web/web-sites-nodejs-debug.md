<properties
    pageTitle="Como depurar uma aplicação web do Node.js na aplicação de serviço do Azure"
    description="Saiba como depurar uma aplicação web do Node.js na aplicação de serviço de Azure."
    tags="azure-portal"
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

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Como depurar uma aplicação web do Node.js na aplicação de serviço do Azure

Azure fornece diagnósticos incorporados para ajudá-lo a depuração de aplicações Node.js alojadas no [Azure aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. Neste artigo, irá aprender como ativar o registo de stdout e stderr, apresentar informações de erro no browser e como transferir e visualizar ficheiros de registo.

Diagnósticos para aplicações Node.js alojado no Azure são fornecido pelo [IISNode]. Enquanto este artigo aborda as definições mais comuns para recolher informações de diagnóstico,-não fornece uma referência completa para trabalhar com IISNode. Para obter mais informações sobre como trabalhar com IISNode, consulte o artigo [IISNode ficheiro Leia-me] GitHub.

<a id="enablelogging"></a>
## <a name="enable-logging"></a>Ativar o registo

Por predefinição, uma aplicação web do serviço de aplicação apenas para capturar informações de diagnóstico sobre implementações, tal como quando implementar uma aplicação web utilizando Git. Esta informação é útil se estiver com problemas durante a implementação, tal como uma falha ao instalar o módulo referenciado nas **package.json**, ou se estiver a utilizar um script de implementação personalizada.

Para ativar o registo de fluxos de stdout e stderr, tem de criar um ficheiro de **IISNode.yml** na raiz da sua aplicação Node.js e adicione o seguinte:

    loggingEnabled: true

Permite que o registo de stderr e stdout a partir da sua aplicação Node.js.

O ficheiro **IISNode.yml** também pode ser utilizado para controlar se amigável erros ou erros de programador são devolvidos ao browser quando ocorre uma falha. Para permitir que os erros de programador, adicione a linha seguinte para o ficheiro **IISNode.yml** :

    devErrorsEnabled: true

Assim que esta opção estiver ativada, IISNode irá devolver o último 64 mil das informações enviadas para stderr em vez de um erro amigável, tais como "Ocorreu um erro de servidor interno".

> [AZURE.NOTE] Enquanto devErrorsEnabled é útil quando diagnosticar problemas durante o desenvolvimento, permitindo-lhe num ambiente de produção pode resultar em erros de desenvolvimento a ser enviados para os utilizadores finais.

Se o ficheiro **IISNode.yml** não já existe na aplicação, tem de reiniciar a aplicação web do após a aplicação actualizada de publicação. Se simplesmente que está a alterar definições de um ficheiro de **IISNode.yml** existente que tenha sido publicada anteriormente, não é necessário reiniciar.

> [AZURE.NOTE] Se a aplicação web foi criada utilizando as ferramentas de linha de comandos do Azure ou os cmdlets do PowerShell do Azure, um ficheiro de **IISNode.yml** predefinido é automaticamente criado.

Para reiniciar a aplicação web, selecione a aplicação web no [Portal do Azure](https://portal.azure.com)e, em seguida, clique em **REINICIAR o** botão:

![Reinicie o botão][restart-button]

Se as ferramentas de linha de comandos do Azure estão instaladas no seu ambiente de desenvolvimento, pode utilizar o seguinte comando para reiniciar a aplicação web:

    azure site restart [sitename]

> [AZURE.NOTE] Enquanto loggingEnabled e devErrorsEnabled são normalmente utilizadas com mais opções de configuração de IISNode.yml para capturar informações de diagnóstico, IISNode.yml podem ser utilizados para configurar uma variedade de opções para o seu ambiente de alojamento. Para obter uma lista completa das opções de configuração, consulte o ficheiro [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) .

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>Aceder a registos

Registos de diagnóstico podem ser acedidos por três formas; Utilizando o ficheiro Transfer Protocol (FTP), transferir um arquivo Zip, ou como um direto actualizados da cadeia do registo (também conhecido como cauda). Transferir o arquivo de postal dos ficheiros de registo ou visualizar o fluxo direto exigir que as ferramentas de linha de comandos do Azure. Estas podem ser instaladas utilizando o seguinte comando:

    npm install azure-cli -g

Depois de instalado, as ferramentas podem ser acedidas utilizando o comando 'azure'. As ferramentas de linha de comandos primeiro tem de ser configuradas para utilizar a sua subscrição do Azure. Para obter informações sobre como realizar esta tarefa, consulte o artigo o **como transferir e importar definições de publicação** secção do artigo [como utilizar o Azure da linha de comandos ferramentas](../xplat-cli-connect.md) .

###<a name="ftp"></a>FTP

Para aceder às informações diagnóstico através do FTP, visite o [Portal do Azure](https://portal.azure.com), selecione a aplicação web do e, em seguida, selecione o **DASHBOARD**. Na secção **ligações rápidas** , as ligações de **FTP registos de diagnóstico** e **Registos de diagnóstico ftps://** fornecem acesso aos registos de utilizar o protocolo FTP.

> [AZURE.NOTE] Se não tiver anteriormente configurado nome de utilizador e palavra-passe para FTP ou implementação, pode fazê-lo a partir da página de gestão de **Guia de introdução** ao selecionar **defina credenciais de implementação**.

É o URL de FTP devolvido no dashboard para o directório de **ficheiros de registo** , que irá conter os seguintes sub directórios:

* [Método de implementação](web-sites-deploy.md) , se utilizar um método de implementação como Git, um diretório do mesmo nome, será criado e irá conter informações relacionadas com implementações.

* nodejs - informações Stdout e stderr capturados a partir de todas as instâncias da sua aplicação (quando loggingEnabled é verdadeira.)

###<a name="zip-archive"></a>Arquivo zip

Para transferir um arquivo Zip de registos de diagnóstico, utilize o seguinte comando Azure da linha de comandos das ferramentas de:

    azure site log download [sitename]

Isto irá transferir um **diagnostics.zip** no diretório atual. Neste arquivo contém a estrutura do directório seguintes:

* implementações - um registo das informações sobre implementações da aplicação

* Ficheiros de registo

    * [Método de implementação](web-sites-deploy.md) , se utilizar um método de implementação como Git, um diretório do mesmo nome, será criado e irá conter informações relacionadas com implementações.

    * nodejs - informações Stdout e stderr capturados a partir de todas as instâncias da sua aplicação (quando loggingEnabled é verdadeira.)

###<a name="live-stream-tail"></a>Fluxo direto (cauda)

Para ver um fluxo direto de informações do registo de diagnóstico, utilize o seguinte comando Azure da linha de comandos das ferramentas de:

    azure site log tail [sitename]

Isto irá devolver uma sequência de eventos de registo que são atualizadas medida que estes ocorrem no servidor. Esta sequência irá devolver informações de implementação, bem como informações stdout e stderr (quando loggingEnabled é verdadeira.)

<a id="nextsteps"></a>
## <a name="next-steps"></a>Próximos passos

Neste artigo o que aprendeu como ativar e aceder a informações de diagnóstico para Azure. Enquanto esta informação é útil em problemas Noções sobre que ocorram com a aplicação, pode apontar para um problema com um módulo que está a utilizar ou a versão do Node.js utilizado por aplicação de serviço Web Apps é diferente do AnonymousUserName a que utilizou no seu ambiente de implementação.

Para obter informações em trabalhar com módulos no Azure, consulte o artigo [Utilizar módulos Node.js com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md).

Para obter informações sobre como especificar uma versão Node.js para a sua aplicação, consulte o artigo [especificando uma versão Node.js numa aplicação do Azure].

Para obter mais informações, consulte também o [Centro de programadores do Node.js](/develop/nodejs/).

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

[IISNode]: https://github.com/tjanczuk/iisnode
[Leia-me do IISNode]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Especificar uma versão Node.js numa aplicação do Azure]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
