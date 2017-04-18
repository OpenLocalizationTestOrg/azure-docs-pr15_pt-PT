<properties
   pageTitle="Utilizar componentes de Python uma topologia de tempestade no HDinsight | Microsoft Azure"
   description="Saiba como pode utilizar componentes de Python partir com Apache tempestade no Azure HDInsight. Vai aprender a utilizar Python componentes de ambas as um Java com base e Clojure com base topologia tempestade."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desenvolver topologias Apache tempestade com Python no HDInsight

Apache tempestade suporta vários idiomas, mesmo que lhe permite combinar componentes de vários idiomas numa topologia de. Este documento, vai aprender a utilizar componentes de Python seu topologias Java e com base em Clojure tempestade no HDInsight.

##<a name="prerequisites"></a>Pré-requisitos

* Python 2.7 ou superior

* Java JDK 1.7 ou superior

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>Suporte de múltiplos idioma tempestade

Tempestade foi concebido para funcionar com componentes escritos utilizando qualquer linguagem de programação, no entanto, isto requer que os componentes compreendem como trabalhar com a [definição de outras entidades para tempestade](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para Python, um módulo é fornecido como parte do projeto Apache tempestade que permite-lhe facilmente a interface com tempestade. Pode encontrar este módulo na [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Uma vez que Apache tempestade é um processo de Java que é executada no Java Máquina Virtual (JVM), são executados componentes escritos noutros idiomas como subprocessos. Os bits tempestade em execução na JVM comunica com estes subprocessos utilizando JSON mensagens enviadas através de stdin/stdout. Obter mais detalhes sobre a comunicação entre os componentes podem encontrar na documentação [Multi lang protocolo](https://storm.apache.org/documentation/Multilang-protocol.html) .

###<a name="the-storm-module"></a>O módulo tempestade

O módulo tempestade (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), fornece os bits necessários para criar componentes Python que funcionam com o tempestade.

Este procedimento fornece coisas como `storm.emit` a emitir cadeia de identificação, e `storm.logInfo` para escrever os registos. Seria encorajar para ler sobre este ficheiro e compreender o que fornece.

##<a name="challenges"></a>Desafios

Utilizar o módulo de __storm.py__ , pode criar spouts Python que consumam dados e parafusos processam dados, no entanto, a definição de topologia tempestade geral que fios o comunicação entre os componentes ainda é escrita utilizando Java ou Clojure. Para além disso, se utilizar Java, também terá de criar componentes Java que funcionam como uma interface para os componentes de Python.

Além disso, uma vez que clusters tempestade executar de forma distribuída, tem de garantir que quaisquer módulos uma ferramenta necessária ao seu componentes Python estão disponíveis em todos os nós de trabalho no cluster. Tempestade não fornece qualquer forma fácil para realizar esta tarefa para recursos de multi lang - significa que tem de incluir todas as dependências como parte do ficheiro para caixa para a topologia ou instalar manualmente o dependências em cada nó de trabalho no cluster.

###<a name="java-vs-clojure-topology-definition"></a>Definição de topologia Java vs. Clojure

Dos dois métodos de definir uma topologia, Clojure é de longe o mais fácil/mais limpo possível diretamente componentes de python referência na definição da topologia. Para obter definições de topologia com base em Java, também tem de definir componentes de Java que processam coisas como declarar os campos a cadeia de identificação devolvido pelos componentes de Python.

Ambos os métodos são descritos neste documento, juntamente com projectos de exemplo.

##<a name="python-components-with-a-java-topology"></a>Componentes de Python com uma topologia de Java

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) no diretório __JavaTopology__ . Este é um projeto Maven com base. Se não estiver familiarizado com Maven, consulte o artigo [com base em desenvolver Java topologias com Apache tempestade no HDInsight](hdinsight-storm-develop-java-topology.md) para obter mais informações sobre como criar um projeto Maven para uma topologia de tempestade.

Uma topologia com base em Java que utiliza Python (ou outros componentes de idioma JVM,) é apresentada inicialmente utilizar componentes de Java; mas se procurar em cada uma do Java spouts/sobre, verá o código semelhante ao seguinte:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Este é onde Java invocar Python e executa o script que contém a lógica raio real. O Java spouts/sobre (neste exemplo), basta declarar os campos na cadeia de identificação que será emitida pelo componente Python subjacente.

Os ficheiros de Python reais são armazenados na `/multilang/resources` directório neste exemplo. O `/multilang` diretório é referenciado na __pom.xml__:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

Isto inclui todos os ficheiros de `/multilang` pasta para a caixa que vai ser criada deste projeto.

> [AZURE.IMPORTANT] Tenha em atenção que este apenas Especifica a `/multilang` diretório e não `/multilang/resources`. Tempestade espera recursos de que não sejam JVM num `resources` diretório, para que a mesma é procurava internamente já. Colocar componentes nesta pasta permite-lhe a referência apenas pelo nome no código de Java. Por exemplo, `super("python", "countbolt.py");`. Outra forma de considerá-la é que tempestade vê o `resources` directório como a raiz (/) ao aceder ao recursos multi lang.
>
> Para este projeto de exemplo, o `storm.py` módulo é incluído na `/multilang/resources` diretório.

###<a name="build-and-run-the-project"></a>Criar e executar o projecto

Para executar este projeto localmente, utilize apenas o seguinte comando Maven para criar e executar no modo de local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Utilize ctrl + c para eliminar o processo.

Para implementar o projeto para um cluster de HDInsight com o Apache tempestade, utilize os passos seguintes:

1. Crie uma para uber caixa:

        mvn package

    Esta opção criará num ficheiro denominado __WordCount – 1.0 SNAPSHOT.jar__ na `/target` directório para este projeto.

2. Carregue o ficheiro para caixa ao cluster Hadoop utilizando um dos seguintes métodos:

    * Para __baseado em Linux__ clusters HDInsight: utilizar `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` para copiar o ficheiro para caixa para cluster, substituir o nome de utilizador com o seu nome de utilizador SSH e o nome de cluster com o nome do cluster HDInsight.

        Assim que o ficheiro de terminar a carregar, ligar ao cluster utilizando SSH e começar a utilizar a topologia`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Para __baseados no Windows__ clusters HDInsight: ligar para o Dashboard tempestade ao aceder a HTTPS://CLUSTERNAME.azurehdinsight.net/ no seu browser. Substitua o nome de cluster com o seu nome de cluster HDInsight e fornecer o nome de administrador e a palavra-passe quando lhe for pedido.

        Utilizar o formulário, execute as seguintes ações:

        * __Ficheiro JAR__: selecione __Procurar__, em seguida, selecione o ficheiro __WordCount-1.0-SNAPSHOT.jar__
        * __Nome de classe__: introduza`com.microsoft.example.WordCount`
        * __Parâmetros adicionais não__: introduza um nome amigável tais como `wordcount` para identificar a topologia

        Por fim, selecione __Submeter__ para iniciar a topologia.

> [AZURE.NOTE] Depois de iniciada, uma topologia de tempestade é executado até que seja parado (sacrificados). Para parar a topologia, utilize o `storm kill TOPOLOGYNAME` comando da linha de comandos (SSH sessão para um cluster de Linux por exemplo,) ou ao utilizar a IU tempestade, selecione a topologia de e, em seguida, selecione o botão __Eliminar__ .

##<a name="python-components-with-a-clojure-topology"></a>Componentes de Python com uma topologia de Clojure

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) no diretório __ClojureTopology__ .

Esta topologia foi criada utilizando [Leiningen](http://leiningen.org) para [criar um novo projeto de Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Após esta ação, as seguintes alterações ao projeto scaffolded foram efetuadas:

* __Project.clj__: adicionados dependências para tempestade e exclusões para itens que podem causar problemas quando implementada no servidor HDInsight.
* __recursos/recursos__: Leiningen cria uma predefinida `resources` directório, no entanto, são apresentados os ficheiros armazenados aqui para obter adicionado à raiz do ficheiro para caixa criada a partir deste projeto, e tempestade espera ficheiros num sub-diretório chamado `resources`. Para que foi adicionado um diretório subpastas e os ficheiros de Python são armazenados no `resources/resources`. Em tempo de execução, este será tratado como a raiz (/) para aceder a componentes Python.
* __src/WordCount/Core.clj__: este ficheiro contém a definição de topologia e é referenciado do ficheiro __project.clj__ . Para mais informações sobre como utilizar Clojure para definir uma topologia de tempestade, consulte o artigo [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###<a name="build-and-run-the-project"></a>Criar e executar o projecto

__Para criar e executar o projeto localmente__, utilize o seguinte comando:

    lein clean, run

Para parar a topologia, utilize __Ctrl + C__.

__Para criar um uberjar e implementar para HDInsight__, utilize os seguintes passos:

1. Crie um uberjar que contém a topologia e dependências necessárias:

        lein uberjar

    Isto irá criar um novo ficheiro chamado `wordcount-1.0-SNAPSHOT.jar` na `target\uberjar+uberjar` diretório.
    
2. Utilize um dos seguintes métodos para implementar e executar a topologia a um cluster de HDInsight:

    * __Baseado em Linux HDInsight__
    
        1. Copie o ficheiro para o utilizando o nó cabeça HDInsight cluster `scp`. Por exemplo:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Substitua o nome de utilizador com um utilizador SSH para o seu cluster e o nome de cluster com o seu nome de cluster HDInsight.
            
        2. Quando o ficheiro foi copiado para o cluster, utilize SSH para ligar ao cluster e submeter a tarefa. Para obter informações sobre como utilizar SSH com HDInsight, consulte um dos seguintes procedimentos:
        
            * [Utilizar SSH com baseado em Linux HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Utilizar SSH com baseado em Linux HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Quando estabelecida, utilize o seguinte para começar a topologia de:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight baseados no Windows__
    
        1. Ligar para o Dashboard tempestade ao aceder a HTTPS://CLUSTERNAME.azurehdinsight.net/ no seu browser. Substitua o nome de cluster com o seu nome de cluster HDInsight e fornecer o nome de administrador e a palavra-passe quando lhe for pedido.

        2. Utilizar o formulário, execute as seguintes ações:

            * __Ficheiro JAR__: selecione __Procurar__, em seguida, selecione o ficheiro __wordcount-1.0-SNAPSHOT.jar__
            * __Nome de classe__: introduza`wordcount.core`
            * __Parâmetros adicionais não__: introduza um nome amigável tais como `wordcount` para identificar a topologia

            Por fim, selecione __Submeter__ para iniciar a topologia.

> [AZURE.NOTE] Depois de iniciada, uma topologia de tempestade é executado até que seja parado (sacrificados). Para parar a topologia, utilize o `storm kill TOPOLOGYNAME` comando da linha de comandos (SSH sessão para um cluster de Linux) ou ao utilizar a IU tempestade, selecione a topologia de e, em seguida, selecione o botão __Eliminar__ .

##<a name="next-steps"></a>Próximos passos

Neste documento, o que aprendeu como utilizar componentes Python a partir de uma topologia de tempestade. Consulte os seguintes documentos para outras formas de utilizar Python com HDInsight:

* [Como utilizar Python para transmissão MapReduce tarefas](hdinsight-hadoop-streaming-python.md)
* [Como utilizar Python utilizador definidos funções (UDF) no porco e ramo](hdinsight-python.md)
