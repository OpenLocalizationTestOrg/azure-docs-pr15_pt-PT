<properties
    pageTitle="Aplicação de node.js API na aplicação de serviço de Azure | Microsoft Azure"
    description="Saiba como criar uma API RESTful Node.js e implementá-lo para uma aplicação do API na aplicação de serviço de Azure."
    services="app-service\api"
    documentationCenter="node"
    authors="bradygaster"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="node"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="rachelap"/>

# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Criar uma API RESTful Node.js e implementá-lo para uma aplicação de API no Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial mostra como criar uma simples [Node.js](http://nodejs.org) API e implementá-lo para uma [aplicação API](app-service-api-apps-why-best-platform.md) na [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md) utilizando [Git](http://git-scm.com). Pode utilizar qualquer sistema operativo que pode executar Node.js e terá de fazer todo o seu trabalho com ferramentas de linha de comandos como cmd.exe ou festa.

## <a name="prerequisites"></a>Pré-requisitos

1. Conta do Microsoft Azure ([Abrir uma conta gratuita aqui](https://azure.microsoft.com/pricing/free-trial/))
1. [Node.js](http://nodejs.org) instalado (neste exemplo assume que tem Node.js versão 4.2.2)
2. [Git](https://git-scm.com/) instalado
1. Conta [GitHub](https://github.com/)

Enquanto a aplicação de serviço de suportar as várias formas para implementar o seu código a uma aplicação do API, este tutorial mostra o método de Git e assume que tem conhecimento básico de como trabalhar com Git. Para obter informações sobre outros métodos de implementação, consulte o artigo [Implementar a aplicação para a aplicação de serviço de Azure](../app-service-web/web-sites-deploy.md).

## <a name="get-the-sample-code"></a>Obter o código de exemplo

1. Abra uma interface de linha de comandos que podem ser executados comandos Node.js e Git.

1. Navegar para uma pasta que pode utilizar para um repositório de Git local e clonar o [GitHub repositório que contém o código de exemplo](https://github.com/Azure-Samples/app-service-api-node-contact-list).

        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

    A API do exemplo fornece dois pontos finais: num pedido Get para `/contacts` devolve uma lista de nomes e endereços de e-mail no formato JSON, enquanto `/contacts/{id}` devolve apenas o contacto selecionado.

## <a name="scaffold-auto-generate-nodejs-code-based-on-swagger-metadata"></a>Scaffold (gerar automática) Node.js código com base em Swagger metadados

[Swagger](http://swagger.io/) é um formato de ficheiro para os metadados que descreve um API RESTful. Aplicação de serviço de Azure tem [suporte incorporado para Swagger metadados](app-service-api-metadata.md). Esta secção iniciação de modelos de um API desenvolvimento fluxo de trabalho na qual criar primeiro Swagger metadados e utilizá-la para scaffold (gerar automática) código do servidor para a API. 

>[AZURE.NOTE] Pode ignorar esta secção se não quiser saber como scaffold Node.js código de um ficheiro de metadados Swagger. Se pretende apenas implementar código de exemplo para uma nova aplicação API, aceda diretamente para a secção de [criar uma aplicação do API no Azure](#createapiapp) .

### <a name="install-and-execute-swaggerize"></a>Instalar e executar Swaggerize

1. Execute os seguintes comandos para instalar a **yo** e **swaggerize gerador** NPM módulos globalmente.

        npm install -g yo
        npm install -g generator-swaggerize

    Swaggerize é uma ferramenta que gera código do servidor para uma API descrita por um ficheiro de metadados Swagger. O ficheiro de Swagger que irá utilizar com o nome *api.json* e está localizado na pasta do repositório que clonar *Iniciar* .

2. Navegue para a pasta *Iniciar* e, em seguida, execute o `yo swaggerize` comando. Swaggerize irá pedir uma série de perguntas.  De **que ligar a este projeto**, introduza "Listaconts.", para o **caminho para swagger documento**, introduza "api.json" e para **Express, satisfeito, ou Restify**, introduza "express".

        yo swaggerize

    ![Swaggerize linha de comandos](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
    **Nota**: se se deparar com um erro neste passo, o passo seguinte explica como corrigi-lo.

    Swaggerize cria uma pasta de aplicações, andaimes processadores e ficheiros de configuração e gera um ficheiro de **package.json** . O motor de vista express é utilizado para gerar a página de ajuda Swagger.  

3. Se o `swaggerize` comando falhar com uma "token inesperado" ou "sequência de escape inválido" um erro, corrigir o problema do erro ao editar o ficheiro gerado *package.json* . No `regenerate` linha em `scripts`, alterar a barra invertida que antecede *api.json* para uma barra, para que a linha de parece semelhante ao exemplo seguinte:

        "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Navegue para a pasta que contém o scaffolded código (neste caso, a subpasta */start/ContactList* ).

1. Executar `npm install`.
    
        npm install
        
2. Instale o módulo NPM **jsonpath** . 

        npm install --save jsonpath
        
    ![Instalar Jsonpath](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Instale o módulo NPM de **IU swaggerize** . 

        npm install --save swaggerize-ui
        
    ![Swaggerize instalação da IU](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### <a name="customize-the-scaffolded-code"></a>Personalizar o código scaffolded

1. Copie a pasta de **biblioteca** a partir da pasta **Iniciar** para a pasta de **Listaconts** criada pelo scaffolder. 

1. Substitua o código no ficheiro **handlers/contacts.js** com o código seguinte. 

    Este código utiliza os dados JSON armazenados no ficheiro **lib/contacts.json** que é fornecido pelo **lib/contactRepository.js**. O novo código de contacts.js responde a pedidos HTTP para todos os contactos e devolvê-los como uma carga útil JSON. 

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Substitua o código no ficheiro **handlers/contacts/{id}.js** com o código de fofllowing. 

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Substitua o código no **server.js** com o código seguinte. 

    As alterações efetuadas ao ficheiro server.js são realçadas utilizando comentários para que possa ver as alterações efetuadas a ser. 

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

### <a name="test-with-the-api-running-locally"></a>Testar com a API a ser executado localmente

1. Ative o servidor utilizando o executável da linha de comandos Node.js. 

        node server.js

1. Procurar a **http://localhost:8000/contactos**, verá a saída JSON da lista de contacto (ou lhe for pedido para transferi-lo, dependendo do seu browser). 

    ![Todos os contactos Api chamada](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Quando procurar **contactos/http://localhost:8000/2**, verá o contacto representado por esse valor de id.

    ![Chamada à Api contacto específico](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Os dados Swagger JSON são servidos via o ponto **final/swagger** :

    ![Swagger Json de contactos](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. IU de Swagger é fornecido através do ponto final **/docs** . Na IU Swagger, pode utilizar as funcionalidades de cliente HTML formatadas para testar a sua API.

    ![IU swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a>Criar uma nova aplicação API

Nesta secção que utilizar o portal do Azure para criar uma nova aplicação API no Azure. Esta aplicação API representa os recursos de cluster Azure irá fornecer para executar o seu código. Nas secções posteriores irá implementar o seu código para a nova aplicação API.

1. Navegue para o [Azure Portal](https://portal.azure.com/). 

1. Clique em **Novo > Web + Mobile > API aplicação**. 

    ![Nova aplicação API no portal](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Introduza um **nome da aplicação** que seja exclusivo no domínio *azurewebsites.net* , tal como NodejsAPIApp assim como um número para o tornar exclusivo. 

    Por exemplo, se o nome é `NodejsAPIApp`, o URL será `nodejsapiapp.azurewebsites.net`.

    Se introduzir um nome que outra pessoa já tenha utilizado, verá um ponto de exclamação vermelho para a direita.

6. No menu pendente **Grupo de recursos** , clique em **Novo**e, em seguida, no **nome do novo grupo recurso** introduza "NodejsAPIAppGroup" ou outro nome se preferir. 

    Um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) é um conjunto de recursos Azure, tais como as aplicações, bases de dados e VMs API. Para este tutorial, é melhor criar um novo grupo de recursos, porque é que o torna mais fácil eliminar num único passo todos os recursos Azure criados para o tutorial.

4. Clique **Plano/localização do serviço de aplicação**e, em seguida, clique em **Criar novo**.

    ![Criar plano de serviço de aplicação](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

    Os passos seguintes, pode criar um plano de serviço de aplicação para o novo grupo de recursos. Um plano de serviço de aplicação Especifica os recursos de cluster a sua aplicação API é executada no. Por exemplo, se escolher a camada livre, a aplicação de API é executada no VMs partilhados, enquanto para alguns camadas pagas é executado no VMs dedicadas. Para obter informações sobre os planos de aplicação de serviço, consulte o artigo [Descrição geral de planos do serviço de aplicação](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Na pá **Plano de serviço de aplicação** , introduza "NodejsAPIAppPlan" ou outro nome se preferir.

5. Na lista pendente de **localização** , escolha a localização que esteja mais próxima para si.

    Esta definição especifica que Centro de dados Azure a sua aplicação será executada em. Para este tutorial, pode selecionar qualquer região e -não fazer uma diferença evidente. Mas, para uma aplicação de produção, pretender que o servidor para ser o mais próximo possível aos clientes de que estão a aceder ao-lo para minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Clique em **preços camadas > ver todos > F1 gratuito**.

    Para este tutorial, a camada comparar gratuita irá fornecer o desempenho suficiente.

    ![Selecione libertar preços de camadas](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. Na pá **Plano de serviço de aplicação** , clique em **OK**.

7. Na pá **API aplicação** , clique em **Criar**.

## <a name="set-up-your-new-api-app-for-git-deployment"></a>Configurar a sua nova aplicação de API para Git implementação

Irá implemente o código para a aplicação de API ao conduza consolidadas para um repositório de Git na aplicação de serviço de Azure. Nesta secção do tutorial, crie as credenciais e Git repositório no Azure que irá utilizar para implementação.  

1. Depois de ter sido criada a sua aplicação API, clique em **Serviços de aplicação > {sua aplicação API}** na home page do portal. 

    O portal apresenta as lâminas **API App** e **Definições** .

    ![Aplicação do portal API e pá definições](media/app-service-api-nodejs-api-app/portalapiappblade.png)

1. Na pá **Definições** , desloque para baixo para a secção de **publicação** e, em seguida, clique em **implementação credenciais**.
 
3. Na pá **Definir credenciais de implementação** , introduza um nome de utilizador e palavra-passe e, em seguida, clique em **Guardar**.

    Irá utilizar estas credenciais para publicar o seu código Node.js para a sua aplicação API. 

    ![Credenciais de implementação](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. No pá **Definições** , clique em **origem da implementação > Escolha da origem > Local Git repositório**, em seguida, clique em **OK**.

    ![Criar Git Repo](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Depois do repositório Git foi criado com as alterações de pá para mostrar-lhe as implementações ativas. Uma vez que o repositório há de novo, tiver sem implementações ativas na lista. 

    ![Sem implementações ativas](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Copie o URL do repositório de Git. Para fazer isto, navegue para a pá para a sua nova aplicação de API e observe a secção **Essentials** a pá. Repare que o **URL de clonar Git** na secção **Essentials** . Quando passar o rato sobre este URL, verá um ícone à direita que irá copiar o URL para sua área de transferência. Clique neste ícone para copiar o URL.

    ![Obter o Url de Git a partir do Portal](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Nota**: É necessário o clonar Git URL na secção seguinte, por isso, certifique-se para guardá-lo num local para o momento em que.

Agora que tem uma aplicação de API com um repositório de Git cópias de segurança-la, pode emissão código para o repositório para implementar o código para a aplicação de API. 

## <a name="deploy-your-api-code-to-azure"></a>Implementar o seu código API em Azure

Nesta secção, crie um repositório de Git local que contém o código do servidor para a API e, em seguida, emissão seu código a partir desse repositório para o repositório no Azure que criou anteriormente.

1. Copiar o `ContactList` pasta para uma localização que pode utilizar para um repositório de Git novo local. Caso tenha gravado a primeira parte do tutorial, copie `ContactList` a partir do `start` pasta; caso contrário, copie `ContactList` a partir do `end` pasta.

1. Na sua ferramenta de linha de comandos, navegue para a nova pasta, em seguida, execute o seguinte comando para criar um repositório de Git novo local. 

        git init

     ![Novo Git Repo Local](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Execute o seguinte comando para adicionar um Git remoto para repositório da sua aplicação API. 

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Nota**: substitua a cadeia "YOUR_GIT_CLONE_URL_HERE" a sua própria URL de clonar Git que copiou anteriormente. 

1. Execute os seguintes comandos para criar uma consolidação que contém todos os do código. 

        git add .
        git commit -m "initial revision"

    ![Consolidar Git saída](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Execute o comando para transmitir o seu código para Azure. Quando lhe for pedido para uma palavra-passe, introduza aquele que criou anteriormente no portal do Azure.

        git push azure master

    Isto accionadores uma implementação para a sua aplicação API.  

1. No seu browser, navegar de volta para o pá **implementações** para a sua aplicação API e verá a implementação está a ocorrer. 

    ![Implementação passa](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Em simultâneo, a interface de linha de comandos apresenta o estado da sua implementação, enquanto que se passa. 

    ![Nó Js implementação acontece](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

    Assim que tiver concluído a implementação, a pá **implementações** reflete a implementação com êxito das suas alterações de código para a sua aplicação API. 

## <a name="test-with-the-api-running-in-azure"></a>Testar com a API em execução no Azure
 
3. Copie o **URL** na secção de **Essentials** da sua pá API aplicação. 

    ![Implementação concluída](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Utilizar um cliente de REST API como Postman ou Fiddler (ou o browser), fornecer o URL dos seus contactos chamada à API, que é o `/contacts` ponto final da sua aplicação API. O URL será`https://{your API app name}.azurewebsites.net/contacts`

    Quando o problema num pedido GET para este ponto final, irá obter o resultado da JSON da sua aplicação API.

    ![Postman atingir Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. Num browser, vá para o `/docs` ponto final para experimentar a IU Swagger medida que é executada no Azure.

Agora que tem entrega contínua com fios para cima, pode efetuar alterações de código e implementá-los para Azure bastando para isso conduza consolidadas para o seu repositório Azure Git.

## <a name="next-steps"></a>Próximos passos

Neste momento com êxito já criou uma aplicação de API e implementado código Node.js API. Mostra o próximo tutorial como [consumir API aplicações a partir dos clientes JavaScript, utilizando CORS](app-service-api-cors-consume-javascript.md).
