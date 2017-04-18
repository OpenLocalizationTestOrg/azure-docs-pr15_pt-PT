<properties
    pageTitle="Como trabalhar com o servidor de back-end Node.js SDK para aplicações Mobile | Azure de aplicação de serviço"
    description="Saiba como trabalhar com o servidor de back-end Node.js SDK para as aplicações móveis do Azure aplicação serviço."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Como utilizar o Azure Mobile aplicações Node.js SDK

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Este artigo fornece informações detalhadas e exemplos de como trabalhar com back-end Node.js nas aplicações móveis do Azure aplicação serviço.

## <a name="Introduction"></a>Introdução

Azure iniciador de aplicações móveis serviço fornece a capacidade para adicionar um acesso a dados otimizado para mobile Web API para uma aplicação web.  O Azure aplicação do serviço móvel aplicações SDK é fornecido para aplicações web do ASP.NET e Node.js.  O SDK fornece as seguintes operações:

- Operações de tabela (de leitura, inserir, atualizar, eliminar) para acesso a dados
- Operações de personalizada API

Fornecem ambas as operações para a autenticação através de todos os fornecedores de identidade permitidos pelo serviço de aplicação do Azure, incluindo os fornecedores de identidade de rede social como o Facebook, Twitter, Google e Microsoft, bem como Azure Active Directory para identidade da empresa.

Pode encontrar amostras para cada casos de utilização no [diretório de amostras no GitHub].

## <a name="supported-platforms"></a>Plataformas suportadas

O Azure Mobile aplicações nó SDK suporta a versão atual do LTS de nó e versões posteriores.  A partir de escrita, a versão mais recente do LTS é nó v4.5.0.  Outras versões do nó poderão funcionar mas não são suportadas.

O Azure Mobile aplicações nó SDK suporta dois controladores de base de dados - o controlador de nó mssql suporta SQL Azure e instâncias do SQL Server locais.  O controlador de sqlite3 suporta SQLite bases de dados em apenas uma única ocorrência.

### <a name="howto-cmdline-basicapp"></a>Como: criar back-end Node.js básica utilizando a linha de comandos

Inicia cada back-end Azure aplicação Serviço móvel aplicação Node.js como uma aplicação de ExpressJS.  ExpressJS é a arquitetura de serviço de web mais popular disponível para Node.js.  Pode criar um basic [Express] aplicação da seguinte forma:

1. Numa janela do PowerShell ou comando, crie um diretório para o seu projeto.

        mkdir basicapp

2. Execute inicialização npm a inicialização da estrutura de pacote.

        cd basicapp
        npm init

    O comando de inicialização npm pede-lhe um conjunto de perguntas para iniciar o projeto.  Ver os resultados de exemplo:

    ![O resultado de inicialização npm][0]

3. Instale as bibliotecas express e aplicações do mobile azure a partir do repositório de npm.

        npm install --save express azure-mobile-apps

4. Crie um ficheiro de app.js para implementar o servidor móvel básico.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Esta aplicação cria uma WebAPI otimizado para mobile com um ponto final de única (`/tables/TodoItem`) que oferece um acesso não autenticado ao arquivo de dados do SQL subjacente utilizando um esquema dinâmico.  É adequado para seguir o inicia rápida da biblioteca de cliente:

- [Guia de introdução do cliente Android]
- [Guia de introdução do Apache Cordova cliente]
- [Guia de introdução do cliente do iOS]
- [Guia de introdução de cliente da loja Windows]
- [Guia de introdução do cliente Xamarin.iOS]
- [Guia de introdução do cliente Xamarin.Android]
- [Guia de introdução do cliente Xamarin.Forms]

Pode localizar o código para esta aplicação básica na [amostra de basicapp GitHub].

### <a name="howto-vs2015-basicapp"></a>Como: criar um back-end nó com Visual Studio 2015

Visual Studio 2015 requer uma extensão para desenvolver aplicações Node.js dentro IDE.  Para começar, instale o [Node.js ferramentas 1.1 para Visual Studio].  Quando são instaladas as ferramentas de Node.js para Visual Studio, crie uma aplicação de 4. x Express:

1. Abrir a caixa de diálogo **Novo projeto** (a partir de **ficheiro** > **Novo** > **projecto...**).

2. Expanda **modelos** > **JavaScript** > **Node.js**.

3. Selecione a **aplicação básica Azure Node.js Express 4**.

4. Preencha o nome do projeto.  Clique em *OK*.

    ![Novo projeto do Visual Studio 2015][1]

5. Clique com o botão direito do rato em **npm** e selecione **instalar novos pacotes de npm...**.

6. Poderá ter de atualizar o catálogo de npm em criar a sua aplicação Node.js primeira.  Clique em **Atualizar** se for necessário.

7. Introduza as _aplicações do mobile azure_ na caixa de pesquisa.  Clique no pacote **azure--aplicações mobile 2.0.0** , em seguida, clique em **Instalar o pacote**.

    ![Instalar novos npm pacotes][2]

8. Clique em **Fechar**.

9. Abra o ficheiro _app.js_ para adicionar suporte para o SDK do Azure Mobile aplicações.  Na linha 6 at parte inferior da biblioteca exigir declarações, adicione o seguinte código:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Linha aproximadamente 27 após as outras demonstrações app.use, adicione o seguinte código:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Guarde o ficheiro.

10. Execute a aplicação localmente (API é servida no http://localhost:3000) ou publicar no Azure.

### <a name="create-node-backend-portal"></a>Como: criar um Node.js back-end, utilizando o portal Azure

Pode criar um direito de back-end aplicação Mobile no [portal do Azure]. Pode seguir os passos seguintes ou criar um cliente e servidor em conjunto, seguindo o tutorial de [criar uma aplicação móvel](app-service-mobile-ios-get-started.md) . O tutorial contém uma versão simplificada destas instruções e é melhor para os prova de projetos conceito.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Novamente na pá _começar_ , em **criar uma tabela API**, selecione **Node.js** como o **idioma de back-end**. Selecione a caixa do "**posso confirme que isto irá substituir todos os sites conteúdos.**", em seguida, clique em **Criar TodoItem tabela**.

### <a name="download-quickstart"></a>Como: Transferir o projeto de código do guia de introdução do Node.js back-end utilizando Git

Quando cria um aplicação de Mobile Node.js do back-end utilizando o portal pá **Guia de introdução** , um projeto Node.js é criado por si e implementado ao seu site. Pode adicionar tabelas e APIs e editar ficheiros de código para back-end Node.js no portal. Também pode utilizar várias ferramentas de implementação para transferir o projeto de back-end, para que o pode adicionar ou modificar APIs e tabelas, em seguida, voltar a publicar o projeto. Para obter mais informações, consulte o [Guia de implementação do Azure aplicação de serviço]. o procedimento seguinte utiliza um repositório de Git para transferir o código de projeto de guia de introdução.

1. Instale Git, se ainda não o tiver feito. Os passos necessários para instalar Git variam entre os sistemas operativos. Consulte o artigo [Instalar o Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) distribuições específicas do sistema operativo e orientação de instalação.

2. Siga os passos em [Ativar o repositório de aplicação de serviço de aplicação](../app-service-web/app-service-deploy-local-git.md#Step3) para ativar o repositório de Git para o seu site de back-end, efetuar uma nota de implementação nome de utilizador e palavra-passe.

3. No pá para sua back-end aplicação Mobile, tome nota da definição **Git clonar URL** .

4. Executar o `git clone` comando utilizando o Git clonar URL, introduzir a palavra-passe quando for necessário, tal como no exemplo seguinte:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Navegue até ao diretório local, que, no exemplo anterior, é /todolist e repare que foram transferidos ficheiros de projeto. Localize o `todoitem.json` de ficheiros na `/tables` diretório.  Este ficheiro define as permissões na tabela.  Também pode encontrar o `todoitem.js` scripts de ficheiro no mesmo directório, que define essa operação CRUD para a tabela.

6. Depois de efetuar alterações para ficheiros de projeto, execute os seguintes comandos para adicionar, consolidar, em seguida, carregue as alterações para o site:

        $ git commit -m "updated the table script"
        $ git push origin master

    Quando adiciona novos ficheiros ao projeto, tem primeiro de executar o `git add .` comando.

O site é artigos sempre que um novo conjunto de consolidadas seguia para o site.

### <a name="howto-publish-to-azure"></a>Como: publicar o seu back-end Node.js no Azure

Microsoft Azure fornece mecanismos muitas para publicar o seu back-end Azure aplicação Serviço móvel aplicações Node.js ao serviço do Azure.  Estas incluem recorrendo ferramentas de implementação integradas no Visual Studio, ferramentas de linha de comandos e opções de implementação contínuo com base no controlo de origem.  Para obter mais informações sobre este tópico, consulte o [Guia de implementação do Azure aplicação de serviço].

Aplicação de serviço de Azure tem conselhos específico para a aplicação de Node.js que devem consultar antes de implementar:

- Como [especificar a versão de nó]
- Como [utilizar módulos nó]

### <a name="howto-enable-homepage"></a>Como: activar uma Home Page da aplicação

Muitas aplicações são uma combinação de web e aplicações móveis e a arquitetura de ExpressJS permite-lhe combinar duas facetas.  Por vezes, no entanto, pode optar por apenas implementar uma interface móvel.  É útil para fornecer uma página de destino para garantir que a aplicação de serviço está a trabalhar.  Pode fornecer a sua própria home page ou ativar uma home page temporária.  Para permitir que uma home page temporária, utilize o seguinte para criar uma instância de aplicações do Azure Mobile:

    var mobile = azureMobileApps({ homePage: true });

Se pretender apenas a esta opção está disponível quando desenvolver localmente, pode adicionar esta definição para sua `azureMobile.js` ficheiro.

## <a name="TableOperations"></a>Operações de tabela 

As aplicações do mobile azure Node.js Server SDK fornece mecanismos para mostrar as tabelas de dados armazenadas na base de dados como uma WebAPI Azure SQL.  Operações de cinco são fornecidas.

| Operação | Descrição |
| --------- | ----------- |
| OBTER /tables/_tabela_ | Obter todos os registos na tabela |
| OBTER /tables/_tabela_/:id | Obter um registo específico na tabela |
| PUBLICAR /tables/_tabela_ | Criar um registo na tabela |
| CORRECÇÃO /tables/_tabela_/:id | Atualizar um registo na tabela |
| Eliminar /tables/_tabela_/:id | Eliminar um registo na tabela |

Este WebAPI suporta [OData] e expande o esquema de tabela para suportar a [sincronização de dados offline].

### <a name="howto-dynamicschema"></a>Como: definir tabelas utilizando um esquema dinâmico

Antes de pode ser utilizada numa tabela, tem de ser definido.  Tabelas podem ser definidas com um esquema estático (onde o programador define as colunas dentro do esquema) ou dinamicamente (onde o SDK controla o esquema com base em pedidos recebidos). Além disso, o programador pode controlar aspetos específicos da WebAPI ao adicionar código Javascript para a definição.

Como prática recomendada, é deve definir cada tabela num ficheiro Javascript no diretório de tabelas, em seguida, utilize o método de tables.import() para importar as tabelas.  Expandir a aplicação basic, o ficheiro app.js seria ser ajustado:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Definir a tabela na. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

As tabelas utilizam esquema dinâmica por predefinição.  Para desativar o esquema dinâmica globalmente, defina a definição de aplicação **MS_DynamicSchema** como false no interior do Azure portal.

Pode encontrar um exemplo completo da [amostra de todo GitHub].

### <a name="howto-staticschema"></a>Como: definir tabelas utilizando um esquema estático

Pode definir explicitamente as colunas para expor através de WebAPI.  Aplicações do mobile azure Node.js SDK adiciona automaticamente qualquer das colunas adicional necessária para a sincronização de dados offline para a lista que indicar.  Por exemplo, as aplicações de cliente do guia de introdução requerem uma tabela com duas colunas: texto (uma cadeia) e concluir (um valor booleano).  
A tabela que se pode ser definida na tabela JavaScript ficheiro de definição do (localizado no diretório tabelas) da seguinte forma:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Se definir estática tabelas, em seguida, tem também de chamar o método de tables.initialize() para criar o esquema da base de dados no arranque.  O método de tables.initialize() devolve um [compromisso] para que o serviço web não servir os pedidos de antes da base de dados a ser inicializado.

### <a name="howto-sqlexpress-setup"></a>Como: utilizar SQL Express como um arquivo de dados de desenvolvimento no seu computador local

O Azure Mobile aplicações de AzureMobile aplicações nó SDK fornece três opções para que servem dados terminar a caixa: SDK fornece três opções para que servem dados terminar a caixa de:

- Utilizar o controlador de **memória** para fornecer um arquivo de exemplo que não sejam persistente
- Utilizar o controlador de **mssql** para fornecer um arquivo de dados SQL Express para o desenvolvimento
- Utilizar o controlador de **mssql** para fornecer um arquivo de dados da base de dados do Azure SQL de produção

O Azure Mobile aplicações Node.js SDK utiliza o [pacote de Node.js mssql] para estabelecer e utilizar uma ligação para SQL Express e base de dados SQL.  Este pacote requer que ativar as ligações de TCP no seu instância do SQL Express.

> [AZURE.TIP]O controlador de memória não fornece um conjunto completo de produção para testar a ligação.  Se pretender testar a sua back-end localmente, recomendamos a utilização de um arquivo de dados SQL Express e o controlador de mssql.

1. Transfira e instale o [Microsoft SQL Server 2014 Express].  Certifique-se de que instala o SQL Server 2014 Express com edição de ferramentas.  A menos que necessite explicitamente suporte de 64 bits, a versão de 32 bits consome menos memória quando a ser executado.

2. Execute o Gestor de configuração do SQL Server 2014.

  1. Expanda o nó de **Configuração de rede do SQL Server** no menu de árvore do lado esquerdo.
  2. Clique em **protocolos para SQLEXPRESS**.
  3. Botão direito do rato **em TCP/IP** e selecione **Ativar**.  Na caixa de diálogo pop-up, clique em **OK** .
  4. Botão direito do rato **em TCP/IP** e selecione **Propriedades**.
  5. Clique no separador **Endereços IP** .
  6. Localize o nó **IPAll** .  No campo **TCP porta** , introduza **1433**.

         ![Configure SQL Express for TCP/IP][3]

  7. Clique em **OK**.  Na caixa de diálogo pop-up, clique em **OK** .
  8. No menu de árvore do lado esquerdo, clique em **SQL Server Services** .
  9. **SQL Server (SQLEXPRESS)** com o botão direito e selecione **reiniciar**
  10. Feche o Gestor de configuração do SQL Server 2014.

3. Executar o SQL Server 2014 Management Studio e ligar à sua local instância do SQL Express

  1. A instância no Explorador do objeto com o botão direito e selecione **Propriedades**
  2. Selecione a página de **segurança** .
  3. Certifique-se de que o **SQL Server e o modo de autenticação do Windows** está selecionada
  4. Clique em **OK**

        ![Configurar uma autenticação Express SQL][4]

  5. Expandir a **segurança** > **inícios de sessão** no Explorador de objeto
  6. **Inícios de sessão** com o botão direito e selecione **Novo início de sessão...**
  7. Introduza um nome de início de sessão.  Selecione a **autenticação do SQL Server**.  Introduza uma palavra-passe e, em seguida, introduza a palavra-passe mesmo **Confirmar**palavra-passe.  A palavra-passe deve cumprir requisitos de complexidade do Windows.
  8. Clique em **OK**

        ![Adicionar um novo utilizador para o SQL Express][5]

  9. Com o botão direito do novo início de sessão e selecione **Propriedades**
  10. Selecione a página de **Funções de servidor**
  11. Selecione a caixa junto **do dbcreator**
  12. Clique em **OK**
  13. Feche o SQL Server 2015 Management Studio

Certifique-se de que gravar o nome de utilizador e palavra-passe que selecionou.  Poderá ter atribuir funções de servidor adicionais ou permissões dependendo aos requisitos da base de dados específico.

A aplicação Node.js lê a variável de ambiente de **SQLCONNSTR_MS_TableConnectionString** para a cadeia de ligação para esta base de dados.  Pode definir esta variável do seu ambiente.  Por exemplo, pode utilizar o PowerShell para definir esta variável de ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Aceder a base de dados através de uma ligação de TCP/IP e fornecer um nome de utilizador e palavra-passe para a ligação.

### <a name="howto-config-localdev"></a>Como: configurar o seu projeto para o desenvolvimento local

Aplicações do Azure Mobile lê um ficheiro JavaScript chamado _azureMobile.js_ a partir do sistema de ficheiros local.  Não utilize este ficheiro para configurar o SDK do Azure Mobile aplicações no produção - como alternativa, utilize as definições da aplicação no interior do [Azure portal] .  O ficheiro _azureMobile.js_ deve exportar um objeto de configuração.  As definições mais comuns são:

- Definições de base de dados
- Definições de registo de diagnóstico
- Definições de CORS alternativo

Segue-se um exemplo de ficheiro _azureMobile.js_ execução as definições anteriores da base de dados:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Recomendamos que adicione _azureMobile.js_ ao seu ficheiro _.gitignore_ (ou outros controlo de código fonte ignorar ficheiro) para impedir que as palavras-passe a ser armazenado na nuvem.  Sempre configure definições de produção nas definições de aplicação no interior do [Azure portal].

### <a name="howto-appsettings"></a>Como: Configurar as definições da aplicação para a sua aplicação móvel

Maior parte das definições no ficheiro _azureMobile.js_ ter uma definição de aplicação equivalente no [portal do Azure].  Utilize a lista seguinte para configurar a sua aplicação nas definições da aplicação:

| Definição de aplicação                 | Definição de _azureMobile.js_  | Descrição                               | Valores válidos                                |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS_MobileAppName**        | nome                      | O nome da aplicação                       | cadeia                                      |
| **MS_MobileLoggingLevel**   | Logging.level             | Nível de registo mínimo de mensagens para iniciar sessão      | erro, aviso, informações, verbosa, depurar, disparatado |
| **MS_DebugMode**            | depurar                     | Ativar ou desativar o modo de depuração              | VERDADEIRO, FALSO                                 |
| **MS_TableSchema**          | data.Schema               | Nome do esquema predefinido para tabelas SQL        | cadeia (predefinido: dbo)                       |
| **MS_DynamicSchema**        | data.dynamicSchema        | Ativar ou desativar o modo de depuração              | VERDADEIRO, FALSO                                 |
| **MS_DisableVersionHeader** | versão (definido para indefinido)| Desativa o cabeçalho X-ZUMO--versão do servidor | VERDADEIRO, FALSO                                 |
| **MS_SkipVersionCheck**     | skipversioncheck          | Desativa a verificação da versão API de cliente     | VERDADEIRO, FALSO                                 |

Para definir uma definição de aplicação:

1. Inicie a sessão [portal do Azure].
2. Selecione **todos os recursos** ou **Serviços de aplicação** , em seguida, clique no nome da sua aplicação móvel.
3. Abre o pá definições por predefinição. Se não tiver, clique em **Definições**.
4. Clique em **definições da aplicação** no menu geral.
5. Desloque-se para a secção definições na aplicação.
6. Se a sua aplicação definir já existir, clique no valor da definição de aplicação para editar o valor.
7. Se está a aplicação não existir, introduza a definição de aplicação na caixa chave e o valor na caixa valor.
8. Assim que estiver concluída, clique em **Guardar**.

Alterar a maior parte das definições de aplicação requer um reinício de serviço.

### <a name="howto-use-sqlazure"></a>Como: utilizar a base de dados de SQL como o arquivo de dados de produção

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Utilizar a base de dados do SQL Azure como um arquivo de dados é idêntico ao longo de todos os tipos de aplicação de serviço de aplicação do Azure. Se ainda não o tiver o fez, siga estes passos para criar a aplicação Mobile back-end.

1. Inicie a sessão [portal do Azure].

2. No canto superior esquerdo da janela, clique no botão **+ Novo** > **Web + Mobile** > **Aplicação Mobile**, em seguida, forneça um nome para o aplicação Mobile do back-end.

3. Na caixa de **Grupo de recursos** , introduza o mesmo nome como a sua aplicação.

4. O plano de serviço de aplicação predefinida está selecionado.  Se pretender alterar o seu plano de serviço de aplicação, pode fazê-lo ao clicar em plano de aplicação de serviço > **+ criar novos**.  Fornecer um nome de novo plano de aplicação de serviço e selecione uma localização apropriada.  Clique na camada de preços e selecione uma camada comparar adequada para o serviço. Selecione **Ver tudo** para ver mais opções de preços, tal como **gratuitos** e **partilhado**.  Assim que tiver selecionado a definição de preços camada, clique no botão **Selecionar** .  Novamente na pá **plano de serviço de aplicação** , clique em **OK**.

5. Clique em **Criar**. Aprovisionamento back-end aplicação Mobile, pode demorar alguns minutos.  Assim que a aplicação Mobile back-end está aprovisionado, o portal abre-se a pá **Definições** para o aplicação Mobile back-end.

Quando o aplicação Mobile back-end estiver criado, pode optar por quer ligar uma base de dados do SQL existente ao seu back-end aplicação Mobile ou criar uma nova base de dados do SQL.  Nesta secção, podemos criar uma base de dados do SQL.

> [AZURE.NOTE]Se já tiver uma base de dados na mesma localização, como o back-end da aplicação móvel, pode escolher em vez disso, **Utilize uma base de dados existente** e, em seguida, selecione essa base de dados. A utilização de uma base de dados numa localização diferente, não é recomendada devido a latências superiores.

6. Na novo aplicação Mobile back-end, clique em **Definições** > **Aplicação Mobile** > **dados** > **+ Adicionar**.

7. No pá **Adicionar ligação de dados** , clique em **Base de dados SQL - configurar definições necessárias** > **criar uma nova base de dados**.  Introduza o nome da base de dados novo no campo **nome** .

8. Clique em **Server**.  Na pá **novo servidor** , introduza um nome de servidor exclusivo no campo **nome do servidor** e fornecer um adequado **início de sessão do servidor administrador** e uma **palavra-passe**.  Certifique-se de que **Permitir azure serviços para aceder ao servidor** está selecionada.  Clique em **OK**.

    ![Criar uma base de dados do Azure SQL][6]

9. No pá **nova base de dados** , clique em **OK**.

10. Novamente no pá **Adicionar ligação de dados** , selecione a **cadeia de ligação**, introduza o ID de utilizador e palavra-passe que forneceu quando criar a base de dados.  Se utilizar uma base de dados existente, fornece as credenciais de início de sessão para essa base de dados.  Assim que introduziu, clique em **OK**.

11. Volte a pá **Adicionar ligação de dados** , clique em **OK** para criar a base de dados.

<!--- END OF ALTERNATE INCLUDE -->

Criação da base de dados pode demorar alguns minutos.  Utilize a área de **notificações** para monitorizar o progresso da implementação.  Não de progresso até que a base de dados foi implementado com êxito.  Depois de implementado com êxito, é criada uma cadeia de ligação para a instância de base de dados SQL na sua as definições da aplicação de back-end móvel.  Pode ver esta definição de aplicação nas **Definições** > **definições da aplicação** > **cadeias de ligação**.

### <a name="howto-tables-auth"></a>Como: exigir autenticação para acesso às tabelas

Se pretender utilizar a aplicação de serviço de autenticação com o ponto final de tabelas, tem de configurar primeiro autenticação do serviço de aplicação no [portal do Azure] .  Para obter mais detalhes sobre como configurar autenticação numa aplicação de serviço do Azure, reveja o guia de configuração para o fornecedor de identidade que pretende utilizar:

- [Como configurar a autenticação do Azure Active Directory]
- [Como configurar a autenticação do Facebook]
- [Como configurar a autenticação do Google]
- [Como configurar Authentication da Microsoft]
- [Como configurar uma autenticação Twitter]

Cada tabela tem uma propriedade de acesso que pode ser utilizada para controlar o acesso à tabela.  O exemplo seguinte mostra uma tabela estática definida com autenticação necessária.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

A propriedade access pode execute uma das três valores

  - *anónima* indica que a aplicação de cliente é permitida para ler dados sem autenticação
  - *autenticados* indica que a aplicação de cliente terá de enviar um token de autenticação válida com o pedido
  - *desativado* indica que esta tabela atualmente está desactivada

Se a propriedade access for indefinida, acesso não autenticado é permitido.

### <a name="howto-tables-getidentity"></a>Como: Utilize a autenticação de afirmações com as tabelas

Pode configurar vários afirmações que forem pedidas quando autenticação está configurada.  Estes afirmações não estão normalmente disponíveis através de `context.user` objeto.  No entanto, só podem ser obtidas utilizando o `context.user.getIdentity()` método.  O `getIdentity()` método devolve um compromisso que é resolvida como um objeto.  O objeto é codificado pelo método de autenticação (facebook, google, twitter, microsoftaccount ou aad).

Por exemplo, se configurou o Account Microsoft de autenticação e pedido que reclamar os endereços de e-mail, pode adicionar o endereço de e-mail para o registo com o controlador da tabela seguinte:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Para ver que afirmações estão disponíveis, utilize um browser para ver o `/.auth/me` ponto final do seu site.

### <a name="howto-tables-disabled"></a>Como: desativar o acesso ao operações específicas da tabela

Para além de aparecer na tabela, a propriedade de access pode ser utilizada para controlar as operações individuais.  Existem quatro operações:

  - *Ler* é a operação obter RESTful da tabela
  - *Inserir* é a operação de mensagem RESTful da tabela
  - *Atualizar* é a operação de correcção RESTful da tabela
  - *Eliminar* é a operação RESTful eliminar da tabela

Por exemplo, poderá pretender fornecer uma tabela não autenticada só de leitura:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Como: ajustar a consulta que é utilizada com operações de tabela

É um requisito comum para operações de tabela fornecer uma vista restrita dos dados.  Por exemplo, pode fornecer uma tabela que está etiquetada com o ID de utilizador autenticado assim que apenas pode ler ou atualizar os seus registos.  A definição da tabela seguinte fornece esta funcionalidade:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Operações que normalmente executar uma consulta tem uma propriedade de consulta que pode ajustar com where cláusula. A propriedade de consulta é um objeto de [QueryJS] que é utilizado para converter uma consulta de OData algo que pode processar dados back-end.  Casos de igualdade simples (como a que anterior), pode ser utilizado um mapa. Também pode adicionar cláusulas SQL específicas:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Como: Configurar eliminar contornos numa tabela

Eliminar contornos realmente não elimina os registos.  Em vez disso, marca-los como eliminadas da base de dados através da definição de coluna removido como verdadeiro.  O SDK do Azure Mobile aplicações remove automaticamente eliminados contornos registos a partir dos resultados, a menos que o SDK do cliente móvel utiliza IncludeDeleted().  Para configurar uma tabela para eliminar contornos, defina o `softDelete` propriedade no ficheiro de definição de tabela:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Deverá estabelecer um mecanismo para remover registos - o de uma aplicação de cliente, por uma WebJob, função Azure ou através de uma API personalizada.

### <a name="howto-tables-seeding"></a>Como: propagar a base de dados com dados

Ao criar uma nova aplicação, pode optar por propagar uma tabela com dados.  Isto pode ser efetuado dentro JavaScript ficheiro de definição de tabela da seguinte forma:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Só é feita a propagação de dados quando a tabela é criada pelo SDK de aplicações móveis do Azure.  Se a tabela já existe na base de dados, são inseridos sem dados na tabela.  Se esquema dinâmica estiver ativada, o esquema é inferido dos dados propagação de.

Recomendamos que explicitamente ligar para o `tables.initialize()` método para criar a tabela quando o serviço começa a funcionar.

### <a name="Swagger"></a>Como: activar o suporte de Swagger

Azure iniciador de aplicações móveis serviço vem com incorporado [Swagger] suporte.  Para ativar o suporte do Swagger, primeiro de instalar a IU swagger como uma dependência:

    npm install --save swagger-ui

Depois de instalado, pode activar o suporte de Swagger no construtor de aplicações do Azure Mobile:

    var mobile = azureMobileApps({ swagger: true });

É provavelmente apenas pretender ativar o suporte do Swagger nas edições de desenvolvimento.  Pode fazê-lo através da utilização de `NODE_ENV` definição de aplicação:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

O ponto final swagger está localizado em http://_yoursite_.azurewebsites.net/swagger.  Pode aceder a IU Swagger através de `/swagger/ui` ponto final.  Se optar por exigir autenticação ao longo da sua aplicação completa, Swagger gera um erro.  Para obter melhores resultados, optar por permitir pedidos não autenticados através de na aplicação Azure Service autenticação / definições de autorização, controlo, em seguida, autenticação, utilizando o `table.access` propriedade.

Também pode adicionar a opção Swagger para sua `azureMobile.js` ficheiro, se pretender apenas suporte Swagger quando desenvolver localmente.

## <a name="a-namepushpush-notifications"></a><a name="push">Notificações de emissão

Aplicações móveis integra-se com o Azure concentradores de notificação para que possa enviar notificações push alvo para milhões de dispositivos em todas as plataformas principais. Ao utilizar concentradores de notificação, pode enviar notificações push para iOS, dispositivos Android e Windows. Para saber mais sobre tudo o que que pode fazer com concentradores de notificação, consulte o artigo [Descrição geral de concentradores de notificação](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Como: enviar as notificações push

O código seguinte mostra como utilizar o objeto push para enviar uma notificação de difusão push para dispositivos iOS registados:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Ao criar um registo de push do modelo a partir do cliente, em vez disso, pode enviar uma mensagem de push do modelo para dispositivos no todas as plataformas suportadas. O código seguinte mostra como enviar uma notificação de modelo:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


###<a name="push-user"></a>Como: enviar as notificações push para um utilizador autenticado utilizar etiquetas

Quando um utilizador autenticado regista para notificações push, uma etiqueta de ID de utilizador é automaticamente adicionada ao registo. Ao utilizar nesta etiqueta, pode enviar notificações push para todos os dispositivos registados por um utilizador específico. O código seguinte obtém o SID do utilizador efetuar o pedido e envia uma notificação de push do modelo para cada registo de dispositivo para esse utilizador:

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Quando registar-se para as notificações push a partir de um cliente não autenticado, certifique-se de que a autenticação estiver concluída antes de tentar registo.

## <a name="CustomAPI"></a>APIs personalizados

###  <a name="howto-customapi-basic"></a>Como: definir uma API personalizada


Para além de aceder a dados API via o ponto final /tables, aplicações de Mobile Azure pode fornecer cobertura de API personalizada.  Personalizada APIs são definidos de forma semelhante para as definições de tabela e pode aceder às mesmas instalações, incluindo a autenticação.

Se pretender utilizar a autenticação do serviço de aplicação com uma API personalizada, tem de configurar primeiro autenticação do serviço de aplicação no [portal do Azure] .  Para obter mais detalhes sobre como configurar autenticação numa aplicação de serviço do Azure, reveja o guia de configuração para o fornecedor de identidade que pretende utilizar:

- [Como configurar a autenticação do Azure Active Directory]
- [Como configurar a autenticação do Facebook]
- [Como configurar a autenticação do Google]
- [Como configurar Authentication da Microsoft]
- [Como configurar uma autenticação Twitter]

APIs personalizados são definidos muito da mesma forma como a API de tabelas.

1. Criar um diretório de **api**
2. Crie um ficheiro de JavaScript de definição de API no diretório **api** .
3. Utilize o método de importação para importar o diretório de **api** .

Segue-se a definição de api protótipo baseada na aplicação basic amostra que utilizámos anterior.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Vamos um exemplo API que devolve a data de servidor utilizando o método de _Date.now()_ .  Eis o ficheiro api/date.js:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Cada parâmetro é um dos verbos padrão RESTful - obter, publicar, PATCHES ou eliminar.  O método é uma função de [Software ExpressJS intermédio] padrão que envia a saída necessária.

### <a name="howto-customapi-auth"></a>Como: exigir autenticação para acesso a uma API personalizado

Azure SDK de aplicações móveis implementa autenticação da mesma forma de ponto final de tabelas e APIs personalizados.  Para adicionar autenticação à API desenvolvido na secção anterior, adicione uma propriedade **access** :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Também pode especificar a autenticação no operações específicas:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

O mesmo token que é utilizado para o ponto final de tabelas deve ser utilizado para APIs personalizados que requer autenticação.

### <a name="howto-customapi-auth"></a>Como: processar carregamentos de ficheiros grandes

Azure SDK de aplicações móveis utiliza o [software analisador de corpo intermédio](https://github.com/expressjs/body-parser) para aceitar e descodificar conteúdo de corpo na sua submissão.  Pode configurar previamente analisador de corpo para aceitar carregamentos de ficheiros maiores:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

O ficheiro é codificado antes da transmissão base-64.  Isto aumenta o tamanho do carregamento real (e, consequentemente, o tamanho deve ter em conta).

### <a name="howto-customapi-sql"></a>Como: executar instruções SQL personalizadas

O SDK de aplicações do Azure Mobile permite o acesso ao contexto através do objecto pedido, permitindo-lhe executar parametrizadas instruções SQL para o fornecedor de dados definido facilmente:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Depuração, tabelas de fácil e APIs fácil

### <a name="howto-diagnostic-logs"></a>Como: depurar, diagnosticar e resolver problemas de aplicações do Azure Mobile

O serviço de aplicação do Azure fornece várias depuração e técnicas para Node.js aplicações de resolução de problemas.
Consulte os artigos seguintes para começar a utilizar na sua back-end Node.js Mobile de resolução de problemas:

- [Monitorização de uma aplicação de serviço Azure]
- [Ativar o registo de diagnóstico no Azure de aplicação de serviço]
- [Resolver problemas de um serviço de aplicação Azure no Visual Studio]

Aplicações de node.js tem acesso a uma vasta gama de ferramentas de registo de diagnóstico.  O Azure Mobile aplicações Node.js SDK utiliza internamente, [Winston] para o registo de diagnóstico.  Registo está ativado automaticamente ao ativar o modo de depuração ou definindo a definição de aplicação **MS_DebugMode** como verdadeiro no [portal do Azure]. Registos gerados aparecem nos registos de diagnóstico no [portal do Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Como: trabalhar com tabelas fácil no portal do Azure

Tabelas fácil no portal do permitem-lhe criar e trabalhar com direita de tabelas no portal. Ainda pode editar operações de tabela utilizando o Editor de serviço de aplicação.

Quando clica em **tabelas fácil** nas definições do site de back-end, pode adicionar, modificar ou eliminar uma tabela. Também pode ver os dados na tabela.

![Trabalhar com tabelas fácil](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Os comandos seguintes estão disponíveis na barra de comandos para uma tabela:

+ **Alterar permissões** - modificar a permissão de leitura, inserir, atualizar e eliminar operações na tabela. 
  Opções estão permitir o acesso anónimo, para exigir autenticação ou para desativar todo o acesso a operação. 
+ **Editar o script** - o ficheiro de script para a tabela é aberto no Editor de serviço de aplicação.
+ **Gerir esquema** - adicionar ou eliminar colunas ou alterar o índice da tabela.
+ **Limpar tabela** - trunca uma tabela existente-se eliminar todas as linhas de dados, mas deixar o esquema inalterada.
+ **Eliminar linhas** - eliminar linhas individuais de dados.
+ **Ver registos de transmissão** - irão ligá-lo para o serviço de registo de transmissão do seu site.

###<a name="work-easy-apis"></a>Como: trabalhar com APIs fácil no portal do Azure

APIs fácil no portal do permitem-lhe criar e trabalhar com direito APIs personalizado no portal. Pode editar os scripts de API utilizando o Editor de serviço de aplicação.

Quando clica em **APIs fácil** nas definições do site de back-end, pode adicionar, modificar ou eliminar um extremo API personalizado.

![Trabalhar com APIs fácil](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

No portal do, pode alterar as permissões de acesso para uma determinada acção HTTP, edite o ficheiro de script API no Editor de serviço de aplicação ou visualizar os registos de transmissão.

###<a name="online-editor"></a>Como: Editar código no Editor de serviço de aplicação

Portal do Azure permite-lhe editar os seus ficheiros de script de back-end Node.js no Editor de serviço de aplicação sem ter de transferir o projeto para o seu computador local. Para editar ficheiros de script no editor de online:

1. No seu pá back-end aplicação Mobile, clique em **todas as definições** > **tabelas fácil** ou **APIs fácil**, clique numa tabela ou API, em seguida, clique em **Editar script**. O ficheiro de script é aberto no Editor de serviço de aplicação.

    ![Editor de serviço de aplicação](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. Faça as alterações ao ficheiro código no editor de online. As alterações são guardadas automaticamente à medida que escreve.


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Guia de introdução do cliente Android]: app-service-mobile-android-get-started.md
[Guia de introdução do Apache Cordova cliente]: app-service-mobile-cordova-get-started.md
[Guia de introdução do cliente do iOS]: app-service-mobile-ios-get-started.md
[Guia de introdução do cliente Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Guia de introdução do cliente Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Guia de introdução do cliente Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Guia de introdução de cliente da loja Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[sincronização de dados offline]: app-service-mobile-offline-data-sync.md
[Como configurar a autenticação do Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Como configurar a autenticação do Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Como configurar a autenticação do Google]: app-service-mobile-how-to-configure-google-authentication.md
[Como configurar Authentication da Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar uma autenticação Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guia de implementação do Azure de aplicação de serviço]: ../app-service-web/web-sites-deploy.md
[Monitorização de uma aplicação de serviço Azure]: ../app-service-web/web-sites-monitor.md
[Ativar o registo de diagnóstico no Azure de aplicação de serviço]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Resolver problemas de um serviço de aplicação Azure no Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[especificar a versão de nó]: ../nodejs-specify-node-version-azure-apps.md
[utilizar módulos nó]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Portal do Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promessa]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemplo de basicapp no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[exemplo de todo no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[diretório de amostras no GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Ferramentas de node.js 1.1 para Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node.js pacote]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Software ExpressJS intermédio]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
