<properties
 pageTitle="Desenvolver escaldaduras MapReduce tarefas com Maven | Microsoft Azure"
 description="Saiba como utilizar Maven para criar uma tarefa de escaldaduras MapReduce, em seguida, implementar e executar a tarefa de um Hadoop num cluster de HDInsight."
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
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Desenvolver escaldaduras MapReduce tarefas com Apache Hadoop no HDInsight

Escaldaduras são uma biblioteca de Scala que torna mais fácil criar Hadoop MapReduce tarefas. Oferece uma sintaxe conciso, bem como justa integração com Scala.

Neste documento, saiba como utilizar Maven para criar uma tarefa de MapReduce de contagem de base do word escrita Scalding. Irá, em seguida, saiba como implementar e executar esta tarefa num HDInsight cluster.

## <a name="prerequisites"></a>Pré-requisitos

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A Windows ou Linux com base Hadoop num cluster de HDInsight**. Para mais informações, consulte [baseado em aprovisionar Linux Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md) ou [baseados no Windows aprovisionar Hadoop no HDInsight](hdinsight-provision-clusters.md) .

* **[Maven](http://maven.apache.org/)**

* **[Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou posterior**

## <a name="create-and-build-the-project"></a>Criar e criar o projecto

1. Utilize o seguinte comando para criar um novo projeto de avançou:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Este comando irá criar um novo directório denominado **scaldingwordcount**e criar andaime para uma aplicação Scala.

2. No diretório **scaldingwordcount** , abra o ficheiro **pom.xml** e substituir o conteúdo com o seguinte:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
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
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Este ficheiro descreve o projeto, dependências e plug-ins. Eis as entradas importantes:

    * **maven.Compiler.Source** e **maven.compiler.target**: define a versão de Java para este projeto

    * **repositórios**: repositórios que contêm os ficheiros de dependência utilizados por este projeto

    * **core_2.11 escaldaduras** e **hadoop core**: este projeto depende Scalding e Hadoop pacotes core

    * **maven-scala-Plug-in**: Plug-in para compilar scala aplicações

    * **maven-sombrear-Plug-in**: Plug-in para criar sombreadas jarros (fat). Aplica-se este plug-in filtros e transformações; specificially:

        * **filtros**: os filtros aplicados modificar as informações de meta incluídas no ficheiro para caixa. Para impedir que iniciar sessão exceções o tempo de execução, isto exclui vários ficheiros de assinatura podem ser incluídos com dependências.

        * **execuções**: A configuração de execução do pacote fase Especifica a classe de **com.twitter.scalding.Tool** como a classe principal para o pacote. Sem isto, tem de especificar com.twitter.scalding.Tool, bem como a classe que contém a lógica de aplicação, ao executar a tarefa com o comando hadoop.

3. Elimine o directório de **src/teste** , à medida que vai não criar testes com este exemplo.

4. Abra o ficheiro **src/main/scala/com/microsoft/example/App.scala** e substituir o conteúdo com o seguinte:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Isto implementa uma tarefa de contagem de base do word.

5. Guarde e feche os ficheiros.

6. Utilize o seguinte comando do diretório **scaldingwordcount** para criar e compactar a aplicação:

        mvn package

    Depois de completa esta tarefa, o pacote que contém a aplicação WordCount pode ser encontrado em **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Executar a tarefa de um cluster baseado em Linux

> [AZURE.NOTE] Os passos seguintes utilizam SSH e o comando de Hadoop. Para outros métodos de MapReduce tarefas em execução, consulte o artigo [Utilizar MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

1. Utilize o seguinte comando para carregar o pacote de atualizações para o seu cluster HDInsight:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Isto copia os ficheiros do sistema local para o nó cabeça.

    > [AZURE.NOTE] Se utilizou uma palavra-passe para proteger a sua conta SSH, vai ser-lhe a palavra-passe. Caso tenha utilizado uma chave SSH, poderá ter de utilizar o `-i` parâmetro e o caminho para a chave privada. Por exemplo,`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Utilize o seguinte comando para ligar para o nó do cabeça cluster:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se utilizou uma palavra-passe para proteger a sua conta SSH, vai ser-lhe a palavra-passe. Caso tenha utilizado uma chave SSH, poderá ter de utilizar o `-i` parâmetro e o caminho para a chave privada. Por exemplo,`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Quando estabelecida a nó cabeça, utilize o seguinte comando para executar a tarefa de contagem do word

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    Esta ação executa a classe de WordCount que implementada anterior. `--hdfs`Indica a tarefa para utilizar HDFS. `--input`Especifica o ficheiro de texto de entrada, enquanto `--output` Especifica a localização de saída.

4. Depois da tarefa estiver concluída, utilize o seguinte para ver o resultado.

        hdfs dfs -text wasbs:///example/wordcountout/*

    Esta ação irá apresentar informações semelhantes ao seguinte:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Executar a tarefa de um cluster baseados no Windows

Os passos seguintes utilizam o Windows PowerShell. Para outros métodos de MapReduce tarefas em execução, consulte o artigo [Utilizar MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Inicie o PowerShell do Azure e início de sessão para a sua conta Azure. Depois de fornecer as suas credenciais, o comando devolve informações sobre a sua conta.

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Se tem múltiplas subscrições, fornece o id da subscrição que pretende utilizar para implementação.

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Pode utilizar `Get-AzureRMSubscription` para obter uma lista de todas as subscrições associada à sua conta, que inclui o Id da subscrição para cada um deles.

4. Utilize o seguinte script para carregar e executar a tarefa de WordCount. Substituir `CLUSTERNAME` com o nome do seu HDInsight cluster e certifique-se de que `$fileToUpload` é o caminho para o ficheiro __scaldingwordcount-1.0-SNAPSHOT.jar__ correto.

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Quando executa o script, vai ser-lhe para introduzir a administração nome de utilizador e palavra-passe para o seu cluster HDInsight. Quaisquer erros que ocorram enquanto a tarefa estiver em execução com sessão iniciados na consola.
     
6. Assim que a tarefa estiver concluída, o resultado será transferido para o ficheiro de __saída__ no diretório atual. Utilize o seguinte comando para apresentar os resultados.

        cat output.txt

    O ficheiro deve conter valores semelhantes ao seguinte:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como utilizar Scalding para criar tarefas de MapReduce para HDInsight, utilize as ligações seguintes para explorar outras formas de trabalhar com Azure HDInsight.

* [Utilizar ramo com HDInsight](hdinsight-use-hive.md)

* [Utilizar porco com HDInsight](hdinsight-use-pig.md)

* [Utilizar tarefas de MapReduce com HDInsight](hdinsight-use-mapreduce.md)
