<properties
pageTitle="Utilizar uma função de Java definidos pelo utilizador (UDF) com ramo no HDInsight | Microsoft Azure"
description="Saiba como criar e utilizar uma função de Java definidos pelo utilizador (UDF) a partir do ramo no HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utilizar um Java UDF com ramo no HDInsight

Ramo é ideal para trabalhar com dados no HDInsight, mas, por vezes, é necessário um geral mais finalidade idioma. Ramo permite-lhe criar funções definidas pelo utilizador (UDF) utilizando uma variedade de linguagens de programação. Este documento, vai aprender a utilizar um UDF Java do ramo.

## <a name="requirements"></a>Requisitos de

* Uma subscrição do Azure

* Um cluster de HDInsight (Windows ou Linux-based)

    > [AZURE.NOTE] A maior parte dos passos neste documento irão funcionar em ambos os tipos de cluster; No entanto, os passos são utilizados para carregar o UDF compilado para cluster e executá-la são específicos a clusters baseado em Linux. São fornecidas hiperligações às informações que podem ser utilizadas com clusters baseados no Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, tal como OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Um editor de texto ou Java IDE

    > [AZURE.IMPORTANT] Se estiver a utilizar um servidor baseado em Linux HDInsight, mas a criar os ficheiros de Python no cliente do Windows, tem de utilizar um editor de que utiliza LF como uma terminação de linha. Se não tiver a certeza se o seu editor utiliza LF ou CRLF, consulte a secção de [resolução de problemas](#troubleshooting) para obter passos sobre como remover o caráter CR utilizando utilitários no HDInsight cluster.

## <a name="create-an-example-udf"></a>Criar um exemplo UDF

1. A partir de uma linha de comandos, utilize o seguinte para criar um novo projeto de Maven:

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Se estiver a utilizar o PowerShell, tem de colocar os parâmetros entre aspas. Por exemplo, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Isto irá criar um novo directório denominado __exampleudf__, que irá conter o projeto Maven.

2. Assim que o projeto tiver sido criado, elimine o directório de __src/exampleudf/teste__ que foi criado como parte do projeto; não será utilizada para este exemplo.

3. Abra o __exampleudf/pom.xml__e substituir os existentes `<dependencies>` entrada com o seguinte:

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    Estas entradas especificam a versão do Hadoop e ramo incluído com o HDInsight 3.3 e 3.4 clusters. Pode encontrar informações sobre as versões do Hadoop e ramo fornecidos com o HDInsight do [controlo de versões do HDInsight componente](hdinsight-component-versioning.md) documento.

    Adicionar um `<build>` secção antes do `</project>` linha no final do ficheiro. Esta secção deve conter o seguinte:

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
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
            </plugins>
        </build>
    
    Estas entradas definem como construir o projeto. Especificamente, a versão do Java que o project utiliza e como construir uma uberjar para a implementação do cluster.

    Guarde o ficheiro assim que foram feitas as alterações.

4. Mudar o nome __exampleudf/src/main/java/com/microsoft/examples/App.java__ para __ExampleUDF.java__e, em seguida, abra o ficheiro no seu editor.

5. Substitua o conteúdo do ficheiro __ExampleUDF.java__ com o seguinte, em seguida, guarde o ficheiro.

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    Isto implementa um UDF que aceita um valor de cadeia e devolve uma versão da cadeia em minúsculas.

## <a name="build-and-install-the-udf"></a>Criar e instalar o UDF

1. Utilize o seguinte comando para compilar e compactar UDF:

        mvn compile package

    Isto irá criar, em seguida, compactar UDF num __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Utilizar o `scp` comando para copiar o ficheiro para o cluster HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Substitua __myuser__ a conta de utilizador para o seu cluster SSH. Substitua o nome do cluster __mycluster__ . Se utilizou uma palavra-passe para proteger a conta SSH, ser-lhe-á pedido para introduzir a palavra-passe. Se tiver utilizado um certificado, poderá ter utilizar o `-i` parâmetro para especificar o ficheiro de chave privada.

3. Ligar ao cluster utilizando SSH. 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Para obter mais informações sobre como utilizar SSH com HDInsight, consulte os seguintes documentos.

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. Da sessão SSH, copie o ficheiro de para caixa ao armazenamento de HDInsight.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Utilizar o UDF do ramo

1. Utilize o seguinte procedimento para iniciar o cliente de Beeline da sessão SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Este comando assume que utilizou a predefinição de __administrador__ para a conta de início de sessão para o seu cluster.

2. Quando chegar à `jdbc:hive2://localhost:10001/>` pedido, introduza o seguinte procedimento para adicionar o UDF ramo e expô-la como uma função.

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. Utilize o UDF para converter valores obtidos a partir de uma tabela minúsculas cadeias.

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    Isto irá selecione a plataforma de dispositivo (Android, Windows, iOS, etc.) a partir da tabela, converter a cadeia para minúsculas e, em seguida, apresente-os. O resultado aparecerá semelhante ao seguinte.

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Próximos passos

Para outras formas de trabalhar com ramo, consulte o artigo [Utilizar Hive com HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre as funções de Hive User-Defined, consulte a secção [Hive operadores e funções definidas pelo utilizador](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do ramo wiki na apache.org.
