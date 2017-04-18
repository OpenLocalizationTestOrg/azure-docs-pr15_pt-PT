<properties
   pageTitle="Topologias Apache tempestade com Visual Studio e c# | Microsoft Azure"
   description="Saiba como criar topologias tempestade no c# através da criação de uma topologia de contagem simples do word no Visual Studio utilizando as ferramentas de HDInsight para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/27/2016"
   ms.author="larryfr"/>

# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Desenvolver c# topologias para Apache tempestade no HDInsight com ferramentas Hadoop para Visual Studio

Saiba como criar uma topologia de c# tempestade utilizando as ferramentas de HDInsight para Visual Studio. Neste tutorial explica o processo de criar um novo projeto de tempestade no Visual Studio, testá-la localmente e implementá-lo para um tempestade Apache num cluster de HDInsight.

Também irá Saiba como criar topologias híbrido que utilizam c# e componentes Java.

> [AZURE.IMPORTANT] Enquanto os passos neste documento dependem de um ambiente de desenvolvimento do Windows com o Visual Studio, o projecto compilado pode ser apresentado ao cluster de um Linux ou HDInsight baseados no Windows. Apenas os clusters baseado em Linux criados depois do suporte de 28/10/2016 SCP.NET topologias.
>
> Para utilizar uma topologia de c# com um cluster baseado em Linux, tem de atualizar o pacote de Microsoft.SCP.Net.SDK NuGet utilizado pelo seu projeto para a versão 0.10.0.6 ou superior. A versão do pacote também tem de corresponder a versão principal de tempestade instalado no HDInsight. Por exemplo, tempestade em versões HDInsight 3.3 e 3.4 utilizar a versão de tempestade 0.10.x, enquanto HDInsight 3.5 utiliza tempestade 1.0.x.
> 
> C# topologias em baseado em Linux clusters tem de utilizar .NET 4,5 e utilizar Mono para executar no HDInsight cluster. A maioria dos elementos irá funcionar, no entanto deve verificar o documento de [Compatibilidade de Mono](http://www.mono-project.com/docs/about-mono/compatibility/) para identificar possíveis incompatibilidades.

## <a name="prerequisites"></a>Pré-requisitos

- Uma das seguintes versões do Visual Studio

    - Visual Studio 2012, com [4 de actualização](http://www.microsoft.com/download/details.aspx?id=39305)

    - Visual Studio 2013 com [Atualizar 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Comunidade](http://go.microsoft.com/fwlink/?LinkId=517284)

    - Visual Studio 2015 ou [Comunidade de 2015 do Visual Studio](https://go.microsoft.com/fwlink/?LinkId=532606)

- Azure SDK 2.9.5 ou posterior

- Ferramentas de HDInsight para Visual Studio: consulte o artigo [começar a utilizar o HDInsight ferramentas para o Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para instalar e configurar as ferramentas de HDInsight para Visual Studio.

    > [AZURE.NOTE] Ferramentas de HDInsight para Visual Studio não são suportadas no Visual Studio Express

-   Apache tempestade num cluster de HDInsight: consulte o artigo [introdução com Apache tempestade no HDInsight](hdinsight-apache-storm-tutorial-get-started.md) para obter os passos para criar um cluster.

## <a name="templates"></a>Modelos

As ferramentas de HDInsight para Visual Studio fornecem seguintes modelos:

| Tipo de projecto | Demonstra |
| ------------ | ------------- |
| Aplicação tempestade | Um projecto de topologia tempestade vazio |
| Exemplo de sénior tempestade Azure SQL | Como escrever a base de dados do Azure SQL |
| Exemplo de leitor DocumentDB tempestade | Saber como ler a partir do Azure DocumentDB |
| Exemplo de DocumentDB sénior tempestade | Como escrever para Azure DocumentDB |
| Exemplo de leitor EventHub tempestade | Saber como ler a partir do Azure evento concentradores |
| Exemplo de EventHub sénior tempestade | Como escrever a concentradores de eventos do Azure |
| Exemplo de leitor HBase tempestade | Saber como ler a partir do HBase sobre clusters de HDInsight |
| Exemplo de HBase sénior tempestade | Como escrever para HBase em HDInsight clusters |
| Exemplo de híbrido tempestade | Como utilizar um componente Java |
| Exemplo de tempestade | Uma topologia de contagem de base do word |

> [AZURE.NOTE] Os exemplos de leitor e writer HBase utilizam a API do resto HBase para comunicar com uma HBase num cluster de HDInsight, não HBase Java API.

Os passos neste documento, irá utilizar o tipo de projecto de aplicação tempestade básico para criar uma nova topologia.

## <a name="create-a-c-topology"></a>Criar uma topologia c#

1. Se já não tiver instalado a versão mais recente das ferramentas de HDInsight para Visual Studio, consulte o artigo [começar a utilizar o HDInsight ferramentas para o Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Abrir o Visual Studio, selecione o **ficheiro** > **Novo**e, em seguida, o **Project**.

3. No ecrã **Novo projeto** , expanda **instalados** > **modelos**e selecione **HDInsight**. A partir da lista de modelos, selecione a **Aplicação de tempestade**. Na parte inferior do ecrã, introduza **WordCount** como o nome da aplicação.

    ![imagem](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Depois do projeto tiver sido criado, deverá ter os seguintes ficheiros:

    - **Program.cs**: Isto define a topologia para o seu projeto. Tenha em atenção que uma topologia de predefinido que consiste em uma vareta e um raio é criada por predefinição.

    - **Spout.cs**: uma vareta de exemplo emite números aleatórios.

    - **Bolt.cs**: um raio de exemplo que mantém uma contagem de números emitidos pela vareta.

    Como parte da criação de projetos, serão transferidos a partir do NuGet mais recentes [SCP.NET pacotes](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) .

    [AZURE.INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

Nas secções seguintes, irá modificar este projeto numa aplicação WordCount básica.

### <a name="implement-the-spout"></a>Implementar a vareta

1. Abra **Spout.cs**. Spouts são utilizadas para ler dados numa topologia de uma origem externa. Os componentes principais para uma vareta são:

    - **NextTuple**: chamado pelo tempestade quando a vareta é permitida a emitir cadeia de identificação nova.

    - **ACK** (apenas topologia transaccional): processa confirmações iniciadas por outros componentes a topologia de cadeia de identificação enviadas a partir deste vareta. Reconhecer uma cadeia de identificação permite vareta saber que foi processada com êxito pelos componentes descendentes.

    - **Falhar** (apenas topologia transaccional): processa cadeia de identificação que é falhas processamento de outros componentes da topologia. Este procedimento fornece a oportunidade de emitir voltar a cadeia de identificação para que possa ser processado novamente.

2. Substitua o conteúdo de classe **Spout** com o seguinte. Esta ação cria uma vareta que aleatoriamente emite uma frase para a topologia.

        private Context ctx;
        private Random r = new Random();
        string[] sentences = new string[] {
            "the cow jumped over the moon",
            "an apple a day keeps the doctor away",
            "four score and seven years ago",
            "snow white and the seven dwarfs",
            "i am at two with nature"
        };

        public Spout(Context ctx)
        {
            // Set the instance context
            this.ctx = ctx;

            Context.Logger.Info("Generator constructor called");

            // Declare Output schema
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // The schema for the default output stream is
            // a tuple that contains a string field
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        }

        // Get an instance of the spout
        public static Spout Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Spout(ctx);
        }

        public void NextTuple(Dictionary<string, Object> parms)
        {
            Context.Logger.Info("NextTuple enter");
            // The sentence to be emitted
            string sentence;

            // Get a random sentence
            sentence = sentences[r.Next(0, sentences.Length - 1)];
            Context.Logger.Info("Emit: {0}", sentence);
            // Emit it
            this.ctx.Emit(new Values(sentence));

            Context.Logger.Info("NextTuple exit");
        }

        public void Ack(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }

        public void Fail(long seqId, Dictionary<string, Object> parms)
        {
            // Only used for transactional topologies
        }
    
    Observar a leitura através dos comentários para compreender o que significa este código.

### <a name="implement-the-bolts"></a>Implementar o sobre

1. Elimine o ficheiro de **Bolt.cs** existente do projeto.

2. No **Explorador de soluções**, com o botão direito do projeto e selecione **Adicionar** > **novo item**. A partir da lista, selecione **Tempestade raio**e introduza **Splitter.cs** como o nome. Repita este procedimento para criar uma segunda bolt com nome **Counter.cs**.

    - **Splitter.cs**: implementa um raio que divide frases em palavras individuais e emite um novo fluxo de palavras.

    - **Counter.cs**: implementa um raio que conta cada palavra e emite um novo fluxo de palavras e a contagem de cada palavra.

    > [AZURE.NOTE] Estes parafusos simplesmente ler e escreverem para sequências, mas também pode utilizar um raio para comunicar com origens como uma base de dados ou serviço.

3. Abra **Splitter.cs**. Note que tem só um método por predefinição: **Executar**. Esta opção é denominada quando o raio recebe uma cadeia de identificação para processamento. Aqui, pode ler e processar cadeia de identificação recebidas e emitir cadeia de identificação de saída.

4. Substitua o conteúdo de classe **Divisor** com o seguinte código:

        private Context ctx;

        // Constructor
        public Splitter(Context ctx)
        {
            Context.Logger.Info("Splitter constructor called");
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // Input contains a tuple with a string field (the sentence)
            inputSchema.Add("default", new List<Type>() { typeof(string) });
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // Outbound contains a tuple with a string field (the word)
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance of the bolt
        public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Splitter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the sentence from the tuple
            string sentence = tuple.GetString(0);
            // Split at space characters
            foreach (string word in sentence.Split(' '))
            {
                Context.Logger.Info("Emit: {0}", word);
                //Emit each word
                this.ctx.Emit(new Values(word));
            }

            Context.Logger.Info("Execute exit");
        }

    Observar a leitura através dos comentários para compreender o que significa este código.

5. Abra **Counter.cs** e substituir o conteúdo de classe com o seguinte:

        private Context ctx;

        // Dictionary for holding words and counts
        private Dictionary<string, int> counts = new Dictionary<string, int>();

        // Constructor
        public Counter(Context ctx)
        {
            Context.Logger.Info("Counter constructor called");
            // Set instance context
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string field - the word
            inputSchema.Add("default", new List<Type>() { typeof(string) });

            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string and integer field - the word and the word count
            outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance
        public static Counter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Counter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the word from the tuple
            string word = tuple.GetString(0);
            // Do we already have an entry for the word in the dictionary?
            // If no, create one with a count of 0
            int count = counts.ContainsKey(word) ? counts[word] : 0;
            // Increment the count
            count++;
            // Update the count in the dictionary
            counts[word] = count;

            Context.Logger.Info("Emit: {0}, count: {1}", word, count);
            // Emit the word and count information
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
            Context.Logger.Info("Execute exit");
        }

    Observar a leitura através dos comentários para compreender o que significa este código.

### <a name="define-the-topology"></a>Definir a topologia

Spouts e parafusos são ordenadas por um gráfico, que define como os dados flua entre componentes. Para este topologia, o gráfico é:

![imagem de como os componentes são dispostos](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Frases são emitidas a partir do vareta, que é distribuído a instâncias do raio divisor. O raio divisor divide os frases em palavras, o que são distribuídas para o raio contador.

Uma vez que a contagem de palavras é mantida localmente na instância do contador, queremos para se certificar de que palavras específicas fluam na mesma instância de raio contador, para que temos apenas uma instância manter o registo de uma determinada palavra. Mas, para raio divisor, na verdade não interessa qual raio recebe qual uma frase, por isso pedimos que simplesmente carregar o saldo frases em várias essas instâncias.

Abra **Program.cs**. O método importante é **GetTopologyBuilder**, que é utilizado para definir a topologia de que está a ser submetida para tempestade. Substitua os conteúdos de **GetTopologyBuilder** com o seguinte código para implementar a topologia descrita anteriormente:

        // Create a new topology named 'WordCount'
        TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

        // Add the spout to the topology.
        // Name the component 'sentences'
        // Name the field that is emitted as 'sentence'
        topologyBuilder.SetSpout(
            "sentences",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
            },
            1);
        // Add the splitter bolt to the topology.
        // Name the component 'splitter'
        // Name the field that is emitted 'word'
        // Use suffleGrouping to distribute incoming tuples
        //   from the 'sentences' spout across instances
        //   of the splitter
        topologyBuilder.SetBolt(
            "splitter",
            Splitter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
            },
            1).shuffleGrouping("sentences");

        // Add the counter bolt to the topology.
        // Name the component 'counter'
        // Name the fields that are emitted 'word' and 'count'
        // Use fieldsGrouping to ensure that tuples are routed
        //   to counter instances based on the contents of field
        //   position 0 (the word). This could also have been
        //   List<string>(){"word"}.
        //   This ensures that the word 'jumped', for example, will always
        //   go to the same instance
        topologyBuilder.SetBolt(
            "counter",
            Counter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
            },
            1).fieldsGrouping("splitter", new List<int>() { 0 });

        // Add topology config
        topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
        {
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

Observar a leitura através dos comentários para compreender o que significa este código.

## <a name="submit-the-topology"></a>Submeter a topologia

1. No **Explorador de soluções**, com o botão direito do projeto e selecione **Submeter para tempestade no HDInsight**.

    > [AZURE.NOTE] Se lhe for pedido, introduza as credenciais de início de sessão para a sua subscrição Azure. Se tiver mais do que uma subscrição, inicie sessão na que contém o seu tempestade num cluster de HDInsight.

2. Selecione o tempestade num cluster de HDInsight a partir da lista pendente de **Cluster tempestade** e, em seguida, selecione **Submeter**. Pode monitorizar se a apresentação for bem sucedida, utilizando a janela de **saída** .

3. Quando a topologia foi submetida com êxito, deverá aparecer a **Tempestade topologias** para o cluster. Selecione a topologia de **WordCount** a partir da lista para ver informações sobre a topologia em execução.

    > [AZURE.NOTE] Também pode ver **Tempestade topologias** a partir do **Explorador de servidor**: expandir **Azure** > **HDInsight**, um tempestade num cluster de HDInsight com o botão direito e, em seguida, selecione **Vista tempestade topologias**.

    Utilize as ligações para o spouts ou parafusos para ver informações sobre estes componentes. Será aberta para cada item seleccionado numa nova janela.

4. A partir da vista de **Resumo de topologia** , clique em **Eliminar** para parar a topologia.

    > [AZURE.NOTE] Topologias tempestade continuam a ser executadas até que são desativados ou cluster é eliminado.

## <a name="transactional-topology"></a>Topologia transaccional

A topologia anterior é não transaccional. Os componentes de topologia da não implementar qualquer da funcionalidade para reproduzir mensagens se processamento falhar por um componente na topologia. Para uma topologia de transaccionais de exemplo, criar um novo projeto e selecione o **Exemplo tempestade** como o tipo de projecto.

Topologias transaccionais implementam o seguinte procedimento para suportar a reprodução de dados:

- **Colocação em cache de metadados**: A vareta tem armazenar metadados sobre os dados emitidos para que podem ser obtidos e emitidos novamente se ocorre uma falha dos dados. Porque são pequenos os dados emitidos por exemplo, os dados não processados para cada cadeia de identificação não são armazenados num dicionário para reprodução.

- **ACK**: pode ligar a cada raio na topologia de `this.ctx.Ack(tuple)` para ack que tem com êxito processadas uma cadeia de identificação. Quando todos os parafusos têm registadas cadeia de identificação, o `Ack` o método da vareta é chamado. Esta opção permite-vareta remover dados em cache para reprodução porque os dados completamente foram processados.

- **Falhar**: pode ligar a cada raio `this.ctx.Fail(tuple)` para indicar que tem falhou o processamento para uma cadeia de identificação. A falha é propagado para o `Fail` método de vareta, onde a cadeia de identificação não pode ser reproduzida utilizando em cache metadados.

- **ID de sequência**: Quando emitir uma cadeia de identificação, um ID de sequência pode ser especificado. Isto deve ser um valor que identifica a cadeia de identificação para processamento de reprodução (Ack e falhas). Por exemplo, vareta no projeto **Tempestade exemplo** utiliza a seguinte quando emissão dados:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Isto emite uma cadeia de identificação nova que contém uma frase na sequência predefinido, com o valor de ID de sequência contido **lastSeqId**. Neste exemplo, **lastSeqId** simplesmente é incrementado para cada cadeia de identificação emitida.

Conforme demonstrado no projeto **Tempestade exemplo** , se um componente é transaccional pode ser definida tempo de execução, com base na configuração.

## <a name="hybrid-topology"></a>Topologia híbrido

Ferramentas de HDInsight para Visual Studio também podem ser utilizadas para criar topologias híbrido, onde alguns componentes são c# e outras pessoas Java.

Para uma topologia de implementações do exemplo, criar um novo projeto e, selecione **Tempestade híbrido exemplo**. Esta ação cria uma amostra totalmente remova que contenha várias topologias que demonstram o seguinte procedimento:

- **Java spout** e **c# raio**: definida na **HybridTopology_javaSpout_csharpBolt**

    - Uma versão transaccional é definida na **HybridTopologyTx_javaSpout_csharpBolt**

- **C# spout** e **raio Java**: definida na **HybridTopology_csharpSpout_javaBolt**

    - Uma versão transaccional é definida na **HybridTopologyTx_csharpSpout_javaBolt**

        > [AZURE.NOTE] Esta versão também demonstra como utilizar o código de Clojure a partir de um ficheiro de texto como um componente Java.

Para alternar entre a topologia de que é utilizada quando for apresentado o projeto, mova-se simplesmente o `[Active(true)]` instrução para a topologia de que pretende utilizar antes de a submeter para cluster.

> [AZURE.NOTE] Todos os ficheiros de Java que são necessários são fornecidos como parte deste projeto na pasta **JavaDependency** .

Considere o seguinte quando criar e submeter uma topologia de híbrido:

- **JavaComponentConstructor** tem de ser utilizados para criar uma nova instância da classe Java para uma vareta ou raio.

- **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** deve ser utilizada para serializar dados para ou retirá componentes Java de objetos de Java para JSON.

- Ao submeter a topologia no servidor, tem de utilizar a opção de **configurações adicionais** para especificar os **caminhos de ficheiro Java**. O caminho especificado deve ser diretório que contém os ficheiros para caixa que contêm as suas Turmas Java.

### <a name="azure-event-hubs"></a>Evento Azure concentradores

Versão SCP.Net 0.9.4.203 apresenta uma nova classe e método especificamente para trabalhar com o evento concentrador Spout (uma Java vareta que lê a partir do Hub do evento.) Ao criar uma topologia de que utiliza esta vareta, utilize os seguintes métodos:

- **EventHubSpoutConfig** escolares: cria um objeto que contém a configuração para o componente de vareta

- Método de **TopologyBuilder.SetEventHubSpout** : Adiciona o componente Spout concentrador de evento para a topologia

> [AZURE.NOTE] Enquanto estes facilitar trabalhar com o concentrador de evento Spout que outros componentes Java, ainda tem de utilizar o CustomizedInteropJSONSerializer para serializar dados produzidos pela vareta.

## <a id="configurationmanager"></a>Utilizar ConfigurationManager

Não utilize ConfigurationManager para obter os valores de configuração de raio e spout componentes; Isto vai originar uma exceção de ponteiro nulo. Em vez disso, a configuração para o seu projeto lhe é transmitida para a topologia de tempestade como um par de valor/chave no contexto da topologia. Cada componente que depende de valores de configuração tem obtê-los a partir do contexto durante a inicialização.

O código seguinte demonstra como obter estes valores:

    public class MyComponent : ISCPBolt
    {
        // To hold configuration information loaded from context
        Configuration configuration;
        ...
        public MyComponent(Context ctx, Dictionary<string, Object> parms)
        {
            // Save a copy of the context for this component instance
            this.ctx = ctx;
            // If it exists, load the configuration for the component
            if(parms.ContainsKey(Constants.USER_CONFIG))
            {
                this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
            }
            // Retrieve the value of "Foo" from configuration
            var foo = this.configuration.AppSettings.Settings["Foo"].Value;
        }
        ...
    }

Se utilizar um `Get` método para devolver uma instância do seu componente, tem de se certificar que transmite ambos os `Context` e `Dictionary<string, Object>` parâmetros do construtor. O exemplo seguinte é um basic `Get` método corretamente transmite estes valores:

    public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new MyComponent(ctx, parms);
    }

## <a name="how-to-update-scpnet"></a>Como atualizar SCP.NET

Edições recentes da SCP.NET suportam o pacote de atualização através de NuGet. Quando uma nova actualização estiver disponível, receberá uma notificação de atualização. Para verificar manualmente uma atualização, execute estes passos:

1. No **Explorador de soluções**, com o botão direito do projeto e selecione **Gerir pacotes de NuGet**.

2. A partir do Gestor de pacote, selecione **as atualizações**. Se estiver disponível uma atualização, este será listado. Clique no botão de **atualização** para o pacote para o instalar.

> [AZURE.IMPORTANT] Se o seu projeto foi criado com uma das versões anteriores do SCP.NET que não utilizou NuGet existência de actualizações de pacote, deve efetuar os seguintes passos para atualizar para a nova versão:
>
> 1. No **Explorador de soluções**, com o botão direito do projeto e selecione **Gerir pacotes de NuGet**.
> 2. Utilizar o campo de **pesquisa** , procurar e, em seguida, adicionar, **Microsoft.SCP.Net.SDK** ao projeto.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="null-pointer-exceptions"></a>Exceções de ponteiro nulo

Quando utilizar uma topologia de c# com um cluster de baseado em Linux HDInsight, parafuso e spout componentes utilizam ConfigurationManager para ler as definições de configuração o tempo de execução podem devolver exceções ponteiro nulo. Isto acontece porque a configuração do domínio carregado não está a partir do assemblagem que contém o seu projeto.

A configuração para o seu projeto é transmitida para a topologia de tempestade como um par de valor/chave no contexto da topologia e pode ser obtida a partir do objeto de dicionário que lhe é transmitido os componentes quando estes são inicializados.

O exemplo seguinte demonstra a carregar os valores de configuração a partir do contexto de topologia, consulte a secção [ConfigurationManager](#configurationmanager) deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Ao utilizar uma topologia de c# com um cluster de baseado em Linux HDInsight, que poderá encontrar o seguinte erro:

    System.TypeLoadException: Failure has occurred while loading a type.

Neste normalmente ocorrer quando estiver a utilizar um ficheiro binário que não é compatível com a versão do .NET mono suporta.

Para clusters baseado em Linux HDInsight, deve certificar-se de que o seu projeto utiliza binários compilados para .NET 4,5.


### <a name="test-a-topology-locally"></a>Testar uma topologia de localmente

Apesar de é fácil implementar uma topologia de um cluster de, em alguns casos, poderá ter de testar uma topologia de localmente. Utilize os passos seguintes para executar e testar a topologia de exemplo neste tutorial localmente no seu ambiente de desenvolvimento.

> [AZURE.WARNING] Testes local só funciona para básicas, c# apenas topologias. Não deve utilizar local testes de topologias híbrido ou topologias que utilizar vários fluxos, à medida que vai receber erros.

1. No **Explorador de soluções**, com o botão direito do projeto e selecione **Propriedades**. Nas propriedades do projeto, altere o **tipo de saída** para a **Aplicação de consola**.

    ![tipo de saída](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)

    > [AZURE.NOTE] Lembre-se alterar o **tipo de saída** regresse a **Biblioteca de classe** antes de implementar a topologia a um cluster.

2. No **Explorador de soluções**, com o botão direito do projeto, em seguida, selecione **Adicionar** > **Novo Item**. Selecione **escolares** e introduza **LocalTest.cs** como o nome de classe. Por fim, clique em **Adicionar**.

3. Abra **LocalTest.cs** e adicione a seguinte instrução **utilizando** na parte superior:

        using Microsoft.SCP;

4. Utilize o seguinte como o conteúdo de classe **LocalTest** :

        // Drives the topology components
        public void RunTestCase()
        {
            // An empty dictionary for use when creating components
            Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

            #region Test the spout
            {
                Console.WriteLine("Starting spout");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext spoutCtx = LocalContext.Get();
                // Get a new instance of the spout, using the local context
                Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

                // Emit 10 tuples
                for (int i = 0; i < 10; i++)
                {
                    sentences.NextTuple(emptyDictionary);
                }
                // Use LocalContext to persist the data stream to file
                spoutCtx.WriteMsgQueueToFile("sentences.txt");
                Console.WriteLine("Spout finished");
            }
            #endregion

            #region Test the splitter bolt
            {
                Console.WriteLine("Starting splitter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext splitterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);


                // Set the data stream to the data created by the spout
                splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    splitter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                splitterCtx.WriteMsgQueueToFile("splitter.txt");
                Console.WriteLine("Splitter bolt finished");
            }
            #endregion

            #region Test the counter bolt
            {
                Console.WriteLine("Starting counter bolt");
                // LocalContext is a local-mode context that can be used to initialize
                // components in the development environment.
                LocalContext counterCtx = LocalContext.Get();
                // Get a new instance of the bolt
                Counter counter = Counter.Get(counterCtx, emptyDictionary);

                // Set the data stream to the data created by splitter bolt
                counterCtx.ReadFromFileToMsgQueue("splitter.txt");
                // Get a batch of tuples from the stream
                List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
                // Process each tuple in the batch
                foreach (SCPTuple tuple in batch)
                {
                    counter.Execute(tuple);
                }
                // Use LocalContext to persist the data stream to file
                counterCtx.WriteMsgQueueToFile("counter.txt");
                Console.WriteLine("Counter bolt finished");
            }
            #endregion
        }

    Observar a leitura através dos comentários de código. Este código utiliza **LocalContext** para executar os componentes no ambiente de desenvolvimento e persisti-lo a sequência de dados entre os componentes para ficheiros de texto na unidade local.

5. Abra **Program.cs** e adicione o seguinte para o método **principais** :

        Console.WriteLine("Starting tests");
        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
        // Initialize the runtime
        SCPRuntime.Initialize();

        //If we are not running under the local context, throw an error
        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
        }
        // Create test instance
        LocalTest tests = new LocalTest();
        // Run tests
        tests.RunTestCase();
        Console.WriteLine("Tests finished");
        Console.ReadKey();

6. Guarde as alterações e, em seguida, clique em **F5** ou selecione **Depurar** > **Depuração de começar** para iniciar o projeto. Uma janela de consola deve aparecer e, inicie sessão Estado como o progresso de testes. Quando **que terminar de testes** for apresentada, prima qualquer tecla para fechar a janela.

7. Utilize o **Explorador do Windows** para localizar o directório que contém o seu projeto, por exemplo, **C:\Users\<Nome_do_utilizador > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Neste directório, abrir **Reciclagem**e, em seguida, clique em **Depurar**. Deverá ver os ficheiros de texto que foram produzidos quando os testes executou: sentences.txt, counter.txt e splitter.txt. Abra a cada ficheiro de texto e verificar se os dados.

    > [AZURE.NOTE] Dados de cadeia são mantidos como uma matriz de valores decimais estes ficheiros. Por exemplo, \[[97,103,111]] a **splitter.txt** ficheiro é a palavra 'e'.

Apesar de testes uma palavra básica contar o número de aplicação localmente é muito comum, que o valor real vem quando tiver uma topologia complexa que comunica com origens de dados externas ou executa a análise de dados complexos. Quando estiver a trabalhar num projeto, poderá ter de definir pontos de interrupção e passo através do código no seu componentes para identificar problemas.

> [AZURE.NOTE] Certifique-se de que definir o **tipo de projecto** para **A biblioteca de classes** antes de implementar para uma tempestade num cluster de HDInsight.

### <a name="log-information"></a>Informações de registo

Pode facilmente registar informações a partir do seu componentes de topologia utilizando `Context.Logger`. Por exemplo, o seguinte procedimento irá criar uma entrada no registo informativo:

    Context.Logger.Info("Component started");

Podem ser visualizadas informações com sessão iniciada a partir do **Registo de serviço do Hadoop**, que se encontra no **Explorador do servidor**. Expanda a entrada para a sua tempestade num cluster de HDInsight, em seguida, expanda o **Registo de Hadoop de serviço**. Por fim, selecione o ficheiro de registo para ver.

> [AZURE.NOTE] Os registos são armazenados na conta de armazenamento do Windows Azure que é utilizada pelo seu cluster. Se esta é uma subscrição diferente daquele que tem sessão iniciada para com o Visual Studio, tem de iniciar sessão na subscrição que contém a conta de armazenamento para ver estas informações.

### <a name="view-error-information"></a>Ver informações de erro

Para ver erros que tenham ocorrido numa topologia em execução, utilize os seguintes passos:

1. A partir do **Explorador do servidor**, tempestade num cluster de HDInsight com o botão direito e, selecione **vista tempestade topologias**.

2. Para o **Spout** e **Bolts**, a coluna do **Último erro** terá de obter informações sobre o último erro ocorreu.

3. Selecione o **Spout Id** ou o **Id de raio** para o componente de que tem um erro listado. Na página de detalhes que é apresentada, informações de erro adicionais serão ser listadas na secção de **erros** na parte inferior da página.

4. Para obter mais informações, selecione uma **porta** a partir da secção **testamenteiros** da página para ver o registo de trabalho tempestade para os últimos alguns minutos.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como desenvolver e implemente topologias tempestade HDInsight das ferramentas de para Visual Studio, saiba como [eventos do processo a partir do Azure evento Hub com tempestade no HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Para obter um exemplo de uma topologia de c# que divide os dados da cadeia em vários fluxos, consulte o artigo [c# tempestade exemplo](https://github.com/Blackmist/csharp-storm-example).

Para obter mais informações sobre a criação de c# topologias de detetar, visite [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para mais formas de trabalhar com HDInsight e mais tempestade em amostras HDinsight, consulte o seguinte:

**Microsoft SCP.NET**

* [Guia de programação SCP](hdinsight-storm-scp-programming-guide.md)

**Apache tempestade no HDInsight**

- [Implementar e monitorizar topologias com Apache tempestade no HDInsight](hdinsight-storm-deploy-monitor-topology.md)

- [Topologias de exemplo para tempestade no HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop no HDInsight**

- [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

- [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

- [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

- [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started.md)
