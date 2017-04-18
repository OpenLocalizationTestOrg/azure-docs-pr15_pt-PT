<properties
    pageTitle="Utilizar Hadoop Oozie fluxos de trabalho no baseado em Linux HDInsight | Microsoft Azure"
    description="Utilize Hadoop Oozie baseado em Linux HDInsight. Saiba como definir um fluxo de trabalho Oozie e submeter uma tarefa Oozie."
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
    ms.date="10/11/2016"
    ms.author="larryfr"/>


# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Utilizar Oozie com Hadoop para definir e executar um fluxo de trabalho baseado em Linux HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Saiba como utilizar Apache Oozie para definir um fluxo de trabalho que utiliza ramo e Sqoop e, em seguida, execute o fluxo de trabalho num cluster baseado em Linux HDInsight.

Apache Oozie é um sistema de fluxo de trabalho/coordenação gere Hadoop tarefas. Está integrado na pilha de Hadoop e suporta tarefas de Hadoop para Apache MapReduce, Apache porco, Apache Hive e Apache Sqoop. Também pode ser utilizada para agendar tarefas que são específicas para um sistema, como os programas Java ou scripts da shell

> [AZURE.NOTE] Outra opção de definição de fluxos de trabalho com HDInsight é Azure fábrica de dados. Para saber mais sobre Azure fábrica de dados, consulte o artigo [utilizar porco e ramo de registo com dados fábrica][azure-data-factory-pig-hive].

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**: consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Clip Azure**: consulte o artigo [instalar e configurar o clip Azure](../xplat-cli-install.md)
    
    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- **HDInsight um cluster**: consulte o artigo [Introdução ao HDInsight em Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Base de dados do Azure SQL**: esta será criada utilizando os passos descritos neste documento

##<a name="example-workflow"></a>Fluxo de trabalho de exemplo

O fluxo de trabalho que irá implementar ao seguir as instruções neste documento contém duas ações. Ações são as definições para tarefas, tais como a executar o ramo, Sqoop, MapReduce ou outro processo:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

1. Uma ação de ramo é executado um script HiveQL para extrair os registos de **hivesampletable** incluído com o HDInsight. Cada linha de dados descreve uma visita a partir de um dispositivo móvel específico. O formato de registo é apresentada semelhante ao seguinte:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    O script de ramo utilizado neste documento conta as visitas total para cada plataforma (como Android ou iPhone) e armazena as contagens para uma nova tabela Hive.

    Para mais informações sobre ramo, consulte o artigo [Utilizar Hive com HDInsight][hdinsight-use-hive].

2.  Uma ação Sqoop exporta o conteúdo da nova tabela Hive para uma tabela numa base de dados Azure SQL. Para mais informações sobre Sqoop, consulte o artigo [Utilizar Hadoop Sqoop com HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Para versões Oozie suportadas em HDInsight clusters, consulte o artigo [quais são as novidades nas versões cluster Hadoop fornecidas pela HDInsight?] [hdinsight-versions].

##<a name="create-the-working-directory"></a>Criar o directório de trabalho

Oozie espera recursos necessários para uma tarefa a ser armazenado no diretório do mesmo. Este exemplo utiliza **wasbs: / / / tutoriais/useoozie**. Utilize o seguinte comando para criar este diretório e o diretório de dados onde vai introduzir a nova tabela Hive criada por este fluxo de trabalho:

    hdfs dfs -mkdir -p /tutorials/useoozie/data

> [AZURE.NOTE] O `-p` parâmetro causados todos os directórios no caminho para ser criada se não existir já. Diretório de **dados** do será utilizado para colocar em espera dados utilizados pelo **useooziewf.hql** script.

Também execute o seguinte comando, que garantem que Oozie pode representar a sua conta de utilizador quando a ser executado ramo e Sqoop tarefas. Substitua o **nome de utilizador** com o seu nome de início de sessão:

    sudo adduser USERNAME users

Se receber um erro que o utilizador já é um membro de utilizadores, que pode ignorá-lo.

##<a name="add-a-database-driver"></a>Adicionar um controlador de base de dados

Uma vez que este fluxo de trabalho utiliza Sqoop para exportar dados para a base de dados SQL, tem de fornecer uma cópia do controlador JDBC utilizada para lidar com a base de dados do SQL. Utilize o seguinte comando para copiá-lo para o directório de trabalho:

    hdfs dfs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/

Se o fluxo de trabalho utilizado outros recursos, tal como para uma caixa que contém uma aplicação de MapReduce, que precisa para adicioná-los também.

##<a name="define-the-hive-query"></a>Definir a consulta de ramo

Utilize os passos seguintes para criar um script HiveQL que define uma consulta, que será utilizada num fluxo de trabalho Oozie posteriormente neste documento.

1. Utilize SSH para ligar ao cluster baseado em Linux HDInsight:

    * **Clientes Linux, Unix ou OS X**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Os clientes do Windows**: consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilize o seguinte comando para criar um novo ficheiro:

        nano useooziewf.hql

1. Quando abre o editor de nano, utilize o seguinte como o conteúdo do ficheiro:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
        FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

    Existem duas variáveis utilizadas no script:

    - **${hiveTableName}**: irá conter o nome da tabela seja criado
    - **${hiveDataFolder}**: irá conter a localização para armazenar os ficheiros de dados para a tabela

    O ficheiro de definição de fluxo de trabalho (workflow.xml neste tutorial) transmite esses valores para este script HiveQL em tempo de execução.

2. Prima Ctrl-X para sair do editor. Quando lhe for pedido, selecione **Y** para guardar o ficheiro, em seguida, utilize a **tecla Enter** para utilizar o nome do ficheiro **useooziewf.hql** .

3. Utilize os seguintes comandos para copiar **useooziewf.hql** para **wasbs:///tutorials/useoozie/useooziewf.hql**:

        hdfs dfs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

    Estes comandos armazenam o ficheiro de **useooziewf.hql** da conta de armazenamento do Windows Azure associada este cluster, que irá preservar o ficheiro, mesmo se cluster é eliminado. Esta opção permite-lhe poupe dinheiro eliminando clusters quando que não sejam utilizado, mantendo a tarefas e fluxos de trabalho.

##<a name="define-the-workflow"></a>Definir o fluxo de trabalho

Definições de fluxos de trabalho Oozie estão escritas em hPDL (um XML processo Definition Language). Utilize os passos seguintes para definir o fluxo de trabalho:

1. Utilize a seguinte instrução para criar e editar um novo ficheiro:

        nano workflow.xml

1. Quando abre o editor de nano, introduza o seguinte como o conteúdo do ficheiro:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>
            <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.job.queue.name</name>
                    <value>${queueName}</value>
                </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
            </action>
            <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                <property>
                    <name>mapred.compress.map.output</name>
                    <value>true</value>
                </property>
                </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveDataFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\t"</arg>
                <archive>sqljdbc41.jar</archive>
                </sqoop>
            <ok to="end"/>
            <error to="fail"/>
            </action>
            <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>
            <end name="end"/>
        </workflow-app>

    Existem duas ações definidas no fluxo de trabalho:

    - **RunHiveScript**: Esta é a ação de início e é executado **useooziewf.hql** ramo de script

    - **RunSqoopExport**: esta exporta os dados criados a partir do script ramo de base de dados do SQL utilizando Sqoop. Só serão executadas se a ação **RunHiveScript** for bem sucedida.

        > [AZURE.NOTE] Para obter mais informações sobre como Oozie fluxo de trabalho e utilizar ações de fluxo de trabalho, consulte a [documentação de Apache Oozie 4.0] [ apache-oozie-400] (para HDInsight versão 3.0) ou [Apache Oozie 3.3.2 documentação] [ apache-oozie-332] (para HDInsight versão 2.1).

    Note que o fluxo de trabalho tem várias entradas, tais como `${jobTracker}`, que serão substituídos por valores que utilizar na definição de tarefa posteriormente neste documento.

    Tenha também em atenção o `<archive>sqljdbc4.jar</arcive>` entrada na secção Sqoop. Isto indica ao Oozie para disponibilizar neste arquivo para Sqoop quando esta ação é executado.

2. Utilize CTRL + X, em seguida, **Y** e **Enter** para guardar o ficheiro.

3. Utilize o seguinte comando para copiar o ficheiro **workflow.xml** para **wasbs:///tutorials/useoozie/workflow.xml**:

        hdfs dfs -copyFromLocal workflow.xml /tutorials/useoozie/workflow.xml

##<a name="create-the-database"></a>Criar a base de dados

Siga os passos no documento [criar uma base de dados do SQL](../sql-database/sql-database-get-started.md) para criar uma nova base de dados. Ao criar a base de dados, utilize __oozietest__ como o nome da base de dados. Também tome nota do nome utilizado para o servidor de base de dados, tal como esta será necessário, na secção seguinte.

###<a name="create-the-table"></a>Criar a tabela

> [AZURE.NOTE] Existem várias formas para ligar à base de dados do SQL para criar uma tabela. Os passos seguintes utilizam [FreeTDS](http://www.freetds.org/) do HDInsight cluster.

3. Utilize o seguinte comando para instalar FreeTDS no HDInsight cluster:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Uma vez FreeTDS ter sido instalado, utilize o seguinte comando para ligar ao servidor de base de dados SQL que criou anteriormente:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest

    Receberá saída semelhante ao seguinte:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

5. Na `1>` pedido, introduza as linhas seguintes:

        CREATE TABLE [dbo].[mobiledata](
        [deviceplatform] [nvarchar](50),
        [count] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
        GO

    Quando o `GO` declaração introduzida, serão avaliadas as declarações anterior. Isto irá criar uma nova tabela denominada **mobiledata** que irão ser escritos por Sqoop.

    Utilize o seguinte para confirmar que foi criada a tabela:

        SELECT * FROM information_schema.tables
        GO

    Deverá visualizar saída semelhante ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

8. Introduza `exit` na `1>` perguntar se quer saia do utilitário tsql.

##<a name="create-the-job-definition"></a>Criar a definição da tarefa

A definição da tarefa descreve onde encontrar o workflow.xml, bem como outros ficheiros utilizados pelo fluxo de trabalho (por exemplo, useooziewf.hql.) Também define os valores para propriedades utilizados no fluxo de trabalho e ficheiros associados.

1. Utilize o seguinte comando para obter o endereço completo do WASB ao armazenamento de predefinido. Será utilizada no ficheiro de configuração num pouco a incorporá:

        sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml

    Isto deve devolver informações semelhante ao seguinte:

        <name>fs.defaultFS</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>

    Guardar a **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** valor, tal como será utilizado nos próximos passos.

2. Utilize o seguinte comando para obter o FQDN da headnode cluster. Será utilizada para o endereço JobTracker cluster. Será utilizada no ficheiro de configuração num pouco a incorporá:

        hostname -f

    Isto irá devolver informações semelhante ao seguinte:

        hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net

    A porta utilizada para o JobTracker é 8050, por isso o endereço completo a utilizar para o JobTracker será **hn0 CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050**.

1. Utilize o seguinte para criar a configuração de definição de tarefa Oozie:

        nano job.xml

2. Quando abre o editor de nano, utilize o seguinte como o conteúdo do ficheiro:

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

          <property>
            <name>nameNode</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
          </property>

          <property>
            <name>jobTracker</name>
            <value>JOBTRACKERADDRESS</value>
          </property>

          <property>
            <name>queueName</name>
            <value>default</value>
          </property>

          <property>
            <name>oozie.use.system.libpath</name>
            <value>true</value>
          </property>

          <property>
            <name>hiveScript</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
          </property>

          <property>
            <name>hiveTableName</name>
            <value>mobilecount</value>
          </property>

          <property>
            <name>hiveDataFolder</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
          </property>

          <property>
            <name>sqlDatabaseConnectionString</name>
            <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
          </property>

          <property>
            <name>sqlDatabaseTableName</name>
            <value>mobiledata</value>
          </property>

          <property>
            <name>user.name</name>
            <value>YourName</value>
          </property>

          <property>
            <name>oozie.wf.application.path</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
          </property>
        </configuration>

    * Substituir todas as instâncias do **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** com o valor que recebeu anteriormente.

    > [AZURE.WARNING] Tem de utilizar o caminho completo da WASB, com a conta contentor e armazenamento como parte do caminho. Utilizando o formato breve (wasbs: / / /) causará a ação de RunHiveScript a falhar quando a tarefa é iniciada.

    * Substitua **JOBTRACKERADDRESS** com o endereço de JobTracker/ResourceManager que recebeu anteriormente.

    * Substitua **O_seu_nome** com o seu nome de início de sessão para o cluster HDInsight.

    * Substitua **nomedoservidor**, **adminLogin**e **adminPassword** com as informações para a base de dados do SQL Azure.

    A maior parte das informações neste ficheiro é utilizado para preencher os valores utilizados nos ficheiros workflow.xml ou ooziewf.hql (tal como ${nameNode}.)

    > [AZURE.NOTE] A entrada de **oozie.wf.application.path** define onde o ficheiro workflow.xml, que contém o fluxo de trabalho executado por esta tarefa.

2. Utilize CTRL + X, em seguida, **Y** e **Enter** para guardar o ficheiro.

##<a name="submit-and-manage-the-job"></a>Submeter e gerir a tarefa

Os passos seguintes utilizam o comando Oozie para submeter e gerir Oozie fluxos de trabalho no cluster. O comando Oozie é uma interface amigável sobre [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [AZURE.IMPORTANT] Ao utilizar o comando Oozie, tem de utilizar o FQDN para o headnode HDInsight. Este FQDN só está acessível a partir de cluster, ou se o cluster estiver numa rede Virtual Azure, a partir de outros computadores na mesma rede.

1. Utilize o seguinte para obter o URL do serviço Oozie:

        sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml

    Isto irá devolver um valor semelhante ao seguinte:

        <name>oozie.base.url</name>
        <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>

    Parte da **http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie** é o URL para utilizar com o comando Oozie.

2. Utilize o seguinte para criar uma variável de ambiente para o URL, para que não tenha escrever os dados para cada comando:

        export OOZIE_URL=http://HOSTNAMEt:11000/oozie

    Substitua o URL aquele que recebeu anteriormente.

3. Utilize o seguinte para submeter a tarefa:

        oozie job -config job.xml -submit

    Isto carrega as informações da tarefa a partir do **job.xml** e submete-à Oozie, mas não é executado.

    Assim que o comando for concluída, deve devolver o ID da tarefa. Por exemplo, `0000005-150622124850154-oozie-oozi-W`. Será utilizada para gerir a tarefa.

4. Ver o estado da tarefa utilizando o seguinte comando. Introduza o ID da tarefa devolvido pelo comando anterior:

        oozie job -info <JOBID>

    Isto irá devolver informações semelhante ao seguinte.

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasbs:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Esta tarefa tem um Estado de `PREP`, que indica que foi submetido, mas ainda não foi iniciado.

4. Utilize o seguinte para começar a tarefa:

        oozie job -start JOBID

    Se verificar o estado após este comando, irá estar num Estado de execução e as ações dentro do projecto no serão devolvidas informações.

5. Assim que a tarefa é concluída com êxito, pode verificar se os dados foram gerados e exportados à tabela de base de dados SQL através dos comandos seguintes:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

    Na `1>` pedido, introduza o seguinte:

        SELECT * FROM mobiledata
        GO

    Deverá receber informações semelhantes ao seguinte:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para mais informações sobre o comando Oozie, consulte o artigo [Oozie ferramenta de linha de comandos](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

##<a name="oozie-rest-api"></a>Oozie REST API

Oozie REST API permitem-lhe criar o seus próprio ferramentas que funcionam com o Oozie. Seguem-se HDInsight informações específicas sobre como utilizar a API do resto Oozie:

* **URI**: A API REST podem ser acedida a partir de fora do cluster no`https://CLUSTERNAME.azurehdinsight.net/oozie`

* **Autenticação**: tem de ser autenticar à API utilizando a conta de cluster HTTP (admin) e a palavra-passe. Por exemplo:

        curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Para mais informações sobre como utilizar a API do resto Oozie, consulte o artigo [API de serviços Web Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

##<a name="oozie-web-ui"></a>Oozie Web IU

IU do Web de Oozie fornece uma vista baseada na web para o estado das tarefas Oozie cluster. Permite-lhe ver o estado da tarefa, a definição da tarefa, configuração, um gráfico das ações na tarefa e registos de início para a tarefa. Também pode ver os detalhes para ações dentro de uma tarefa.

Para aceder à IU Oozie Web, utilize os seguintes passos:

1. Crie um túnel SSH ao HDInsight cluster. Para obter informações sobre como fazer isto, consulte o artigo [Utilizar SSH túnel para aceder ao web Ambari IU, ResourceManager, JobHistory, NameNode, Oozie e outros web da IU](hdinsight-linux-ambari-ssh-tunnel.md).

2. Assim que um túnel ter sido criado, abra a web de Ambari IU no seu browser. O URI para o site Ambari é **https://CLUSTERNAME.azurehdinsight.net**. Substitua o **nome de cluster** com o nome do seu cluster baseado em Linux HDInsight.

3. A partir do lado esquerdo da página, selecione **Oozie**, em seguida, **Ligações rápidas**e, por fim **Oozie Web IU**.

    ![imagem dos menus](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. IU do Web de Oozie predefinições para apresentar as tarefas de fluxo de trabalho em execução. Para ver todas as tarefas de fluxo de trabalho, selecione **Todas as tarefas**.

    ![Todas as tarefas apresentadas](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Selecione uma tarefa para ver mais informações sobre a tarefa.

    ![Informações de tarefa](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. A partir do separador informações da tarefa, pode ver informações sobre a tarefa básicas, bem como as ações individuais dentro da tarefa. Utilizar os separadores na parte superior pode ver o acesso de definição de tarefa, configuração da tarefa, o registo de projecto ou ver um direcionado incluindo Graph (Comprimentos) da tarefa.

    * **Registo de projecto**: selecione o botão de **GetLogs** para obter todos os registos de início para a tarefa ou utilize o campo **Introduza filtro de pesquisa** para filtrar registos

        ![Registo de projecto](./media/hdinsight-use-oozie-linux-mac/joblog.png)

    * **JobDAG**: O Comprimentos é uma descrição geral gráfica dos caminhos de dados tomadas através do fluxo de trabalho

        ![Tarefa Comprimentos](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Selecionar uma das ações a partir do separador **Informações da tarefa** moldados as informações para a ação. Por exemplo, selecione a ação **RunHiveScript** .

    ![Informações de ação](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Pode ver os detalhes para a ação, incluindo uma ligação para o **URL de consola**, que podem ser utilizados para ver informações de JobTracker para a tarefa.

##<a name="scheduling-jobs"></a>Agendar tarefas

O coordenador permite-lhe especificar um início, fim e frequência de ocorrência de tarefas, para que possa ser agendados para determinados vezes.

Para definir uma agenda para o fluxo de trabalho, utilize os passos seguintes:

1. Utilize o seguinte para criar um novo ficheiro chamado **coordinator.xml**:

        nano coordinator.xml

    Utilize o seguinte como o conteúdo do ficheiro:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
          <action>
            <workflow>
              <app-path>${workflowPath}</app-path>
            </workflow>
          </action>
        </coordinator-app>

    Nota Esta opção utiliza `${...}` variáveis que serão substituídas pelos valores na definição de tarefa. As variáveis são:

    * **${coordFrequency}**: tempo entre instâncias do trabalho em execução
    * **${coordStart}**: A tarefa de hora de início
    * **${coordEnd}**: A hora de fim de tarefa
    * **${coordTimezone}**: tarefas de coordenador são num fixo fuso horário com sem hora de Verão (normalmente representada utilizando UTC). Este fuso horário é referido como o "Oozie processamento fuso horário"
    * **${wfPath}**: O caminho para o workflow.xml

2. Utilize CTRL + X, em seguida, **Y** e **Enter** para guardar o ficheiro.

3. Utilize o seguinte procedimento para copiá-lo para o directório de trabalho para esta tarefa:

        hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml

4. Utilize o seguinte para modificar o ficheiro **job.xml** :

        nano job.xml

    Efetue as seguintes alterações:

    * Alterar `<name>oozie.wf.application.path</name>` para `<name>oozie.coord.application.path</name>`. Isto indica Oozie para executar o ficheiro de coordenador em vez do ficheiro de fluxo de trabalho

    * Adicione o seguinte, que será conjuntos de uma variável utilizada na coordinator.xml para apontar para a localização da workflow.xml:

            <property>
              <name>workflowPath</name>
              <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
            </property>

        Substitua os valores de **mycontainer** e **mystorageaccount** os valores utilizados no outras entradas no ficheiro job.xml.

    * Adicione o seguinte, que definem o início, fim e frequência para utilizar para o ficheiro coordinator.xml:

            <property>
              <name>coordStart</name>
              <value>2015-06-25T12:00Z</value>
            </property>

            <property>
              <name>coordEnd</name>
              <value>2015-06-27T12:00Z</value>
            </property>

            <property>
              <name>coordFrequency</name>
              <value>1440</value>
            </property>

            <property>
              <name>coordTimezone</name>
              <value>UTC</value>
            </property>

        Estes definem a hora de início para 12:00 PM 25º de Junho de 2015, a hora de fim para 27th de Junho de 2015 e o intervalo para executar esta tarefa para diariamente (a frequência for em minutos, por isso, 24 horas x 60 minutos = 1440 minutos.) Por fim, o fuso horário está definido para UTC.

5. Utilize CTRL + X, em seguida, **Y** e **Enter** para guardar o ficheiro.

6. Para executar a tarefa, utilize o seguinte comando:

        oozie job -config job.xml -run

    Isto irá submeter e iniciar a tarefa.

7. Se visite Oozie Web IU e selecione o separador **Tarefas do coordenador** , deverá informações semelhantes ao seguinte:

    ![separador de tarefas do coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    Tenha em atenção a **Ocorrência seguinte** entrada; Este é quando será executada a tarefa seguinte.

8. Semelhante à tarefa de fluxo de trabalho anteriores, selecionar a entrada de projeto na web IU serão apresentadas informações em da tarefa:

    ![Informações de tarefa coordenador](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    Tenha em atenção que este mostra apenas com êxito é executado da tarefa, ações não individuais dentro do fluxo de trabalho agendada. Para ver que, selecione uma das entradas de **ação** . Isto vai mostrar informações semelhantes às obtidos para a tarefa de fluxo de trabalho anterior.

    ![Informações de ação](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

##<a name="troubleshooting"></a>Resolução de problemas

Quando a resolução de problemas com tarefas de Oozie, a IU Oozie é muito útil tal como permite-lhe visualizar facilmente ambos os registos de Oozie, bem como ligações aos registos de JobTracker para tarefas MapReduce como ramo de consultas. Em geral, o padrão para resolver o problema deverá ser:

1. Veja a tarefa na IU do Oozie Web.

2. Se existir um erro ou falha para uma acção específica, selecione a ação para ver se o campo de **Mensagem de erro** fornece mais informações sobre a falha.

3. Se estiver disponível, utilize o URL da ação para ver mais detalhes (como registos JobTracker,) para a ação.

Seguem-se os erros específicos que poderá encontrar e como para os resolver.

###<a name="ja009-cannot-initialize-cluster"></a>JA009: Não é possível iniciar cluster

**Sintomas**: irá alterar o estado da tarefa para **suspenso**. Detalhes para a tarefa irão mostrar o estado de RunHiveScript como **START_MANUAL**. Selecionar a ação revelarem a seguinte mensagem de erro:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: os endereços de WASB o utilizados no ficheiro **job.xml** não contenham o contentor de armazenamento ou o nome de conta de armazenamento. O formato de endereço WASB tem de ser `wasbs://containername@storageaccountname.blob.core.windows.net`.

**Resolução**: alterar os endereços WASB utilizados pela tarefa.

###<a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie não é permitida para representar &lt;utilizador >

**Sintomas**: irá alterar o estado da tarefa para **suspenso**. Detalhes para a tarefa irão mostrar o estado de RunHiveScript como **START_MANUAL**. Selecionar a ação revelarem a seguinte mensagem de erro:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: as definições de permissão atual não permitem Oozie representar a conta de utilizador especificado.

**Resolução**: Oozie tem permissão para representar os utilizadores do grupo de **utilizadores** . Utilizar o `groups USERNAME` para ver os grupos que a conta de utilizador é membro. Se o utilizador não é um membro do grupo de **utilizadores** , utilize o seguinte comando para adicionar o utilizador ao grupo:

    sudo adduser USERNAME users

> [AZURE.NOTE] Poderá demorar vários minutos antes de HDInsight reconhece que o utilizador foi adicionado ao grupo.

###<a name="launcher-error-sqoop"></a>Iniciador de erro (Sqoop)

**Sintomas**: O estado da tarefa será alterado para **KILLED**. Detalhes para a tarefa irão mostrar o estado de RunSqoopExport como um **erro**. Selecionar a ação revelarem a seguinte mensagem de erro:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: Sqoop é não é possível carregar o controlador da base de dados necessário para aceder a base de dados.

**Resolução**: quando utilizar Sqoop a partir de uma tarefa de Oozie, tem de incluir o controlador de base de dados com os outros recursos (como workflow.xml,) utilizado pela tarefa.

Também tem de referenciar o arquivo que contém o controlador de base de dados a partir do `<sqoop>...</sqoop>` secção da workflow.xml.

Por exemplo, para a tarefa neste documento, teria de utilizar os seguintes passos:

1. Copie o ficheiro sqljdbc4.1.jar ao diretório /tutorials/useoozie:

         hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar

2. Modificar workflow.xml para adicionar o seguinte numa nova linha acima `</sqoop>`:

        <archive>sqljdbc41.jar</archive>

##<a name="next-steps"></a>Próximos passos
Neste tutorial, aprendeu como definir um fluxo de trabalho Oozie e como executar uma tarefa de Oozie. Para saber mais sobre como trabalhar com HDInsight, consulte os artigos seguintes:

- [Utilizar baseados no tempo Oozie o coordenador com HDInsight][hdinsight-oozie-coordinator-time]
- [Carregar dados para trabalhos de Hadoop no HDInsight][hdinsight-upload-data]
- [Utilizar Sqoop com Hadoop no HDInsight][hdinsight-use-sqoop]
- [Utilizar ramo com Hadoop no HDInsight][hdinsight-use-hive]
- [Utilizar porco com Hadoop no HDInsight][hdinsight-use-pig]
- [Desenvolver Java MapReduce programas para HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
