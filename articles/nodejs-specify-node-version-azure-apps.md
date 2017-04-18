<properties
    pageTitle="Especificar uma versão Node.js"
    description="Obter informações sobre como especificar a versão do Node.js utilizado pelo Azure Web Sites e serviços em nuvem"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Especificar uma versão Node.js numa aplicação do Azure

Quando a alojar uma aplicação de Node.js, poderá querer para se certificar de que a aplicação utiliza uma versão específica do Node.js. Existem várias formas para realizar esta tarefa para aplicações alojadas no Azure.

##<a name="default-versions"></a>Versões predefinidas

As versões de Node.js fornecidas pela Azure são constantemente atualizadas. Salvo caso contrário, a versão predefinida especificado na `WEBSITE_NODE_DEFAULT_VERSION` variável de ambiente será utilizada. Para substituir este valor predefinido, siga os passos nas secções seguintes, deste artigo

> [AZURE.NOTE] Se estiver a alojar sua aplicação de um serviço em nuvem Azure (função web ou trabalhador) e é a primeira vez que tenham implementado a aplicação, o Azure tentará utilizar a mesma versão do Node.js como tiver instalado no seu ambiente de desenvolvimento se que corresponda a uma das versões predefinido disponíveis no Azure.

##<a name="versioning-with-packagejson"></a>Controlo de versões com package.json

Pode especificar a versão do Node.js para ser utilizado, adicionando o seguinte para o seu ficheiro **package.json** :

    "engines":{"node":version}

Onde *versão* é o número de versão específica a utilizar. Pode pode especificar condições mais complexas para versão, tais como:

    "engines":{"node": "0.6.22 || 0.8.x"}

Uma vez que 0.6.22 não for uma das versões disponíveis no ambiente de alojamento, a versão mais recente de 0,8 série que está disponível será utilizado - 0.8.4.

##<a name="versioning-websites-with-app-settings"></a>Sites públicos do controlo de versões com as definições da aplicação
Se estiver a alojar a aplicação num Web site, pode definir a variável de ambiente **WEBSITE_NODE_DEFAULT_VERSION** para a versão pretendida. 

##<a name="versioning-cloud-services-with-powershell"></a>Controlo de versões serviços em nuvem com o PowerShell

Se estiver a alojar a aplicação de um serviço na nuvem e estiver a implementar a aplicação através do Azure PowerShell, pode substituir a versão de Node.js predefinida utilizando o cmdlet do PowerShell **AzureServiceProjectRole conjunto** . Por exemplo:

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Tenha em atenção que os parâmetros na instrução acima são entre maiúsculas e minúsculas.  Pode verificar que a versão correta do Node.js tiver sido seleccionada marcando a propriedade **motores** na **package.json sua função**.

Também pode utilizar a **Obter AzureServiceProjectRoleRuntime** para obter uma lista de versões Node.js disponíveis para aplicações alojadas como um serviço na nuvem.  Verificar sempre a versão do Node.js depende do seu projeto está nesta lista.

##<a name="using-a-custom-version-with-azure-websites"></a>Utilizar uma versão personalizada com sites públicos do Azure

Enquanto Azure fornece várias versões de predefinidas do Node.js, poderá querer utilizar uma versão que não seja fornecida por predefinição. Se a sua aplicação está alojada como um site do Azure, pode fazê-lo utilizando o ficheiro **iisnode.yml** . Os passos seguintes guiá-lo durante o processo de utilizar uma versão personalizada do Node.Js com um Web site Azure:

1. Criar um novo directório e, em seguida, crie um ficheiro de **server.js** no diretório. O ficheiro **server.js** deve conter o seguinte:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Isto vai mostrar a versão de Node.js a ser utilizada quando navega o Web site.

2. Criar um novo site e tome nota do nome do site. Por exemplo, o seguinte utiliza as [Ferramentas de linha de comandos Azure] para criar um novo site Azure **meuwebsite**com o nome e, em seguida, ativar um repositório de Git para o Web site.

        azure site create mywebsite --git

3. Crie um novo directório denominado **posição** como um subordinado do diretório que contém o ficheiro **server.js** .

4. Transfira a versão específica do **node.exe** (a versão do Windows) que pretende utilizar com a aplicação. Por exemplo, o seguinte utiliza **curl** para transferir a versão 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Guarde o ficheiro **node.exe** para a pasta de **posição** que criou anteriormente.

5. Criar um ficheiro de **iisnode.yml** no mesmo directório que o ficheiro **server.js** e, em seguida, adicione o seguinte conteúdo para o ficheiro **iisnode.yml** :

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Este caminho é onde o ficheiro **node.exe** dentro do seu projeto será localizado uma vez que publicou a sua aplicação para o Web site Azure.

6. Publica a sua aplicação. Por exemplo, uma vez que criei um novo Web site com o parâmetro – git anterior, os seguintes comandos irão adicionar os ficheiros da aplicação para o meu repositório Git local e, em seguida, transmiti-los para o repositório de Web site:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    Depois do pedido foi publicado, abra o Web site num browser. Deverá visualizar uma mensagem a indicar "Olá a partir do Azure execução da versão nó: v0.8.1".

##<a name="next-steps"></a>Próximos passos

Agora que a compreender como especificar a versão de Node.js utilizado pela sua aplicação, saiba como [trabalhar com módulos], [criar e implementar um Web Site Node.js]e [como utilizar as ferramentas de linha de comandos do Azure para Mac e o Linux].

Para mais informações, consulte o [Centro de programadores do Node.js](/develop/nodejs/).

[Como utilizar as ferramentas de linha de comandos do Azure para Mac e o Linux]: xplat-cli-install.md
[Ferramentas de linha de comandos Azure]: xplat-cli-install.md
[trabalhar com módulos]: nodejs-use-node-modules-azure-apps.md
[criar e implementar um Web Site Node.js]: web-sites-nodejs-develop-deploy-mac.md
