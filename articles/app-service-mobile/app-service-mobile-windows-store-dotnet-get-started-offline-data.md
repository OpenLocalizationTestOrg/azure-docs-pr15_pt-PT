<properties
    pageTitle="Ativar a sincronização offline para a sua aplicação Universal Windows plataforma (UWP) com aplicações Mobile | Azure de aplicação de serviço"
    description="Saiba como utilizar uma aplicação móvel do Azure a cache e sincronização dados offline na sua aplicação Universal Windows plataforma (UWP)."
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>Ativar a sincronização offline para a sua aplicação do Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como adicionar suporte offline para uma aplicação de Universal Windows plataforma (UWP) com um aplicação do Azure Mobile do back-end. Sincronização offline permite aos utilizadores finais interagir com uma aplicação móvel – ver, adicionar ou modificar dados - mesmo quando não existe nenhuma ligação de rede. As alterações são armazenadas numa base de dados local. Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o back-end remoto.

Neste tutorial, atualize o projeto de aplicação UWP iniciação ao [criar uma aplicação do Windows] para suportar as funcionalidades de offline do Azure Mobile aplicações. Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Sincronização de dados Offline no Azure Mobile aplicações].

## <a name="requirements"></a>Requisitos de

Neste tutorial requer as pré-requisitos do seguintes:

* Visual Studio 2013 em execução no Windows 8.1 ou posterior.
* Conclusão de [criar uma aplicação do Windows],[criar uma aplicação do windows].
* [Arquivo de SQLite do Azure Mobile serviços][sqlite store nuget]
* [SQLite para o desenvolvimento de plataforma Windows Universal](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Atualizar a aplicação de cliente para suportar funcionalidades offline

Funcionalidades de offline aplicação Mobile Azure permitem-lhe interagir com uma base de dados local quando estiver num cenário de offline. Para utilizar estas funcionalidades na sua aplicação, pode iniciar uma [SyncContext] [ synccontext] para um arquivo local. Referência, em seguida, a sua tabela através da interface do[IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite é utilizado como o arquivo local no dispositivo.

1. Instale o [tempo de execução do SQLite para a plataforma Windows Universal](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. No Visual Studio, abra o Gestor de pacote NuGet para o projeto de aplicação UWP concluída no tutorial [criar uma aplicação do Windows] .
    Procure e instale o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

4. No Explorador de solução, com o botão direito **referências** > **Add Reference...**  >  **Universal Windows** > **extensões**, em seguida, ativar **SQLite para Universal plataforma Windows** e **Visual C++ 2015 Runtime para aplicações Universal plataforma Windows**.

    ![Adicionar uma referência de SQLite UWP][1]

5. Abra o ficheiro MainPage.xaml.cs e remova os comentários a `#define OFFLINE_SYNC_ENABLED` definição.

6. No Visual Studio, prima a tecla **F5** reconstruir e executar a aplicação de cliente. A aplicação funciona da mesma como estava antes de ter ativado sincronização offline. No entanto, a base de dados local agora é povoada com os dados que podem ser utilizados num cenário de offline.

## <a name="update-sync"></a>Atualizar a aplicação para desligar o back-end

Nesta secção, quebrar a ligação para o aplicação Mobile do back-end para simular uma situação offline. Quando adiciona itens de dados, o seu processador de exceção indica que a aplicação estiver em modo offline. Neste estado, os novos itens adicionada no arquivo local em serão sincronizados para o back-end da aplicação móvel quando push seguinte é executado num estado ligado.

1. Edite App.xaml.cs no projeto partilhado. Comentar a inicialização da **MobileServiceClient** e adicione a linha seguinte, que utiliza um URL da aplicação móvel inválidos:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Também pode demonstrar comportamento offline ao desativar a conta e redes móveis no dispositivo ou utilize o modo de um avião.

2. Prima **F5** para criar e executar a aplicação. Repare que a sincronização falha ao atualizar quando a aplicação iniciada.

3. Introduza os novos itens e repare que push falha com o estado de [CancelledByNetworkError] cada vez, clique em **Guardar**. Contudo, os novos itens de todo existirem no arquivo local até que pode ser enviados para o aplicação móvel back-end.  Numa aplicação de produção, se suprimir estes exceções a aplicação de cliente comporta-se como ainda está ligado para o aplicação móvel back-end.

4. Feche a aplicação e reinicie-o para verificar que os novos itens que criou são permanentes para o arquivo local.

5. (Opcional) No Visual Studio, abra o **Explorador de servidor**. Navegue para a base de dados no **Azure**->**Bases de dados SQL**. A base de dados com o botão direito e selecione **Abrir no Explorador de objeto do SQL Server**. Agora, pode navegar para a sua tabela de base de dados SQL e os seus conteúdos. Certifique-se de que os dados na base de dados back-end não foi alterado.

6. (Opcional) Utilizar uma ferramenta de descanso como Fiddler ou Postman para consultar o seu back-end móvel, utilizando uma consulta de obter no formulário de `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualizar a aplicação para voltar a ligar a sua aplicação Mobile do back-end

Nesta secção, restabelecer a ligação na aplicação para a aplicação móvel back-end. Estas alterações simular uma nova ligação de rede na aplicação.

Ao executar a aplicação, pela primeira vez a `OnNavigatedTo` chamadas de processador de eventos `InitLocalStoreAsync`. Este método sucessivamente chamadas `SyncAsync` para sincronizar o arquivo local com a base de dados back-end. A aplicação tenta sincronizar no arranque.

1. Abra o App.xaml.cs no projeto partilhado e remova os comentários do seu inicialização anterior da `MobileServiceClient` para utilizar a correta o URL da aplicação móvel.

2. Prima a tecla **F5** reconstruir e executar a aplicação. A aplicação sincroniza as suas alterações locais com a aplicação do Azure Mobile back-end, utilizando as operações de emissão e solicitação quando o `OnNavigatedTo` executa processador de eventos.

3. (Opcional) Ver dados atualizados com o Explorador de objeto do SQL Server ou uma ferramenta de descanso como Fiddler. Aviso dos dados tenha sido sincronizado entre a base de dados de back-end de aplicação do Azure Mobile e o arquivo local.

4. Na aplicação, clique na caixa de verificação ao lado de alguns itens para conclui-los no arquivo local.

  `UpdateCheckedTodoItem`chamadas `SyncAsync` ao item de sincronização cada foi concluída com o aplicação Mobile back-end. `SyncAsync`chamadas emissão e solicitação. No entanto, **sempre que executa uma solicitação relativamente a uma tabela que o cliente tem de efetuar as alterações, um push sempre é executado automaticamente**. Este comportamento assegura que todas as tabelas no arquivo local juntamente com relações permanecem consistentes. Este comportamento pode resultar numa push inesperado.  Para mais informações sobre este comportamento, consulte o artigo [Offline dados sincronização nas aplicações móveis do Azure].


##<a name="api-summary"></a>API de resumo

Para suportar as funcionalidades de serviços móveis offline, podemos utilizado a interface de [IMobileServiceSyncTable] e inicializado [MobileServiceClient.SyncContext] [ synccontext] com uma base de dados local SQLite. Quando estiver offline, as operações CRUD normais para aplicações Mobile trabalhar como se a aplicação ainda está ligada enquanto que as operações ocorrem contra o arquivo local. Métodos seguintes são utilizados para sincronizar o arquivo local com o servidor:

*  **[PushAsync]** Uma vez que este método é um membro da [IMobileServicesSyncContext], as alterações ao longo de todas as tabelas são enviadas para o back-end. Apenas os registos com alterações locais são enviados para o servidor.

* **[PullAsync] ** 
   uma solicitação é iniciada a partir de um [IMobileServiceSyncTable]. Quando existirem alterações registadas na tabela, um push implícito é executada para se certificar de que todas as tabelas no arquivo local juntamente com relações permanecem consistentes. O parâmetro de *pushOtherTables* controla se são seguia outras tabelas no contexto num push implícito. O parâmetro de *consulta* leva-o até uma [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
   ou OData cadeia de consulta para filtrar os dados devolvidos. O parâmetro *queryId* é utilizado para definir utilizarão sincronização. Para mais informações, consulte o artigo  [Offline dados sincronização nas aplicações móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** A aplicação deverá periodicamente chamar este método para remover dados obsoletos a partir da loja local. Utilize o parâmetro *Forçar* quando precisar de remover quaisquer alterações que ainda não foram sincronizadas.

Para mais informações sobre estes conceitos, consulte o artigo [Offline dados sincronização nas aplicações móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mais informações

Os tópicos seguintes fornecem informações gerais adicionais sobre a funcionalidade de sincronização offline de aplicações móveis:

* [Sincronização de dados offline no Azure aplicações móveis]
* [How to do Azure aplicações móveis .NET SDK][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronização de dados offline no Azure aplicações móveis]: app-service-mobile-offline-data-sync.md
[criar uma aplicação do windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
