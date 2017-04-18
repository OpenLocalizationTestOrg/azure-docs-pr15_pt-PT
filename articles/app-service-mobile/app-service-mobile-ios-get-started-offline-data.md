<properties
    pageTitle="Ativar a sincronização offline para a sua aplicação de Mobile Azure (iOS)"
    description="Saiba como utilizar aplicações do serviço de aplicação Mobile a cache e sincronização dados offline na aplicação do iOS"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Ativar a sincronização offline para a sua aplicação de dispositivos móveis iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial abrange a funcionalidade de sincronização offline do Azure Mobile aplicações para iOS. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede. As alterações são guardadas na base de dados local; Assim que o dispositivo estiver novamente online, estas alterações são sincronizadas com o back-end remoto.

Se esta é a sua primeira experiência com aplicações do Azure Mobile, primeiro deve concluir o tutorial [criar uma aplicação iOS do]. Se não utiliza o projeto de servidor do guia de introdução do transferido, tem de adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para mais informações sobre pacotes de extensão server, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre a funcionalidade de sincronização offline, consulte o tópico [Sincronização de dados Offline no Azure Mobile aplicações].

## <a name="review-sync"></a>Rever o código de sincronização de cliente

Projecto do cliente que transferiu para o tutorial [criar uma aplicação iOS do] já contém o código de sincronização offline utilizando uma base de dados local com base em dados essenciais de suporte. Esta secção é um resumo do que já está incluído no código de iniciação. Para obter uma descrição geral conceptual da funcionalidade, consulte o artigo [Offline dados sincronização nas aplicações móveis do Azure].

A funcionalidade de sincronização de sincronização de dados offline de Azure Mobile aplicações permite aos utilizadores finais interagir com uma base de dados local quando a rede não está acessível. Para utilizar estas funcionalidades na sua aplicação, pode iniciar o contexto de sincronização do `MSClient` e um arquivo local de referência. Referência, em seguida, a sua tabela através de `MSSyncTable` interface.

1. No **QSTodoService.m** (objectivo-C) ou **ToDoTableViewController.swift** (Swift), repare o tipo do membro `syncTable` é `MSSyncTable`. Sincronização offline utiliza esta interface da tabela de sincronização em vez de `MSTable`. Quando é utilizada uma tabela de sincronização, todas as operações de ir para o arquivo local e apenas são sincronizadas com o back-end remote com operações de emissão e solicitação explícitos.

    Para obter uma referência a uma tabela de sincronização, utilize o método `syncTableWithName` no `MSClient`. Para remover a funcionalidade de sincronização offline, utilize `tableWithName` em vez disso.

2. Antes de qualquer operações de tabela podem ser efetuadas, tem de ser inicializado o arquivo local. Eis o código relevante. 
    
    **Objectivo-C**:
    
    No `QSTodoService.init` método:
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **Swift**:
    
    No `ToDoTableViewController.viewDidLoad` método:
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    Esta ação cria um arquivo local utilizando a interface do `MSCoreDataStore`, que é fornecido no SDK aplicações móveis. Em vez disso pode um fornecer um arquivo local diferente através da implementação a `MSSyncContextDataSource` protocolo. 
    
    Além disso, o primeiro parâmetro da `MSSyncContext` é utilizada para especificar um processador de conflito. Uma vez que recomendamos passaram `nil`, podemos receberão processador de conflito predefinido, falha em qualquer conflito.
    
3. Agora, vamos executar a operação de sincronização real e obter dados de back-end remoto.

    **Objectivo-C**:
    
    `syncData`em primeiro lugar emite novas alterações, em seguida, chama `pullData` para obter dados de back-end remoto. Em ativar, o método `pullData` obtém novos dados que correspondem a uma consulta:
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **Swift**:
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    Na versão objectivo-C, no `syncData`, chamamos pela primeira vez `pushWithCompletion` no contexto de sincronização. Este método é um membro da `MSSyncContext` (em vez da sincronização da tabela própria) porque será de emissão alterações ao longo de todas as tabelas. Apenas os registos que foram modificados algumas forma localmente (através de operações de CUD) serão enviados para o servidor. Em seguida, a auxiliar `pullData` denomina-se, as chamadas `MSSyncTable.pullWithQuery` para recuperar dados remotos e armazenar na base de dados local.
    
    Na versão rápida, não existe nenhuma chamada para `pushWithCompletion`. Isto acontece porque a operação de emissão não era estritamente necessária. Se existirem quaisquer alterações pendentes no contexto de sincronização para a tabela que está a fazer uma operação de push, separar sempre problemas um push pela primeira vez. No entanto, se tiver mais do que uma tabela de sincronização, é melhor chamar explicitamente push para se certificar de que tudo está consistente em tabelas relacionadas.
    
    Nas versões objectivo-C tanto Swift, o método `pullWithQuery` permite-lhe especificar uma consulta para filtrar os registos que pretende obter. Neste exemplo, a consulta apenas obtém todos os registos de controlo remoto `TodoItem` tabela.
    
    O segundo parâmetro para `pullWithQuery` é um ID de consulta que é utilizado para *sincronizar utilizarão*. Sincronização utilizarão obtém apenas os registos desde a última sincronização, utilizando o registo de modificação `UpdatedAt` carimbo (denominado `updatedAt` no arquivo local.) O ID da consulta deve ser uma cadeia descritiva que seja exclusiva para cada consulta lógica na sua aplicação. Para optar ativamente de sincronização elementar, passar `nil` como o ID da consulta. Tenha em atenção que isto pode ser potencialmente ineficaz, uma vez que irá obter todos os registos em cada operação de solicitação.

5. Sincroniza de aplicação objectivo-C quando podemos modificar ou adicionar dados, um utilizador efetua o gesto de atualização e na iniciação. A aplicação rápida sincroniza as quando um utilizador efetua o gesto de atualização e na iniciação. 

Uma vez que o sincroniza de aplicação sempre que os dados são modificados (objectivo-C) ou sempre que inicia a aplicação (objectivo-C & Swift), a aplicação assume que o utilizador está online. Na secção outra, podemos irá atualizar a aplicação para que os utilizadores podem editar mesmo quando estão offline.

## <a name="review-core-data"></a>Rever o modelo de dados principais

Ao utilizar o arquivo offline Core dados, tem de definir determinadas tabelas e campos no seu modelo de dados. A aplicação de exemplo já inclui um modelo de dados com o formato correto. Nesta secção podemos irá guiá-lo através destas tabelas e como são utilizadas.

- Abra **QSDataModel.xcdatamodeld**. Existem quatro tabelas definidas - três que são utilizados pelo SDK, e itens de uma tabela para a todo próprios:     *MS_TableOperations: para controlar os itens que têm de ser sincronizados com o servidor    * MS_TableOperationErrors: para controlar quaisquer erros que ocorrem durante a sincronização offline     *MS_TableConfig: para controlar o último atualizados hora para a última operação de sincronização para todas as operações de solicitação    * TodoItem : Para armazenar os itens de todo. O sistema colunas **createdAt**, **updatedAt**e **versão** são propriedades do sistema opcional.

>[AZURE.NOTE] O SDK do Azure Mobile aplicações reserva-se os nomes das colunas que a ser com "**``**". Não deve utilizar este prefixo no nada que não seja colunas de sistema, caso contrário, serão modificados nomes de coluna ao utilizar o remoto back-end.

- Quando utilizar a funcionalidade de sincronização offline, tem de definir as tabelas de sistema conforme apresentado abaixo.

    ### <a name="system-tables"></a>Tabelas de sistema

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Inteiro de 64  |
  	| itemId     | Cadeia      |
  	| Propriedades | Dados binários |
  	| tabela      | Cadeia      |
  	| tableKind  | Inteiro de 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Cadeia      |
  	| IDOperação | Inteiro de 64 |
  	| Propriedades | Dados binários |
  	| tableKind  | Inteiro de 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| ID         | Cadeia      |
  	| chave        | Cadeia      |
  	| keyType    | Inteiro de 64  |
  	| tabela      | Cadeia      |
  	| valor      | Cadeia      |

    ### <a name="data-table"></a>Tabela de dados

    **TodoItem**

  	| Atributo    |  Tipo   | Nota                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| ID           | Cadeia, marcadas como necessária  | chave primária na loja remota                            |
  	| concluir     | Booleano | todo o campo do item                                        |
  	| texto         | Cadeia  | todo o campo do item                                        |
  	| createdAt | Data    | mapas (opcionais) para a propriedade do sistema createdAt         |
  	| updatedAt | Data    | mapas (opcionais) para a propriedade do sistema updatedAt         |
  	| versão   | Cadeia  | (opcional) utilizado para detetar conflitos, mapas à versão |


## <a name="setup-sync"></a>Alterar o comportamento de sincronização da aplicação

Nesta secção, irá modificar a aplicação para que não sincronizar em Iniciar aplicação ou quando inserir e atualizar os itens, mas apenas quando é efetuado no botão de gesto de atualizar.

**Objectivo-C**:

1. No **QSTodoListViewController.m**, alterar o método de **viewDidLoad** para remover a chamada para `[self refresh]` no final do método. Agora, os dados não serão sincronizados com o servidor em Iniciar aplicação, mas em vez disso, será o conteúdo do arquivo local.

2. No **QSTodoService.m**, modificar a definição de `addItem` para que não sincroniza depois do item é inserido. Remover o `self syncData` bloquear e substituir com o seguinte:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modificar a definição de `completeItem` como acima; remover o bloco de para `self syncData` e substituir com o seguinte:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. No `viewDidLoad` comentar, **ToDoTableViewController.swift**, estas duas linhas, parar de sincronizar em Iniciar aplicação. No momento da escrita neste artigo, a aplicação Swift Todo não atualiza o serviço quando alguém adiciona ou conclui um item, apenas em Iniciar aplicação.

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Testar a aplicação

Nesta secção, irá ligar para um URL inválido para simular um cenário offline. Quando adiciona itens de dados, estes serão contidos no arquivo de dados de Core local, mas não sincronizados com o móvel back-end.

1. Alterar o URL da aplicação móvel de **QSTodoService.m** para um URL inválido e execute novamente a aplicação:

    **Objectivo-C** na QSTodoService.m:
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **Swift** no ToDoTableViewController.swift:

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Adicione alguns itens de todo. Saia do simulator (ou forçar fecha a aplicação) e reiniciar. Certifique-se de que as alterações tenham sido persistentes.

3. Ver o conteúdo da tabela TodoItem remoto:

    + Para um back-end Node.js, aceda ao [portal do Azure](https://portal.azure.com/)e, na sua aplicação Mobile back-end clique **Tabelas fácil** > **TodoItem** para ver o conteúdo do `TodoItem` tabela.
    + Para um back-end .NET, ver o conteúdo da tabela com uma ferramenta SQL como SQL Server Management Studio ou um cliente de descanso como Fiddler ou Postman.

    Certifique-se de que os novos itens *não* foram sincronizados para o servidor:

4. Alterar o URL para o correto em **QSTodoService.m** e execute novamente a aplicação. Execute o gesto de atualização solicitando-se para baixo na lista de itens. Irá ver um controlo giratório progresso.

5. Ver os dados de TodoItem novamente. Agora deverá aparecer a TodoItems novas e alteradas.

## <a name="summary"></a>Resumo

Para suportar a funcionalidade de sincronização offline, utilizámos o `MSSyncTable` interface e inicializado `MSClient.syncContext` com um arquivo local. Neste caso o arquivo local foi uma base de dados com base em dados Core.

Ao utilizar um arquivo local Core dados, tem de definir várias tabelas com as [Propriedades do sistema correto](#review-core-data).

As operações CRUD normais para as aplicações móveis do Azure trabalhar como se a aplicação ainda está ligada mas todas as operações ocorrerem contra o arquivo local.

Quando queremos sincronizar o arquivo local com o servidor, utilizámos o `MSSyncTable.pullWithQuery`método.


## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de dados offline no Azure aplicações móveis]

* [Na nuvem rosto: Sincronização Offline no Azure serviços móveis] \(Nota: o vídeo está no Mobile Services, mas sincronização offline funciona de forma semelhante em aplicações móveis do Azure\)

<!-- URLs. -->


[Criar uma aplicação do iOS]: app-service-mobile-ios-get-started.md
[Sincronização de dados offline no Azure aplicações móveis]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Capa da nuvem: Sincronização Offline no Azure serviços móveis]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
