<properties
    pageTitle="Desenvolver Java MapReduce programas para baseado em Linux HDInsight | Microsoft Azure"
    description="Saiba como desenvolver Java MapReduce programas e implementá-los ao baseado em Linux HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Desenvolver Java MapReduce programas para Hadoop no HDInsight Linux

Este documentos orienta utilizar Apache Maven para criar uma aplicação de MapReduce, em seguida, implementar e executá-la num Hadoop baseado em Linux num cluster de HDInsight.

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, tal como OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Uma subscrição do Azure**

- **Clip Azure**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando instala Java e o JDK. No entanto, deve verificar que existem e que contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - devem apontar para o diretório onde está instalado o ambiente do runtime Java (JRE). Por exemplo, num sistema OS X, Unix ou Linux, deve ter um valor semelhante ao `/usr/lib/jvm/java-7-oracle`. No Windows, teria um valor semelhante a`c:\Program Files (x86)\Java\jre1.7`

* **Caminho** - devem conter caminhos que se seguem:

    * **JAVA_HOME** (ou o caminho equivalente)

    * **JAVA_HOME\bin** (ou o caminho equivalente)

    * Diretório onde está instalado o Maven

##<a name="create-a-new-maven-project"></a>Criar um novo projeto de Maven

1. A partir de uma sessão de terminal ou linha de comandos no seu ambiente de desenvolvimento, altere directórios para a localização que pretende armazenar este projeto.

3. Utilize o comando de __mvn__ , que é instalado com Maven, para gerar andaime do projeto.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Isto irá criar um novo directório no diretório atual, com o nome especificado pelo parâmetro __artifactID__ (**wordcountjava** neste exemplo.) Este diretório irá conter os seguintes itens:

    * __pom.xml__ - o [Modelo de objeto do projeto (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contém informações e detalhes de configuração utilizados para construir o projeto.

    * __src__ - diretório que contém o directório __java/principais/organograma/hadoop/apache/exemplos__ , onde irá criar a aplicação.

3. Elimine o ficheiro de __src/test/java/org/apache/hadoop/examples/apptest.java__ , tal como não serão utilizada neste exemplo.

##<a name="add-dependencies"></a>Adicionar dependências

1. Editar o ficheiro __pom.xml__ e adicione o seguinte dentro de `<dependencies>` secção:

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    Isto indica que a Maven que o projeto requer as bibliotecas (listados dentro &lt;artifactId\>) com uma versão específica (listados dentro &lt;versão\>). No momento da compilação, este será transferido do repositório de Maven predefinido. Pode utilizar a [pesquisa do repositório de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver mais.

    O `<scope>provided</scope>` indica Maven que estas dependências não devem ser compactadas com a aplicação, tal como serão fornecidos pelo cluster HDInsight em tempo de execução.

2. Adicione o seguinte para o ficheiro __pom.xml__ . Isto tem de estar dentro de `<project>...</project>` tags num ficheiro; Por exemplo, entre `</dependencies>` e `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
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
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    O plug-in primeiro configura o [Tom de Maven Plug-in](http://maven.apache.org/plugins/maven-shade-plugin/), que é utilizado para criar um uberjar (por vezes denominada um fatjar) que contém dependências necessárias pela aplicação. Também impede que duplicação de licenças no interior do pacote para caixa, que pode causar problemas em alguns sistemas.

    O segundo Plug-in configura o compilador Maven, que é utilizado para definir a versão do Java obrigatório por esta aplicação para a versão utilizada no HDInsight cluster.

3. Guarde o ficheiro __pom.xml__ .

##<a name="create-the-mapreduce-application"></a>Criar a aplicação de MapReduce

1. Aceda ao diretório __wordcountjava/src/principais/java/organograma/apache/hadoop/exemplos__ e mude o nome de ficheiro __App.java__ para __WordCount.java__.

2. Abra o ficheiro de __WordCount.java__ num editor de texto e substituir o conteúdo com o seguinte:

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

    Repare que o nome do pacote é **org.apache.hadoop.examples** e o nome de classe é **WordCount**. Irá utilizar os seguintes nomes quando que submete a tarefa de MapReduce.

3. Guarde o ficheiro.

##<a name="build-the-application"></a>Criar a aplicação

1. Alterar para o directório __wordcountjava__ , se ainda não estiver.

2. Utilize o seguinte comando para criar um ficheiro para caixa que contém a aplicação:

        mvn clean package

    Isto irá limpar qualquer artefactos compilação anterior, transferir as dependências que já não tem sido instaladas e, em seguida, criar e compactar a aplicação.

3. Assim que tiver terminado do comando, o directório de __wordcountjava/destino__ irá conter num ficheiro denominado __wordcountjava-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] O ficheiro __wordcountjava-1.0-SNAPSHOT.jar__ é uma uberjar, que contém não só a WordCount tarefa, mas também dependências que a tarefa requer o tempo de execução.


##<a id="upload"></a>Carregar para a caixa

Utilize o seguinte comando para carregar o ficheiro para caixa para o headnode HDInsight:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Isto copia os ficheiros do sistema local para o nó cabeça.

> [AZURE.NOTE] Se utilizou uma palavra-passe para proteger a sua conta SSH, vai ser-lhe a palavra-passe. Caso tenha utilizado uma chave SSH, poderá ter de utilizar o `-i` parâmetro e o caminho para a chave privada. Por exemplo, `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Executar a tarefa de MapReduce

1. Ligar ao HDInsight usando SSH, tal como descrito nos seguintes artigos:

    - [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. A partir do sessão SSH, utilize o seguinte comando para executar a aplicação MapReduce:

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    Isto irá utilizar a aplicação WordCount MapReduce para contar as palavras no ficheiro davinci.txt e armazenar os resultados para __wasbs: / / / exemplo/dados/wordcountout__. O ficheiro de entrada e de saída são armazenados ao armazenamento de predefinida para o cluster.

3. Assim que a tarefa estiver concluída, utilize o seguinte para ver os resultados:

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    Deverá receber uma lista de palavras e as contagens, com valores semelhantes ao seguinte:

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>Próximos passos

Neste documento, aprendeu como desenvolver uma tarefa de Java MapReduce. Consulte os seguintes documentos para outras formas de trabalhar com HDInsight.

- [Utilizar ramo com HDInsight][hdinsight-use-hive]
- [Utilizar porco com HDInsight][hdinsight-use-pig]
- [Utilizar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações, consulte também o [Centro de programadores do Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

