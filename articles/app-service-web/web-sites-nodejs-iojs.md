<properties 
    pageTitle="Como utilizar io.js Azure aplicação de serviço Web Apps" 
    description="Saiba como utilizar uma aplicação web na aplicação de serviço de Azure com io.js." 
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
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Como utilizar io.js Azure aplicação de serviço Web Apps

O populares nó bifurcação [io.js] funcionalidades de vários diferenças ao projeto de Node.js do Joyent, incluindo um modelo de governação mais aberto, um ciclo de disponibilização mais rápido e uma adoção mais rápida das funcionalidades de JavaScript novas e a experimentais.

Enquanto [Azure aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps tem muitas versões de Node.js pré-instalado, também permite para um ficheiro binário Node.js fornecidos pelo utilizador. Este artigo aborda dois métodos para ativar a utilização de io.js na aplicação de serviço Web Apps: A utilização de um script de implementação adicional, que configura automaticamente Azure para utilizar a versão mais recente do io.js disponível, bem como o carregamento de um io.js binário manual. 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>Utilizar um Script de implementação

Após a implementação de uma aplicação do Node.js, a aplicação de serviço Web Apps é executado um número de pequenas comandos para se certificar de que o ambiente está configurado corretamente. Utilizar um script de implementação, este processo pode ser personalizado para incluir a transferência e configuração do io.js.

O [io.js Script de implementação](https://github.com/felixrieseberg/iojs-azure) está disponível no GitHub. Para ativar io.js na sua aplicação web, basta copiar **.deployment**, **deploy.cmd** e **IISNode.yml** na raiz da sua pasta de aplicação e implemente Web Apps.  

O primeiro ficheiro, **.deployment**, instrui Web Apps para executar **deploy.cmd** após a implementação. Este script é executado todos os passos para uma aplicação Node.js habituais, mas também transfere a versão mais recente do io.js. Por fim, **IISNode.yml** configura Web Apps utilizar apenas o io.js transferido binário em vez de um binário Node.js pré-instalado.

> [AZURE.NOTE] Para atualizar o ficheiro binário io.js utilizados, basta implementar novamente a sua aplicação - o script irá transferir uma versão nova do io.js sempre única que a aplicação é implementada.

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>Utilizar instalação Manual

A instalação manual de uma versão io.js personalizado inclui apenas dois passos. Em primeiro lugar, transfira **win x64** binário diretamente a partir da [distribuição de io.js]. Obrigatório são dois ficheiros - **iojs.exe** e **iojs.lib**. Guarde ambos os ficheiros numa pasta dentro da sua aplicação web, por exemplo na **Reciclagem/iojs**.

Para configurar o Web Apps utilizar **iojs.exe** em vez de uma versão de nó pré-instalada, crie um ficheiro de **IISNode.yml** na raiz da sua aplicação e adicione a linha seguinte.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>Próximos passos

Neste artigo que aprendeu como utilizar o io.js com a aplicação de serviço Web Apps, utilizar ambos fornecidos scripts de implementação instalação manual, assim como. 

> [AZURE.NOTE] IO.js está em desenvolvimento frequente e actualizado mais frequentemente Node.js. Um número de Node.js módulos poderão não funcionar em io.js - fórum consultar [io.js no GitHub] para resolução de problemas.

## <a name="whats-changed"></a>O que é alterado
* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação web do starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

[IO.js]: https://iojs.org
[distribuição IO.js]: https://iojs.org/dist/
[IO.js no GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 