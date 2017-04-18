<properties
    pageTitle="Ativar a sincronização offline para a sua aplicação de Mobile Azure (Xamarin.Forms) | Microsoft Azure"
    description="Saiba como utilizar a aplicação do aplicação do serviço Mobile a cache e sincronização dados offline na sua aplicação Xamarin.Forms"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Ativar a sincronização offline para a sua aplicação móvel Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial apresenta a funcionalidade de sincronização offline do Azure Mobile aplicações para Xamarin.Forms. Sincronização offline permite aos utilizadores finais interagir com uma aplicação móvel – ver, adicionar ou modificar dados – mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o serviço de remoto.

Neste tutorial é baseado a solução de guia de introdução Xamarin.Forms para as aplicações móveis que criar quando concluir o tutorial [criar numa aplicação para iOS Xamarin]. A solução de guia de introdução para Xamarin.Forms contém o código para suportar a sincronização offline, que apenas tem de estar ativada. Neste tutorial, atualize a solução de guia de introdução para ativar as funcionalidades de offline do Azure Mobile aplicações. Recomendamos também realçar o código offline específicas na aplicação. Se não utilizar a solução de guia de introdução transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure][1].

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico de [Sincronização de dados Offline nas aplicações móveis do Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Ativar a funcionalidade de sincronização offline da solução de guia de introdução

O código de sincronização offline está incluído no projeto utilizando c# pré-processador. Quando o **OFFLINE\_SINCRONIZAÇÃO\_ATIVADO** símbolo é definido, estes caminhos de código estão incluídos na compilação. Para as aplicações do Windows, também tem de instalar a plataforma SQLite.

1. No Visual Studio, botão direito do rato a solução > **Gerir pacotes de NuGet para solução...**, em seguida, procurar e instalar o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos os projetos da solução.

2. No Explorador de solução, abra o ficheiro de TodoItemManager.cs do projeto **portátil** com o nome, o que é o projeto de biblioteca de classes portátil, em seguida, remova os comentários a diretiva de pré-processador seguinte:

        #define OFFLINE_SYNC_ENABLED

4. (Opcional) Para suportar dispositivos com Windows, instale um dos seguintes pacotes de runtime SQLite:

    * **Runtime do Windows 8.1:** Instalar [SQLite para o Windows 8.1][3].
    * **Do windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1][4].
    * **Plataforma universal Windows** Instalar [SQLite para o Universal Windows Universal][5].

    Apesar do guia de introdução não contiver um projeto Universal Windows, a plataforma Universal Windows é suportada com formulários do Xamarin.

5. (Opcional) Em cada projecto de aplicação do Windows, com o botão direito **referências** > **Add Reference...**, expanda a pasta do **Windows** > **extensões**.
    Ative adequado **SQLite para Windows** SDK juntamente com o **Visual C++ 2013 Runtime para o Windows** SDK.
    Os nomes de SQLite SDK variam ligeiramente com cada plataforma Windows.

## <a name="review-the-client-sync-code"></a>Rever o código de sincronização de cliente

Eis uma breve descrição geral do que já está incluído no código de iniciação dentro de `#if OFFLINE_SYNC_ENABLED` diretivas do. A funcionalidade de sincronização offline está no ficheiro de projeto TodoItemManager.cs no projeto a biblioteca de classes portátil. Para obter uma descrição geral conceptual da funcionalidade, consulte o artigo [Sincronizar de dados Offline nas aplicações móveis do Azure][2].

* Antes de qualquer operações de tabela podem ser efetuadas, tem de ser inicializado o arquivo local. A base de dados do arquivo local é inicializado no construtor de classe **TodoItemManager** utilizando o seguinte código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Este código cria um novo local SQLite da base de dados utilizando a classe de **MobileServiceSQLiteStore** .

    O método de **DefineTable** cria uma tabela no arquivo local que corresponda aos campos no tipo de fornecido.  Não tem o tipo de incluir todas as colunas que estejam na base de dados remota. É possível armazenar um subconjunto de colunas.

* O campo **todoTable** **TodoItemManager** é um tipo de **IMobileServiceSyncTable** em vez de **IMobileServiceTable**. Este utilizações de classe base de dados local para todas as criar, ler, atualizar e eliminar (CRUD) operações de tabela. Decida quando essas alterações são enviadas para o aplicação Mobile back-end ao contactar o suporte **PushAsync** a **IMobileServiceSyncContext**. O contexto de sincronização ajuda preservar relações de tabela por a controlar e conduza alterações em todas as tabelas para que uma aplicação de cliente tenha modificado quando é chamado **PushAsync** .

    O seguinte método **SyncAsync** chama-se para a sincronização com a aplicação Mobile back-end:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Este exemplo utiliza com o suporte de sincronização predefinido de processamento de erros simples. Uma aplicação real seria processar vários erros como as condições da rede e conflitos de servidor ao utilizar uma implementação **IMobileServiceSyncHandler** personalizada.

## <a name="offline-sync-considerations"></a>Considerações sobre a sincronização offline

No exemplo, o método de **SyncAsync** apenas denomina-se no arranque e quando é necessária uma sincronização.  Para iniciar uma sincronização numa aplicação do Android ou iOS, separar para baixo na lista de itens; para o Windows, utilize o botão **sincronizar** . Numa aplicação reais, também pode fazer o accionador sincronização quando altera o estado da rede.

Quando uma solicitação é executada em relação a uma tabela que tenha pendente atualizações locais controlada pelo contexto que separar operação automaticamente accionadores um push contexto anterior. Quando atualizar, adicionar e concluir os itens neste exemplo, pode omitir a chamada **PushAsync** explícita.

No código fornecido, todos os registos na tabela TodoItem remoto são avisados, mas também é possível filtrar registos, passando um id de consulta e uma consulta para **PushAsync**. Para obter mais informações, consulte a secção *Sincronização utilizarão* na [Offline dados sincronização nas aplicações móveis do Azure][2].

## <a name="run-the-client-app"></a>Executar a aplicação de cliente

Com a sincronização offline agora ativada, execute a aplicação de cliente, pelo menos, uma vez por cada plataforma para preencher a base de dados do arquivo local. Mais tarde, simular um cenário offline e modifique os dados no arquivo local enquanto a aplicação está offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Atualizar o comportamento de sincronização da aplicação de cliente

Nesta secção, modifique o projeto de cliente para simular um cenário offline utilizando um URL de aplicação inválido para o seu back-end. Em alternativa, pode desativar ligações de rede ao mover o seu dispositivo para "Modo de um avião".  Quando adicionar ou alterar itens de dados, estas alterações são contidas no arquivo local, mas não sincronizadas com o arquivo de dados back-end até que a ligação é estabelecida novamente.

1. No Explorador de solução, abra o ficheiro de projeto de Constants.cs do projeto **portáteis** e altere o valor do `ApplicationURL` para apontar para um URL inválido:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. Abra o ficheiro de TodoItemManager.cs do projeto **portátil** , em seguida, adicionar uma **captura** para a classe de **exceção** base ao bloco de **... Experimente capturas** no **SyncAsync**. Este bloco **de captura** escreve a mensagem de exceção da consola, da seguinte forma:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Criar e executar a aplicação de cliente.  Adicione alguns novos itens. Repare que uma exceção tem sessão iniciada na consola de cada tentativa sincronizar com o back-end. Estes novos itens existem apenas no arquivo local até que pode ser enviados para o telemóvel back-end. A aplicação de cliente comporta-se como que está ligado ao back-end, de suporte que todos a criar, ler, atualizar, eliminar (CRUD) operações.

4. Feche a aplicação e reinicie-o para verificar que os novos itens que criou são permanentes para o arquivo local.

5. (Opcional) Utilize o Visual Studio para ver a sua tabela de base de dados do Azure SQL para ver que os dados na base de dados back-end não foi alterado.

    No Visual Studio, abra o **Explorador de servidor**. Navegue para a base de dados no **Azure**->**Bases de dados SQL**. A base de dados com o botão direito e selecione **Abrir no Explorador de objeto do SQL Server**. Agora, pode navegar para a sua tabela de base de dados SQL e os seus conteúdos.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Atualizar a aplicação de cliente para voltar a ligar a sua móvel back-end

Nesta secção, voltar a ligar a aplicação móvel back-end, que simula a aplicação novamente a chegar a um estado online. Ao efetuar o gesto de atualização, dados estiver sincronizados com o seu telemóvel back-end.

1. Reabra Constants.cs. Corrigir o `applicationURL` para apontarem para o URL correto.

2. Reconstruir e execute a aplicação de cliente. Tenta sincronizar com o aplicação móvel back-end depois de iniciar a aplicação. Certifique-se de que nenhuma exceções com sessão iniciadas na consola de depuração.

3. (Opcional) Ver os dados atualizados com SQL Server Object Explorer ou uma ferramenta de descanso como Fiddler ou [Postman][6]. Repare que os dados tem foram sincronizados entre a base de dados back-end e o arquivo local.

    Repare os dados sincronizou entre a base de dados e o arquivo local contém os itens que adicionou enquanto a aplicação foi desligada.

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline no Azure aplicações móveis][2]
* [How to do Azure aplicações móveis .NET SDK][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md