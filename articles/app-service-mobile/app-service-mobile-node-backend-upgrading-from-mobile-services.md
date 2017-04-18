<properties
    pageTitle="Atualizar a partir de serviços móveis para o serviço de aplicação Azure - Node.js"
    description="Saiba como atualizar facilmente a sua aplicação de serviços móveis para uma aplicação do aplicação do serviço móvel"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Atualizar o serviço móvel do Azure de Node.js existente para o serviço de aplicação

Aplicação de serviço móvel é uma forma nova para criar aplicações móveis através do Microsoft Azure. Para obter mais informações, consulte o artigo [o que são aplicações Mobile?].

Este tópico descreve como atualizar uma aplicação de back-end Node.js existente a partir do Azure Mobile Services para uma nova aplicação Serviço móvel aplicações. Enquanto executar esta atualização, a aplicação de serviços móveis existente pode continuar a funcionar.  Se precisar de atualizar uma aplicação de back-end Node.js, consulte [atualizar os seus serviços de Mobile .NET](./app-service-mobile-net-upgrading-from-mobile-services.md).

Quando é atualizada back-end móvel para a aplicação de serviço de Azure, tem acesso a todas as funcionalidades de aplicação de serviço e são faturada de acordo com a [aplicação de serviço de preços], não os serviços de Mobile preços.

## <a name="migrate-vs-upgrade"></a>Migrar vs. atualização

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] É recomendado que [executar uma migração](app-service-mobile-migrating-from-mobile-services.md) antes de entrar através de uma atualização. Desta forma, pode colocar ambas as versões da sua aplicação no mesmo plano de serviço de aplicação e implicam sem custos adicionais.

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Melhorias no Mobile aplicações Node.js server SDK

Atualizar para o novo [Mobile aplicações SDK](https://www.npmjs.com/package/azure-mobile-apps) fornece muitas melhorias, incluindo:

- O SDK nó novos com base no [Express framework](http://expressjs.com/en/index.html), é mais simplificada e concebido para acompanhar novas versões de nó como provêm de. Pode personalizar o comportamento da aplicação com software Express intermédio.

- Melhoramentos significativos ao desempenho em comparação comparada o SDK de serviços móveis.

- Agora pode alojar um Web site juntamente com o seu back-end móvel; da mesma forma, é fácil adicionar o SDK do Azure Mobile a qualquer aplicação express.v4 existente.

- Criadas para local e em diferentes plataformas de desenvolvimento, o SDK de aplicações móveis pode ser desenvolvido e executar localmente plataformas Windows, Linux e os x. Agora, é fácil de utilizar técnicas de desenvolvimento nó comuns, como executar [Café Moca](https://mochajs.org/) testes antes de implementação.

## <a name="overview"></a>Descrição geral de atualização básica

Para ajudar a atualizar Node.js back-end, a aplicação de serviço de Azure disponibilizou um pacote de compatibilidade.  Após atualização, terá de um site de niew que pode ser implementado para um novo site de aplicação de serviço.

O cliente de serviços de Mobile SDK são **não** é compatível com o servidor de aplicações Mobile SDK novo. Para fornecer continuidade do serviço para a sua aplicação, que não deve publicá alterações a um site atualmente servir clientes publicados. Em vez disso, deverá criar uma nova aplicação móvel que serve um duplicado. Pode colocar esta aplicação no mesmo plano da aplicação de serviço para evitar sempre custo financeiro adicional.

Em seguida, terá de duas versões da aplicação: uma que permanecer na mesma e serve publicados aplicações numa silvestres e outros que, em seguida, pode atualizar e destino com uma versão nova do cliente. Pode mover e testar o seu código ao seu ritmo, mas deve certificar-se de que quaisquer correções de erros que efetuar são aplicadas ao ambos. Uma vez sentir-se que um número de aplicações no meio natural tem atualizadas para a versão mais recente do cliente pretendido, pode eliminar a aplicação migrada original se que pretende. -Não implicam quaisquer custos monetários adicionais, se alojado no mesmo plano da aplicação de serviço a aplicação Mobile.

O destaque completo para o processo de atualização é da seguinte forma:

1. Transferir o serviço de Mobile Azure (migrados) existente.
2. Converta o projeto para uma aplicação móvel do Azure utilizando o pacote de compatibilidade.
3. Corrija quaisquer diferenças (tais como as definições de autenticação).
4. Implemente o projeto de aplicação do Azure Mobile convertido para um novo serviço de aplicação.
4. Liberte uma nova versão da sua aplicação de cliente que utilizar a nova aplicação móvel.
5. (Opcional) Elimine a sua aplicação de serviço móvel migradas original.

Eliminação pode ocorrer quando não vir qualquer tráfego no seu serviço móvel do migrados original.

## <a name="install-npm-package"></a>Instalar as pré-requisitos

Deve instalar [Node] no seu computador local.  Também deve instalar o pacote de compatibilidade.  Depois de nó estiver instalado, pode executar o seguinte comando de uma nova cmd ou linha de comandos do PowerShell:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Obter os Scripts de serviços móveis Azure

- Inicie a sessão [Portal do Azure].
- Utilizar **Todos os recursos** ou **Serviços de aplicação**, encontre o seu site de serviços móveis.
- Dentro de um site, clique em **Ferramentas** -> **Kudu** -> **Ir** para abrir o site Kudu.
- Clique na **Consola depurar** -> **PowerShell** para abrir a consola de depuração.
- Navegue para `site/wwwroot/App_Data/config` clicando em cada directório sucessivamente
- Clique no ícone Transferir junto ao `scripts` diretório.

Isto irá transferir os scripts em formato ZIP.  Criar um novo directório no seu computador local e descompactar a `scripts.ZIP` ficheiro no diretório.  Esta opção criará uma `scripts` diretório.

## <a name="scaffold-app"></a>Scaffold o novo back-end de aplicações do Azure Mobile

Execute o seguinte comando do diretório que contém o directório de scripts:

```scaffold-mobile-app scripts out```

Esta opção criará scaffolded Azure Mobile aplicações back-end na `out` diretório.  Apesar de não é necessária, é uma boa ideia para verificar o `out` directório para um repositório de código de origem da sua escolha.

## <a name="deploy-ama-app"></a>Implementar o back-end de aplicações do Azure Mobile

Durante a implementação, terá de fazer o seguinte procedimento:

1. Crie uma nova aplicação Mobile no [Portal do Azure].
2. Executar o `createViews.sql` script na sua base de dados ligada.
3. Ligar a base de dados ligado ao seu serviço móvel para o novo serviço de aplicação.
4. Ligação quaisquer outros recursos (como notificação Hubs) para o novo serviço de aplicação.
5. Implemente o código gerado para o novo site.

### <a name="create-a-new-mobile-app"></a>Criar uma nova aplicação móvel

1. Inicie sessão no [Portal do Azure].

2. Clique em **+ Novo** > **Web + Mobile** > **Aplicação Mobile**, em seguida, forneça um nome para o aplicação Mobile do back-end.

3. Para o **Grupo de recursos**, selecione um grupo de recursos existente ou crie um novo (utilizando o mesmo nome como a sua aplicação.) 
 
    Pode selecionar outro plano de serviço de aplicação ou crie um novo. Para obter mais informações sobre os serviços de aplicação planos e como criar um novo plano num preços diferentes camada e na sua localização pretendida, consulte o artigo [Descrição geral de aprofundada de planos do serviço de aplicação do Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Para o **plano de serviço de aplicação**, o esquema predefinido (na [camada padrão](https://azure.microsoft.com/pricing/details/app-service/)) está selecionado. Também pode selecionar um plano diferente ou [crie um novo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Definições do plano de serviço de aplicação determinam a [localização, funcionalidades, de custos e recursos de calcular](https://azure.microsoft.com/pricing/details/app-service/) associado com a sua aplicação. 

    Depois de decidir no plano do, clique em **Criar**. Esta ação cria o aplicação Mobile back-end. 


### <a name="run-createviewssql"></a>Executar CreateViews.SQL

A aplicação scaffolded contém um ficheiro denominado `createViews.sql`.  Este script tem de ser executada em relação a base de dados de destino.  A cadeia de ligação para a base de dados de destino pode ser obtida do seu serviço móvel migrado a partir do pá **Definições** em **Cadeias de ligação**.  Se chamar `MS_TableConnectionString`.

Pode executar este script a partir do SQL Server Management Studio ou Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Ligar a base de dados no seu serviço de aplicação

Ligar a base de dados existente no seu serviço de aplicação:

- No [Portal do Azure], abra o seu serviço de aplicação.
- Selecione **todas as definições de** -> **ligações de dados**.
- Clique em **+ Add**.
- No menu pendente, selecione a **Base de dados SQL**
- Em **Base de dados SQL**, selecione a base de dados existente, em seguida, clique em **Selecionar**.
- Em **cadeia de ligação**, introduza o nome de utilizador e palavra-passe para a base de dados, em seguida, clique em **OK**.
- No pá **adicionar ligações de dados** , clique em **OK**.

Podem encontrar o nome de utilizador e palavra-passe ao ver a cadeia de ligação para a base de dados de destino no seu serviço móvel do migrado.


### <a name="set-up-authentication"></a>Configurar autenticação

Aplicações do Azure Mobile permite-lhe configurar Azure Active Directory, Facebook, Google, Microsoft e Twitter autenticação dentro do serviço.  Autenticação personalizada terá de ser desenvolvidos separadamente.  Consulte a documentação de [Conceitos de autenticação] e a documentação de [Autenticação guia de introdução] para obter mais informações.  

## <a name="updating-clients"></a>Atualizar os clientes móveis

Assim que tiver um operacional back-end de aplicação Mobile, pode trabalhar numa nova versão da sua aplicação de cliente que consome-lo. Aplicações móveis também inclui uma versão nova do cliente SDK e semelhante a atualização do servidor acima, é necessário remover todas as referências para o SDK de serviços móveis antes de instalar as versões de aplicações Mobile.

Uma das principais alterações entre as versões é que os construtores já não requerem uma tecla de aplicação. É agora basta passar o URL da sua aplicação móvel. Por exemplo, nos clientes .NET, o `MobileServiceClient` construtor é agora:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Pode ler sobre como instalar os SDK novos e utilizar a nova estrutura através das ligações em baixo:

- [Versão Android 2.2 ou posterior](app-service-mobile-android-how-to-use-client-library.md)
- [iOS versão 3.0.0 ou posterior](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) versão 2.0.0 ou posterior](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Apache Cordova versão 2.0 ou posterior](app-service-mobile-cordova-how-to-use-client-library.md)

Se a aplicação torna utilizar de notificações push, tome nota das instruções registo específico para cada plataforma, tal como tem ocorrido existem algumas alterações, assim.

Quando tiver a versão nova do cliente pronto, experimente contra o project server atualizado. Após a validação que funciona, pode Liberte uma nova versão da sua aplicação para clientes. Eventualmente, assim que os seus clientes tiveram a oportunidade de receber estas atualizações, pode eliminar a versão de serviços móveis da sua aplicação. Neste momento, tiver atualizado completamente a uma aplicação do aplicação do serviço móvel utilizando o servidor de aplicações Mobile SDK mais recente.

<!-- URLs. -->

[Portal do Azure]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[O que são aplicações Mobile?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Aplicação de serviço de preços]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Conceitos de autenticação]: ../app-service/app-service-authentication-overview.md
[Guia de introdução de autenticação]: app-service-mobile-auth.md

[Portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
