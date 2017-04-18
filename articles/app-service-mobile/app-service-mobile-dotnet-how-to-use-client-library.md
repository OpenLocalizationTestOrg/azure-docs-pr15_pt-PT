<properties
    pageTitle="Trabalhar com a biblioteca do cliente geridos aplicações do serviço de aplicação Mobile (Windows | Xamarin) | Microsoft Azure"
    description="Saiba como utilizar um cliente .NET para aplicações Mobile de serviço de aplicação do Azure com aplicações do Windows e Xamarin."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Como utilizar o cliente gerido para as aplicações móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>Descrição geral

Este guia mostra-lhe como efetuar cenários comuns a utilizar a biblioteca do cliente geridos para Azure aplicação Serviço móvel aplicações para o Windows e Xamarin apps. Se estiver familiarizado com aplicações Mobile, deverá tomar em consideração pela primeira vez a concluir as [aplicações do Azure Mobile guia de introdução] [ 1] tutorial. Neste guia, iremos concentrar-no SDK gerido do lado do cliente. Para saber mais sobre os SDK do lado do servidor para aplicações Mobile, consulte a documentação para o [.NET Server SDK] [ 2] ou o [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Documentação de referência

A documentação de referência para o SDK do cliente está localizada aqui: [referência de cliente do Azure Mobile aplicações .NET][4].
Também pode encontrar várias amostras de cliente no [Azure amostras GitHub repositório][5].

## <a name="supported-platforms"></a>Plataformas suportadas

A plataforma .NET suporta as seguintes plataformas:

* Versões de Xamarin Android para API 19 a 24 (KitKat através de Nougat)
* Xamarin iOS liberta para iOS 8.0 e versões posteriores
* Plataforma universal Windows
* Windows Phone 8.1
* Windows Phone 8.0 exceto aplicações do Silverlight

A autenticação de "servidor de fluxo de caixa" utiliza uma vista da Web para a IU apresentada.  Se o dispositivo não for possível apresentar uma vista da Web IU, são necessários os outros métodos de autenticação.  Este SDK, pelo que não é adequado para o tipo de observação ou da mesma forma dispositivos restritos.

##<a name="setup"></a>Configuração e pré-requisitos

Vamos assumir que já criadas e publicados projeto back-end aplicação Mobile, que inclui pelo menos uma tabela.  No código utilizado neste tópico, a tabela é denominada `TodoItem` e que tem as seguintes colunas: `Id`, `Text`, e `Complete`. Esta tabela é a mesma tabela criada quando concluir o [aplicações do Azure Mobile guia de introdução].

O tipo do lado do cliente escrito correspondente no c# é a classe seguinte:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

O [JsonPropertyAttribute] [ 6] é utilizado para definir o mapeamento de *PropertyName* entre o tipo de cliente e a tabela.

Para saber como criar tabelas no seu back-end aplicações Mobile, consulte as informações no [tópico .NET Server SDK] [ 7] ou o [tópico Node.js Server SDK][8]. Se tiver criado o seu back-end aplicação Mobile no portal do Azure ao utilizar o guia de introdução, também pode utilizar a definição de **fácil tabelas** no [portal do Azure].

###<a name="how-to-install-the-managed-client-sdk-package"></a>Como: Instale o pacote SDK cliente geridos

Utilize um dos seguintes métodos para instalar o pacote SDK cliente geridos para aplicações móveis do [NuGet][9]:

+ **Visual Studio** Com o botão direito do projeto, clique em **Gerir pacotes de NuGet**, procure o `Microsoft.Azure.Mobile.Client` compactar, em seguida, clique em **instalar**.

+ **Xamarin Studio** Com o botão direito do projeto, clique em **Adicionar** > **Adicionar pacotes de NuGet**, procure o `Microsoft.Azure.Mobile.Client `compactar e, em seguida, clique em **Adicionar pacote**.

No seu ficheiro de atividade principal, lembre-se adicionar a seguinte instrução **utilizando** :

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>Como: trabalhar com símbolos de depuração no Visual Studio

Os símbolos para o espaço de nomes de Microsoft.Azure.Mobile estão disponíveis no [SymbolSource][10].  Consulte as [instruções de SymbolSource] [ 11] para integrar SymbolSource com o Visual Studio.

##<a name="create-client"></a>Criar o cliente de aplicações Mobile

O código seguinte cria a [MobileServiceClient] [ 12] objeto que é utilizado para aceder ao seu back-end aplicação Mobile.

    var client = new MobileServiceClient("MOBILE_APP_URL");

No código anterior, substitua `MOBILE_APP_URL` com o URL da aplicação Mobile back-end, que poderá encontrar no pá para o aplicação Mobile do back-end no [portal do Azure]. O objeto MobileServiceClient deve ser singleton.

## <a name="work-with-tables"></a>Trabalhar com tabelas

A secção seguinte apresenta detalhes sobre como procurar e obter registos e modificar os dados da tabela.  Aborda os seguintes tópicos:

* [Criar uma referência de tabela](#instantiating)
* [Dados da consulta](#querying)
* [Filtrar os dados devolvidos](#filtering)
* [Ordenar dados devolvido](#sorting)
* [Devolver dados nas páginas](#paging)
* [Seleccionar colunas específicas](#selecting)
* [Procurar um registo por Id](#lookingup)
* [Trabalhar com consultas sem tipos](#untypedqueries)
* [Inserir dados](#inserting)
* [Actualização de dados](#updating)
* [Eliminação de dados](#deleting)
* [Resolução de conflitos e simultaneidade optimista](#optimisticconcurrency)
* [Vincular a Interface de utilizador do Windows](#binding)
* [Alterar o tamanho da página](#pagesize)

###<a name="instantiating"></a>Como: criar uma referência de tabela

O código que acede ou modifica dados numa tabela de back-end a chamadas de funções na `MobileServiceTable` objeto. Obter uma referência para a tabela ao contactar o método de [GetTable] , da seguinte forma:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

O objeto devolvido utiliza o modelo de serialização escrito. Um modelo de serialização sem tipos também é suportado. O seguinte exemplo [cria uma referência a uma tabela sem tipos]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Consultas sem tipos, tem de especificar a cadeia de consulta OData subjacente.

###<a name="querying"></a>Como: consultar dados da sua aplicação móvel

Esta secção descreve como emitir consultas para o aplicação Mobile do back-end, que inclui as seguintes funcionalidades:

- [Filtrar os dados devolvidos](#filtering)
- [Ordenar dados devolvido](#sorting)
- [Devolver dados nas páginas](#paging)
- [Seleccionar colunas específicas](#selecting)
- [Procurar dados por ID](#lookingup)

>[AZURE.NOTE]Um tamanho de página condicionada pelo servidor é aplicado para impedir que todas as linhas que está a ser devolvido.  Paginação mantém predefinido os pedidos de grandes conjuntos de dados a partir do negativa que afetam o serviço.  Para devolver mais de 50 linhas, utilize o `Skip` e `Take` método, tal como descrito no [devolver dados em páginas].

###<a name="filtering"></a>Como: filtrar dados devolvidos

O código seguinte ilustra como filtrar dados por incluindo uma `Where` cláusula numa consulta. Devolve todos os itens da `todoTable` cujo `Complete` for igual a propriedade `false`. A função [onde] aplica-se uma linha de filtragem predicado à consulta contra a tabela.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Pode ver o URI do pedido enviado para o back-end utilizando o software de inspeção de mensagem, tais como as ferramentas de programador do browser ou [Fiddler]. Se observe o pedido URI, repare que a cadeia de consulta é modificada:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Este pedido de OData é traduzido numa consulta SQL Server SDK:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

A função que lhe é transmitida a `Where` método pode ter um número arbitrário de condições.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

Este exemplo seria traduzido de uma consulta SQL pelo SDK do servidor:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Esta consulta também pode ser dividida em várias cláusulas:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Os dois métodos são equivalentes e podem ser utilizados alternadamente.  A opção antiga&mdash;de concatenação de vários predicados numa consulta&mdash;é mais compacto e recomendadas.

O `Where` cláusula suporta operações ser traduzido o subconjunto de OData. Operações incluem:

* Operadores relacionais (= =,! =, <, < =, >, > =),
* Operadores aritméticos (+, -, /, *, %),
* Número de precisão (Math.Floor, Math.Ceiling)
* Funções de cadeia (comprimento, subcadeia, substituir, IndexOf, StartsWith, EndsWith),
* Propriedades de data (ano, mês, dia, hora, minuto, segundo)
* Propriedades de um objeto de acesso e
* Combinar qualquer uma destas operações de expressões.

Quando considerar o que suporta o SDK do servidor, poderá considerar a [OData v3 documentação].

###<a name="sorting"></a>Como: ordenar dados devolvidos

O código seguinte ilustra como ordenar os dados ao incluir uma função [OrdenarPor] ou [OrderByDescending] na consulta. Devolve itens a partir de `todoTable` por ordem ascendente pelo `Text` campo.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Como: devolver os dados em páginas

Por predefinição, o back-end devolve apenas as primeiras 50 linhas. Pode aumentar o número de linhas devolvidas ao contactar o método de [tomar] . Utilizar `Take` juntamente com o método de [Ignorar] para pedir um específico "página" do total dataset devolvido pela consulta. A seguinte consulta quando executada, devolve os primeiros três itens na tabela.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

A seguinte consulta revista ignora os três primeiros resultados e devolve o resultado seguinte três. Esta consulta produz a segunda "página" de dados, onde o tamanho da página está três itens.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

O método de [IncludeTotalCount] os pedidos a contagem total de _todos_ os registos que iria foram devolvidos, ignorando quaisquer cláusula/limite de paginação especificada:

    query = query.IncludeTotalCount();

Numa aplicação do mundo real, pode utilizar consultas semelhantes ao exemplo anterior com um controlo de pager ou IU comparável para navegar entre páginas.

>[AZURE.NOTE]Para substituir o limite de linha de 50 na aplicação Mobile back-end, também tem de aplicar a [EnableQueryAttribute] para o método GET público e especificar o comportamento de paginação. Quando aplicadas para o método, o seguinte procedimento define o valor máximo devolvido linhas a 1000:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Como: seleccionar colunas específicas

Pode especificar qual o conjunto de propriedades para incluir nos resultados da adicionando uma cláusula [Selecione] a sua consulta. Por exemplo, o código seguinte mostra como selecionar apenas uma campo e também como selecionar e formatar vários campos:

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Todas as funções descritas até ao momento são aditivos, por isso Pedimos pode manter a interligação-los. Cada chamada interligada afeta mais da consulta. Um exemplo mais:

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Como: procurar dados por ID

A função de [LookupAsync] pode ser utilizada para procurar objetos da base de dados com um ID específico.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Como: executar sem tipos de consultas

Quando executar uma consulta utilizando um objeto sem tipos tabela, tem de especificar explicitamente a cadeia de consulta de OData ao contactar o suporte [ReadAsync], tal como no exemplo seguinte:

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Voltar valores JSON que pode utilizar como um conjunto de propriedades. Para mais informações sobre JToken e Newtonsoft Json.NET, consulte o site [Json.NET] .

### <a name="inserting"></a>Como: Inserir dados back-end aplicação Mobile

Todos os tipos de cliente tem de conter um membro com o nome **Id**, que está por predefinição, uma cadeia. Este **Id** é necessário para efetuar operações CRUD e para a sincronização offline. O código seguinte ilustra como utilizar o método de [InsertAsync] para inserir novas linhas numa tabela. O parâmetro contém os dados a ser inserida como um objeto de .NET.

    await todoTable.InsertAsync(todoItem);

Se um valor de ID exclusivo personalizado não é incluído na `todoItem` durante um insert, um GUID é gerado pelo servidor.
Pode obter o código gerado através da inspeção o objeto depois da chamada devolve.

Para inserir sem tipos de dados, poderá tira partido de Json.NET:

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Eis um exemplo utilizando um endereço de e-mail como um id de cadeia exclusiva:

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Trabalhar com valores de ID

Aplicações móveis suporta valores exclusivos cadeia personalizada para a coluna da tabela **id** . Um valor de cadeia permite que as aplicações utilizar valores personalizados como endereços de correio eletrónico ou nomes de utilizador para o ID.  IDs de cadeia fornecerem-lhe as seguintes vantagens:

* IDs são gerados sem fazer uma ida e volta à base de dados.
* Registos são mais fáceis de impressão em série a partir de bases de dados ou de tabelas diferentes.
* Valores de iDs podem integrar o melhor com lógica de uma aplicação.

Quando um valor de ID de cadeia não estiver definido num registo inserido, o aplicação Mobile back-end gera um valor exclusivo para o ID. Pode utilizar o método de [Guid.NewGuid] para gerar o seus próprio valores de ID do cliente ou no back-end.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Como: modificar os dados na aplicação Mobile back-end

O código seguinte ilustra como utilizar o método de [UpdateAsync] para atualizar um registo existente com o mesmo ID com novas informações. O parâmetro contém os dados sejam atualizadas como um objeto de .NET.

    await todoTable.UpdateAsync(todoItem);

Para atualizar sem tipos de dados, pode tirar partido das [Json.NET] da seguinte forma:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Um `id` campo deve ser especificado quando efetuar uma atualização. Utiliza o back-end a `id` campo para identificar que linhas para atualizar. O `id` campo pode ser obtido a partir dos resultados do `InsertAsync` de chamadas. Um `ArgumentException` é elevado se tentar atualizar um item sem facultar o `id` valor.

###<a name="deleting"></a>Como: eliminar dados de back-end aplicação Mobile

O código seguinte ilustra como utilizar o método de [DeleteAsync] para eliminar uma instância existente. A instância está identificada pelo `id` conjunto de campos na `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Para eliminar sem tipos de dados, pode tirar partido das Json.NET da seguinte forma:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Quando efetuar um pedido de eliminar, tem de ser especificado um ID. Outras propriedades não são transmitidas para o serviço ou são ignoradas ao serviço. O resultado de um `DeleteAsync` chamada costuma ser `null`. O ID para passar numa pode ser obtido a partir do resultado da `InsertAsync` de chamadas. A `MobileServiceInvalidOperationException` é devolvido quando tenta eliminar um item sem especificar a `id` campo.

###<a name="optimisticconcurrency"></a>Como: utilizar simultaneidade optimista para resolução de conflitos

Os clientes de duas ou mais poderão escrever as alterações ao mesmo item ao mesmo tempo. Sem detecção de conflitos, o último escrita iria substituir qualquer atualizações anterior. **Controlo de simultaneidade optimista** assume que cada transação possível consolidar e, consequentemente, não utiliza o bloqueio qualquer recurso.  Antes de fazer uma transação, controlo de simultaneidade optimista verifica que não existem outros transação tenha modificado os dados. Se os dados foram modificados, a consolidar transação é revertida.

Aplicações móveis suportar o controlo de simultaneidade optimista pelo registo de alterações para cada item utilizando a `version` coluna de propriedade de sistema que está definida para cada tabela no seu back-end aplicação Mobile. Conjuntos de aplicações Mobile sempre que um registo é atualizado, o `version` propriedade para esse registo para um novo valor. Durante a cada pedido de atualização, o `version` propriedade do registo incluído com o pedido é comparado com a mesma propriedade do registo no servidor. Se a versão passado com o pedido não corresponder a back-end, em seguida, a biblioteca do cliente eleva um `MobileServicePreconditionFailedException<T>` exceção. O tipo incluído com a exceção é o registo a partir de back-end que contém a versão de servidores do registo. A aplicação, em seguida, pode utilizar estas informações para decidir se executar o pedido de actualização novamente com o correto `version` valor a partir de back-end para consolidar alterações.

Definir uma coluna a classe de tabela para a `version` propriedade do sistema para ativar simultaneidade optimista. Por exemplo:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Aplicações utilizando tabelas sem tipos ativar simultaneidade optimista definindo a `Version` sinalize a `SystemProperties` da tabela da seguinte forma.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Para além de ativar simultaneidade optimista, também tem captura o `MobileServicePreconditionFailedException<T>` exceção no seu código ao chamar [UpdateAsync].  Resolver o conflito aplicando a correto `version` para o registo actualizado e chamada [UpdateAsync] com o registo resolvido. O código seguinte mostra como resolver um conflito de escrita detetado uma vez:

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

Para mais informações, consulte o tópico de [Sincronização de dados Offline no Azure Mobile aplicações] .

###<a name="binding"></a>Como: dados de aplicações do Mobile vincular a uma interface de utilizador do Windows

Esta secção mostra como apresentar objetos de dados devolvidos através de elementos IU numa aplicação do Windows.  O seguinte código de exemplo liga-se à origem da lista com uma consulta para itens incompletos. O [MobileServiceCollection] cria uma coleção de encadernação móvel ciente aplicações.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Alguns controlos no runtime do geridos suportam uma interface denominada [ISupportIncrementalLoading]. Esta interface permite que os controlos pedir dados extra quando o utilizador desloca. Existe suporte incorporado para esta interface para universais aplicações do Windows através do [MobileServiceIncrementalLoadingCollection], que trata automaticamente as chamadas a partir dos controlos. Utilizar `MobileServiceIncrementalLoadingCollection` nas aplicações do Windows da seguinte forma:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Para utilizar a coleção de nova no Windows Phone 8 e "Silverlight" aplicações, utilize o `ToCollection` métodos de extensão no `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para carregar os dados, contacte o `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Quando utiliza a coleção de criados pela chamada `ToCollectionAsync` ou `ToCollection`, receberá uma coleção de que pode ser associada a controlos de IU.  Esta colecção está deverá ter em consideração paginação.  Uma vez que está a coleção de carregar dados da rede, carregar, por vezes, falhará. Para lidar com essas falhas, substituir o `OnException` método no `MobileServiceIncrementalLoadingCollection` para processar exceções resultantes chamadas para `LoadMoreItemsAsync`.

Considere se a sua tabela tiver muitos campos mas pretende apresentar algumas páginas no seu controlo. Pode utilizar as informações na secção anterior "[Seleccionar colunas específicas](#selecting)" para selecionar colunas específicas para apresentar na interface de utilizador.

###<a name="pagesize"></a>Alterar o tamanho da página

Aplicações do Azure Mobile devolve um máximo de 50 itens por pedido por predefinição.  Pode alterar o tamanho de paginação, aumentando o tamanho máximo de página no cliente e no servidor.  Para aumentar o tamanho de página pedida, especifique `PullOptions` ao utilizar `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

Partindo do princípio de que tenha efetuado o `PageSize` igual ou maior que 100 no servidor, um pedido de devolve até 100 itens.

##<a name="#offlinesync"></a>Trabalhar com tabelas Offline

Tabelas offline utilizam um arquivo para o arquivo de dados locais do SQLite para utilização offline.  Tabela de todas as operações são feitas contra local SQLite guardar em vez do arquivo de servidor remoto.  Para criar uma tabela offline, pela primeira vez a preparar o seu projeto:

1. No Visual Studio, botão direito do rato a solução > **Gerir pacotes de NuGet para solução...**, em seguida, procurar e instalar o pacote de NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos os projetos da solução.

2. (Opcional) Para suportar dispositivos com Windows, instale um dos seguintes pacotes de runtime SQLite:

    * **Runtime do Windows 8.1:** Instalar [SQLite para o Windows 8.1][3].
    * **Do windows Phone 8.1:** Instalar [SQLite para Windows Phone 8.1][4].
    * **Plataforma universal Windows** Instalar [SQLite para o Universal Windows Universal][5].

3. (Opcional). Para dispositivos com Windows, clique em **referências** > **Add Reference...**, expanda a pasta do **Windows** > **extensões**, em seguida, ativar adequado **SQLite para Windows** SDK juntamente com o **Visual C++ 2013 Runtime para o Windows** SDK.
    Os nomes de SQLite SDK variam ligeiramente com cada plataforma Windows.

Antes de pode ser criada uma referência de tabela, tem de ser preparado o arquivo local:

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

Loja inicialização é normalmente feita imediatamente após o cliente é criado.  O **OfflineDbPath** deve ser um nome de ficheiro adequado para utilização em todas as plataformas que suporta.  Se o caminho é um caminho completamente qualificado (ou seja, é iniciado com uma barra) e, em seguida, esse caminho é utilizado.  Se o caminho não está totalmente qualificado, o ficheiro é colocado numa localização específica da plataforma.

* Para dispositivos iOS e Android, o caminho predefinido é a pasta "Ficheiros pessoais".
* Para dispositivos com Windows, o caminho predefinido é a pasta de "AppData" específicos da aplicação.

Uma referência de tabela pode ser obtida utilizando o `GetSyncTable<>` método:

    var table = client.GetSyncTable<TodoItem>();

Não é necessário autenticar para utilizar uma tabela offline.  Só tem de autenticar quando está a comunicar com o serviço de back-end.

###<a name="syncoffline"></a>Sincronizar uma tabela Offline

Tabelas offline não são sincronizadas com o back-end por predefinição.  A sincronização é dividida em dois blocos.  Pode transmitir alterações separadamente a partir de novos itens a transferir.  Eis um método de sincronização normal:

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
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

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
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

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Se o primeiro argumento `PullAsync` é nulo, em seguida, sincronizar utilizarão não é utilizada.  Cada operação de sincronização obtém todos os registos.

O SDK executa uma implícito `PushAsync()` antes no registos.

Processamento de conflito acontece num `PullAsync()` método.  Pode lidar com conflitos da mesma forma como tabelas onlinehttps.  O conflito é produzido quando `PullAsync()` chama-se em vez de durante a inserir, atualizar ou eliminar. Se ocorrem a vários conflitos, eles estão agrupados numa única MobileServicePushFailedException.  Alça de cada falha em separado.

##<a name="#customapi"></a>Trabalhar com uma API personalizada

Uma API personalizada permite-lhe definir personalizados pontos finais que expõem a funcionalidade de servidor que não mapear para um insert, atualizar, eliminar ou operação de leitura. Ao utilizar uma API personalizada, pode ter mais controlo sobre mensagens, incluindo de leitura e definir cabeçalhos de mensagens HTTP e definir um formato de corpo da mensagem que não seja JSON.

Ligar uma API personalizada ao contactar um dos métodos [InvokeApiAsync] no cliente. Por exemplo, a linha seguinte de código envia um pedido de mensagem para a **completeAll** API no back-end:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Este formulário é uma chamada de método escrito e requer que o tipo devolvido **MarkAllResult** é definido. Métodos digitados e sem tipos são suportados.

##<a name="authentication"></a>Autenticar os utilizadores

Aplicações móveis suporta autenticar e autorizar utilizadores da aplicação utilizar vários fornecedores de identidades externas: Facebook, Google, Account Microsoft, Twitter e Azure Active Directory. Pode definir permissões em tabelas para restringir o acesso operações específicas para apenas utilizadores autenticados. Também pode utilizar a identidade do utilizadores autenticados para implementar regras de autorização nos scripts do servidor. Para obter mais informações, consulte o tutorial [Adicionar autenticação para a sua aplicação].

Dois fluxos de autenticação suportados: fluxo _gerido de cliente_ e _servidor gerido_ . O fluxo de servidor gerido fornece a experiência de autenticação mais simples, como baseia-se na interface de autenticação de web do fornecedor. O fluxo de cliente com gestão permite para integração mais aprofundada com capacidades específicas de dispositivo, como baseia-se no específicas do fornecedor específicas do dispositivo SDK.

>[AZURE.NOTE] Recomendamos que utilize um fluxo de cliente com gestão nas suas aplicações de produção.

Para configurar autenticação, tem de registar a sua aplicação com um ou mais fornecedores de identidade.  O fornecedor de identidade gera um ID de cliente e uma palavra-passe cliente para a sua aplicação.  Estes valores são definidos em seguida, no seu back-end para ativar a aplicação de serviço do Azure autenticação/autorização.  Para mais informações, siga as instruções detalhadas no tutorial [Adicionar autenticação para a sua aplicação].

Esta secção abrange os seguintes tópicos:

+ [Autenticação gerido por cliente](#clientflow)
+ [Autenticação de servidor gerido](#serverflow)
+ [O token de autenticação de colocação em cache](#caching)

###<a name="clientflow"></a>Autenticação gerido por cliente

A aplicação pode independentemente de contactar o fornecedor de identidade e, em seguida, fornecer o token devolvido durante o início de sessão com o seu back-end. Este fluxo de cliente permite-lhe para fornecer uma experiência de início de sessão único para utilizadores ou para obter dados de utilizador adicionais do fornecedor de identidade. Autenticação de fluxo de cliente é preferencial para utilizar um fluxo de servidor, tal como o fornecedor de identidade SDK fornece um funcionalidade UX mais nativo e permite adicionais de personalização.

Exemplos são fornecidos para os padrões de autenticação de fluxo de cliente seguintes:

+ [Biblioteca de autenticação do Active Directory](#adal)
+ [Facebook ou do Google](#client-facebook)
+ [Live SDK](#client-livesdk)

#### <a name="adal"></a>Autenticar utilizadores com a biblioteca de autenticação do Active Directory

Pode utilizar o Active Directory autenticação biblioteca (ADAL) para a autenticação de utilizador iniciar a partir do cliente utilizando a autenticação do Azure Active Directory.

1. Configure a sua aplicação móvel do back-end para AAD sign-on ao seguir o tutorial de [como configurar o serviço de aplicação para o início de sessão do Active Directory] . Certifique-se concluir o passo opcional de registar uma aplicação de cliente nativa.
2. No Visual Studio ou Xamarin Studio, abra o projeto e adicione uma referência para a `Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet pacote. Quando a procurar, incluem versões de pré-lançamento.
3. Adicione o seguinte código à aplicação, de acordo com a plataforma de que está a utilizar. Em cada, certifique das substituições seguintes:

    * Substitua **Aqui de certificação de inserir** com o nome do inquilino na qual aprovisionado que a aplicação. O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado a partir do separador de domínio no seu Azure Active Directory no [Azure portal clássica].
    * Substitua **Inserir recurso-ID aqui** o ID do cliente para a sua aplicação móvel do back-end. Pode obter o ID de cliente a partir do separador **Avançadas** , em **Definições do Azure Active Directory** , no portal.
    * Substitua o ID do cliente que copiou da aplicação de cliente nativo **Inserir cliente-ID aqui** .
    * Substitua **Inserir-REDIRECT-URI-aqui** ponto final de _/.auth/login/done_ do seu site, utilizando o esquema de HTTPS. Este valor deverá ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_.

    O código necessário para cada plataforma segue:

    **Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>Single Sign-On utilizando um token do Facebook ou Google

Pode utilizar o fluxo de cliente, conforme apresentado neste fragmento do Facebook ou Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>Única sessão utilizando Account Microsoft com o SDK Live

Para autenticar os utilizadores, tem de registar a sua aplicação na conta Microsoft Developer Center. Configure os detalhes de registo no seu back-end aplicação Mobile. Para criar um registo de conta Microsoft e ligá-lo à sua back-end aplicação Mobile, conclua os passos em [Registe-se a sua aplicação para utilizar uma sessão de conta Microsoft]. Se tiver o da loja Windows e o Windows Phone 8/Silverlight versões da sua aplicação, registe-se a versão da loja Windows pela primeira vez.

O código seguinte autentica a utilizar o Live SDK e utiliza o token devolvido para iniciar sessão no seu back-end aplicação Mobile.

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Para obter mais informações, consulte a documentação do [Windows Live SDK] .

###<a name="serverflow"></a>Autenticação de servidor gerido

Depois de ter registado o seu fornecedor de identidade, chame o método de [LoginAsync] no [MobileServiceClient] com o valor de [MobileServiceAuthenticationProvider] do fornecedor. Por exemplo, o código seguinte inicia a um servidor fluxo iniciar sessão utilizando o Facebook.

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Se estiver a utilizar um fornecedor de identidades diferente de Facebook, altere o valor de [MobileServiceAuthenticationProvider] para o valor para o seu fornecedor.

Num fluxo de servidor, o serviço de aplicação do Azure gere o fluxo de autenticação OAuth ao apresentar a página de início de sessão do fornecedor seleccionado.  Assim que os retornos de fornecedor de identidade, aplicação de serviço de Azure gera um token de autenticação de aplicação de serviço. O método [LoginAsync] devolve um [MobileServiceUser], que fornece tanto o [ID de utilizador] do utilizador autenticado como [MobileServiceAuthenticationToken], como um token de web JSON (JWT). Este token pode ser em cache e reutilizada até que expire. Para mais informações, consulte o artigo [o token de autenticação de colocação em cache](#caching).

###<a name="caching"></a>O token de autenticação de colocação em cache

Em alguns casos, pode ser evitada a chamada para o método de início de sessão após a primeira autenticação bem sucedida, guardando-o token de autenticação a partir do fornecedor.  Aplicações da loja Windows e UWP podem utilizar [PasswordVault] para colocar em cache o token de autenticação atual depois de um bem sucedida iniciar sessão, da seguinte forma:

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

O valor de ID de utilizador é armazenado como o nome de utilizador a credencial e o token é armazenado como a palavra-passe. No arranque subsequentes, pode verificar a **PasswordVault** para introduzir as credenciais em cache. O exemplo seguinte utiliza credenciais em cache quando forem encontrados e, caso contrário tentativas para autenticar novamente com o back-end:

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Quando terminar sessão um utilizador, também terá de remover credenciais armazenadas, da seguinte forma:

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Aplicações de Xamarin utilizam [Xamarin.Auth] APIs para armazenar credenciais em segurança de um objeto de **conta** . Para obter um exemplo de utilização destas APIs, consulte o ficheiro de código [AuthStore.cs] na [fotografia ContosoMoments partilha de exemplo].

Quando é utilizada a autenticação gerido de cliente, também pode cache token de acesso obtido a partir do seu fornecedor de como o Facebook e Twitter. Este token pode ser fornecido pedir um novo token de autenticação a partir de back-end, da seguinte forma:

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>Notificações de emissão

Os tópicos seguintes abrangem as notificações de emissão:

* [Registe-se para as notificações Push](#register-for-push)
* [Obter um pacote da loja Windows SID](#package-sid)
* [Registe-se com modelos em diferentes plataformas](#register-xplat)

###<a name="register-for-push"></a>Como: registe-se para as notificações Push

O cliente de aplicações Mobile permite-lhe registe-se para as notificações push com concentradores de notificação do Azure. Quando se ter registado, obtém uma alça de que, são obtidos a partir do específicos da plataforma de emissão notificação de serviço (PNS). Em seguida, fornecer este valor juntamente com quaisquer etiquetas ao criar o registo. O código seguinte regista a aplicação do Windows para as notificações push com o serviço de notificação do Windows (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Se o é a instalação push WNS, em seguida, tem de [obter um pacote da loja Windows SID](#package-sid).  Para mais informações sobre as aplicações do Windows, incluindo como registar para registos de modelo, consulte o artigo [Adicionar as notificações push para a sua aplicação].

Pedir etiquetas a partir do cliente não é suportada.  Pedidos de etiqueta silenciosamente desaparecem a partir do registo.
Se pretender registar o seu dispositivo com sinalizadores, crie uma API personalizada que utiliza a API do concentradores notificação para executar o registo em seu nome.  [Chamar a API personalizado](#customapi) em vez do `RegisterNativeAsync()` método.

###<a name="package-sid"></a>Como: obter um pacote da loja Windows SID

É necessário um pacote SID para ativar as notificações push nas aplicações da loja Windows.  Para receber um pacote SID, registe a aplicação da loja Windows.

Para obter este valor:

1. No Explorador de solução do Visual Studio, com o botão direito do projeto de aplicação da loja Windows, clique em **loja** > **Associar aplicação com o arquivo de...**.
2. No assistente, clique em **seguinte**, inicie sessão com a sua conta Microsoft, escreva um nome para a sua aplicação na **reserva um novo nome de aplicação**, em seguida, clique em **reserva**.
3. Depois do registo de aplicação é criado com êxito, selecione o nome da aplicação, clique em **seguinte**e, em seguida, clique em **associar**.
4. Inicie sessão no [Windows Dev Center do] utilizando a Account Microsoft. Em **as minhas aplicações**, clique no registo de aplicação que criou.
5. Clique em **Gestão de aplicação** > **identidade da aplicação**e, em seguida, desloque-se para baixo para localizar o seu **Pacote SID**.

Utilizações muitos do pacote SID processado como um URI, caso em que tem de utilizar _aplicação ms: / /_ como o esquema. Tome nota da versão do seu pacote SID formado ao concatenar este valor como um prefixo.

Aplicações de Xamarin requerem algum código adicional para conseguir registe-se uma aplicação a executar o iOS ou Android plataformas. Para obter mais informações, consulte o tópico para a sua plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Como: modelos de push Register para enviar notificações em diferentes plataformas

Para registar modelos, utilize o `RegisterAsync()` método com os modelos, da seguinte forma:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Os modelos devem ser `JObject` tipos e podem conter vários modelos no seguinte formato JSON:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

O método **RegisterAsync()** também aceita mosaicos secundário:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Todas as etiquetas são removidas ausente durante o registo de título. Para adicionar etiquetas a instalações ou modelos dentro instalações, consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure].

Para enviar notificações com estes modelos registados, consulte a [Notificação concentradores APIs].

##<a name="misc"></a>Tópicos de diversos

###<a name="errors"></a>Como: processar erros

Quando ocorre um erro no back-end, o cliente SDK eleva um `MobileServiceInvalidOperationException`.  O exemplo seguinte mostra como lidar com uma exceção que é devolvida pelo back-end:

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

Outro exemplo de lidar com as condições de erro pode ser encontrado na [Amostra de ficheiros de aplicações móveis]. O exemplo [LoggingHandler] fornece um processador de delegado de registo (seguinte) para iniciar sessão pedidos efetuados para o back-end.

###<a name="headers"></a>Como: Personalizar pedir cabeçalhos

Para suportar o seu cenário de aplicação específica, poderá ter de personalizar a comunicação com a aplicação Mobile back-end. Por exemplo, poderá querer adicionar um cabeçalho personalizado a todos os pedidos de envio ou mesmo alterar códigos de estado de respostas. Pode utilizar um personalizado [DelegatingHandler], tal como no exemplo seguinte:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Adicionar autenticação para a sua aplicação]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronização de dados offline no Azure aplicações móveis]: app-service-mobile-offline-data-sync.md
[Adicionar as notificações push para a sua aplicação]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registe-se a sua aplicação para utilizar uma sessão de conta Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Como configurar o serviço de aplicação para o início de sessão do Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[cria uma referência a uma tabela sem tipos]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrdenarPor]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Tomar]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Selecione]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Ignorar]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[ID de utilizador]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Onde]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Portal do Azure]: https://portal.azure.com/
[Azure portal clássico]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center do]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[SDK do Windows Live]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notificação concentradores APIs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exemplo de ficheiros de aplicações móveis]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentação do OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[Exemplo de partilha de fotografias de ContosoMoments]: https://github.com/azure-appservice-samples/ContosoMoments