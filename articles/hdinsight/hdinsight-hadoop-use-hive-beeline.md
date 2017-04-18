<properties
   pageTitle="Utilizar Beeline para trabalhar com ramo de HDInsight (Hadoop) | Microsoft Azure"
   description="Saiba como utilizar SSH para ligar a um cluster de Hadoop no HDInsight e, em seguida, submeter consultas ramo de forma interativa utilizando Beeline. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC."
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
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Utilizar ramo com Hadoop no HDInsight com Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este artigo vai aprender a utilizar Shell seguro (SSH) para ligar a um cluster de baseado em Linux HDInsight e, em seguida, submeta consultas ramo de forma interativa utilizando a ferramenta de linha de comandos [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [AZURE.NOTE] Beeline utiliza JDBC para ligar ao ramo. Para mais informações sobre como utilizar JDBC com ramo, consulte o artigo [ligar ao ramo no Azure HDInsight a utilizar o controlador de Hive JDBC](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

* Um Hadoop baseado em Linux num cluster de HDInsight.

* Um cliente SSH. Sistema operativo Mac, Unix e Linux devem vêm com um cliente SSH. Utilizadores do Windows tem de transferir um cliente, como [betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Ligar-se a SSH

Ligar para o nome de domínio completamente qualificado (FQDN) do seu cluster HDInsight utilizando o comando SSH. O FQDN será o nome que lhe atribuiu cluster, em seguida, **. azurehdinsight.net**. Por exemplo, o seguinte procedimento seria ligar a um cluster denominado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se lhe fornecidos uma chave de certificado para SSH autenticação** quando criou o cluster HDInsight, poderá ter especificar a localização da chave privada no sistema de cliente:

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se lhe fornecidos uma palavra-passe para autenticação SSH** quando criou o cluster HDInsight, terá de fornecer a palavra-passe quando lhe for pedido.

Para mais informações sobre como utilizar SSH com HDInsight, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight do Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Betumes (clientes baseados no Windows)

Windows não fornece um cliente SSH incorporado. Recomendamos que utilize **betumes**, que podem ser transferidos das [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para mais informações sobre como utilizar betumes, consulte o artigo [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Utilize o comando Beeline

1. Quando estabelecida, utilize o seguinte para começar a Beeline:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Isto irá iniciar o cliente Beeline e ligue ao url JDBC. Aqui, `localhost` é utilizado uma vez que HiveServer2 é executado em ambos os nós de cabeça no cluster, podemos estiver a executar o Beeline diretamente na headnode principal.
    
    Assim que o comando for concluída, irá chegar um `jdbc:hive2://localhost:10001/>` linha de comandos.

3. Comandos de beeline normalmente começam com um `!` caráter, por exemplo `!help` apresenta a ajuda. No entanto a `!` com frequência pode ser omitido. Por exemplo, `help` também irá funcionar.

    Se visualizar a ajuda, irá reparar `!sql`, que é utilizado para executar HiveQL declarações. No entanto, HiveQL é por isso, geralmente utilizada que pode omitir anterior `!sql`. Duas seguintes declarações tem exatamente os mesmos resultados; apresentar as tabelas que se encontra-se disponível através do ramo:
    
        !sql show tables;
        show tables;
    
    No novo cluster, deve ser indicada apenas uma tabela: __hivesampletable__.

4. Utilize o seguinte para apresentar o esquema para o hivesampletable:

        describe hivesampletable;
        
    Isto irá devolver as seguintes informações:
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Esta ação apresentará as colunas na tabela. Enquanto podemos conseguiu executar algumas consultas contra estes dados, vamos em vez disso, crie uma nova tabela para demonstrar como carregar os dados para ramo e aplicar um esquema.
    
5. Introduza as seguintes instruções para criar uma nova tabela denominada **log4jLogs** utilizando dados de exemplo fornecidos com o cluster HDInsight:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Estas instruções executar as seguintes ações:

    * **LARGAR tabela** - elimina a tabela e o ficheiro de dados, caso já existe na tabela.
    * **Criar tabela externa** - cria uma nova tabela «externa» no ramo. Tabelas externas armazenam apenas a definição da tabela no ramo. Os dados falta na localização original.
    * **Formato de linha** - indica Hive a forma como os dados estão formatados. Neste caso, os campos no registo de cada são separados por um espaço.
    * **Localização de TEXTFILE como ARMAZENADOS** - indica Hive onde estão os dados armazenados (o directório de exemplo de dados) e que a mesma está armazenada como texto.
    * **SELECIONE** - seleciona uma contagem de todas as linhas onde coluna **t4** contém o valor **[erro]**. Isto deve devolver um valor de **3** como existem três linhas que contêm este valor.
    * **INPUT__FILE__NAME como '%.log'** - indica Hive que recomendamos só deve devolver dados a partir de ficheiros que terminem em. log. Normalmente, apenas terá de dados com o mesmo esquema dentro da mesma pasta quando consultar com ramo, no entanto, este ficheiro de registo de exemplo é armazenado com outros formatos de dados.

    > [AZURE.NOTE] Tabelas externas devem ser utilizadas quando pretende os dados subjacentes ser atualizados por uma fonte externa, tal como um processo de carregamento de dados automatizado, ou por outra MapReduce operação, mas pretende sempre ramo de consultas para utilizar os dados mais recentes.
    >
    > Largar uma tabela externa é que **não** eliminar os dados, apenas a definição da tabela.
    
    O resultado deste comando deve ser semelhante ao seguinte:
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Para sair do Beeline, utilize `!quit`.

##<a id="file"></a>Executar um ficheiro de HiveQL

Beeline podem também ser utilizados para executar um ficheiro que contenha declarações de HiveQL. Utilize os passos seguintes para criar um ficheiro, em seguida, executá-la com Beeline.

1. Utilize o seguinte comando para criar um novo ficheiro chamado __query.hql__:

        nano query.hql
        
2. Quando o editor é aberto, utilize o seguinte como o conteúdo do ficheiro. Esta consulta irá criar uma nova tabela «interna» denominada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Estas instruções executar as seguintes ações:

    * **Criar a tabela se não existe** - cria uma tabela, se ainda não existir. Uma vez que não é utilizada a palavra-chave **externa** , este é uma tabela interna, que está armazenada no armazém de ramo de dados e é gerida completamente por ramo.
    * **ARMAZENADOS ORC como** - armazena os dados no formato de otimizado linha colunar (ORC). Este é um formato de altamente optimizado e eficaz para armazenar dados ramo.
    * SUBSTITUIR **INSERT... SELECIONE** - seleciona linhas da tabela **log4jLogs** que contêm **[erro]**, em seguida, insere os dados na tabela **errorLogs** .
    
    > [AZURE.NOTE] Ao contrário de tabelas externas, largar uma tabela interna irá eliminar dados subjacentes.
    
3. Para guardar o ficheiro, utilize __Ctrl__+ ___X__, em seguida, introduza __Y__e, por fim, __Enter__.

4. Utilize o seguinte procedimento para executar o ficheiro utilizando Beeline. Substitua o __nome do anfitrião__ com o nome obtido anterior para o nó cabeça e a __palavra-passe__ com a palavra-passe da conta de administrador:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] O `-i` parâmetro é iniciado Beeline, é executado as instruções no ficheiro query.hql e permanece na Beeline na `jdbc:hive2://localhost:10001/>` linha de comandos. Também pode executar um ficheiro utilizando a `-f` parâmetro, que regressa à festa depois do ficheiro foi processado.

5. Para verificar que a tabela de **errorLogs** foi criada, utilize a seguinte instrução para devolver todas as linhas de **errorLogs**:

        SELECT * from errorLogs;

    Deve ser devolvido três linhas de dados, todos os que contenham **[erro]** na coluna t4:
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Mais informações sobre a conectividade de Beeline

Os passos neste documento utilização `localhost` para ligar à HiveServer2 em execução na headnode cluster. Enquanto também pode utilizar o nome do anfitrião ou o nome de domínio completamente qualificado da headnode aqueles requerem passos adicionais para o processo (passos para localizar o nome do anfitrião ou o FQDN). Utilizar `localhost` é suficiente ao utilizar Beeline a partir do headnode.

Se tiver um nó margem no seu cluster com Beeline instalado, terá de utilizar o nome do anfitrião ou o FQDN da headnode para ligar.

Se tiver instalado num cliente fora do seu cluster de Beeline, pode ligar utilizando o seguinte comando. Substitua o __nome de cluster__ com o nome do seu cluster HDInsight. Substitua __palavra-passe__ a palavra-passe da conta de administrador (início de sessão de HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Tenha em atenção que seja diferente quando em execução diretamente num headnode ou a partir de um nó limite dentro do cluster parâmetros/URI. Esta é uma vez que ligar ao cluster da internet utiliza um gateway público que encaminha o tráfego através da porta 443. Além disso, vários outros serviços são expostos através do gateway público na porta 443, por isso o URI é diferente do AnonymousUserName quando ligar diretamente. Ao ligar a partir da internet, que também tem autenticar a sessão ao fornecer a palavra-passe.

##<a id="summary"></a><a id="nextsteps"></a>Próximos passos

Como pode ver, o comando Beeline fornece uma forma fácil de forma interativa executar consultas de ramo num HDInsight cluster.

Para obter informações gerais sobre ramo no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se estiver a utilizar Tez com ramo, consulte os seguintes documentos para as informações de depuração:

* [Utilizar a IU Tez no HDInsight baseados no Windows](hdinsight-debug-tez-ui.md)

* [Utilizar a vista de Ambari Tez no baseado em Linux HDInsight](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

