<properties 
    pageTitle="Analisar os dados de atrasos em voos com ramo de baseado em Linux HDInsight | Microsoft Azure" 
    description="Saiba como utilizar Hive para analisar dados de voo no baseado em Linux HDInsight, em seguida, exporte os dados para a base de dados do SQL utilizando Sqoop." 
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

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analisar os dados de atrasos em voos utilizando ramo no HDInsight

Saiba como analisar os dados de atrasos em voos com ramo no baseado em Linux HDInsight, em seguida, exporte os dados para a base de dados do SQL Azure utilizando Sqoop.

> [AZURE.NOTE] Enquanto peças individuais deste documento podem ser utilizadas com clusters de HDInsight baseados no Windows (Python e ramo por exemplo), muitos passos são específicos a clusters baseado em Linux. Para obter os passos que funcionam com um cluster baseados no Windows, consulte o artigo [Analisar os dados de atrasos em voos utilizando ramo no HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Cluster de um HDInsight__. Consulte o artigo [começar a utilizar o Hadoop com ramo no HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md) para obter passos sobre como criar um novo cluster de baseado em Linux HDInsight.

- __Base de dados azure SQL__. Irá utilizar uma base de dados do Azure SQL como um arquivo de dados de destino. Se já não tem uma base de dados do SQL, consulte o artigo [tutorial de base de dados SQL: criar uma base de dados do SQL no minutos](../sql-database/sql-database-get-started.md).

- __Clip azure__. Se não tiver instalado o clip do Azure, consulte o artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md) para obter os passos.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Transfira os dados de voo

1. Navegue para a [pesquisa e tecnologia inovadora administração, Instituto de transporte estatísticas][rita-website].
2. Na página, selecione os seguintes valores:

  	| Nome | Valor |
  	| ---- | ---- |
  	| Ano de filtro | 2013 |
  	| Filtrar período | Janeiro |
  	| Campos | Ano, Datadovoo, UniqueCarrier, Carrier, FlightNum, OriginAirportID, origem, OriginCityName, OriginState, DestAirportID, destino, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Desmarque todos os outros campos |

3. Clique em **Transferir**. 

##<a name="upload-the-data"></a>Carregar os dados

1. Utilize o seguinte comando para carregar o ficheiro zip para o nó do cabeça cluster HDInsight:

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Substitua o __nome de ficheiro__ com o nome do ficheiro zip. Substitua __nomedeutilizador__ login SSH para o cluster HDInsight. Substitua o nome de cluster com o nome do HDInsight cluster.
    
    > [AZURE.NOTE] Se utilizar uma palavra-passe para autenticar o início de sessão SSH, ser-lhe-á pedido a palavra-passe. Se utilizou uma chave pública, poderá ter utilizar o `-i` parâmetro e especifique o caminho para a chave privada correspondente. Por exemplo `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Assim que tiver concluído a carregar, ligar-se ao cluster utilizando SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Para obter mais informações sobre como utilizar SSH com baseado em Linux HDInsight, consulte os artigos seguintes:
    
    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Quando estabelecida, utilize o seguinte procedimento para deszipar o ficheiro. zip:

        unzip FILENAME.zip
    
    Isto irá extrair um ficheiro. csv que seja aproximadamente 60MB de tamanho.
    
4. Utilize o seguinte para criar um novo directório no WASB (o arquivo de dados distribuídos utilizado pelo HDInsight) e copie o ficheiro:

    hdfs dfs - mkdir -p /tutorials/flightdelays/data hdfs dfs-colocar FILENAME.csv/tutoriais flightdelays/dados /
    
##<a name="create-and-run-the-hiveql"></a>Criar e executar o HiveQL

Utilize os passos seguintes para importar dados a partir do ficheiro CSV para uma tabela Hive denominada __atrasos__.

1. Utilize o seguinte para criar e editar um novo ficheiro chamado __flightdelays.hql__:

        nano flightdelays.hql
        
    Utilize o seguinte como o conteúdo deste ficheiro:
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. Utilize __Ctrl + X__e __Y__ para guardar o ficheiro.

3. Utilize o seguinte para começar a ramo e execute o ficheiro __flightdelays.hql__ :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] Neste exemplo, `localhost` é utilizado uma vez que estão ligadas a nó cabeça do cluster HDInsight, que é onde HiveServer2 está em execução.

4. Utilize o seguinte comando para abrir uma sessão de Beeline interativa:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Quando receber a `jdbc:hive2://localhost:10001/>` pedir, utilize o seguinte para obter dados a partir dos dados de atraso voo importados.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Isto irá obter uma lista de cidades que ocorreu ao atrasos Meteorologia, juntamente com o tempo de atraso média e guardá-lo para `/tutorials/flightdelays/output`. Mais tarde, Sqoop vai ler os dados a partir desta localização e exportá-lo para a base de dados do SQL Azure.

6. Para sair do Beeline, introduza `!quit` na linha de comandos.

## <a name="create-a-sql-database"></a>Criar uma base de dados SQL

Se já tiver uma base de dados do SQL, terá de obter o nome do servidor. Pode encontrar esta no [Portal do Azure](https://portal.azure.com) selecionando __Bases de dados SQL__e, em seguida, filtragem sobre o nome da base de dados que pretende utilizar. O nome do servidor estiver listado na coluna __SERVER__ .

Se ainda não tiver uma base de dados do SQL, utilize as informações na [tutorial de base de dados SQL: criar uma base de dados do SQL no minutos](../sql-database/sql-database-get-started.md) para criar uma. Terá de guardar o nome do servidor utilizado para a base de dados.

##<a name="create-a-sql-database-table"></a>Criar uma tabela de base de dados SQL

> [AZURE.NOTE] Existem várias formas para ligar à base de dados do SQL para criar uma tabela. Os passos seguintes utilizam [FreeTDS](http://www.freetds.org/) do HDInsight cluster.

1. Utilizar SSH para ligar ao cluster baseado em Linux HDInsight e executar os passos seguintes a partir da sessão SSH.

3. Utilize o seguinte comando para instalar FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Depois de ter sido instalado FreeTDS, utilize o seguinte comando para ligar ao servidor de base de dados SQL. Substitua o nome do servidor de base de dados SQL __nomedoservidor__ . Substitua __adminLogin__ e __adminPassword__ o início de sessão para a base de dados SQL. Substitua __databaseName__ com o nome da base de dados.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Receberá saída semelhante ao seguinte:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Na `1>` pedido, introduza as linhas seguintes:

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Quando o `GO` declaração introduzida, serão avaliadas as declarações anterior. Esta opção criará uma nova tabela denominada __atrasos__, com um índice agrupado (obrigatório base de dados SQL.)

    Utilize o seguinte para confirmar que foi criada a tabela:

        SELECT * FROM information_schema.tables
        GO

    Deverá visualizar saída semelhante ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Introduza `exit` na `1>` perguntar se quer saia do utilitário tsql.
    
##<a name="export-data-with-sqoop"></a>Exportar dados com Sqoop

2. Utilize o seguinte comando para verificar que Sqoop podem ver a sua base de dados do SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Isto deve devolver uma lista de bases de dados, incluindo a base de dados que criou a tabela de atrasos nas versões anteriores.

3. Utilize o seguinte comando para exportar dados do hivesampletable à tabela mobiledata:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    Isto indica Sqoop para ligar à base de dados SQL, para a base de dados que contém a tabela de atrasos e exportar dados a partir do wasbs: / / / tutoriais/flightdelays/saída (onde podemos armazenados o resultado da consulta ramo versões anteriores,) para a tabela de atrasos.

4. Após completa o comando, utilize o seguinte para ligar à base de dados utilizando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Quando estabelecida, utilize as instruções seguintes para verificar que foram exportados os dados para a tabela mobiledata:
    
        SELECT * FROM delays
        GO

    Deverá ver uma lista de dados na tabela. Tipo de `exit` para sair o utilitário tsql.

##<a id="nextsteps"></a>Próximos passos

Agora compreender como carregar um ficheiro para o armazenamento de Blobs do Azure, como preencher uma tabela Hive utilizando os dados a partir do armazenamento de Blobs do Azure, como executar consultas de ramo e como utilizar Sqoop para exportar dados do HDFS para uma base de dados do Azure SQL. Para saber mais, consulte os artigos seguintes:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Utilizar ramo com HDInsight][hdinsight-use-hive]
* [Utilizar Oozie com HDInsight][hdinsight-use-oozie]
* [Utilizar Sqoop com HDInsight][hdinsight-use-sqoop]
* [Utilizar porco com HDInsight][hdinsight-use-pig]
* [Desenvolver Java MapReduce programas para HDInsight][hdinsight-develop-mapreduce]
* [Desenvolver Hadoop Python transmissão de programas para HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
