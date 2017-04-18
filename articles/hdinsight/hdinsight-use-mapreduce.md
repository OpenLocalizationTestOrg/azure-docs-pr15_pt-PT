<properties
   pageTitle="MapReduce com Hadoop no HDInsight | Microsoft Azure"
   description="Saiba como executar tarefas de MapReduce no Hadoop em HDInsight clusters. Vai executar uma operação de contagem de base do word implementada como uma tarefa Java MapReduce."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Utilizar MapReduce no Hadoop no HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Este artigo vai aprender a executar tarefas de MapReduce no Hadoop em HDInsight clusters. Vamos executar uma operação de contagem de base do word implementada como uma tarefa Java MapReduce.

##<a id="whatis"></a>O que é MapReduce?

Hadoop MapReduce é uma estrutura de software para escrever publicações de tarefas que processam grandes quantidades de dados. Dados de entrada são divididos em blocos independentes, que são processados, em seguida, em paralelo em todos os nós no seu cluster. Uma tarefa MapReduce composto por duas funções:

* **Mapeamento de pontos**: consome dados de entrada, analisa-la (normalmente com filtro e operações de ordenação) e emite cadeia de identificação (pares valor de chave)
* **Redutor**: consome cadeia de identificação emitida pelo mapeamento de pontos e executa uma operação de resumo que cria um resultado mais pequeno e combinado a partir dos dados de mapeamento de pontos

Um exemplo de trabalho do word básicas contar MapReduce é ilustrado no diagrama seguinte:

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

O resultado desta tarefa é uma contagem do número de vezes cada palavra ocorreu no texto que foi analisado.

* O mapeamento de pontos entra em cada linha a partir do texto como uma entrada de entrada e divide-lo em palavras. -Emite um valor/chave par de cada vez que ocorre uma palavra da palavra é seguido de um 1. O resultado é ordenado antes de o enviar para redutor.

* O redutor soma destes contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguida a soma dos seus ocorrências.

Pode ser implementada MapReduce uma variedade de idiomas. Java é a aplicação mais comuns e é utilizado para fins de demonstração neste documento.

### <a name="hadoop-streaming"></a>Hadoop transmissão

Idiomas ou quadros são baseados no Java e o Java Virtual Machine (por exemplo, Scalding ou em cascata,) pode ser executado diretamente como uma tarefa de MapReduce, semelhante a uma aplicação de Java. As outras pessoas, como c# ou Python ou executáveis autónomo, tem de utilizar Hadoop transmissão.

Transmissão de Hadoop comunica com o mapeamento de pontos e redutor através da STDIN e STDOUT - o mapeamento de pontos redutor ler os dados de uma linha de cada vez a partir do STDIN e escrever o resultado para STDOUT. Cada linha ler ou emitida pela mapeamento de pontos de redutor deve estar no formato de um par de valor/chave, delimitado pela charaacter separador:

    [key]/t[value]

Para mais informações, consulte o artigo [Hadoop transmissão](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obter exemplos da utilização Hadoop transmissão com HDInsight, consulte o seguinte:

* [Desenvolver Python MapReduce tarefas](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>Sobre os dados de exemplo

Neste exemplo, para os dados de exemplo, irá utilizar os blocos de notas Leonardo Da Vinci, que são fornecidos como um documento de texto no seu cluster HDInsight.

Os dados de exemplo estão armazenados armazenamento de Blobs do Azure HDInsight utiliza como o sistema de ficheiro predefinido para Hadoop clusters. HDInsight pode aceder a ficheiros armazenados no armazenamento Blob utilizando o prefixo **wasb** . Por exemplo, para aceder ao ficheiro de sample.log, teria de utilizar a seguinte sintaxe:

    wasbs:///example/data/gutenberg/davinci.txt

Porque o armazenamento de Blobs do Azure for o armazenamento de predefinido de HDInsight, também pode aceder o ficheiro utilizando **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] A sintaxe anterior **wasbs: / / /** é utilizada para aceder aos ficheiros que estão armazenados no contentor de armazenamento predefinido para o seu cluster HDInsight. Se tiver especificado contas de armazenamento adicional quando aprovisionado o seu cluster e que pretende aceder a ficheiros armazenados nestas contas, pode aceder aos dados ao especificar o endereço de conta de nome e armazenamento contentor. Por exemplo, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>Sobre o exemplo MapReduce

A tarefa de MapReduce utilizados neste exemplo está localizada em **wasbs://example/jars/hadoop-mapreduce-examples.jar**e é fornecido com o seu cluster HDInsight. Este contém um exemplo de contagem do word que será executada contra **davinci.txt**.

> [AZURE.NOTE] Em clusters HDInsight 2.1, a localização do ficheiro é **wasbs:///example/jars/hadoop-examples.jar**.

Para sua referência, é o seguinte código Java para a tarefa de MapReduce de contagem de word:

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

      public static class TokenizerMapper
           extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
          StringTokenizer itr = new StringTokenizer(value.toString());
          while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
          }
        }
      }

      public static class IntSumReducer
           extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
                           ) throws IOException, InterruptedException {
          int sum = 0;
          for (IntWritable val : values) {
            sum += val.get();
          }
          result.set(sum);
          context.write(key, result);
        }
      }

      public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
          System.err.println("Usage: wordcount <in> <out>");
          System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
      }
    }

Para obter instruções escrever a sua tarefa MapReduce, consulte o artigo [desenvolver MapReduce Java programas para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Executar o MapReduce

HDInsight pode executar tarefas HiveQL utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual o método é ideal para si, em seguida, siga a hiperligação para obter instruções sobre.

| **Utilize esta opção**...                                                    | **… .to faça o seguinte**                                       | … .with este **sistema operativo de cluster** | … .from este **sistema operativo do cliente** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | Utilize o comando Hadoop através de **SSH**                  | Linux                                     | Windows, Mac OS X, Unix ou Linux        |
| [Laço](hdinsight-hadoop-use-mapreduce-curl.md)                     | Submeter a tarefa de remotamente utilizando o **resto**               | Linux ou o Windows                          | Windows, Mac OS X, Unix ou Linux        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Submeter a tarefa de remotamente ao utilizar o **Windows PowerShell** | Linux ou o Windows                          | Windows                                  |
| [Ambiente de trabalho remoto](hdinsight-hadoop-use-mapreduce-remote-desktop)    | Utilize o comando Hadoop através de **Ambiente de trabalho remoto**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>Próximos passos

Apesar de MapReduce fornece capacidades de diagnóstico eficientes, pode ser um pouco um desafio ao modelo global. Existem várias quadros com base em Java que tornam mais fácil de definir MapReduce aplicações, bem como tecnologias, tais como porco e ramo, que disponibiliza uma maneira mais fácil de trabalhar com dados no HDInsight. Para saber mais, consulte os artigos seguintes:

* [Desenvolver Java MapReduce programas para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Desenvolver Python transmissão MapReduce programas para HDInsight](hdinsight-hadoop-streaming-python.md)

* [Desenvolver escaldaduras MapReduce tarefas com Apache Hadoop no HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Utilizar ramo com HDInsight][hdinsight-use-hive]

* [Utilizar porco com HDInsight][hdinsight-use-pig]

* [Executar as amostras HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
