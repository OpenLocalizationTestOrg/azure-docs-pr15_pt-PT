<properties
    pageTitle="Ativar a sincronização offline para a sua aplicação de Mobile Azure (Cordova) | Microsoft Azure"
    description="Saiba como utilizar a aplicação do aplicação do serviço Mobile a cache e sincronização dados offline na sua aplicação Cordova"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Ativar a sincronização offline para a sua aplicação móvel Cordova

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Neste tutorial apresenta a funcionalidade de sincronização offline do Azure Mobile aplicações para Cordova. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede. As alterações são guardadas na base de dados local; Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço de remoto.

Neste tutorial é baseado a solução de guia de introdução Cordova para as aplicações móveis que criar quando concluir o tutorial [Apache Cordova rápida iniciar]. Neste tutorial, irá atualizar a solução de guia de introdução para adicionar funcionalidades de offline do Azure Mobile aplicações. Recomendamos também irá realçar o código offline específicas na aplicação.

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Sincronização de dados Offline no Azure Mobile aplicações]. Para obter detalhes sobre a utilização de API, consulte o artigo o ficheiro [Leia-me] o plug-in.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adicionar a sincronização offline para a solução de guia de introdução

O código de sincronização offline deve ser adicionado à aplicação. Sincronização offline requer o plug-in Armazenamento de sqlite cordova, que é adicionado automaticamente à sua aplicação quando o plug-in de aplicações do Azure Mobile está incluído no projeto. O projeto de guia de introdução inclui ambos estes plug-ins.

1. No Explorador de solução do Visual Studio, abra index.js e substitua o seguinte código

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    com este código:

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. Em seguida, substitua o seguinte código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    com este código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    As adições código anterior iniciar o arquivo local e definir uma tabela local que corresponde aos valores da coluna utilizados no seu Azure back-end. (Não ter de incluir todos os valores de coluna neste código.)

    Obter uma referência para o contexto de sincronização ao contactar o suporte **getSyncContext**. O contexto de sincronização ajuda preservar relações de tabela por a controlar e conduza alterações em todas as tabelas para que uma aplicação de cliente tenha modificado quando é chamado **push** .

3. Atualize o URL da aplicação para o URL da aplicação aplicação Mobile.

4. Em seguida, substitua este código:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    com este código:

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    O código precedente inicia o contexto de sincronização e, em seguida, liga getSyncTable (em vez de getTable) para obter uma referência para a tabela local.

    Utilizações este código base de dados local para todas as criar, ler, atualizar e eliminar (CRUD) operações de tabela.

    Este exemplo executa no conflitos de sincronização de processamento de erros simples. Uma aplicação real seria processar vários erros como as condições da rede, conflitos de servidor e as outras pessoas. Para obter exemplos de código, consulte o artigo a [amostra de sincronização offline].

5. Em seguida, adicione esta função para efetuar a sincronização real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    A decidir quando transmitir alterações para o aplicação Mobile back-end ao contactar o suporte **push** no objeto **syncContext** utilizado pelo cliente. Por exemplo, pode adicionar uma chamada para **syncBackend** a um processador de eventos do botão na aplicação como um novo botão de sincronização ou pode adicionar a chamada para a função **addItemHandler** para sincronizar sempre que é adicionado um novo item.

##<a name="offline-sync-considerations"></a>Considerações sobre a sincronização offline

No exemplo, o método **push** do **syncContext** chama-se apenas no arranque da aplicação na função chamada de retorno para o início de sessão.  Numa aplicação reais, também pode fazer esta funcionalidade de sincronização acionou manualmente ou quando altera o estado da rede.

Quando uma solicitação é executada em relação a uma tabela que tem actualizações locais pendentes registadas pelo contexto, essa operação solicitação será automaticamente accionar um push contexto anterior. Quando atualizar, adicionar e concluir os itens neste exemplo, pode omitir chamada explícita **push** , uma vez que pode ser redundante (verificação primeira o [ficheiro Leia-me] para o estado atual de funcionalidade).

No código fornecido, todos os registos na tabela todoItem remoto são avisados, mas também é possível filtrar registos, passando um id de consulta e uma consulta para **push**. Para mais informações, consulte a secção *Sincronização utilizarão* na [Offline dados sincronização nas aplicações móveis do Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Desactivar a autenticação

Se porque não já configurou autenticação e não quer configurar autenticação antes de teste de sincronização offline, comentar a função de chamada de retorno para o início de sessão, mas deixar o código dentro uncommented a função de chamada de retorno.

O código deve ter este aspeto após dos comentários as linhas do início de sessão.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, a aplicação irá sincronizar com o Azure back-end ao executar a aplicação em vez de quando iniciar sessão.

## <a name="run-the-client-app"></a>Executar a aplicação de cliente

Com a sincronização offline agora ativada, pode agora executar a aplicação de cliente pelo menos uma vez no cada plataforma para preencher a base de dados do arquivo local. Mais tarde, irá simular um cenário offline e modificar os dados no arquivo local enquanto a aplicação está offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Testar o comportamento de sincronização

Nesta secção, irá modificar projecto do cliente para simular um cenário offline utilizando um URL de aplicação inválido para o seu back-end. Quando adicionar ou alterar itens de dados, estas alterações serão contidas no arquivo local, mas não sincronizadas com o arquivo de dados back-end até que a ligação é estabelecida novamente.

1. No Explorador de solução, abra o ficheiro de projeto index.js e alterar o URL da aplicação para apontar para um URL inválido, como o seguinte:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. No Index, atualize o CSP `<meta>` elemento com o mesmo URL inválido.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Criar e executar a aplicação de cliente e repare que uma exceção tem sessão iniciada na consola do quando tenta obter a aplicação sincronizar com o back-end depois de início de sessão. Todos os novos itens que adiciona serão existe apenas no arquivo local até que pode ser enviados para o telemóvel back-end. A aplicação de cliente comporta-se como se estiver ligado a back-end, de suporte que todos a criar, ler, atualizar, eliminar (CRUD) operações.

4. Feche a aplicação e reinicie-o para verificar que os novos itens que criou são permanentes para o arquivo local.

5. (Opcional) Utilize o Visual Studio para ver a sua tabela de base de dados do Azure SQL para ver que os dados na base de dados back-end não foi alterado.

    No Visual Studio, abra o **Explorador de servidor**. Navegue para a base de dados no **Azure**->**Bases de dados SQL**. A base de dados com o botão direito e selecione **Abrir no Explorador de objeto do SQL Server**. Agora, pode navegar para a sua tabela de base de dados SQL e os seus conteúdos.


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Testar restabelecer a ligação para o seu telemóvel back-end

Nesta secção irá restabelecer a ligação a aplicação móvel back-end, que simula a aplicação novamente a chegar a um estado online. Quando iniciar sessão, dados serão sincronizados para o seu telemóvel back-end.

1. Voltar a abrir index.js e corrija o URL da aplicação para apontarem para o URL correto.

2. Voltar a abrir Index e corrigir o URL da aplicação no CSP `<meta>` elemento.

3. Reconstruir e execute a aplicação de cliente. A aplicação tentativas para sincronizar com o aplicação móvel back-end depois de início de sessão. Certifique-se de que nenhuma exceções com sessão iniciadas na consola de depuração.

4. (Opcional) Ver dados atualizados com o Explorador de objeto do SQL Server ou uma ferramenta de descanso como Fiddler. Repare que os dados tem foram sincronizados entre a base de dados back-end e o arquivo local.

    Repare os dados sincronizou entre a base de dados e o arquivo local contém os itens que adicionou enquanto a aplicação foi desligada.

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline no Azure aplicações móveis]

* [Na nuvem rosto: Sincronização Offline no Azure serviços móveis] \(Nota: o vídeo está no Mobile Services, mas sincronização offline funciona de forma semelhante em aplicações móveis do Azure\)

* [Ferramentas do Visual Studio para Apache Cordova]

## <a name="next-steps"></a>Próximos passos

* Veja mais avançadas funcionalidades de sincronização offline como a resolução de conflitos no [exemplo de sincronização offline]
* Observe a referência da API no [ficheiro Leia-me] de sincronização offline

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Guia de introdução do Apache Cordova]: app-service-mobile-cordova-get-started.md
[LEIA-ME]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[exemplo de sincronização offline]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Sincronização de dados offline no Azure aplicações móveis]: app-service-mobile-offline-data-sync.md
[Capa da nuvem: Sincronização Offline no Azure serviços móveis]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
