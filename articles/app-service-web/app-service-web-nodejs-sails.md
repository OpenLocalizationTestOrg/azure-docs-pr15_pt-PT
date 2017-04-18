<properties
    pageTitle="Implementar uma aplicação web do Sails.js ao serviço de aplicação do Azure"
    description="Saiba como implementar uma aplicação de Node.js Azure aplicação de serviço. Este tutorial mostra como implementar uma aplicação web do Sails.js."
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
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="cephalin"/>

# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Implementar uma aplicação web do Sails.js ao serviço de aplicação do Azure

Este tutorial mostra como implementar uma aplicação de Sails.js a aplicação de serviço de Azure. Durante o processo, pode glean alguns conhecimentos gerais sobre como configurar a sua aplicação Node.js para executar na aplicação de serviço. 

Que deve ter conhecimento trabalho Sails.js. Neste tutorial não se destina a ajudá-lo com problemas relacionados com a execução Sail.js em geral.


## <a name="prerequisites"></a>Pré-requisitos

- [NODE.js](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [Git](http://www.git-scm.com/downloads)
- [Clip Azure](../xplat-cli-install.md)
- Uma conta do Microsoft Azure. Se não tiver uma conta, pode [Inscrever-se para uma avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios da sua Visual Studio subscritor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Para ver a aplicação de serviço de Azure em ação antes de inscrever-se para uma conta do Azure, aceda ao [Serviço de aplicação tente](http://go.microsoft.com/fwlink/?LinkId=523751). Não existem, imediatamente pode criar uma aplicação starter curto na aplicação de serviço — sem cartão de crédito obrigatório, sem compromissos.

## <a name="step-1-create-a-sailsjs-app-locally"></a>Passo 1: Criar uma aplicação Sails.js localmente

Em primeiro lugar, crie rapidamente uma aplicação de Sails.js predefinido no seu ambiente de desenvolvimento seguindo estes passos:

1. Abra o terminal da linha de comandos da sua preferência e `CD` para um diretório de trabalho.

2. Criar uma aplicação de Sails.js e executá-la:

        sails new <appname>
        cd <appname>
        sails lift

    Certifique-se de que pode navegar para a home page predefinida na http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Passo 2: Criar o recurso de aplicação Azure

Em seguida, crie o recurso de aplicação de serviço no Azure. Vai implemente a sua aplicação Sails.js ao mesmo mais tarde.

1. iniciar sessão no Azure gosto por isso:
1. No mesmo terminal, alterar o modo de ASM e iniciar sessão no Azure:

        azure config mode asm
        azure login

    Siga a linha de comandos para continuar o início de sessão num browser com uma conta Microsoft que tem a sua subscrição do Azure.

2. Certifique-se de que ainda se encontrarem no diretório de raiz do seu projeto Sails.js. Crie o recurso de aplicação de serviço de aplicação no Azure com um nome de aplicação exclusiva com o comando seguinte. URL do web app é http://&lt;nome >. azurewebsites.net.

        azure site create --git <appname>

    Siga a linha de comandos para selecionar uma região Azure para implementar. Se nunca configurou credenciais de implementação de Git/FTP para a sua subscrição Azure, também será solicitado criá-los.

    Assim que um recurso de aplicação de serviço de aplicação é criado:

    - Aplicação de Sails.js está inicializado Git
    - Do repositório inicializado Git local está ligado para a nova aplicação de serviço de aplicação como um Git remoto, aptly denominada "azure", e
    - E iisnode.yml ficheiro é criado no seu diretório de raiz. Pode utilizar este ficheiro para configurar [iisnode](https://github.com/tjanczuk/iisnode), que utiliza de aplicação de serviço para executar Node.js aplicações.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Passo 3: Configurar e implementar a sua aplicação Sails.js

 Trabalhar com uma aplicação de Sails.js na aplicação de serviço é constituído por três passos principais:

 - Configurar a sua aplicação para o mesmo para executar na aplicação de serviço
 - Implementá-lo para a aplicação de serviço
 - Leitura registos stderr e stdout para resolver quaisquer problemas de implementação

Siga estes passos:

1. Abra o novo ficheiro de iisnode.yml no seu diretório de raiz e adicione as duas linhas seguintes:

        loggingEnabled: true
        logDirectory: iisnode

    Registo agora está ativado para iisnode. Para mais informações sobre como isto funciona, consulte o artigo  [obter registos stdout e stderr a partir do iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Abrir config/env/production.js para configurar o seu ambiente de produção e definir `port` e `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Pode encontrar documentação para estas definições de configuração na  [Documentação Sails.js](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Em seguida, é necessário para se certificar de que [roncador](https://www.npmjs.com/package/grunt) é compatível com unidades de rede do Azure. Versões de roncador menor que 1.0.0 utiliza um pacote de Desatualizadas [glob](https://www.npmjs.com/package/glob) (inferior a 5.0.14), não suporta unidades de rede. 

3. Abra package.json e altere o `grunt` versão para `1.0.0` e remova todos os `grunt-*` pacotes. O `dependencies` propriedade deve ter este aspeto:

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. No package.json, adicione o seguinte `engines` propriedade para configurar a versão de Node.js para uma que pretendemos.

        "engines": {
            "node": "6.6.0"
        },

6. Guardar as suas alterações e experimentar as alterações para se certificar de que a aplicação ainda é executada localmente. Para fazer isto, elimine o `node_modules` pasta e, em seguida, executar:

        npm install
        sails lift

4. Agora, utilize git para implementar a aplicação Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Por fim, basta inicie a aplicação Azure direto no browser:

        azure site browse

    Agora deverá ver mesmo Sails.js home page.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Resolver problemas de sua implementação

Se a sua aplicação Sails.js falhar por algum motivo na aplicação de serviço, localize os registos de stderr para ajudar a resolvê-lo.
Para mais informações, consulte o artigo [obter registos stdout e stderr a partir do iisnode](app-service-web-nodejs-sails.md#iisnodelog).
Se tiver iniciado com êxito, o registo de stdout deve mostrar a mensagem familiar:

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Pode controlar granularidade os registos de stdout no ficheiro [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Ligar a uma base de dados no Azure

Para ligar a uma base de dados no Azure, pode criar a base de dados da sua escolha no Azure, tal como a base de dados do SQL Azure, MySQL, MongoDB, Cache Azure (Redis), etc. e utilizar o correspondente [placa de arquivo de dados](https://github.com/balderdashy/sails#compatibility) para se ligar à mesma. Os passos nesta secção mostram-lhe como ligar a uma base de dados do MySQL no Azure.

1. Siga a tutoriais [aqui](../store-php-create-mysql-database.md) para criar uma base de dados do MySQL no Azure.

2. A partir do seu terminal da linha de comandos, instale a placa MySQL:

        npm install sails-mysql --save

3. Abra config/connections.js e adicionar o objeto de ligação seguinte à lista: 

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. Para cada variável de ambiente (`process.env.*`), tem de defini-lo na aplicação de serviço. Para fazer isto, execute os seguintes comandos a partir do seu terminal. Todas as informações de ligação que necessita são no portal do Azure (consulte o artigo [ligar a sua base de dados do MySQL](../store-php-create-mysql-database.md#connect)).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    As definições de colocação das definições da aplicação Azure mantém dados sensíveis a maiúsculas e terminar o controlo da origem (Git). Em seguida, irá configurar o seu ambiente de desenvolvimento para utilizar as mesmas informações de ligação.

4. Abra config/local.js e adicione o objeto de ligações seguintes:

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Esta configuração substitui as definições no seu ficheiro config/connections.js ambiente no local. Este ficheiro é excluído por .gitignore a predefinição no seu projeto, para que não serão armazenado no Git. Agora, está conseguir ligar à sua base de dados do MySQL da sua aplicação web do Azure e a partir do seu ambiente de desenvolvimento local.

4. Abra o config/env/production.js para configurar o seu ambiente de produção e adicione o seguinte `models` objeto:

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Abra o config/env/development.js para configurar o seu ambiente de desenvolvimento e adicione o seguinte `models` objeto:

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    `migrate: 'alter'`permite-lhe utilizar funcionalidades de migração de bases de dados para criar e atualizar as suas tabelas de base de dados no seu MySQL facilmente. No entanto, `migrate: 'safe'` é utilizado para o ambiente do Azure (produção) porque Sails.js não permite a utilização de `migrate: 'alter'` num ambiente de produção (consulte  [A documentação de Sails.js](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. A partir do terminal [Gerar](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) uma Sails.js [plano API](http://sailsjs.org/documentation/concepts/blueprints) como normalmente faria, em seguida, execute `sails lift` para criar a base de dados com Sails.js migração de base de dados. Por exemplo:

         sails generate api mywidget
         sails lift

    O `mywidget` modelo gerado por este comando está vazio, mas recomendamos pode utilizá-lo para mostrar que temos conectividade de base de dados.
    Quando executa `sails lift`, que cria as tabelas em falta para os modelos da aplicação utiliza.

6. Aceda a planta API que acabou de criar no browser. Por exemplo:

        http://localhost:1337/mywidget/create
    
    API deve devolver a entrada criada lhe na janela do browser, o que significa que a base de dados é criado com êxito.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. Agora, transmitir as suas alterações para Azure e navegue para a sua aplicação para se certificar de que continua a funcionar.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Aceda o API plano da sua aplicação Azure web. Por exemplo:

        http://<appname>.azurewebsites.net/mywidget/create

    Se a API devolve outra nova entrada, em seguida, a aplicação Azure web está a falar para a base de dados do MySQL.

## <a name="more-resources"></a>Mais recursos

- [Introdução ao Node.js web apps no serviço de aplicação do Azure](app-service-web-nodejs-get-started.md)
- [Utilização Node.js módulos com aplicações do Azure](../nodejs-use-node-modules-azure-apps.md)
