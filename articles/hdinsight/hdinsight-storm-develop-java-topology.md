<properties
   pageTitle="Desenvolver com base em Java topologias para Apache tempestade | Microsoft Azure"
   description="Saiba como criar topologias tempestade no Java através da criação de uma topologia de contagem simples do word."
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

#<a name="develop-java-based-topologies-for-a-basic-word-count-application-with-apache-storm-and-maven-on-hdinsight"></a>Desenvolver com base em Java topologias para uma aplicação de base de contagem de palavras com Apache tempestade e Maven no HDInsight

Saiba como criar uma topologia de Java para Apache tempestade no HDInsight utilizando Maven. Irá guiá-lo durante o processo de criação de uma aplicação de contagem de palavras básica utilizando Maven e Java, onde a topologia é definida em Java. Em seguida, vai aprender definir a topologia utilizando a arquitetura de fluxo, somando.

> [AZURE.NOTE] A arquitetura de fluxo, somando está disponível no tempestade 0.10.0 ou posterior. Tempestade 0.10.0 está disponível com HDInsight 3.3 e 3.4.

Depois de concluir os passos neste documento, terá uma topologia básica que pode implementar Apache tempestade no HDInsight.

> [AZURE.NOTE] Uma versão completa das topologias criada neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

##<a name="prerequisites"></a>Pré-requisitos

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Kit de programador Java (JDK) versão 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven é um sistema de compilação de projeto para projetos Java.

* Editor de texto como o bloco de notas, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Texto Sublime</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Ou pode utilizar um ambiente de desenvolvimento integrado (IDE) como <a href="https://eclipse.org/" target="_blank">Eclipse</a> (versão Luna ou posterior).

    > [AZURE.NOTE] O editor de ou IDE poderá ter funcionalidades específicas para trabalhar com Maven que não é resolvida neste documento. Para obter informações sobre as capacidades do seu ambiente de edição, consulte a documentação para o produto que está a utilizar.

##<a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando instala Java e o JDK. No entanto, deve verificar que existem e que contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - devem apontar para o diretório onde está instalado o ambiente do runtime Java (JRE). Por exemplo, de uma distribuição Unix ou Linux, deve ter um valor semelhante ao `/usr/lib/jvm/java-7-oracle`. No Windows, teria um valor semelhante a`c:\Program Files (x86)\Java\jre1.7`

* **Caminho** - devem conter caminhos que se seguem:

    * **JAVA_HOME** (ou o caminho equivalente)

    * **JAVA_HOME\bin** (ou o caminho equivalente)

    * Diretório onde está instalado o Maven

##<a name="create-a-new-maven-project"></a>Criar um novo projeto de Maven

A partir da linha de comandos, utilize o seguinte código para criar um novo projeto de Maven denominado **WordCount**:

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Isto irá criar um novo directório denominado **WordCount** na localização atual, que contém um projecto Maven básica.

Diretório **WordCount** irá conter os seguintes itens:

* **pom.XML**: contém definições para o projeto Maven.

* **src\main\java\com\microsoft\example**: contém o código da aplicação.

* **src\test\java\com\microsoft\example**: contém testes para a sua aplicação. Neste exemplo, vamos vão não criar testes.

###<a name="remove-the-example-code"></a>Remover o código de exemplo

Uma vez que recomendamos vão criar nossa aplicação, elimine o teste gerado e os ficheiros da aplicação:

*  **src\test\java\com\microsoft\example\AppTest.Java**

*  **src\main\java\com\microsoft\example\App.Java**

##<a name="add-properties"></a>Adicionar propriedades

Maven permite-lhe definir valores ao nível do projeto denominados propriedades. Adicionar o seguinte depois do `<url>http://maven.apache.org</url>` linha:

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--
        Storm 0.10.0 is for HDInsight 3.3 and 3.4.
        To find the version information for earlier HDInsight cluster
        versions, see https://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
        -->
        <storm.version>0.10.0</storm.version>
    </properties>

Pode agora Utilizamos estes valores em outras secções. Por exemplo, quando especificar a versão dos componentes de tempestade, podemos utilizar `${storm.version}` em vez de disco rígido codificação de um valor.

##<a name="add-dependencies"></a>Adicionar dependências

Dado que se trata de uma topologia de tempestade, tem de adicionar uma dependência de componentes de tempestade. Abra o ficheiro **pom.xml** e adicione o seguinte código na ** &lt;dependências >** secção:

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-core</artifactId>
      <version>${storm.version}</version>
      <!-- keep storm out of the jar-with-dependencies -->
      <scope>provided</scope>
    </dependency>

No momento da compilação, Maven utiliza estas informações para procurar as **principais tempestade** no repositório de Maven. Procura primeiro no repositório no seu computador local. Se os ficheiros não existir, irá transferi-los a partir do repositório de Maven público e armazená-los no repositório de local.

> [AZURE.NOTE] Aviso de `<scope>provided</scope>` linha na secção adicionámos. Isto indica que avançou para excluir **tempestade core** quaisquer ficheiros para caixa que criamos, porque serão fornecido pelo sistema. Isto permite que os pacotes de criar para ser um pouco mais pequeno e assegura que utilizará os bits **tempestade core** incluídos nos tempestade num cluster de HDInsight.

##<a name="build-configuration"></a>Construir a configuração

Plug-ins do maven permitem-lhe personalizar as fases de compilação do projeto, tal como como o projeto é compilado ou como compactá-lo para um ficheiro para caixa. Abra o ficheiro **pom.xml** e adicione o seguinte código diretamente acima a `</project>` linha.

    <build>
      <plugins>
      </plugins>
      <resources>
      </resources>
    </build>

Esta secção será utilizada para adicionar os plug-ins, recursos e outras opções de configuração da compilação. Para obter uma referência completa do ficheiro __pom.xml__ , consulte o artigo [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

###<a name="add-plug-ins"></a>Adicionar plug-ins

Para topologias tempestade, o <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Plug-in Maven de execução e</a> é útil porque permite-lhe executar facilmente a topologia localmente no seu ambiente de desenvolvimento. Adicionar o seguinte procedimento para o `<plugins>` secção do ficheiro **pom.xml** para incluir o plug-in Maven execução:

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.4.0</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

> [AZURE.NOTE] Tenha em atenção que a `<mainClass>` entrada utilizações `${storm.topology}`. Vamos não definir este anterior na secção Propriedades (mas poderia temos.) Em vez disso, podemos irá defina este valor da linha de comandos quando a topologia a ser executado no seu ambiente de desenvolvimento num passo posterior.

Outro útil Plug-in é o <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Apache Maven compilador Plug-in</a>, que é utilizado para alterar as opções de compilação. O motivo principal que precisamos isto é alterar a versão de Java Maven utiliza para a origem e destino para a sua aplicação. Pretendemos versão 1.7.

Adicionar o seguinte procedimento na `<plugins>` secção do ficheiro **pom.xml** para incluir o plug-in Apache Maven compilador e definir as versões de origem e destino como 1.7.

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

###<a name="configure-resources"></a>Configurar recursos

Na secção recursos permite-lhe incluir recursos de código que não sejam, tais como ficheiros de configuração necessários pelos componentes na topologia. Neste exemplo, adicionar o seguinte procedimento na `<resources>` secção do ficheiro **pom.xml** .

    <resource>
        <directory>${basedir}/resources</directory>
        <filtering>false</filtering>
        <includes>
          <include>log4j2.xml</include>
        </includes>
    </resource>

Esta ação adiciona o diretório de recursos na raiz do projeto (`${basedir}`) como uma localização que contém recursos e inclui o ficheiro denominado __log4j2.xml__. Este ficheiro é utilizado para configurar a que informações são registadas da topologia.

##<a name="create-the-topology"></a>Criar a topologia

Uma topologia de tempestade com base em Java é constituído por três componentes que tem de criar (ou referência) como uma dependência.

* **Spouts**: lê dados externos a partir de fontes e emite sequências de dados para a topologia.

* **Bolts**: efetua processamento com base em fluxos de emitida pelo spouts ou outros parafusos e emite sequências de uma ou mais.

* **Topologia**: define como a spouts e parafusos estiverem dispostas e fornece o ponto de entrada para a topologia.

###<a name="create-the-spout"></a>Criar a vareta

Para reduzir os requisitos de configuração de origens de dados externas, a seguinte vareta emite simplesmente frases aleatórios. É uma versão modificada de uma vareta que é fornecida com os [exemplos de tempestade Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [AZURE.NOTE] Para um exemplo de um vareta que lê a partir de uma origem de dados externos, consulte um dos exemplos seguintes:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): uma vareta de exemplo que lê do Twitter
>
> * [Tempestade Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): uma vareta que lê do Kafka

Para vareta, criar um novo ficheiro chamado **RandomSentenceSpout.java** no diretório **src\main\java\com\microsoft\example** e utilizar o seguinte como o conteúdo:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Observar a leitura através dos comentários de código para compreender como funciona esta vareta.

> [AZURE.NOTE] Apesar desta topologia utiliza apenas uma vareta, as outras pessoas poderão ter várias que feed de dados de origens diferentes para a topologia.

###<a name="create-the-bolts"></a>Criar o sobre

Sobre a alça da transformação de dados. Para este topologia, temos duas parafusos:

* **SplitSentence**: divide frases emitidos por **RandomSentenceSpout** em palavras individuais.

* **WordCount**: conta o número de vezes ocorreu cada palavra.

> [AZURE.NOTE] Parafusos podem fazer literalmente alguma coisa, por exemplo, cálculo, persistente ou falar para componentes externos.

Crie duas novos ficheiros, **SplitSentence.java** e **WordCount.Java** no diretório **src\main\java\com\microsoft\example** . Utilize o seguinte como o conteúdo para os ficheiros:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;
    import java.util.Iterator;

    import backtype.storm.Constants;
    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;
    import backtype.storm.Config;

    // For logging
    import org.apache.logging.log4j.Logger;
    import org.apache.logging.log4j.LogManager;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
        //Create logger for this class
        private static final Logger logger = LogManager.getLogger(WordCount.class);
        //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();
        //How often we emit a count of words
        private Integer emitFrequency;

        // Default constructor
        public WordCount() {
            emitFrequency=5; // Default to 60 seconds
        }

        // Constructor that sets emit frequency
        public WordCount(Integer frequency) {
            emitFrequency=frequency;
        }

        //Configure frequency of tick tuples for this bolt
        //This delivers a 'tick' tuple on a specific interval,
        //which is used to trigger certain actions
        @Override
        public Map<String, Object> getComponentConfiguration() {
            Config conf = new Config();
            conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
            return conf;
        }

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
            //If it's a tick tuple, emit all words and counts
            if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
                    && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
                for(String word : counts.keySet()) {
                    Integer count = counts.get(word);
                    collector.emit(new Values(word, count));
                    logger.info("Emitting a count of " + count + " for word " + word);
                }
            } else {
                //Get the word contents from the tuple
                String word = tuple.getString(0);
                //Have we counted any already?
                Integer count = counts.get(word);
                if (count == null)
                    count = 0;
                //Increment the count and store it
                count++;
                counts.put(word, count);
            }
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declare(new Fields("word", "count"));
        }
    }

Observar a leitura através dos comentários de código para compreender como funciona a cada raio.

###<a name="define-the-topology"></a>Definir a topologia

A topologia une as spouts e bolts em conjunto para um gráfico, que define como os fluxos de dados entre os componentes. Também fornece sugestões de paralelismo tempestade utiliza quando cria um ocorrências de componentes dentro do cluster.

Segue-se um diagrama básico do gráfico de componentes para esta topologia.

![diagrama que mostra a disposição spouts e parafusos](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Para implementar a topologia, crie um novo ficheiro chamado **WordCountTopology.java** no diretório **src\main\java\com\microsoft\example** . Utilize o seguinte como o conteúdo para o ficheiro:

    package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        //Set to false to disable debug information
        // when running in production mode.
        conf.setDebug(false);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Observar a leitura através dos comentários de código para compreender como a topologia de é definida e, em seguida, submetida para cluster.

###<a name="configure-logging"></a>Configurar o registo

Tempestade utiliza Apache Log4j para registar informações. Se não a configurar o registo, a topologia irá emitir muitas informações de diagnóstico, que podem ser difícil de ler. Para controlar o que tem sessão iniciado, crie um ficheiro com o nome __log4j2.xml__ no diretório de __recursos__ . Utilize o seguinte como o conteúdo do ficheiro.

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
    </Configuration>

Este procedimento configura um novo registo para a classe de __com.microsoft.example__ , que inclui os componentes nesta topologia de exemplo. O nível de está definido para rastreio para este registo, que irá capturar quaisquer informações de registo emitidas pelos componentes nesta topologia. Se voltar aspeto através de código para este projeto, vai notar que apenas o ficheiro WordCount.java implementa registo; registará a contagem de cada palavra.

O `<Root level="error">` secção configura o nível de raiz de início de sessão (tudo o que não na __com.microsoft.example__,) apenas registar informações de erro.

> [AZURE.IMPORTANT] Enquanto Isto diminui significativamente as informações que tem sessão iniciadas quando testar uma topologia no seu ambiente de desenvolvimento, não remove todas as informações de depuração produzidas quando a ser executado num cluster de produção. Para reduzir essas informações, também tem de definir depuração falso na configuração submetida para cluster. Ver o código de WordCountTopology.java neste documento para obter um exemplo. 

Para mais informações sobre como configurar o registo para o Log4j, consulte o artigo [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [AZURE.NOTE] Versão tempestade 0.10.0 utiliza Log4j 2. x. Versões mais antigas do tempestade utilizavam Log4j 1. x, utilizado num formato diferente para a configuração de registo. Para obter informações sobre a configuração do mais antiga, consulte o artigo [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).

##<a name="test-the-topology-locally"></a>Testar a topologia de localmente

Depois de guardar os ficheiros, utilize o seguinte comando para testar a topologia de localmente.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Tal como é executado, a topologia irá apresentar informações sobre o arranque. Em seguida, começa a apresentar linhas semelhantes ao seguinte como frases são emitidas a partir da vareta e processadas pelo sobre.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Verificando início de sessão emitido por raio de WordCount, podemos pode ver que ' and' tenha sido emitida 113 vezes. A contagem de irão continuar subir desde que é executada a topologia de uma vez que o vareta emite continuamente os mesmo frases.

Também haverá um intervalo de segundo 5 entre emissão de palavras e as contagens. Isto ocorre porque o componente de __WordCount__ está configurado para apenas emitir informações quando chega uma cadeia de identificação de escala e os pedidos que essa cadeia de identificação apenas é entregues 5 segundos por predefinição.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia de fluxo, somando

Fluxo de somando é um novo quadro disponível com tempestade 0.10.0, permite-lhe separar configuração a partir da implementação. Os componentes (parafusos e spouts,) ainda são definidos em Java, mas a topologia é definida com um ficheiro YAML.

O ficheiro YAML define os componentes a utilizar para a topologia, como os fluxos de dados entre-los e quais os valores a utilizar quando a inicialização os componentes. Pode incluir um ficheiro YAML como parte do ficheiro para caixa que contém o seu projeto quando implementá-los ou pode utilizar um ficheiro YAML externo quando iniciar a topologia.

1. Mova o ficheiro __WordCountTopology.java__ terminar o projeto. Anteriormente, isto definido a topologia, mas recomendamos não ser utilizá-lo para o fluxo.

2. No directório de __recursos__ , crie um novo ficheiro chamado __topology.yaml__. Utilize o seguinte como o conteúdo deste ficheiro.

        # topology definition

        # name to be used when submitting. This is what shows up...
        # in the Storm UI/storm command-line tool as the topology name
        # when submitted to Storm
        name: "wordcount"

        # Topology configuration
        config:
        # Hint for the number of workers to create
        topology.workers: 1

        # Spout definitions
        spouts:
        - id: "sentence-spout"
            className: "com.microsoft.example.RandomSentenceSpout"
            # parallelism hint
            parallelism: 1

        # Bolt definitions
        bolts:
        - id: "splitter-bolt"
            className: "com.microsoft.example.SplitSentence"
            parallelism: 1

        - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 10
            parallelism: 1

        # Stream definitions
        streams:
        - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
            # The stream emitter
            from: "sentence-spout"
            # The stream consumer
            to: "splitter-bolt"
            # Grouping type
            grouping:
            type: SHUFFLE

        - name: "Splitter -> Counter"
            from: "splitter-bolt"
            to: "counter-bolt"
            grouping:
            type: FIELDS
            # field(s) to group on
            args: ["word"]

    Observar para ler e compreender o que faz cada secção e como se relaciona com a definição Java baseada no ficheiro __WordCountTopology.java__ .

3. Efetue as seguintes alterações ao ficheiro __pom.xml__ .

    * Adicionar a nova dependência seguinte na `<dependencies>` secção:

            <!-- Add a dependency on the Flux framework -->
            <dependency>
                <groupId>org.apache.storm</groupId>
                <artifactId>flux-core</artifactId>
                <version>${storm.version}</version>
            </dependency>

    * Adicionar o plug-in seguinte para a `<plugins>` secção. Este plug-in processa a criação de uma embalagem (ficheiro para caixa) para o projeto e aplica-se algumas transformações específicas ao fluxo, somando quando criar o pacote.

            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                        <!-- Keep us from getting a "can't overwrite file error" -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                        <!-- We're using Flux, so refer to it as main -->
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>org.apache.storm.flux.Flux</mainClass>
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

    * Na __execução-maven-Plug-in__ `<configuration>` secção, alterar o valor para `<mainClass>` para `org.apache.storm.flux.Flux`. Esta opção permite-fluxo, somando lidar com a topologia de quando podemos executá-la localmente no desenvolvimento.

    * No `<resources>` secção, adicione o seguinte para o `<includes>`. Isto inclui o ficheiro YAML que define a topologia como parte do projeto.
    
            <include>topology.yaml</include>

## <a name="test-the-flux-topology-locally"></a>Testar a topologia de fluxo, somando localmente

1. Utilize o seguinte procedimento para compilar e executar a topologia de fluxo, somando utilizando Maven.

        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    
    Se estiver a utilizar o PowerShell, utilize o seguinte:
    
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"

    Se estiver a um sistema de Unix/Linux/OS X e tiver [instalado tempestade no seu ambiente de desenvolvimento](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), pode utilizar em vez disso, os seguintes comandos:

        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml

    O `--local` parâmetro executa a topologia no modo de local no seu ambiente de desenvolvimento. O `-R /topology.yaml` parâmetro utiliza o `topology.yaml` recurso de ficheiro do ficheiro para caixa para definir a topologia.

    Tal como é executado, a topologia irá apresentar informações sobre o arranque. Em seguida, começa a apresentar linhas semelhantes ao seguinte como frases são emitidas a partir da vareta e processadas pelo sobre.

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    
    Haverá um atraso de segundo 10 entre secções de informações com sessão iniciada, como o `topology.yaml` ficheiro transmite um valor de `10` quando é criado o componente de WordCount. Este parâmetro define o intervalo de atraso para a cadeia de identificação de escala 10 segundos.

2.  Fazer uma cópia de `topology.yaml` ficheiro do projeto. Chamar algo parecido com `newtopology.yaml`. No ficheiro, localize a seguinte secção e altere o valor do `10` para `5`. Isto altera o intervalo entre lotes emissão contagens de palavras de 10 segundos a 5.

          - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 5
            parallelism: 1

3. Para executar a topologia, utilize o seguinte comando:

        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"

    Em alternativa, se tiver tempestade no seu ambiente de desenvolvimento Unix/Linux/OS X:

        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml

    Alterar o `/path/to/newtopology.yaml` o caminho para o ficheiro de newtopology.yaml que criou no passo anterior. Este comando irá utilizar o newtopology.yaml como a definição da topologia. Uma vez que recomendamos não incluiu a `compile` parâmetro, Maven irá reutilizar a versão do projeto compilado nos passos anteriores.

    Assim que a topologia é iniciado, deverá Repare que o tempo entre secções emitidas foi alterado para refletir o valor na newtopology.yaml. Para que possa ver que pode alterar a configuração através de um ficheiro YAML sem ter de recompilar a topologia.

Existem várias outras funcionalidades que fluxo, somando fornece que não são abordados aqui, tais como a substituição de variáveis no ficheiro YAML com base nos parâmetros transmitidos em tempo de execução ou de variáveis de ambiente. Para mais informações sobre estas e outras funcionalidades da arquitetura de fluxo, somando, consulte o artigo [fluxo, somando (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

##<a name="trident"></a>Trident

Trident é uma abstracção de alto nível fornecido pelo tempestade. Suporta processamento com estado. A vantagem principal de Trident é que pode garantir que todas as mensagens que introduz a topologia são processada apenas uma vez. Este é difícil alcançar numa topologia Java observou, será processada pelo menos uma vez que garantia que as mensagens. Também existem diferenças, tal como componentes incorporados que podem ser utilizadas em vez de criar parafusos. Na verdade, parafusos completamente são substituídos pelos componentes do menor genérico, tal como filtros, projecções e funções.

Aplicações Trident podem ser criadas ao utilizar projetos Maven. Utilizar os mesmos passos básicos, tal como apresentados neste artigo — apenas o código for diferente. (Trident também não é possível a) ser utilizada com a arquitetura de fluxo, somando.

Para mais informações sobre Trident, consulte o artigo <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Descrição geral de API Trident</a>.

Para obter um exemplo de uma aplicação de Trident, consulte o artigo [Twitter tópicos tendência com Apache tempestade no HDInsight](hdinsight-storm-twitter-trending.md).

##<a name="next-steps"></a>Próximos passos

Aprendeu como criar uma topologia de tempestade utilizando Java. Agora Saiba como:

* [Implementar e gerir topologias Apache tempestade no HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Desenvolver c# topologias para Apache tempestade no HDInsight utilizando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Pode encontrar mais exemplo topologias tempestade visitando [topologias de exemplo para tempestade no HDInsight](hdinsight-storm-example-topology.md).
