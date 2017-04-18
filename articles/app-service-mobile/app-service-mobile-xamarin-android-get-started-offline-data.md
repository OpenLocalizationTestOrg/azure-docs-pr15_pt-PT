<properties
    pageTitle="Ativar a sincronização offline para a sua aplicação de Mobile Azure (Xamarin Android)"
    description="Saiba como utilizar a aplicação do aplicação do serviço Mobile a cache e sincronização dados offline na sua aplicação Xamarin Android"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Ativar a sincronização offline para a sua aplicação móvel Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial apresenta a funcionalidade de sincronização offline do Azure Mobile aplicações para Xamarin.Android. Sincronização offline permite aos utilizadores finais interagir com uma aplicação móvel – ver, adicionar ou modificar dados – mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local.
Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço de remoto.

Neste tutorial, pode atualizar o projeto cliente a partir da iniciação ao [criar uma aplicação do Xamarin Android] para suportar as funcionalidades de offline do Azure Mobile aplicações. Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Sincronização de dados Offline no Azure Mobile aplicações].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar funcionalidades offline

Funcionalidades de offline aplicação Mobile Azure permitem-lhe interagir com uma base de dados local quando estiver num cenário de offline. Para utilizar estas funcionalidades na sua aplicação, iniciar uma [SyncContext] para um arquivo local. Referência, em seguida, a sua tabela através da interface do [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite é utilizado como o arquivo local no dispositivo.

1. No Visual Studio, abra o Gestor de pacote NuGet no projecto que tiver concluído no tutorial [criar uma aplicação do Xamarin Android] .  Procure e instale o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

2. Abra o ficheiro ToDoActivity.cs e remova os comentários a `#define OFFLINE_SYNC_ENABLED` definição.

3. No Visual Studio, prima a tecla **F5** reconstruir e executar a aplicação de cliente. A aplicação funciona da mesma como estava antes de ter ativado sincronização offline. No entanto, a base de dados local agora é povoada com os dados que podem ser utilizados num cenário de offline.

## <a name="update-sync"></a>Atualizar a aplicação para desligar o back-end

Nesta secção, quebrar a ligação para o aplicação Mobile do back-end para simular uma situação offline. Quando adiciona itens de dados, o seu processador de exceção indica que a aplicação estiver em modo offline. Neste estado, os novos itens adicionada no arquivo local em são sincronizados para o back-end da aplicação móvel quando um push for executado num estado ligado.

1. Edite ToDoActivity.cs no projeto partilhado. Altere o **applicationURL** para apontar para um URL inválido:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Também pode demonstram comportamento offline ao desativar a conta e redes móveis no dispositivo ou utilizar o modo de um avião.

2. Prima **F5** para criar e executar a aplicação. Repare que a sincronização falha ao atualizar quando a aplicação iniciada.

3. Introduza os novos itens e repare que push falha com o estado de [CancelledByNetworkError] cada vez, clique em **Guardar**. Contudo, os novos itens todo existirem no arquivo local até que pode ser enviados para o aplicação móvel back-end.  Numa aplicação de produção, se suprimir estes exceções a aplicação de cliente comporta-se como ainda está ligado para o aplicação móvel back-end.

4. Feche a aplicação e reinicie-o para verificar que os novos itens que criou são permanentes para o arquivo local.

5. (Opcional) No Visual Studio, abra o **Explorador de servidor**. Navegue para a base de dados no **Azure**->**Bases de dados SQL**. A base de dados com o botão direito e selecione **Abrir no Explorador de objeto do SQL Server**. Agora, pode navegar para a sua tabela de base de dados SQL e os seus conteúdos. Certifique-se de que os dados na base de dados back-end não foi alterado.

6. (Opcional) Utilizar uma ferramenta de descanso como Fiddler ou Postman para consultar o seu back-end móvel, utilizando uma consulta de obter no formulário de `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação para voltar a ligar a sua aplicação Mobile do back-end

Nesta secção, voltar a ligar a aplicação para a aplicação móvel back-end. Ao executar a aplicação, pela primeira vez a `OnCreate` chamadas de processador de eventos `OnRefreshItemsSelected`. Este método chamadas `SyncAsync` para sincronizar o arquivo local com a base de dados back-end.

1. Abra o ToDoActivity.cs no projeto partilhado e reverter a alteração da propriedade **applicationURL** .

2. Prima a tecla **F5** reconstruir e executar a aplicação. A aplicação sincroniza as suas alterações locais com a aplicação do Azure Mobile back-end, utilizando as operações de emissão e solicitação quando o `OnRefreshItemsSelected` método executa.

3. (Opcional) Ver dados atualizados com o Explorador de objeto do SQL Server ou uma ferramenta de descanso como Fiddler. Aviso dos dados tenha sido sincronizado entre a base de dados de back-end de aplicação do Azure Mobile e o arquivo local.

4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para conclui-los no arquivo local.

  `CheckItem`chamadas `SyncAsync` ao item de sincronização cada foi concluída com o aplicação Mobile back-end. `SyncAsync`chamadas emissão e solicitação. **Sempre que executa uma solicitação relativamente a uma tabela que o cliente tem de efetuar as alterações, um push sempre é executado automaticamente**. Isto assegura que todas as tabelas no arquivo local juntamente com relações permanecem consistentes. Este comportamento pode resultar numa push inesperado. Para mais informações sobre este comportamento, consulte o artigo [Offline dados sincronização nas aplicações móveis do Azure].

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização de cliente

Projecto do cliente Xamarin que transferiu quando se já tiver concluído a iniciação ao [criar uma aplicação Xamarin Android] contém código sincronização offline utilizando uma base de dados local SQLite de suporte. Eis uma breve descrição geral do que já está incluído na tutorial ome do nó c. Para obter uma descrição geral conceptual da funcionalidade, consulte o artigo [Offline dados sincronização nas aplicações móveis do Azure].

* Antes de qualquer operações de tabela podem ser efetuadas, tem de ser inicializado o arquivo local. A base de dados do arquivo local é inicializado quando `ToDoActivity.OnCreate()` executa `ToDoActivity.InitLocalStoreAsync()`. Este método cria um local SQLite da base de dados utilizando o `MobileServiceSQLiteStore` classe fornecido pelo cliente Azure Mobile aplicações SDK.

    O `DefineTable` método cria uma tabela no arquivo local que corresponda aos campos no tipo de fornecido, `ToDoItem` neste caso. Não tem o tipo de incluir todas as colunas que estejam na base de dados remota. É possível armazenar apenas um subconjunto de colunas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* O `toDoTable` membro `ToDoActivity` da `IMobileServiceSyncTable` escreva em vez de `IMobileServiceTable`. O IMobileServiceSyncTable direciona os todos criam, ler, atualizam e eliminar (CRUD) operações de tabela para a base de dados do arquivo local.

    Decidir quando as alterações são enviadas para o aplicação do Azure Mobile back-end ao contactar o suporte `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda-o a preservar relações de tabela por a controlar e conduza alterações em todas as tabelas para uma aplicação de cliente tenha modificado quando `PushAsync` chama-se.

    As chamadas de código fornecido `ToDoActivity.SyncAsync()` para sincronizar sempre que a lista de todoitem é atualizada ou um todoitem é adicionado ou concluída. Sincroniza o código depois de cada alteração local.

    No código fornecido, todos os registos no controlo remoto `TodoItem` tabela são avisados, mas também é possível filtrar registos, passando um id de consulta e de consulta para `PushAsync`. Para mais informações, consulte a secção *Sincronização utilizarão* na [Offline dados sincronização nas aplicações móveis do Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline no Azure aplicações móveis]
* [How to do Azure aplicações móveis .NET SDK][8]

<!-- URLs. -->
[Criar uma aplicação Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Sincronização de dados offline no Azure aplicações móveis]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Criar uma aplicação Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Sincronização de dados offline no Azure aplicações móveis]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
