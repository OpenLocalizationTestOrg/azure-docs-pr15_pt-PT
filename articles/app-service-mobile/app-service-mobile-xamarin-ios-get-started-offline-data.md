<properties
    pageTitle="Ativar a sincronização offline para a sua aplicação de Mobile Azure (Xamarin iOS)"
    description="Saiba como utilizar a aplicação do aplicação do serviço Mobile a cache e sincronização dados offline na sua aplicação iOS do Xamarin"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Ativar a sincronização offline para a sua aplicação móvel Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial apresenta a funcionalidade de sincronização offline do Azure Mobile aplicações para Xamarin.iOS. Sincronização offline permite aos utilizadores finais interagir com uma aplicação móvel – ver, adicionar ou modificar dados – mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço de remoto.

Neste tutorial, atualize o projeto de aplicação Xamarin.iOS a partir de [criar uma aplicação para iOS Xamarin] para suportar as funcionalidades de offline do Azure Mobile aplicações. Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico de [Sincronização de dados Offline no Azure Mobile aplicações].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar funcionalidades offline

Funcionalidades de offline aplicação Mobile Azure permitem-lhe interagir com uma base de dados local quando estiver num cenário de offline. Para utilizar estas funcionalidades na sua aplicação, iniciar uma [SyncContext] para um arquivo local. Fazer referência a sua tabela através da interface do [IMobileServiceSyncTable]. SQLite é utilizado como o arquivo local no dispositivo.

1. Abrir o Gestor de pacote NuGet no projeto que concluídas tutorial [criar numa aplicação para iOS Xamarin] , em seguida, procure e instale o pacote de **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.

2. Abra o ficheiro QSTodoService.cs e remova os comentários a `#define OFFLINE_SYNC_ENABLED` definição.

3. Reconstruir e execute a aplicação de cliente. A aplicação funciona da mesma como estava antes de ter ativado sincronização offline. No entanto, a base de dados local agora é povoada com os dados que podem ser utilizados num cenário de offline.

## <a name="update-sync"></a>Atualizar a aplicação para desligar o back-end

Nesta secção, quebrar a ligação para o aplicação Mobile do back-end para simular uma situação offline. Quando adiciona itens de dados, o seu processador de exceção indica que a aplicação estiver em modo offline. Neste estado, os novos itens adicionada no arquivo local em serão sincronizados para o back-end da aplicação móvel quando push seguinte é executado num estado ligado.

1. Edite QSToDoService.cs no projeto partilhado. Altere o **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Também pode demonstram comportamento offline ao desativar a conta e redes móveis no dispositivo ou utilizar o modo de um avião.

2. Criar e executar a aplicação. Repare que a sincronização falha ao atualizar quando a aplicação iniciada.

3. Introduza os novos itens e repare que push falha com o estado de [CancelledByNetworkError] cada vez, clique em **Guardar**. Contudo, os novos itens todo existirem no arquivo local até que pode ser enviados para o aplicação móvel back-end.  Numa aplicação de produção, se suprimir estes exceções a aplicação de cliente comporta-se como ainda está ligado para o aplicação móvel back-end.

4. Feche a aplicação e reinicie-o para verificar que os novos itens que criou são permanentes para o arquivo local.

5. (Opcional) Se tiver o Visual Studio instalado num PC, abra o **Explorador de servidor**. Navegue para a base de dados no **Azure**-> **Bases de dados SQL**. A base de dados com o botão direito e selecione **Abrir no Explorador de objeto do SQL Server**. Agora, pode navegar para a sua tabela de base de dados SQL e os seus conteúdos. Certifique-se de que os dados na base de dados back-end não foi alterado.

6. (Opcional) Utilizar uma ferramenta de descanso como Fiddler ou Postman para consultar o seu back-end móvel, utilizando uma consulta de obter no formulário de `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação para voltar a ligar a sua aplicação Mobile do back-end

Nesta secção, voltar a ligar a aplicação para a aplicação móvel back-end. Isto simula a aplicação mover a partir de um estado offline para um estado online com o aplicação móvel back-end.   Se simulado a quebra de rede ao desativar a conectividade da rede, sem alterações de código são necessários.
Ative a rede novamente.  Ao executar a aplicação, pela primeira vez a `RefreshDataAsync` método é chamado. Isto sucessivamente chamadas `SyncAsync` para sincronizar o arquivo local com a base de dados back-end.

1. Abra o QSToDoService.cs no projeto partilhado e reverter a alteração da propriedade **applicationURL** .

2. Reconstruir e execute a aplicação. A aplicação sincroniza as suas alterações locais com a aplicação do Azure Mobile back-end, utilizando as operações de emissão e solicitação quando o `OnRefreshItemsSelected` método executa.

3. (Opcional) Ver dados atualizados com o Explorador de objeto do SQL Server ou uma ferramenta de descanso como Fiddler. Aviso dos dados tenha sido sincronizado entre a base de dados de back-end de aplicação do Azure Mobile e o arquivo local.

4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para conclui-los no arquivo local.

  `CompleteItemAsync`chamadas `SyncAsync` ao item de sincronização cada foi concluída com o aplicação Mobile back-end. `SyncAsync`chamadas emissão e solicitação.
  **Sempre que executa uma solicitação relativamente a uma tabela que o cliente tem de efetuar as alterações, um push no contexto de sincronização de cliente é sempre executada em primeiro lugar automaticamente**. A operação de push implícita assegura que todas as tabelas no arquivo local juntamente com relações permanecem consistentes. Para mais informações sobre este comportamento, consulte o artigo [Offline dados sincronização nas aplicações móveis do Azure].

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização de cliente

Projecto do cliente Xamarin que transferiu quando se já tiver concluído a [criar uma aplicação para iOS Xamarin] iniciação contém código sincronização offline utilizando uma base de dados local SQLite de suporte. Eis uma breve descrição geral do que já está incluído no código de iniciação. Para obter uma descrição geral conceptual da funcionalidade, consulte o artigo [Offline dados sincronização nas aplicações móveis do Azure].

* Antes de qualquer operações de tabela podem ser efetuadas, tem de ser inicializado o arquivo local. A base de dados do arquivo local é inicializado quando `QSTodoListViewController.ViewDidLoad()` executa `QSTodoService.InitializeStoreAsync()`. Este método cria um novo local SQLite da base de dados utilizando o `MobileServiceSQLiteStore` classe fornecido pelo cliente Azure Mobile aplicação SDK.

    O `DefineTable` método cria uma tabela no arquivo local que corresponda aos campos no tipo de fornecido, `ToDoItem` neste caso. Não tem o tipo de incluir todas as colunas que estejam na base de dados remota. É possível armazenar apenas um subconjunto de colunas.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* O `todoTable` membro `QSTodoService` é o `IMobileServiceSyncTable` escreva em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona os todos criam, ler, atualizam e eliminar (CRUD) operações de tabela para a base de dados do arquivo local.

    Decidir quando essas alterações são enviadas para o aplicação do Azure Mobile back-end ao contactar o suporte `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda-o a preservar relações de tabela por a controlar e conduza alterações em todas as tabelas para uma aplicação de cliente tenha modificado quando `PushAsync` chama-se.

    As chamadas de código fornecido `QSTodoService.SyncAsync()` para sincronizar sempre que a lista de todoitem é atualizada ou um todoitem é adicionado ou concluída. Sincroniza a aplicação depois de cada alteração local. Se uma solicitação é executada em relação a uma tabela que tem actualizações locais pendentes registadas pelo contexto, essa operação solicitação será automaticamente acionar uma push contexto pela primeira vez.

    No código fornecido, todos os registos no controlo remoto `TodoItem` tabela são avisados, mas também é possível filtrar registos, passando um id de consulta e de consulta para `PushAsync`. Para mais informações, consulte a secção *Sincronização utilizarão* na [Offline dados sincronização nas aplicações móveis do Azure].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }


## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline no Azure aplicações móveis]
* [How to do Azure aplicações móveis .NET SDK][8]

<!-- Images -->

<!-- URLs. -->
[Criar uma aplicação para iOS Xamarin]: app-service-mobile-xamarin-ios-get-started.md
[Sincronização de dados offline no Azure aplicações móveis]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md