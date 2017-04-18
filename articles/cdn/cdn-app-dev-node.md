<properties
    pageTitle="Introdução ao Azure CDN SDK para Node.js | Microsoft Azure"
    description="Saiba como escrever Node.js aplicações para gerir Azure CDN."
    services="cdn"
    documentationCenter="nodejs"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introdução ao desenvolvimento do Azure CDN

> [AZURE.SELECTOR]
- [NODE.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Pode utilizar o [Azure CDN SDK para Node.js](https://www.npmjs.com/package/azure-arm-cdn) para automatizar a criação e gestão de perfis CDN e os pontos finais.  Neste tutorial explica a criação de uma aplicação de consola Node.js simple que demonstra diversas as operações disponíveis.  Neste tutorial não se destina para descrever todos os aspetos do Azure CDN SDK para Node.js detalhadamente.

Para concluir este tutorial, que já deve ter [Node.js](http://www.nodejs.org) **4.x.x** ou superior instalado e configurado.  Pode utilizar qualquer editor de texto que pretende criar a sua aplicação Node.js.  Para escrever neste tutorial, posso utilizado [Código do Visual Studio](https://code.visualstudio.com).  

> [AZURE.TIP] A [conclusão do projeto a partir deste tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) está disponível para transferência no MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Criar o seu projeto e adicionar NPM dependências

Agora que recomendamos ter criado um grupo de recursos para os nossos perfis CDN e tendo em conta os nossos permissão de aplicação do Azure AD para gerir perfis de CDN e os pontos finais dentro do grupo, podemos podem começar a criar a nossa aplicação.

Crie uma pasta para armazenar a sua aplicação.  A partir de uma consola com as ferramentas de Node.js no seu caminho actual, definir a localização atual para esta nova pasta e iniciar o projeto através da execução:
    
    npm init
    
Em seguida, será apresentada uma série de perguntas para iniciar o projeto.  Para o **ponto de entrada**, este tutorial, utiliza *app.js*.  Pode ver a minha outras opções no seguinte exemplo.

![NPM inicialização saída](./media/cdn-app-dev-node/cdn-npm-init.png)

Os nossos projeto está agora inicializado com um ficheiro de *packages.json* .  Os nossos projeto vai utilizar algumas bibliotecas Azure contidas pacotes NPM.  Vamos utilizar o tempo de execução do cliente do Azure para Node.js (ms-resto azure) e a biblioteca de cliente do Azure CDN para Node.js (cd de processador de azure).  Vamos adicioná-los ao projeto como dependências.
 
    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Depois de terminar os pacotes de instalação, o *package.json* ficheiro deverá ter o aspeto semelhante a este exemplo (versão números podem ser diferentes):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Por fim, utilizando o editor de texto, crie um ficheiro de texto em branco e guarde-o na raiz da pasta nosso projeto como *app.js*.  Vamos agora está prontos começar a escrever código.

## <a name="requires-constants-authentication-and-structure"></a>Exigir, constantes, autenticação e estrutura

Com *app.js* aberto no nosso editor, vamos a estrutura básica do nosso programa escrito.

1. Adicione o "requer" para os nossos pacotes NPM na parte superior com o seguinte:

    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```

2. Precisamos de definir algumas constantes que irão utilizar os nossos métodos.  Adicione o seguinte.  Certifique-se de que substitua os marcadores de posição, incluindo o ** &lt;parênteses em ângulo&gt;**, com os seus próprios valores conforme necessário.

    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";

    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. Em seguida, vamos criar uma instância o cliente de gestão de CDN e dar-lhe os nossos credenciais.

    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
    
    Se estiver a utilizar autenticação de utilizador individual, estas duas linhas serão o aspeto ligeiramente diferentes.

    >[AZURE.IMPORTANT] Utilize este exemplo de código apenas se estiver a escolher ter autenticação de utilizador individual em vez de um serviço principal.  Tenha cuidado para proteger as suas credenciais de utilizador individual e mantê-las secreta.

    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```

    Certifique-se substituir os itens na ** &lt;parênteses em ângulo&gt; ** com as informações corretas.  Para `<redirect URI>`, utilize o redirecionamento URI que introduziu quando registado a aplicação no Azure AD.
    

4.  Nossa aplicação de consola Node.js vai demorar alguns parâmetros da linha de comandos.  Vamos validar que pelo menos um parâmetro foi passado.

    ```javascript
    //Collect command-line parameters
    var parms = process.argv.slice(2);

    //Do we have parameters?
    if(parms == null || parms.length == 0)
    {
        console.log("Not enough parameters!");
        console.log("Valid commands are list, delete, create, and purge.");
        process.exit(1);
    }
    ```

5. Que dá-na parte principal do nosso programa, onde podemos ramificar outras funções com base no que parâmetros foram passados.

    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;

        case "create":
            cdnCreate();
            break;
        
        case "delete":
            cdnDelete();
            break;

        case "purge":
            cdnPurge();
            break;

        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```

6.  Em vários locais no nosso programa, precisamos de Certifique-se o número de à direita de parâmetros foram transmitido e apresenta ajuda se não aspeto corretos.  Vamos criar funções para o fazer.

    ```javascript
    function requireParms(parmCount) {
        if(parms.length < parmCount) {
            usageHelp(parms[0].toLowerCase());
            process.exit(1);
        }
    }

    function usageHelp(cmd) {
        console.log("Usage for " + cmd + ":");
        switch(cmd)
        {
            case "list":
                console.log("list profiles");
                console.log("list endpoints <profile name>");
                break;

            case "create":
                console.log("create profile <profile name>");
                console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
                break;
            
            case "delete":
                console.log("delete profile <profile name>");
                console.log("delete endpoint <profile name> <endpoint name>");
                break;

            case "purge":
                console.log("purge <profile name> <endpoint name> <path>");
                break;

            default:
                console.log("Invalid command.");
        }
    }
    ```

7. Por fim, as funções que iremos utilizar no cliente de gestão de CDN estão assíncronas, pelo que precisam de um método para ligar novamente quando estes terminado.  Vamos tornar uma que pode apresentar o resultado a partir do cliente de gestão CDN (se existir) e saia do programa correctamente.

    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Agora que a estrutura básica do nosso programa escrita, deverá criamos as funções chamadas com base no nossos parâmetros.

## <a name="list-cdn-profiles-and-endpoints"></a>Perfis CDN de lista e os pontos finais

Vamos começar com o código para listar os nossos perfis existentes e os pontos finais.  Meus comentários de código fornecem a sintaxe esperada, para que recomendamos saibam onde vai cada parâmetro.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Criar perfis de CDN e os pontos finais

Em seguida, podemos irá escrever as funções para criar perfis e os pontos finais.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Limpar um ponto final

Partindo do princípio de que foi criado o ponto final, a uma tarefa comuns que poderá queremos para executar no nosso programa é Limpar conteúdo no nosso ponto final.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar perfis CDN e os pontos finais

A função última que podemos irá incluir elimina perfis e os pontos finais.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Executar o programa

Vamos agora pode executar o nosso programa Node.js utilizando o nosso depurador favorito ou na consola.

> [AZURE.TIP] Se estiver a utilizar o código do Visual Studio como o depurador, terá de configurar o seu ambiente para passar nos parâmetros da linha de comandos.  Código do Visual Studio faz isto no ficheiro **lanuch.json** .  Procure uma propriedade denominada **argumentos** e adicionar uma matriz de valores de cadeia para os parâmetros de, para que o mesmo aspeto semelhante a este: `"args": ["list", "profiles"]`.

Vamos começar por listar os nossos perfis.

![Perfis de lista](./media/cdn-app-dev-node/cdn-list-profiles.png)

Vamos novamente tem uma matriz vazia.  Uma vez que não temos quaisquer perfis no nosso grupo de recursos, que é esperado.  Vamos criar um perfil agora.

![Criar perfil](./media/cdn-app-dev-node/cdn-create-profile.png)

Agora, vamos adicionar um ponto final.

![Criar o ponto final](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Por fim, vamos eliminar nosso perfil.

![Eliminar o perfil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Próximos passos

Para ver concluído projeto a partir destas instruções, [Transfira a amostra](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Para ver a referência para o SDK do Azure CDN para Node.js, visualize a [referência](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Para localizar documentação adicional sobre o SDK do Azure para Node.js, ver a [referência completa](http://azure.github.io/azure-sdk-for-node/).

Gerir os seus recursos CDN com [PowerShell](./cdn-manage-powershell.md).

