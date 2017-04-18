<properties
    pageTitle="Como utilizar a biblioteca de cliente de aplicações do telemóvel Android"
    description="Como utilizar o Android SDK do cliente para as aplicações móveis do Azure."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Como utilizar a biblioteca do cliente Android para aplicações Mobile

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Este guia mostra-lhe como utilizar o cliente Android SDK para aplicações móveis para implementar cenários comuns, tais como:

- Consultar dados (inserir, atualizar e eliminar).
- Autenticação.
- Processamento de erros.
- Personalizar o cliente. 

Também é que um aprofundada para comuns cliente código utilizado nas aplicações móveis mais.

Este guia foca-se sobre o SDK Android do lado do cliente.  Para saber mais sobre os SDK do lado do servidor para aplicações Mobile, consulte o artigo [trabalhar com back-end de .NET SDK] [ 10] ou [como utilizar o Node.js back-end SDK][11].

## <a name="reference-documentation"></a>Documentação de referência

Pode encontrar a [referência da Javadocs API] [ 12] para a biblioteca cliente Android GitHub.

## <a name="supported-platforms"></a>Plataformas suportadas

O SDK do Azure Mobile aplicações Android suporta níveis de API 19 a 24 (KitKat através de Nougat).  

A autenticação de "servidor de fluxo de caixa" utiliza uma vista da Web para a IU apresentada. Se o dispositivo não for possível apresentar uma vista da Web IU, em seguida, outros métodos de autenticação são necessários que esteja fora do âmbito do produto.  Este SDK não é adequado para o tipo de observação ou da mesma forma dispositivos restritos.

## <a name="setup-and-prerequisites"></a>Configuração e pré-requisitos

Conclua o tutorial de [aplicações Mobile guia de introdução](app-service-mobile-android-get-started.md) .  Esta tarefa garante que todos os pré-requisitos para desenvolver aplicações do Azure Mobile foram satisfeitos.  O guia de introdução ajuda-o a configurar a sua conta e crie a sua primeira aplicação Mobile do back-end.

Se decidir não concluir o tutorial de guia de introdução, preencha as seguintes tarefas:

- [criar um aplicação móvel do back-end] [ 13] para utilizar com a sua aplicação Android.
- No Android Studio, [atualizar os ficheiros de compilação Gradle](#gradle-build).
- [Ativar a permissão de internet](#enable-internet).

###<a name="gradle-build"></a>Atualizar o ficheiro de compilação Gradle

Altere ambos os ficheiros de **build.gradle** :

1. Adicione este código para o ficheiro de nível **build.gradle** *projecto* dentro da tag *buildscript* :

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Adicione este código ao ficheiro de nível **build.gradle** da *aplicação de módulo* dentro da tag *dependências* :

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    A versão mais recente está 3.1.0. As versões suportadas estão listadas [aqui][14].

###<a name="enable-internet"></a>Ativar a permissão de internet

Para aceder ao Azure, a aplicação tem de ter a permissão de INTERNET ativada. Se ainda não estiver ativada, adicione a linha seguinte de código para o seu ficheiro **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>Vôo picado abrangente as noções básicas

Esta secção descreve alguns do código de na aplicação do guia de introdução que diz respeito à utilização do Azure Mobile aplicações.  

###<a name="data-object"></a>Definir classes de dados do cliente

Aceder a dados de tabelas do SQL Azure, pode defina classes de dados do cliente que correspondem as tabelas na aplicação Mobile back-end. Os exemplos neste tópico partem do pressuposto de uma tabela com o nome **ToDoItem**, que tem as seguintes colunas:

- ID
- texto
- concluir

O correspondente escreveu o objeto do lado do cliente:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

O código reside num ficheiro denominado **ToDoItem.java**.

Se a tabela do SQL Azure contiver mais colunas, seria necessário adicionar os campos correspondentes a esta classe.  Por exemplo, se o DTO (objecto de transferência de dados) tinha uma coluna de prioridade de número inteiro, em seguida, pode adicionar este campo, juntamente com os seus métodos obtenha e setter:

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Para saber como criar tabelas adicionais no seu back-end aplicações Mobile, consulte o artigo [como: definir um controlador de tabela] [ 15] (.NET back-end) ou [definir tabelas utilizando um esquema dinâmico] [ 16] (Node.js back-end). Para um back-end Node.js, também pode utilizar a definição de **fácil tabelas** no [portal do Azure].

###<a name="create-client"></a>Como: criar o contexto de cliente

Este código cria o objeto de **MobileServiceClient** que é utilizado para aceder ao seu back-end aplicação Mobile. Vai para o código de `onCreate` método da **atividade de** classe especificado no *AndroidManifest.xml* como uma ação de **principal** e **INICIADOR de** categoria. No código de guia de introdução, vai no ficheiro **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

Neste código, substitua `MobileAppUrl` com o URL do seu back-end aplicação Mobile, que pode encontrar no [portal do Azure] no pá para o aplicação Mobile do back-end. Esta linha de código para compilar, também terá de adicionar a seguinte instrução **Importar** :

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Como: criar uma referência de tabela

É a forma mais fácil de consulta ou modificar dados back-end utilizando o *modelo de programação de escreveu*, uma vez que Java é um idioma vivamente escrito. Este modelo disponibiliza serialização de JSON totalmente integrada e desserialização utilizando o [gson] [ 3] biblioteca quando enviar dados entre tabelas e objetos de cliente no Azure SQL back-end.

Para aceder a uma tabela, criar uma [MobileServiceTable] [ 8] objeto ao contactar o **getTable** método na [MobileServiceClient][9].  Este método tem dois overloads:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

O código seguinte, **mClient** é uma referência ao objeto MobileServiceClient.  A primeira sobrecarga é utilizada onde o nome de classe e o nome da tabela são iguais e é aquele utilizado o guia de introdução:

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

A segunda sobrecarga é utilizada quando o nome da tabela é a diferença entre o nome de classe: o primeiro parâmetro é o nome da tabela.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Como: vincular dados para a interface de utilizador

Ligação de dados envolve os três componentes:

- A origem de dados
- O esquema de ecrã
- A placa que une as duas em conjunto.

No nosso código de exemplo, vamos devolver os dados a partir da tabela do SQL Azure com aplicações móveis **ToDoItem** para uma matriz. Esta actividade é um padrão comum para aplicações de dados.  Consultas de base de dados com frequência devolvem uma colecção de linhas que o cliente obtém uma lista ou numa matriz. Neste exemplo, a matriz é a origem de dados.

O código especifica um esquema de ecrã que define a vista de dados que aparece no dispositivo.  As duas estão vinculadas juntamente com uma placa de, que este código é uma extensão do **ArrayAdapter&lt;ToDoItem&gt; ** escolares.

#### <a name="layout"></a>Como: definir o esquema

O esquema é definido por vários fragmentos de código XML. Dada um esquema existente, o código seguinte representa a **vista de lista** queremos para preencher com os nossos dados de servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

No código anterior, o atributo *itemdelista* Especifica o id do esquema para uma linha individual na lista. Este código especifica uma caixa de verificação e o texto associado e obtém criar instâncias de uma vez para cada item na lista. Este esquema não apresenta o campo **id** e um esquema mais complexo seria especificar campos adicionais na apresentação. Este código está no ficheiro **row_list_to_do.xml** .

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>Como: definir a placa

Uma vez que a origem de dados do nosso view é uma matriz de **ToDoItem**, podemos subclasse nosso adaptador a partir de um **ArrayAdapter&lt;ToDoItem&gt; ** escolares. Este subclasse produz uma vista para cada **ToDoItem** utilizando o esquema de **row_list_to_do** .

No nosso código definir estamos a seguinte classe é uma extensão do **ArrayAdapter&lt;"e"&gt; ** classe:

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Substitua o método de **getView** placas. Por exemplo:

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Vamos criar uma instância desta classe nossa atividade da seguinte forma:

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

O segundo parâmetro do construtor de ToDoItemAdapter é uma referência para o esquema. Agora que possamos criar uma instância a **vista de lista** e atribuir a placa para a **vista de lista**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>A estrutura de API

Operações de tabela aplicações Mobile e chamadas de API personalizadas estão assíncronas. Utilize os objectos [futuras] e [AsyncTask] para os métodos de assíncronos que envolvam consultas, atualizações, inserções e eliminações. Utilizar futuros torna mais fácil executar várias operações sobre um tópico de fundo sem ter de lidar com várias chamadas de retorno aninhadas.

Reveja o ficheiro de **ToDoActivity.java** no projeto a partir do [Azure portal] guia de introdução Android para obter um exemplo.

#### <a name="use-adapter"></a>Como: utilizar a placa

Agora está pronto para utilizar a ligação de dados. O código seguinte mostra como obter itens na tabela e preenche o adaptador local com os itens devolvidos.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Ligar a placa de qualquer altura, modificar a tabela **ToDoItem** . Uma vez que as modificações são efetuadas numa base de registo ao registo, alça de uma única linha em vez de uma coleção de. Quando insere um item, ligar para o método **Adicionar** adaptador; ao eliminar, ligue para o método **Remover** .

##<a name="querying"></a>Como: consultar dados a partir do seu back-end aplicação Mobile

Esta secção descreve como emitir consultas para o aplicação Mobile do back-end, que inclui as seguintes tarefas:

- [Devolver todos os itens]
- [Filtrar os dados devolvidos]
- [Ordenar dados devolvido]
- [Devolver dados nas páginas]
- [Seleccionar colunas específicas]
- [Concatenar métodos de consulta](#chaining)

### <a name="showAll"></a>Como: devolver todos os itens de uma tabela

A seguinte consulta devolve todos os itens na tabela **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

A variável de *resultados* devolve o conjunto de resultados da consulta como uma lista.

### <a name="filtering"></a>Como: filtrar dados devolvidos

A execução da seguinte consulta devolve todos os itens a partir da tabela **ToDoItem** onde **concluída** é igual a **Falso**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** é a referência para a tabela de serviços móveis que criámos anteriormente.

Defina um filtro com a chamada do método **onde** na referência de tabela. O método de **onde** é seguido por um método de **campo** seguido por um método que especifica o predicado lógico. Métodos predicados possíveis incluem **eq** (é igual a), **** (não igual), **gt** (maior que), **página** (maior ou igual a), **lt** (inferior), **le** (menor ou igual a). Estes métodos permitem-lhe comparar campos número e a cadeia para valores específicos.

Pode filtrar datas. Os seguintes métodos permitem-lhe comparar o campo Data inteira ou partes de data: **ano**, **mês**, **dia**, **hora**, **minuto**e **segundo**. O exemplo seguinte adiciona um filtro para itens cuja *data para conclusão* é igual a 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Os seguintes métodos suportam filtros complexos em campos de cadeia: **startsWith**, **endsWith**, **concat**, **subcadeia**, **indexOf**, **Substituir**, **toLower**, **toUpper**, **Cortar**e **comprimento**. O exemplo seguinte filtra para as linhas da tabela onde a coluna de *texto* começa por "PRI0."

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Os seguintes métodos de operador são suportadas pelo campos numéricos: **Adicionar**, **sub**, **mul**, **div**, **resto**, **Arred**, **Arred. excesso**e **arredondar**. O exemplo seguinte filtros para as linhas da tabela onde a **duração** é um número par.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Pode combinar predicados com estes métodos de lógicos: **e**, **ou** e **não**. O exemplo seguinte combina dois dos exemplos anteriores.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Agrupar e aninhar os operadores lógicos:

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Para obter informações mais detalhadas e exemplos de filtragem, consulte o artigo [explorar a capacidade do modelo de consulta de cliente Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Como: ordenar dados devolvidos

O código seguinte devolve que todos os itens de uma tabela de **ToDoItems** por ordem ascendente por campo de *texto* . *mToDoTable* é a referência para a tabela de back-end que criou anteriormente:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

O primeiro parâmetro do método **OrdenarPor** é uma cadeia igual ao nome do campo no qual pretende ordenar. O segundo parâmetro utiliza a enumeração **QueryOrder** para especificar se pretende ordenar por ordem ascendente ou descendente.  Se estiver a filtrar utilizando o método ***onde*** , o método ***onde*** deve ser chamado antes do método de ***OrdenarPor*** .

### <a name="paging"></a>Como: devolver os dados em páginas

O primeiro exemplo mostra como selecionar os itens de cinco principais de uma tabela. A consulta devolve os itens de uma tabela de **ToDoItems**. **mToDoTable** é a referência para a tabela de back-end que criou anteriormente:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Eis uma consulta que ignora os primeiros cinco itens e, em seguida, devolve os cinco seguintes:

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Como: seleccionar colunas específicas

O código seguinte ilustra como devolver todos os itens de uma tabela de **ToDoItems**, mas só apresenta os campos **conclusão** e **texto** . **mToDoTable** é a referência para a tabela de back-end que criámos anteriormente.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Os parâmetros da função selecionar são cadeias de nomes de colunas da tabela à qual pretende que seja devolvido.

Método **select** tem de seguir métodos como **onde** e **OrdenarPor**. Pode ser seguido de métodos de paginação como **início**.

### <a name="chaining"></a>Como: concatenar métodos de consulta

Podem ser concatenados métodos utilizados para consultar as tabelas de back-end. Interligação de métodos de consulta permite-lhe selecionar colunas específicas de linhas filtradas que são ordenadas e paginadas. Pode criar filtros lógicos complexos.
Cada método de consulta devolve um objeto de consulta. Para terminar a série dos métodos e realmente executar a consulta, contacte o método **Executar** . Por exemplo:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Os métodos de consulta em cadeia tem de ser ordenados da seguinte forma:

1. Métodos de filtragem (**onde**).
2. Métodos de ordenação (**OrdenarPor**).
3. Métodos de seleção (**Selecionar**).
4. métodos paginação (**Ignorar** e **início**).

##<a name="inserting"></a>Como: Inserir dados back-end

Criar uma instância de uma instância da classe *ToDoItem* e defina as respetivas propriedades.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Em seguida, utilize **Insert ()** para inserir um objeto:

    ToDoItem entity = mToDoTable.insert(item).get();

As correspondências entidade devolvida os dados inseridos tabela back-end, incluído o ID e outros valores de configurar num back-end.

Tabelas de aplicações Mobile necessitam de uma coluna de chave primária com o nome **id**. Por predefinição, esta coluna é uma cadeia. O valor predefinido da coluna ID do é um GUID.  Pode fornecer outros valores exclusivos, como endereços de correio eletrónico ou nomes de utilizador. Quando um valor de ID de cadeia não for fornecido para um registo inserido, back-end gera um novo GUID.

Valores de ID de cadeia fornecem as seguintes vantagens:

+ IDs podem ser gerados sem fazer uma ida e volta à base de dados.
+ Registos são mais fáceis de impressão em série a partir de bases de dados ou de tabelas diferentes.
+ Valores de ID uma melhor integração com lógica de uma aplicação.

Valores de ID de cadeia são **necessárias** para o suporte de sincronização offline.

##<a name="updating"></a>Como: atualizar os dados numa aplicação móvel

Para atualizar dados numa tabela, passe o novo objeto para o método **Update ()** .

    mToDoTable.update(item).get();

Neste exemplo, o *item* é uma referência a uma linha na tabela *ToDoItem* , que tenha tido algumas alterações efetuadas ao mesmo.
A linha com o mesmo **id** é atualizada.

##<a name="deleting"></a>Como: eliminar dados numa aplicação móvel

O código seguinte mostra como eliminar dados de uma tabela, especificando o objeto de dados.

    mToDoTable.delete(item);

Também pode eliminar um item, especificando o campo **id** da linha para eliminar.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>Como: Procurar um item específico

Procure um item com um campo específico de **id** com o método de **lookUp()** :

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>Como: trabalhar com dados sem tipos

O modelo de programação sem tipos dá-lhe controlo exato sobre serialização JSON.  Existem alguns cenários comuns onde pode optar por utilizar um modelo de programação sem tipos. Por exemplo, se a sua tabela back-end contém o número de colunas e só precisa de um subconjunto das colunas de referência.  O modelo escrito requer que definir todas as aplicações móveis colunas da tabela na sua aula de dados.  

A maior parte das chamadas de API para aceder aos dados é semelhantes das chamadas de programação escritas. A diferença principal é que no modelo de sem tipos invoca métodos no objeto **MobileServiceJsonTable** , em vez do objeto **MobileServiceTable** .

### <a name="json_instance"></a>Como: criar uma instância de uma tabela sem tipos

Semelhante ao modelo de escrito, começar ao obter uma referência de tabela, mas, neste caso seja um objeto de **MobileServicesJsonTable** . Obter a referência ao contactar o **getTable** método numa instância do cliente:

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Assim que tiver criado uma instância da **MobileServiceJsonTable**, praticamente tem a mesma API disponível como com o modelo de programação escrito. Em alguns casos, os métodos de tirar um parâmetro sem tipos em vez de um parâmetro escrito.

### <a name="json_insert"></a>Como: Inserir uma tabela sem tipos

O código seguinte mostra como efetuar uma inserir. O primeiro passo é criar uma [JsonObject][1], que faz parte da [gson] [ 3] biblioteca.

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Em seguida, utilize **Insert ()** para inserir o objeto sem tipos na tabela.

    mJsonToDoTable.insert(jsonItem).get();

Se o que precisa para obter o ID do objeto inserido, utilize o método de **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Como: eliminar a partir de uma tabela sem tipos

O código seguinte mostra como eliminar uma instância, neste caso, a mesma instância do **JsonObject** que foi criado no exemplo anterior, *Inserir* . O código é igual com as maiúsculas/minúsculas escrita, mas o método tem uma assinatura diferente, uma vez que faz referência a uma **JsonObject**.

         mToDoTable.delete(jsonItem);

Também pode eliminar uma instância diretamente ao utilizar o seu ID de:

         mToDoTable.delete(ID);

### <a name="json_get"></a>Como: devolver todas as linhas de uma tabela sem tipos

O código seguinte mostra como obter uma tabela inteira. Uma vez que estiver a utilizar uma tabela de JSON, pode obter seletivamente apenas algumas das colunas da tabela.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

O mesmo conjunto de filtragem, filtragem e paginação métodos que estão disponíveis para o modelo escrito estão disponíveis para o modelo de sem tipos.

##<a name="custom-api"></a>Como: ligar uma API personalizada

Uma API personalizada permite-lhe definir personalizados pontos finais que expõem a funcionalidade de servidor que não mapear para um insert, atualizar, eliminar ou operação de leitura. Ao utilizar uma API personalizada, pode ter mais controlo sobre mensagens, incluindo de leitura e definir cabeçalhos de mensagens HTTP e definir um formato de corpo da mensagem que não seja JSON.

A partir de um cliente Android, ligar para o método de **invokeApi** para ligar o ponto final de API personalizado. O exemplo seguinte mostra como ligar um ponto final API denominado **completeAll**, que devolve uma classe de coleções de sites com o nome **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

O método de **invokeApi** chama-se no cliente envia um pedido de mensagem para a nova API personalizada. O resultado devolvido pela API personalizado é apresentado na caixa de diálogo mensagem, como estão todos os erros. Outras versões do **invokeApi** permitem-lhe enviar um objeto no corpo do pedido opcionalmente, especifique o método de HTTP e enviar parâmetros de consulta com o pedido. Sem tipos versões do **invokeApi** são fornecidos também.

##<a name="authentication"></a>Como: Adicionar autenticação para a sua aplicação

Tutoriais descrevem já detalhadamente como adicionar estas funcionalidades.

Suporta a aplicação de serviço de [autenticação de utilizadores da aplicação](app-service-mobile-android-get-started-users.md) utilizando uma variedade de fornecedores de identidades externas: Facebook, Google, Account Microsoft, Twitter e Azure Active Directory. Pode definir permissões em tabelas para restringir o acesso operações específicas para apenas utilizadores autenticados. Também pode utilizar a identidade do utilizadores autenticados lhe implementar regras de autorização no seu back-end.

Dois fluxos de autenticação suportados: um fluxo de **servidor** e um fluxo de **cliente** . O fluxo de servidor fornece a experiência de autenticação mais simples, como baseia-se na interface de web de fornecedores de identidade.  Sem SDK adicional é necessários para implementar a autenticação de fluxo de servidor. Autenticação do servidor fluxo não fornece uma integração abrangente para o dispositivo móvel e só é recomendada para prova de cenários de conceito.

O fluxo de cliente permite para integração mais aprofundada com capacidades específicas do dispositivo, tais como o início de sessão único como baseia-se no SDK fornecido pelo fornecedor de identidade.  Por exemplo, pode integrar o SDK do Facebook a sua aplicação móvel.  O cliente móvel troca para a aplicação do Facebook e confirma a sua sessão antes de trocar novamente para a sua aplicação móvel.

Quatro passos são necessários para ativar a autenticação na sua aplicação:

- Registe-se a sua aplicação para autenticação com um fornecedor de identidade.
- Configure a sua aplicação de serviço do back-end.
- Restringir permissões de tabela para os utilizadores autenticados apenas na aplicação de serviço back-end.
- Adicione o código de autenticação para a sua aplicação.

Pode definir permissões em tabelas para restringir o acesso operações específicas para apenas utilizadores autenticados. Também pode utilizar o SID de um utilizador autenticado para modificar pedidos.  Para mais informações, reveja [começar com autenticação] e a documentação de How to SDK do servidor.

### <a name="caching"></a>Como: adicionar o código de autenticação para a sua aplicação

O código seguinte inicia um processo de início de sessão do fluxo de servidor utilizando o fornecedor do Google:

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obter o ID do utilizador com sessão iniciada a partir de um **MobileServiceUser** utilizando o método de **getUserId** . Para obter um exemplo de como utilizar futuros para o início de sessão assíncrono APIs de chamadas, consulte o artigo [começar a trabalhar com autenticação].

### <a name="caching"></a>Como: tokens de autenticação em Cache

Colocação em cache tokens de autenticação requer que armazenar o ID de utilizador e token de autenticação localmente no dispositivo. Da próxima vez que inicia a aplicação, dar entrada a cache e se estiverem presentes estes valores, pode ignorar o registo no procedimento e rehydrate o cliente com estes dados. No entanto estes dados são sensíveis e deve ficar armazenada encriptados de segurança, caso o telefone for roubado.

Pode ver um exemplo concluído de como a tokens de autenticação de cache na [secção de tokens de autenticação de Cache][7].

Quando tentar utilizar um token expirado, recebe uma resposta *401 não autorizado* . Pode processar erros de autenticação através dos filtros.  Filtros interceptar pedidos para o aplicação de serviço back-end. O código de filtro testa a resposta para um 401, accionadores o processo de início de sessão no e, em seguida, currículos pedido que gerou a 401. 

## <a name="adal"></a>Como: autenticar utilizadores com a biblioteca de autenticação do Active Directory

Pode utilizar o Active Directory autenticação biblioteca (ADAL) para iniciar sessão os utilizadores na sua aplicação utilizando o Azure Active Directory. Utilizar um início de sessão do fluxo de cliente é preferível ao utilizar o `loginAsync()` métodos, tal como é fornece um funcionalidade UX mais nativo e permite adicionais de personalização.

1. Configure a sua aplicação móvel back-end AAD iniciar sessão, seguindo o tutorial de [como configurar o serviço de aplicação para o início de sessão do Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Certifique-se concluir o passo opcional de registar uma aplicação de cliente nativa.

2. Instale ADAL modificando o ficheiro de build.gradle para incluir as seguintes definições:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Adicione o código seguinte à aplicação, tornando das substituições seguintes:

* Substitua **Aqui de certificação de inserir** com o nome do inquilino na qual aprovisionado que a aplicação. O formato deve ser https://login.windows.net/contoso.onmicrosoft.com. Este valor pode ser copiado a partir do separador de domínio no seu Azure Active Directory no [Azure clássico portal do].

* Substitua **Inserir recurso-ID aqui** o ID do cliente para a sua aplicação móvel do back-end. Pode obter o ID de cliente a partir do separador **Avançadas** , em **Definições do Azure Active Directory** , no portal.

* Substitua o ID do cliente que copiou da aplicação de cliente nativo **Inserir cliente-ID aqui** .

* Substitua **Inserir-REDIRECT-URI-aqui** ponto final de _/.auth/login/done_ do seu site, utilizando o esquema de HTTPS. Este valor deverá ser semelhante ao _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>Como: Adicionar notificações push para a sua aplicação

Pode [ler uma descrição geral] [ 6] que descreve como concentradores de notificação do Microsoft Azure suporta uma grande variedade de notificações push.  [Neste]tutorial[5], uma notificação de emissão é enviada para todos os dispositivos sempre que um registo é inserido.

## <a name="how-to-add-offline-sync-to-your-app"></a>Como: adicionar a sincronização offline para a sua aplicação

O tutorial de guia de introdução contém código que implementa sincronização offline. Procure o prefixo com comentários de código:

    // Offline Sync

Por uncommenting as linhas seguintes do código que possa implementar a sincronização offline e, pode adicionar código semelhante ao código outras aplicações móveis.

##<a name="customizing"></a>Como: personalizar o cliente

Existem várias formas que pode personalizar o comportamento predefinido do cliente.

### <a name="headers"></a>Como: Personalizar pedir cabeçalhos

Configure um **ServiceFilter** para adicionar um cabeçalho HTTP personalizado para cada pedido:

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Como: Personalizar serialização

O cliente assume que os nomes de tabela, os nomes das colunas e dados tipos no back-end todos corresponderem exatamente os objetos de dados definidos no cliente. Pode ser qualquer número das razões porque é que os nomes de servidor e o cliente não podem corresponder. No seu cenário, poderá pretender efetue os seguintes tipos de personalizações:

- Os nomes das colunas utilizadas na tabela de aplicação de serviço não correspondem aos nomes que estiver a utilizar no cliente.
- Utilize uma tabela de aplicação de serviço que tem um nome diferente que a classe-mapas no cliente.
- Ative o uso de maiúsculas/minúsculas propriedade automática.
- Adicione propriedades complexas a um objeto.

### <a name="columns"></a>Como: mapear nomes de cliente e servidor diferentes

Suponha que o seu código do cliente Java utiliza os nomes de estilo Java padrão para as propriedades do objeto **ToDoItem** , tais como as seguintes propriedades:

- seg. texto
- mText
- mComplete
- MDURAÇÃO

Serializar os nomes de cliente em nomes JSON que correspondem aos nomes das colunas da tabela **ToDoItem** no servidor. O código seguinte utiliza a [gson] [ 3] biblioteca para anotar as propriedades:

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Como: mapear nomes de tabelas diferentes entre o cliente e o back-end

Mapear o nome da tabela Cliente para o nome de uma tabela de serviços móveis diferentes utilizando uma substituição do [getTable()] [ 4] método:

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Como: automatizar mapeamentos de nome de coluna

Pode especificar uma estratégia de conversão que se aplica a todas as colunas utilizando o [gson] [ 3] API. A biblioteca do cliente Android utiliza [gson] [ 3] nos bastidores para serializar objectos Java aos dados JSON antes dos dados são enviados para a aplicação de serviço de Azure.  O código seguinte utiliza o método de **setFieldNamingStrategy()** para definir a estratégia. Este exemplo irá eliminar o caráter inicial (um "m") e, em seguida, minúsculas o caráter seguinte, para cada nome de campo. Por exemplo,-seria transformar "seg. texto" "id."

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Este código tem de ser executado antes de utilizar o **MobileServiceClient**.

### <a name="complex"></a>Como: armazenar um objeto ou de uma matriz de propriedade numa tabela

Até ao momento, o nossos exemplos serialização tenham envolvido tipos primitivos como cadeias e números inteiros.  Tipos primitivos serializar facilmente para JSON.  Se queremos adicionar um objeto complexo que não serializar automaticamente ao JSON, é necessário fornecer o método de serialização JSON.  Para ver um exemplo de como fornecer serialização de JSON personalizada, reveja a mensagem de blogue [serialização personalizar utilizando a biblioteca de gson no cliente móvel serviços Android][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Devolver todos os itens]: #showAll
[Filtrar os dados devolvidos]: #filtering
[Ordenar dados devolvido]: #sorting
[Devolver dados nas páginas]: #paging
[Seleccionar colunas específicas]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portal do Azure]: https://portal.azure.com
[Começar a trabalhar com autenticação]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Futuro]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html