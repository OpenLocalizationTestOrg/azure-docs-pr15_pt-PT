<properties
    pageTitle="Criar uma aplicação de HBase utilizando Maven e Java, em seguida, implementar baseado em Linux HDInsight | Microsoft Azure"
    description="Saiba como utilizar Apache Maven para criar uma aplicação baseada em Java HBase Apache, em seguida, implementá-lo ao HDInsight Linux baseado na nuvem Azure."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Utilizar Maven para criar aplicações de Java que utilizam HBase com HDInsight baseado em Linux (Hadoop)

Saiba como criar e criar uma aplicação do [Apache HBase](http://hbase.apache.org/) em Java utilizando Apache Maven. Em seguida, utilize a aplicação com um cluster de baseado em Linux HDInsight.

[Maven](http://maven.apache.org/) é uma gestão de projetos de software ferramenta e, compreensão que permite-lhe criar software, documentação e relatórios para projetos Java. Neste artigo, vai aprender a utilizá-lo para criar uma aplicação de Java básica que que cria, consultas e elimina uma tabela de HBase num cluster baseado em Linux HDInsight.

> [AZURE.NOTE] Os passos neste documento partem do pressuposto de que está a utilizar um cluster de baseado em Linux HDInsight. Para obter informações sobre como utilizar um cluster de HDInsight baseados no Windows, consulte o artigo [Utilizar Maven para construir Java aplicações que utilizam HBase com HDInsight baseados no Windows](hdinsight-hbase-build-java-maven.md)

##<a name="requirements"></a>Requisitos de

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou posterior

* [Maven](http://maven.apache.org/)

* [Um cluster de baseado em Linux Azure HDInsight com HBase](../hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] Os passos neste documento foram testados com versões de cluster HDInsight 3,2, 3.3 e 3.4. Os valores predefinidos fornecidos nos exemplos são para um cluster de HDInsight 3.4.

* **Familiaridade com SSH e SCP**. Para obter mais informações sobre como utilizar SSH e SCP com HDInsight, consulte o seguinte:

    * **Clientes Linux, Unix ou OS X**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Os clientes do Windows**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="create-the-project"></a>Criar o projecto

1. A partir da linha de comandos no seu ambiente de desenvolvimento, altere directórios para a localização onde pretende criar o projeto, por exemplo, `cd code/hdinsight`.

2. Utilize o comando de __mvn__ , que é instalado com Maven, para gerar andaime do projeto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Esta ação cria um novo directório no diretório atual, com o nome especificado pelo parâmetro __artifactID__ (**hbaseapp** neste exemplo.) Este diretório irá conter os seguintes itens:

    * __pom.XML__: O modelo de objeto do projecto ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contém informações e detalhes de configuração utilizados para construir o projeto.

    * __src__: diretório que contém o directório __principais/java/com/microsoft/exemplos__ , onde irá criar a aplicação.

3. Uma vez que não serão utilizada neste exemplo, elimine o ficheiro de __src/test/java/com/microsoft/examples/apptest.java__ .

##<a name="update-the-project-object-model"></a>Atualizar o modelo de objeto do projeto

1. Editar o ficheiro __pom.xml__ e adicione o seguinte código dentro de `<dependencies>` secção:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Isto indica que avançou que o projeto requer __hbase cliente__ versão __1.1.2__. No momento da compilação, este será transferido do repositório de Maven predefinido. Pode utilizar a [Pesquisa de repositório Central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre esta dependência.

    > [AZURE.IMPORTANT] O número da versão tem de corresponder a versão do HBase é fornecido com o seu cluster HDInsight. Utilize a tabela seguinte para encontrar o número da versão correto.

  	| Versão de cluster HDInsight | Versão HBase a utilizar |
  	| ----- | ----- |
  	| 3,2 | 0.98.4-hadoop2 |
  	| 3.3 e 3.4 | 1.1.2 |

    Para mais informações sobre componentes e versões do HDInsight, consulte o artigo [o que são os diferentes componentes de Hadoop disponíveis com o HDInsight](hdinsight-component-versioning.md).

2. Se estiver a utilizar um 3.3 HDInsight ou 3.4 cluster, também tem de adicionar o seguinte procedimento para o `<dependencies>` secção:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Isto irá carregar os elementos de Lisboa core, que são necessários com a versão de Hbase 1.1.x.

2. Adicione o seguinte código para o ficheiro __pom.xml__ . Isto tem de estar dentro de `<project>...</project>` etiquetas no ficheiro, por exemplo, entre `</dependencies>` e `</project>`.

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

    Este procedimento configura um recurso (__hbase/as por acesso telefónico-site.xml__), que contém informações de configuração para HBase.

    > [AZURE.NOTE] Também pode definir valores através do código de configuração. Ver os comentários no exemplo __CreateTable__ que se segue para como o fazer.

    Este procedimento também configura o [Plug-in do Maven compilador](http://maven.apache.org/plugins/maven-compiler-plugin/) e [Aplicar sombreado de Maven Plug-in](http://maven.apache.org/plugins/maven-shade-plugin/). O plug-in de compilador é utilizado para a topologia de compilação. O plug-in de tom é utilizado para impedir a duplicação de licença no pacote para caixa que é criada pelo Maven. O motivo que esta opção é utilizada é que os ficheiros de licença duplicados originar um erro em tempo de execução no HDInsight cluster. Utilizar maven-sombrear-Plug-in com o `ApacheLicenseResourceTransformer` este erro impede a implementação.

    O maven-sombrear-Plug-in também produz um para uber caixa (ou para fat caixa,) que contém todas as dependências necessárias pela aplicação.

3. Guarde o ficheiro __pom.xml__ .

4. Crie um novo directório denominado __as por acesso telefónico__ no diretório __hbaseapp__ . Será utilizada para colocar em espera informações de configuração para estabelecer ligação ao HBase.

5. Utilize o seguinte comando para copiar a configuração de HBase do servidor HDInsight ao diretório __as por acesso telefónico__ . Substituir o **nome de utilizador** o nome do seu início de sessão SSH. Substitua o **nome de cluster** com o seu nome de cluster HDInsight:

        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

    > [AZURE.NOTE] Se utilizou uma palavra-passe para a sua conta SSH, ser-lhe-á pedido para introduzir a palavra-passe. Caso tenha utilizado uma chave SSH com a conta, poderá ter utilizar o `-i` parâmetro para especificar o caminho para o ficheiro de chave. O exemplo seguinte irá carregar a chave privada de `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##<a name="create-the-application"></a>Criar a aplicação

1. Aceda ao diretório __hbaseapp/src/principais/java/com/microsoft/exemplos__ e mude o nome de ficheiro app.java para __CreateTable.java__.

2. Abra o ficheiro __CreateTable.java__ e substituir o conteúdo existente com o seguinte:

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
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. No diretório __hbaseapp/src/principais/java/com/microsoft/exemplos__ , crie um novo ficheiro chamado __SearchByEmail.java__. Utilize o seguinte como o conteúdo deste ficheiro:

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

6. No diretório __hbaseapp/src/principais/hava/com/microsoft/exemplos__ , crie um novo ficheiro chamado __DeleteTable.java__. Utilize o seguinte como o conteúdo deste ficheiro:

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

2. A partir do diretório __hbaseapp__ , utilize o seguinte comando para criar um ficheiro para caixa que contém a aplicação:

        mvn clean package

    Isto limpa qualquer artefactos compilação anterior, transferências quaisquer dependências que já não tem sido instaladas, em seguida, constrói e pacotes a aplicação.

3. Quando o comando estiver concluída, o directório de __hbaseapp/destino__ irá conter num ficheiro denominado __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] O ficheiro __hbaseapp-1.0-SNAPSHOT.jar__ é uma uber para caixa (por vezes denominada para uma caixa fat,) que contém todas as dependências necessárias para executar a aplicação.

##<a name="upload-the-jar-file-and-run-jobs"></a>Carregue o ficheiro para caixa e executar tarefas

1. Utilize o seguinte procedimento para carregar para a caixa ao HDInsight cluster. Substituir o **nome de utilizador** o nome do seu início de sessão SSH. Substitua o **nome de cluster** com o seu nome de cluster HDInsight:

        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Isto irá carregar o ficheiro para o diretório da sua conta de utilizador SSH.

    > [AZURE.NOTE] Se utilizou uma palavra-passe para a sua conta SSH, ser-lhe-á pedido para introduzir a palavra-passe. Caso tenha utilizado uma chave SSH com a conta, poderá ter utilizar o `-i` parâmetro para especificar o caminho para o ficheiro de chave. O exemplo seguinte irá carregar a chave privada de `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Utilize SSH para ligar ao HDInsight cluster. Substituir o **nome de utilizador** o nome do seu início de sessão SSH. Substitua o **nome de cluster** com o seu nome de cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se utilizou uma palavra-passe para a sua conta SSH, ser-lhe-á pedido para introduzir a palavra-passe. Caso tenha utilizado uma chave SSH com a conta, poderá ter utilizar o `-i` parâmetro para especificar o caminho para o ficheiro de chave. O exemplo seguinte irá carregar a chave privada de `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Quando estabelecida, utilize o seguinte para criar uma nova tabela HBase utilizando a aplicação de Java:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

    Isto vai criar uma nova tabela HBase chamada __pessoas__e, preencha-a com dados.

4. Em seguida, utilize o seguinte para procurar endereços de correio eletrónico armazenados na tabela:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

    Deverá receber os seguintes resultados:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

##<a name="delete-the-table"></a>Eliminar a tabela

Quando tiver terminado o exemplo, utilize o seguinte comando da sessão Azure PowerShell para eliminar a tabela de __pessoas__ utilizada neste exemplo:

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

