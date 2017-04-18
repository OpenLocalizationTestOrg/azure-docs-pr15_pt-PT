<properties
pageTitle="Criar uma aplicação de HBase utilizando Maven e implementar para HDInsight baseados no Windows | Microsoft Azure"
description="Saiba como utilizar Apache Maven para criar uma aplicação baseada em Java HBase Apache, em seguida, implementá-lo a um cluster de baseados no Windows Azure HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"
tags="azure-portal"/>

<tags
ms.service="hdinsight"
ms.workload="big-data"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Utilizar Maven para criar aplicações de Java que utilizam HBase com HDInsight baseados no Windows (Hadoop)

Saiba como criar e criar uma aplicação do [Apache HBase](http://hbase.apache.org/) em Java utilizando Apache Maven. Em seguida, utilize a aplicação com Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) é uma gestão de projetos de software ferramenta e, compreensão que permite-lhe criar software, documentação e relatórios para projetos Java. Neste artigo, saiba como utilizá-la para criar uma aplicação de Java básica que que cria, consultas e elimina uma tabela de HBase num cluster Azure HDInsight.

> [AZURE.NOTE] Os passos neste documento partem do pressuposto de que está a utilizar um cluster de HDInsight baseados no Windows. Para obter informações sobre como utilizar um cluster de baseado em Linux HDInsight, consulte o artigo [Utilizar Maven para construir Java aplicações que utilizam HBase com baseado em Linux HDInsight](hdinsight-hbase-build-java-maven-linux.md)

##<a name="requirements"></a>Requisitos de

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou posterior

* [Maven](http://maven.apache.org/)


* [Um cluster de baseados no Windows HDInsight com HBase](hdinsight-hbase-tutorial-get-started.md#create-hbase-cluster)


    > [AZURE.NOTE] Os passos neste documento foram testados com versões de cluster HDInsight 3,2 e 3.3. Os valores predefinidos fornecidos nos exemplos são para um cluster de HDInsight 3.3.

##<a name="create-the-project"></a>Criar o projecto

1. Na linha de comandos no seu ambiente de desenvolvimento, altere directórios para a localização onde pretende criar o projeto, por exemplo, `cd code\hdinsight`.

2. Utilize o comando de __mvn__ , que é instalado com Maven, para gerar andaime do projeto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Este comando cria um diretório na localização atual, com o nome especificado pelo parâmetro __artifactID__ (**hbaseapp** neste exemplo.) Este diretório contém os seguintes itens:

    * __pom.XML__: O modelo de objeto do projecto ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém informações e detalhes de configuração utilizados para construir o projeto.

    * __src__: diretório que contém o directório __main\java\com\microsoft\examples__ , onde irá criar a aplicação.

3. Porque não é utilizada neste exemplo, elimine o ficheiro de __src\test\java\com\microsoft\examples\apptest.java__ .

##<a name="update-the-project-object-model"></a>Atualizar o modelo de objeto do projeto

1. Editar o ficheiro __pom.xml__ e adicione o seguinte código dentro de `<dependencies>` secção:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Esta secção explica Maven que o projeto requer __hbase cliente__ versão __1.1.2__. No momento da compilação, esta dependência é transferida do repositório de Maven predefinido. Pode utilizar a [Pesquisa de repositório Central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre esta dependência.

    > [AZURE.IMPORTANT] O número da versão tem de corresponder a versão do HBase é fornecido com o seu cluster HDInsight. Utilize a tabela seguinte para encontrar o número da versão correto.

  	| Versão de cluster HDInsight | Versão HBase a utilizar |
  	| ----- | ----- |
  	| 3,2 | 0.98.4-hadoop2 |
  	| 3.3 | 1.1.2 |

    Para mais informações sobre componentes e versões do HDInsight, consulte o artigo [o que são os diferentes componentes de Hadoop disponíveis com o HDInsight](hdinsight-component-versioning.md).

2. Se estiver a utilizar um cluster de HDInsight 3.3, também tem de adicionar o seguinte procedimento para o `<dependencies>` secção:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Esta dependência irá carregar os componentes principais Portalegre, que são utilizados pela versão Hbase 1.1.x.

2. Adicione o seguinte código para o ficheiro __pom.xml__ . Esta secção tem de estar dentro de `<project>...</project>` etiquetas no ficheiro, por exemplo, entre `</dependencies>` e `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
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

    O `<resources>` secção configura um recurso (__conf\hbase site.xml__) que contém informações de configuração para HBase.

    > [AZURE.NOTE] Também pode definir valores através do código de configuração. Ver os comentários no exemplo __CreateTable__ que se segue para como o fazer.

    Isto `<plugins>` secção configura o [Plug-in do Maven compilador](http://maven.apache.org/plugins/maven-compiler-plugin/) e [Aplicar sombreado de Maven Plug-in](http://maven.apache.org/plugins/maven-shade-plugin/). O plug-in de compilador é utilizado para a topologia de compilação. O plug-in de tom é utilizado para impedir a duplicação de licença no pacote para caixa que é criada pelo Maven. O motivo que esta opção é utilizada é que os ficheiros de licença duplicados originar um erro em tempo de execução no HDInsight cluster. Utilizar maven-sombrear-Plug-in com o `ApacheLicenseResourceTransformer` este erro impede a implementação.

    O maven-sombrear-Plug-in também produz um para uber caixa (ou para fat caixa) que contém todas as dependências necessárias pela aplicação.

3. Guarde o ficheiro __pom.xml__ .

4. Crie um novo directório denominado __as por acesso telefónico__ no diretório __hbaseapp__ . No diretório __as por acesso telefónico__ , crie um ficheiro denominado __hbase site.xml__. Utilize o seguinte como o conteúdo do ficheiro:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Este ficheiro será utilizado para carregar a configuração de HBase para um cluster de HDInsight.

    > [AZURE.NOTE] Este é um ficheiro de mínimas hbase-site.xml e contém as definições de mínimas simples para o cluster HDInsight.

3. Guarde o ficheiro __hbase site.xml__ .

##<a name="create-the-application"></a>Criar a aplicação

1. Aceda ao diretório __hbaseapp\src\main\java\com\microsoft\examples__ e mude o nome de ficheiro app.java para __CreateTable.java__.

2. Abra o ficheiro __CreateTable.java__ e substituir o conteúdo existente com o seguinte código:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Esta é a classe de __CreateTable__ , o que vai criar uma tabela com o nome __pessoas__ e preencha-a com alguns utilizadores predefinidos.

3. Guarde o ficheiro __CreateTable.java__ .

4. No diretório __hbaseapp\src\main\java\com\microsoft\examples__ , crie um novo ficheiro chamado __SearchByEmail.java__. Utilize o seguinte código como o conteúdo deste ficheiro:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    A classe de __SearchByEmail__ pode ser utilizada para consultar de linhas por endereço de e-mail. Uma vez que utiliza um filtro de expressões normais, pode fornecer uma cadeia ou uma expressão regular ao utilizar a classe.

5. Guarde o ficheiro __SearchByEmail.java__ .

6. No diretório __hbaseapp\src\main\hava\com\microsoft\examples__ , crie um novo ficheiro chamado __DeleteTable.java__. Utilize o seguinte código como o conteúdo deste ficheiro:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Esta classe é para limpar neste exemplo, desativar e largando a tabela criada pela classe __CreateTable__ .

7. Guarde o ficheiro __DeleteTable.java__ .

##<a name="build-and-package-the-application"></a>Criar e compactar a aplicação

1. Abra uma linha de comandos e altere directórios ao diretório __hbaseapp__ .

2. Utilize o seguinte comando para criar um ficheiro para caixa que contém a aplicação:

        mvn clean package

    Isto limpa qualquer artefactos compilação anterior, transferências quaisquer dependências que já não tem sido instaladas, em seguida, constrói e pacotes a aplicação.

3. Quando o comando estiver concluída, o directório de __hbaseapp\target__ contém num ficheiro denominado __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] O ficheiro __hbaseapp-1.0-SNAPSHOT.jar__ é uma uber para caixa (por vezes denominada para uma caixa fat,) que contém todas as dependências necessárias para executar a aplicação.

##<a name="upload-the-jar-file-and-start-a-job"></a>Carregue o ficheiro para caixa e iniciar uma tarefa

Existem várias formas para carregar um ficheiro para o seu cluster HDInsight, conforme descrito no [carregar dados para trabalhos de Hadoop no HDInsight](hdinsight-upload-data.md). Os passos seguintes utilizam Azure PowerShell.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Depois de instalar e configurar o Azure PowerShell, crie um novo ficheiro chamado __hbase runner.psm1__. Utilize o seguinte como o conteúdo deste ficheiro:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Este ficheiro contém dois módulos:

    * __Adicionar HDInsightFile__ - utilizado para carregar ficheiros para o HDInsight

    * __Iniciar HBaseExample__ - utilizado para executar as classes criadas anteriormente

2. Guarde o ficheiro __hbase runner.psm1__ .

3. Abrir uma nova janela do Azure PowerShell, alterar directórios ao diretório __hbaseapp__ e, em seguida, execute o seguinte comando.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Altere o caminho para a localização do ficheiro __hbase runner.psm1__ criada anteriormente. Este procedimento regista o módulo para esta sessão Azure PowerShell.

2. Utilize o seguinte comando para carregar o __hbaseapp-1.0-SNAPSHOT.jar__ para o seu cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Substitua o nome do seu cluster HDInsight __hdinsightclustername__ . O comando carrega o __hbaseapp-1.0-SNAPSHOT.jar__ para a localização de __exemplo/jarros__ no armazenamento principal para o seu cluster HDInsight.

3. Depois dos ficheiros são carregados, utilize o seguinte código para criar uma tabela utilizando o __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Substitua o nome do seu cluster HDInsight __hdinsightclustername__ .

    Este comando cria uma nova tabela denominada __pessoas__ no seu cluster HDInsight. Este comando não mostrar qualquer saída na janela da consola.

2. Para procurar entradas na tabela, utilize o seguinte comando:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Substitua o nome do seu cluster HDInsight __hdinsightclustername__ .

    Este comando utiliza a classe de **SearchByEmail** para procurar as linhas onde a família de coluna __contactinformation__ e a coluna de __correio eletrónico__ , contém a cadeia __contoso.com__. Deverá receber os seguintes resultados:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Utilizar __fabrikam.com__ para o `-emailRegex` valor devolve os utilizadores que têm __fabrikam.com__ no campo de correio eletrónico. Uma vez que esta pesquisa é implementada utilizando um filtro baseado na expressão normal, também pode introduzir expressões regulares, tal como __^ r__, quais as entradas devolve onde o e-mail começa com a letra 'r'.

##<a name="delete-the-table"></a>Eliminar a tabela

Quando tiver terminado o exemplo, utilize o seguinte comando da sessão Azure PowerShell para eliminar a tabela de __pessoas__ utilizada neste exemplo:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Substitua o nome do seu cluster HDInsight __hdinsightclustername__ .

##<a name="troubleshooting"></a>Resolução de problemas

###<a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Sem resultados ou resultados inesperados quando utilizar HBaseExample iniciar

Utilizar o `-showErr` parâmetro para ver o erro-padrão (STDERR) que é produzido ao executar a tarefa.
