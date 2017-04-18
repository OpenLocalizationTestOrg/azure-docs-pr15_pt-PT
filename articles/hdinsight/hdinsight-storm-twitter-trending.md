<properties
   pageTitle="Twitter tópicos tendência com Apache tempestade no HDInsight | Microsoft Azure"
   description="Saiba como utilizar Trident para criar uma topologia de Apache tempestade que determina tópicos tendência no Twitter com base em hashtags."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Determinar Twitter tópicos tendência com Apache tempestade no HDInsight

Saiba como utilizar Trident para criar uma topologia de tempestade que determina tópicos tendência (etiquetas de hash), no Twitter.

Trident é uma abstracção de alto nível que fornece ferramentas como associações, agregações, agrupamento, funções e filtros. Para além disso, Trident adiciona primitivas para efetuar processamento com estado, utilizarão. Este exemplo demonstra como pode criar uma topologia de com uma vareta personalizada, função e várias funções incorporadas fornecidas pela Trident.

> [AZURE.NOTE] Este exemplo é fortemente baseado o exemplo [Trident tempestade](https://github.com/jalonsoramos/trident-storm) por dia Alonso.

##<a name="requirements"></a>Requisitos de

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java e o JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Uma conta de programador do Twitter

##<a name="download-the-project"></a>Transferir o projecto

Utilize o seguinte código para clonar localmente o projeto.

    git clone https://github.com/Blackmist/TwitterTrending

##<a name="topology"></a>Topologia

A topologia para este exemplo é da seguinte forma:

![topologia](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Esta é uma vista simplificada da topologia. Várias instâncias dos componentes serão distribuídas entre os nós no cluster.

O código de Trident que implementa a topologia é da seguinte forma:

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Este código faz o seguinte:

1. Cria uma nova sequência a partir de vareta. A vareta obtém tweets Twitter e os filtros de-los para palavras-chave específicas (amor, música e café neste exemplo).

2. HashtagExtractor, uma função personalizada, é utilizado para extrair etiquetas hash de cada tweet. Estas são emitidas na sequência.

3. A sequência é agrupada por etiqueta hash e transmitida para uma agregação. Este agregação cria uma contagem do número de vezes cada etiqueta hash ocorreu. Estes dados são mantidos na memória. Por fim, uma nova sequência é emitida que contém a etiqueta hash e a contagem.

4. Uma vez que recomendamos apenas estiver interessados em etiquetas para um determinado conjunto de tweets hash mais populares, a assemblagem **FirstN** é aplicada para devolver apenas os primeiros 10 valores, baseados no campo de contagem.

> [AZURE.NOTE] Diferente de vareta e HashtagExtractor, estamos a utilizar a funcionalidade Trident incorporada.
>
> Para obter informações sobre as operações incorporadas, consulte o artigo <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin do pacote</a>.
>
> Para implementações Trident Estados que não seja MemoryMapState, consulte o seguinte:
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Pesquisa flexível do tempestade Trident</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">Trident redis</a>

###<a name="the-spout"></a>A vareta

Vareta, **TwitterSpout**, utiliza <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> para obtê tweets do Twitter. É criado um filtro (amor, música e café neste exemplo), e tweets recebidas (estado) que correspondam ao filtro são armazenadas numa fila de bloqueio ligada. (Para obter mais informações, consulte <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue de classe</a>). Por fim, os itens são importados desativar fila de espera e emitidos para a topologia.

###<a name="the-hashtagextractor"></a>O HashtagExtractor

Para extrair etiquetas hash, <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> é utilizada para obter todas as etiquetas de hash contidos a tweet. Em seguida, estes são emitidas na sequência.

##<a name="enable-twitter"></a>Ativar Twitter

Utilize os passos seguintes para registar uma nova aplicação Twitter e obter as informações de tokens consumidor e acesso necessárias para ler a partir do Twitter:

1. Aceda a <a href="https://apps.twitter.com" target="_blank">Aplicações Twitter</a> e clique no botão **Criar nova aplicação** . Quando preencher o formulário, deixe o campo de **URL de chamada de retorno** vazio.

2. Quando a aplicação é criada, clique no separador **teclas e Tokens de acesso** .

3. Copie as informações da **Chave do consumidor** e **Consumidor secreta** .

4. Na parte inferior da página, selecione **Criar meu token de acesso** que existam sem tokens. Quando os tokens de tem sido criados, copie as informações do **Token de acesso** e o **Segredo Token de acesso** .

5. No projeto **TwitterSpoutTopology** que anteriormente clonar, abra o ficheiro **resources/twitter4j.properties** , adicione as informações reuniu nos passos anteriores e, em seguida, guarde o ficheiro.

##<a name="build-the-topology"></a>Construir a topologia

Utilize o seguinte código para criar o projeto:

        cd [directoryname]
        mvn compile

##<a name="test-the-topology"></a>Testar a topologia

Utilize o seguinte comando para testar a topologia de localmente:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Depois de inicia a topologia, deverá ver informações de depuração que contém o hash etiquetas e conta emitidas da topologia. O resultado deverá ser semelhante ao seguinte:

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##<a name="next-steps"></a>Próximos passos

Agora que tiverem testado a topologia de localmente, Descubra como implementar a topologia: [Implementar e gerir topologias Apache tempestade no HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Também poderá estar interessado os seguintes tópicos de tempestade:

* [Desenvolver topologias Java para tempestade no HDInsight utilizando Maven](hdinsight-storm-develop-java-topology.md)

* [Desenvolver c# topologias para tempestade no HDInsight utilizando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para obter mais exemplos de tempestade para HDinsight:

* [Topologias de exemplo para tempestade no HDInsight](hdinsight-storm-example-topology.md)
