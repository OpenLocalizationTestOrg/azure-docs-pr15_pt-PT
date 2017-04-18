<properties
    pageTitle="Ativar a sincronização offline para a sua aplicação de Mobile Azure (Android)"
    description="Saiba como utilizar aplicações do serviço de aplicação Mobile a cache e sincronização dados offline no seu Android aplicação"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Ativar a sincronização offline para a sua aplicação móvel Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial abrange a funcionalidade de sincronização offline de aplicações do Azure Mobile para Android. Sincronização offline permite aos utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o back-end remoto.

Se esta é a sua primeira experiência com aplicações do Azure Mobile, primeiro deve concluir o tutorial [criar uma aplicação do Android]. Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Sincronização de dados Offline no Azure Mobile aplicações].

## <a name="update-the-app-to-support-offline-sync"></a>Atualizar a aplicação para suportar a sincronização offline

Com a sincronização offline, para de leitura e escrita a partir de uma *tabela de sincronização* (utilizando a interface de *IMobileServiceSyncTable* ), que faz parte de uma base de dados **SQLite** no seu dispositivo.

Para notificações push e separar alterações entre o dispositivo e dos serviços do Azure Mobile, utilize um *contexto de sincronização* (*MobileServiceClient.SyncContext*), que inicialização com a base de dados local para armazenar localmente dados.

1. No `TodoActivity.java`, comenta a definição existente de `mToDoTable` e remova os comentários da versão de tabela de sincronização:

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. No `onCreate` método, comenta a inicialização existente do `mToDoTable` e remova os comentários esta definição:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. No `refreshItemsFromTable` comenta a definição de `results` e remova os comentários esta definição:

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Comentário saída a definição de `refreshItemsFromMobileServiceTable`.

5. Remova os comentários a definição de `refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. Remova os comentários a definição de `sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testar a aplicação

Nesta secção, teste o comportamento com conta em e, em seguida, desativar conta para criar um cenário offline.

Quando adiciona itens de dados, estes são mantidas na loja SQLite local, mas não sincronizados com o serviço móvel até premir o botão **Atualizar** . Outras aplicações podem ter requisitos diferentes relativamente ao quando necessitam de dados a ser sincronizada mas, para fins de demonstração este tutorial tem o utilizador pedir explicitamente.

Quando prime esse botão, inicia uma nova tarefa de fundo. Emite-lo em primeiro lugar, todas as alterações efetuadas para o arquivo local com o contexto de sincronização, em seguida, puxa todos os alteradas dados a partir do Azure à tabela local.

### <a name="offline-testing"></a>Testes offline

1. Coloque o dispositivo ou simulator no *Modo de um avião*. Esta ação cria um cenário offline.

2. Adicione alguns itens de *ToDo* ou marcar alguns itens como concluída. Saia do dispositivo ou simulator (ou forçar fecha a aplicação) e reiniciar. Certifique-se de que as suas alterações tem sido persistentes no dispositivo, uma vez que estes ocorrem na loja SQLite local.

3. Ver o conteúdo da tabela Azure *TodoItem* quer seja com SQL ferramenta como *SQL Server Management Studio*ou um cliente de descanso como *Fiddler* ou *Postman*. Certifique-se de que os novos itens _não_ foram sincronizados para o servidor

    + Para um back-end Node.js, aceda ao [portal do Azure](https://portal.azure.com/)e, na sua aplicação Mobile back-end clique **Tabelas fácil** > **TodoItem** para ver o conteúdo do `TodoItem` tabela.
    + Para um back-end .NET, ver o conteúdo da tabela com uma ferramenta SQL como *SQL Server Management Studio*ou um cliente de descanso como *Fiddler* ou *Postman*.

4. Ative a conta no dispositivo ou simulator. Em seguida, prima o botão **Atualizar** .

5. Ver os dados de TodoItem novamente no portal do Azure. Agora deverá aparecer a TodoItems novas e alteradas.

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline no Azure aplicações móveis]

* [Na nuvem rosto: Sincronização Offline no Azure serviços móveis] \(Nota: o vídeo está no Mobile Services, mas sincronização offline funciona de forma semelhante em aplicações móveis do Azure\)


<!-- URLs. -->

[Sincronização de dados offline no Azure aplicações móveis]: app-service-mobile-offline-data-sync.md

[Criar uma aplicação do Android]: app-service-mobile-android-get-started.md

[Capa da nuvem: Sincronização Offline no Azure serviços móveis]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

