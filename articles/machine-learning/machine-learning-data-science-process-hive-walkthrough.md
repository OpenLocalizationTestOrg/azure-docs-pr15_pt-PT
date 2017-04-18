<properties
    pageTitle="O processo de ciências de dados de equipa em ação: utilizar Hadoop clusters | Microsoft Azure"
    description="Utilizar o processo de ciências de dados de equipa para um cenário de ponto a ponto que utilizem um cluster de HDInsight Hadoop para criar e implementar um modelo de utilizando um conjunto de dados disponível ao público."
    services="machine-learning,hdinsight"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />


# <a name="the-team-data-science-process-in-action-using-hdinsight-hadoop-clusters"></a>O processo de ciências de dados de equipa em ação: utilizar HDInsight Hadoop clusters

Neste tutorial, utilizamos a [Equipa dados ciência processo (TDSP)](data-science-process-overview.md) num cenário de ponto a ponto utilizando um [cluster de Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e dados de engenharia de conjunto de dados de [Viagens de veículo pt](http://www.andresmh.com/nyctaxitrips/) disponível ao público de funcionalidades e para baixo os dados de exemplo. Modelos de dados foram criados com o Azure máquina formação para processar binárias e multiclass classificação e regressão aspeto do Office tarefas.

Para obter instruções sobre que mostra como lidar com um conjunto de dados (1 terabyte) maior para um cenário semelhante com HDInsight Hadoop clusters para processamento de dados, consulte o artigo [Processo de ciências de dados de equipa - utilizando Azure HDInsight Hadoop Clusters de um conjunto de dados 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md).

Também é possível utilizar um bloco de notas IPython para realizar as tarefas apresentadas guiadas utilizando o conjunto de dados 1 TB. Os utilizadores que gostaria de experimentar esta abordagem, deverão consultar o tópico de [instruções Criteo utilizando uma ligação Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Descrição do conjunto de dados do pt veículo viagens

Os dados de viagem de veículo pt são de cerca de 20GB de ficheiros comprimido valores separados por vírgulas (CSV) (não comprimido GB de ~ 48), que inclua mais de 173 milhão individuais viagens e as tarifas pagos para cada viagem. Cada registo de viagem inclui a recolha e remessa a localização e hora, ataque à anónimos (controlador) número de licenças e número de medallion (id exclusivo do veículo). Os dados abrange viagens de todos os do ano 2013 e são fornecidos em conjuntos de dados de duas seguintes para cada mês:

1. Os ficheiros CSV 'trip_data' contêm detalhes de viagem, como o número de passageiros, recolha e dropoff pontos, duração de viagem e comprimento de viagem. Aqui estão alguns registos de exemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Os ficheiros CSV 'trip_fare' contêm detalhes de tarifa paga para cada viagem, tal como o tipo de pagamento, quantidade tarifa, sobretaxa e impostos, sugestões e portagens e o montante total pago. Aqui estão alguns registos de exemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para participar viagem\_dados e viagem\_tarifa é composta pelos campos: medallion, ataque à\_licença e recolha\_datetime.

Para obter todos os detalhes pertinentes para uma viagem específica, é suficiente participar com três chaves de: "medallion", "ataque\_licença" e "recolha\_datetime".

Descrevemos algumas mais detalhes dos dados quando podemos armazená-los em tabelas do ramo brevemente.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Quando a aproximação de dados, determinar o tipo de previsões que pretende fazer com base na sua análise ajuda-o a clarificar as tarefas que irá precisar de incluir do processo.
Eis três exemplos de problemas de previsão que é que podemos abordar neste tutorial cuja composição baseia-o *Sugestão\_quantidade*:

1. **Classificação binária**: prever ou não uma sugestão foi paga uma viagem, ou seja, um *Sugestão\_quantidade* que for maior que $0 é um exemplo positivo, enquanto um *Sugestão\_quantidade* de €0 é um exemplo negativo.

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0

2. **Classificação multiclass**: para prever o intervalo de montantes de sugestão pagos relativamente a viagem. Vamos dividir o *Sugestão\_quantidade* para cinco posições ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tarefa de regressão**: para prever a quantidade de sugestão paga uma viagem.  


## <a name="setup"></a>Configurar um cluster de HDInsight Hadoop de análise avançadas

>[AZURE.NOTE] Isto é, normalmente, uma tarefa do **administrador** .

Pode configurar um ambiente do Azure de análise avançadas que utiliza um cluster de HDInsight em três passos:

1. [Criar uma conta de armazenamento](../storage/storage-create-storage-account.md): esta conta de armazenamento é utilizada para armazenar dados no armazenamento de Blobs do Azure. Os dados utilizados no HDInsight clusters residem também aqui.

2. [Personalizar o Azure HDInsight Hadoop clusters para a tecnologia e o processo de análise avançadas](machine-learning-data-science-customize-hadoop-cluster.md). Este passo cria uma Hadoop de HDInsight Azure cluster de 64 bits Anaconda Python 2.7 instalado em todos os nós. Existem dois passos importantes a não esquecer enquanto personalizar o seu cluster HDInsight.

    * Lembre-se ligar a conta de armazenamento que criou no passo 1 com o seu cluster HDInsight durante criação do mesmo. Esta conta de armazenamento é utilizada para aceder aos dados que são processados dentro do cluster.

    * Depois de criado o cluster, Active o acesso remoto para o nó do cluster de cabeça. Navegue para o separador **configuração** e clique em **Ativar remoto**. Este passo Especifica as credenciais de utilizador utilizadas para o início de sessão remoto.

3. [Criar uma área de trabalho Azure máquina de formação](machine-learning-create-workspace.md): nesta máquina de Azure formação a área de trabalho é utilizada para construir modelos de aprendizagem automática. Esta tarefa é dirigida depois de concluir uma informações detalhadas inicial dos dados e para baixo amostragem utilizando o cluster HDInsight.

## <a name="getdata"></a>Obter os dados de uma origem de pública

>[AZURE.NOTE] Isto é, normalmente, uma tarefa do **administrador** .

Para obter o conjunto de dados de [Viagens de veículo pt](http://www.andresmh.com/nyctaxitrips/) da respetiva localização pública, pode utilizar qualquer um dos métodos descritos na [Mover dados de armazenamento de Blobs do Azure e para](machine-learning-data-science-move-azure-blob.md) copiar os dados para o seu computador.

Aqui vamos descrevem como utilizar AzCopy para transferir os ficheiros que contêm dados. Para transferir e instalar AzCopy siga as instruções na [Introdução ao utilitário de linha de comandos do AzCopy](../storage/storage-use-azcopy.md).

1. A partir de uma janela de linha de comandos, emita os seguintes comandos AzCopy, substituir *< path_to_data_folder >* com o destino pretendido:


        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. Quando concluir a cópia, um total de 24 ficheiros compactados estão na pasta dados escolhida. Deszipe os ficheiros transferidos para o mesmo directório no seu computador local. Anote a pasta onde residem os ficheiros não comprimidos. Será referida esta pasta como a *< caminho\_para\_unzipped_data\_ficheiros\> * é o que se segue.


## <a name="upload"></a>Carregar os dados para o contentor predefinido do Azure HDInsight Hadoop cluster de

>[AZURE.NOTE] Isto é, normalmente, uma tarefa do **administrador** .

Nos seguintes comandos AzCopy, substitua os seguintes parâmetros os valores reais que especificou quando criar o cluster Hadoop e unzipping os ficheiros de dados.

* ***& #60; path_to_data_folder >*** diretório (juntamente com o caminho) no seu computador que contêm os ficheiros de dados deszipado  
* ***& #60; nome de conta de armazenamento de Hadoop cluster >*** da conta de armazenamento associada seu cluster HDInsight
* ***& #60; contentor predefinido de Hadoop cluster >*** o contentor predefinido utilizado pelo seu cluster. Tenha em atenção que o nome do contentor predefinido é normalmente o mesmo nome que o próprio cluster. Por exemplo, se o cluster for chamado "abc123.azurehdinsight.net", o contentor predefinido é abc123.
* ***& #60; chave da conta de armazenamento >*** a chave para a conta de armazenamento utilizada pelo seu cluster

A partir de uma linha de comandos ou numa janela do Windows PowerShell no seu computador, execute os seguintes comandos AzCopy duas.

Este comando carrega os dados de viagem de diretório ***nyctaxitripraw*** no contentor predefinido do Hadoop cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Este comando carrega os dados de tarifa de diretório ***nyctaxifareraw*** no contentor predefinido do Hadoop cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Os dados deverão agora na armazenamento de Blobs do Azure e pronto para ser consumida dentro do cluster HDInsight.

## <a name="#download-hql-files"></a>Iniciar sessão no nó do Hadoop cluster de cabeça e e preparar para uma análise de dados exploratória

>[AZURE.NOTE] Isto é, normalmente, uma tarefa do **administrador** .

Para aceder o nó cabeça do cluster para análise de dados exploratória e para baixo amostragem dos dados, siga o procedimento descrito no [Access de cabeça de Hadoop Cluster de nó](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

Neste tutorial, utilizamos principalmente consultas escritas [Hive](https://hive.apache.org/), um idioma de consulta SQL semelhantes, para efetuar explorations dados prévia. As consultas de ramo são armazenadas em ficheiros de .hql. Vamos, em seguida, para baixo de exemplo estes dados ao ser utilizados no Azure máquina formação para a criação de modelos.

Para preparar o cluster para análise de dados exploratória, podemos transferir os ficheiros de .hql que contém os scripts ramo relevantes do [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) para um diretório local (C:\temp) no nó cabeça. Para fazer isto, abra a **linha de comandos** de dentro de cabeça nó do cluster e emitir dois seguintes comandos:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Estes duas comandos irão transferir todos os ficheiros de .hql necessários neste tutorial ao diretório local ***C:\temp & #92;*** no nó de cabeça.

## <a name="#hive-db-tables"></a>Criar ramo de base de dados e tabelas divididas por mês

>[AZURE.NOTE] Isto é, normalmente, uma tarefa do **administrador** .

Vamos agora está prontos para criar tabelas de Hive para o conjunto de dados de veículo nosso pt.
No cabeça nó do Hadoop cluster, abra a ***linha de comandos Hadoop*** no ambiente de trabalho do nó cabeça e introduza o diretório de ramo introduzindo o comando

    cd %hive_home%\bin

>[AZURE.NOTE] **Executar todos os comandos de ramo neste tutorial a partir da posição acima do ramo / pedido de diretório. Isto irá encarregam dos problemas caminho automaticamente. Utilizamos os termos "Ramo diretório linha de comandos", "ramo posição / linha de comandos do diretório" e "Hadoop linha de comandos" alternadamente destas instruções.**

A partir da linha de comandos do diretório de ramo, introduza o seguinte comando no Hadoop linha de comandos do nó de cabeça para submeter a consulta Hive para criar tabelas e ramo de base de dados:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Aqui é o conteúdo da ***C:\temp\sample\_ramo\_criar\_db\_e\_tables.hql*** ficheiro que cria ramo da base de dados ***nyctaxidb*** e tabelas de ***viagem*** e ***tarifa***.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Este script ramo cria duas tabelas:

* a tabela "viagem" contém detalhes de viagem de cada percurso (detalhes do controlador, escolha tempo, distância de viagem e vezes)
* a tabela "tarifa" contém detalhes tarifa (quantidade tarifa, quantidade de sugestão, portagens e sobretaxas).

Se necessitar de assistência adicional com estes procedimentos ou pretende investigar alternativos, consulte a secção [Submeter Hive consultas diretamente a partir da linha de comandos Hadoop ](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="#load-data"></a>Carregar os dados a tabelas ramo pela partições

>[AZURE.NOTE] Isto é, normalmente, uma tarefa do **administrador** .

O conjunto de dados de veículo pt tem uma partição singulares por mês, que utilizamos para ativar os tempos de processamento e consulta mais rápidos. Os comandos do PowerShell abaixo (emitidos do diretório ramo utilizando a **linha de comandos Hadoop**) carregar os dados com as tabelas de ramo "viagem" e "tarifa" divididos por mês.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

O *exemplo\_ramo\_carregar\_dados\_por\_partitions.hql* ficheiro contém os seguintes comandos **carregar** .

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Note que um número de consultas de ramo que utilizamos aqui no processo de informações detalhadas envolvam está à procura de apenas uma única partição ou apenas algumas das partições. Mas estas consultas podem ser executadas ao longo dos dados inteiros.

### <a name="#show-db"></a>Mostrar as bases de dados do HDInsight Hadoop cluster

Para mostrar as bases de dados criadas num cluster de HDInsight Hadoop dentro da janela de linha de comandos Hadoop, execute o seguinte comando na linha de comandos do Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Mostrar as tabelas de ramo na base de dados nyctaxidb

Para mostrar as tabelas na base de dados nyctaxidb, execute o seguinte comando na linha de comandos do Hadoop:

    hive -e "show tables in nyctaxidb;"

Pode confirmamos que as tabelas são divididas por emitir o comando abaixo:

    hive -e "show partitions nyctaxidb.trip;"

O resultado esperado é mostrado abaixo:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Da mesma forma, podemos pode Certifique-se de que a tabela tarifa é dividida por emitir o comando abaixo:

    hive -e "show partitions nyctaxidb.fare;"

O resultado esperado é mostrado abaixo:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Informações detalhadas de dados e engenharia de funcionalidade no ramo

>[AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

A funcionalidade tarefas para os dados carregados para as tabelas de ramo de engenharia e informações detalhadas de dados podem ser feitos através de consultas de ramo. Eis alguns exemplos dessas tarefas que iremos guiá-lo nesta secção:

- Ver os registos de 10 principais em ambas as tabelas.
- Explore as distribuições de dados de alguns campos no windows variados do tempo.
- Investigar a qualidade dos dados dos campos longitude e latitude.
- Gerar etiquetas de classificação binário e multiclass com base na **Sugestão\_quantidade**.
- Gera funcionalidades por computação a distância entre viagem direta.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Informações detalhadas: Ver os registos de 10 principais em viagem de tabela

>[AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Para ver o aspeto dos dados, podemos examine 10 registos de cada tabela. Execute as duas seguintes consultas separadamente a partir do pedido de diretório ramo na consola de linha de comandos Hadoop para inspecionar os registos.

Para obter os registos de 10 principais da tabela "viagem de" a partir do primeiro mês:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Para obter os registos de 10 superiores na tabela "tarifa" a partir do primeiro mês:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Muitas vezes é útil para guardar os registos para um ficheiro para uma visualização conveniente. Uma pequena alteração à consulta acima uma efetua isto:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Informações detalhadas: Ver o número de registos de cada uma das 12 partições

>[AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

De interesse é como o número de viagens varia durante o ano de calendário. O agrupamento por mês permite-nos ver o aspeto esta distribuição de viagens.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Isto dá-no resultado:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Aqui, a primeira coluna é o mês e o segundo é o número de viagens, para esse mês.

Recomendamos também pode contar o número total de registos no nosso conjunto de dados de viagem através da emissão o seguinte comando na linha de comandos do ramo de diretório.

    hive -e "select count(*) from nyctaxidb.trip;"

Este número contiver:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Comandos semelhantes às apresentadas para o conjunto de dados de viagem podemos emitir ramo consultas da linha de diretório do ramo tarifa conjunto de dados validar o número de registos.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Isto dá-no resultado:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Tenha em atenção que é devolvido o mesmo número exato de viagens por mês para ambos os conjuntos de dados. Este procedimento fornece a primeira validação de dados tem sido carregados corretamente.

Contar o número total de registos no conjunto de dados de tarifa pode ser efetuado utilizando o comando abaixo da linha de comandos do diretório do ramo:

    hive -e "select count(*) from nyctaxidb.fare;"

Este número contiver:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

O número total de registos em ambas as tabelas também é o mesmo. Este procedimento fornece uma segunda validação de dados tem sido carregados corretamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Informações detalhadas: Distribuição de viagem por medallion

>[AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Este exemplo identifica o medallion (veículo números) com mais de 100 viagens dentro de um determinado período de tempo. Os benefícios de consulta a partir do access a tabela com partições uma vez que deve ser condicionado pela variável de partição **mês**. Os resultados da consulta são escritos queryoutput.tsv um ficheiro local no `C:\temp` no nó cabeça.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Aqui é o conteúdo da *exemplo\_ramo\_viagem\_contar\_por\_medallion.hql* ficheiro para o controlo.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medallion o conjunto de dados de veículo pt identifica um cab exclusivo. Pode identificamos quais cabinas estão "estado ocupadas" ao perguntar quais efetuadas mais do que um determinado número de viagens num determinado período de tempo. O exemplo seguinte identifica cabinas efetuadas mais que uma centena viagens no primeiro três meses e guarda a consulta de resultados para um ficheiro local, C:\temp\queryoutput.tsv.

Aqui é o conteúdo da *exemplo\_ramo\_viagem\_contar\_por\_medallion.hql* ficheiro para o controlo.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A partir da linha de comandos do diretório de ramo, emita o comando em baixo:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Informações detalhadas: Distribuição de viagem ao medallion e hack_license

>[AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Quando explorar um conjunto de dados, podemos frequentemente pretende examinar o número de co-ocorrências de grupos de valores. Nesta secção fornecem um exemplo de como fazê-lo para cabinas e controladores.

O *exemplo\_ramo\_viagem\_contar\_por\_medallion\_license.hql* ficheiro agrupa o conjunto de dados tarifa em "medallion" e "hack_license" e devolve contagens de cada combinação. Seguem-se os seus conteúdos.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Esta consulta devolve cab e as combinações de controlador em particular ordenadas pelo número descendente de viagens.

A partir da linha de comandos do diretório de ramo, execute:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Os resultados da consulta são escritos para um ficheiro local C:\temp\queryoutput.tsv.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Informações detalhadas: Avaliar a qualidade dos dados ao selecionar para registos de latitude/longitude inválida

>[AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Um objectivo comum de análise de dados exploratória é infestantes registos inválidas ou incorretas. O exemplo nesta secção determina se os campos a longitude ou latitude contenham um valor extremidade fora da área do pt. Uma vez que é provável que nesses registos têm valores uma erradas de latitude de longitude, queremos de eliminá-los a partir de qualquer dados que está a ser utilizado para modelação.

Aqui é o conteúdo da *exemplo\_ramo\_qualidade\_assessment.hql* ficheiro para o controlo.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A partir da linha de comandos do diretório de ramo, execute:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

O argumento *-S* incluído neste comando suprime impressão ecrã estado das tarefas Hive mapa/reduzir. Isto é útil porque torna o ecrã impressão do resultado da consulta ramo mais legível.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Informações detalhadas: As distribuições de classe binário sugestões de viagem

> [AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Para o problema de classificação binário descrito na secção de [exemplos de tarefas de previsão](machine-learning-data-science-process-hive-walkthrough.md#mltasks) , é útil para saber se uma sugestão foi dada ou não. Esta distribuição de sugestões é binária:

* Sugestão tendo em conta (1 de classe, sugestão\_montante > $0)  
* sem sugestão (classe 0, sugestão\_quantidade = $0).

O *exemplo\_ramo\_inclinado\_frequencies.hql* ficheiro é mostrado abaixo faz isto.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A partir da linha de comandos do diretório de ramo, execute:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Informações detalhadas: Classe distribuições na definição multiclass

> [AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Para o problema de classificação multiclass descrito na secção de [exemplos de tarefas de previsão](machine-learning-data-science-process-hive-walkthrough.md#mltasks) este conjunto de dados também é utilizado por uma classificação natural onde gostaríamos de prever a quantidade de sugestões tendo em conta. Pode utilizamos agrupamentos a definição de intervalos de sugestão na consulta. Para obter as distribuições de classe para os vários sugestão intervalos, utilizamos a *exemplo\_ramo\_sugestão\_intervalo\_frequencies.hql* ficheiro. Seguem-se os seus conteúdos.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Execute o seguinte comando a partir da consola de linha de comandos Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Informações detalhadas: Calcular a distância direta entre duas localizações de Longitude Latitude

> [AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Está a ter uma medida da distância direta nos permite saiba a diferença entre ela e a distância de viagem real. Esta funcionalidade podemos motivar apontando que um passageiro poderá ser menos provável que Sugestão: se o descobriu que o controlador da tem intencionalmente tomadas-los por uma via muito mais.

Para ver a comparação entre distância de viagem real e a [distância Haversine](http://en.wikipedia.org/wiki/Haversine_formula) entre dois pontos longitude latitude (a distância "Grã círculo"), utilizamos as funções trigonométricas disponíveis dentro ramo, assim:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

Na consulta, R é o raio da terra em milhas e pi é convertido em radianos. Tenha em atenção que os pontos de longitude latitude são "filtrados" para remover os valores que são longe de ser a área de pt.

Neste caso, vamos escrever nossos resultados de um diretório chamado "queryoutputdir". A sequência de comandos mostrado abaixo pela primeira vez cria este diretório de saída e, em seguida, executa o comando ramo.

A partir da linha de comandos do diretório de ramo, execute:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Os resultados da consulta são escritos 9 Azure blobs ***queryoutputdir/000000\_0*** para ***queryoutputdir/000008\_0*** no contentor predefinido do Hadoop cluster.

Para ver o tamanho das blobs individuais, podemos execute o seguinte comando da linha de comandos do diretório do ramo:

    hdfs dfs -ls wasb:///queryoutputdir

Para ver o conteúdo de um determinado ficheiro, diga 000000\_0, utilizamos do Hadoop `copyToLocal` assim, o comando.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [AZURE.WARNING] `copyToLocal`pode ser muito lenta para ficheiros grandes e não é recomendado para utilização com os mesmos.  

Uma chave vantagem de ter estes dados residem numa BLOBs do Azure é que recomendamos poderá explorar os dados de formação de máquina Azure utilizando os [Importar dados] [ import-data] módulo.


## <a name="#downsample"></a>Para baixo de modelos de dados e compilação de exemplo no Azure máquina aprendizagem

> [AZURE.NOTE] Isto é, normalmente, uma tarefa **Cientista de dados** .

Após a fase de análise de dados exploratória, vamos agora está prontos para baixo exemplo os dados para a criação de modelos no Azure máquina aprendizagem. Nesta secção, podemos apresentar como utilizar uma consulta de Hive para baixo exemplo os dados, em seguida, acedido a partir dos [Dados de importação] [ import-data] módulo no Azure máquina aprendizagem.

### <a name="down-sampling-the-data"></a>Para baixo amostragem os dados

Existem dois passos descritos neste procedimento. Participar pela primeira vez as tabelas **nyctaxidb.trip** e **nyctaxidb.fare** em três chaves que estão presentes em todos os registos: "medallion", "ataque\_licença", e "recolha\_datetime". Em seguida, podemos gerar uma etiqueta de classificação binário **inclinado** e uma etiqueta de classificação de classe com várias **Sugestão\_classe**.

Para poder utilizar a seta para baixo amostra dados diretamente a partir dos [Dados de importação] [ import-data] módulo no Azure máquina formação, é necessário armazenar os resultados da consulta acima para uma tabela Hive interna. No que se segue, criar uma tabela Hive interna e preencher os seus conteúdos com a associados e para baixo de dados de amostra.

A consulta aplica-se a padrão ramo funções diretamente para gerar a hora do dia, semana do ano, dia da semana (1 bicicleta para segunda-feira e 7 descanso de Domingo) a partir de "recolha\_datetime" campo e, a distância entre as localizações de recolha e dropoff direta. Os utilizadores podem consultar a [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) para uma lista completa dessas funções.

A consulta para baixo a amostras, em seguida, os dados para que os resultados da consulta podem se ajustam a Studio de aprendizagem do Azure máquina. Apenas cerca de 1% do conjunto de dados original é importada para o Studio.

Abaixo estão os conteúdos de *exemplo\_ramo\_preparar\_para\_aml\_full.hql* ficheiro prepara dados para o modelo de criação no Azure máquina aprendizagem.

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Para executar esta consulta, a partir da linha de comandos do diretório do ramo:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Agora, temos uma tabela interna "nyctaxidb.nyctaxi_downsampled_dataset" que pode ser acedido a utilizar os [Dados de importação] [ import-data] módulo a partir do Azure máquina aprendizagem. Além disso, podemos utilizar este conjunto de dados para a criação de modelos de máquina aprendizagem.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Utilizar o módulo de importar dados em Azure máquina aprender para aceder aos dados de amostra para baixo

Como a pré-requisitos para a emissão ramo consultas na [Importar dados] [ import-data] módulo do Azure máquina formação, precisamos acesso a uma área de trabalho de aprendizagem automática que se Azure e acesso as credenciais do cluster e a sua conta de armazenamento associado.

Alguns detalhes sobre os [Dados de importação] [ import-data] módulo e os parâmetros de entrada:

**URI do servidor de HCatalog**: se o nome do cluster é abc123, em seguida, este é simplesmente: https://abc123.azurehdinsight.net

**Hadoop nome da conta de utilizador** : O nome de utilizador escolhido para cluster (**não** o nome de utilizador de acesso remoto)

**Palavra-passe de conta do Hadoop than** : A palavra-passe escolhida para cluster (**não** a palavra-passe de acesso remoto)

**Localização dos dados de saída** : Este é escolhido para ser Azure.

**Nome da conta Azure armazenamento** : nome da conta predefinida de armazenamento associado ao cluster.

**Nome do contentor Azure** : Este é o nome do contentor predefinido para o cluster e é normalmente igual o nome do cluster. Para um cluster de denominado "abc123", este é apenas abc123.

> [AZURE.IMPORTANT] **Qualquer gostaríamos de consulta utilizando a [Importar dados] de tabela[ import-data] módulo no Azure máquina aprendizagem tem de ser uma tabela interna.** Uma sugestão para determinar se uma tabela T numa base de dados D.db é uma tabela interna é da seguinte forma.

A partir da linha de comandos do diretório de ramo, emita o comando:

    hdfs dfs -ls wasb:///D.db/T

Se a tabela é uma tabela interna e é preenchida, os seus conteúdos devem mostrar aqui. Outra forma de determinar se uma tabela é uma tabela interna é utilizar o Explorador de armazenamento do Azure. Utilizá-lo para navegar para o nome do contentor predefinido do cluster e, em seguida, filtrar pelo nome da tabela. Se a tabela e os seus conteúdos apresentada, isto confirma que é uma tabela interna.

Eis um instantâneo da consulta ramo e [Importar dados] [ import-data] módulo:

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

Note que desde para baixo os nossos dados de amostra residem no contentor predefinido, a consulta ramo resultante a partir do Azure máquina formação é muito simple e apenas é um "SELECIONAR * de nyctaxidb.nyctaxi\_subamostradas\_dados".

O conjunto de dados agora pode ser utilizado como ponto de partida para criar modelos de máquina aprendizagem.

### <a name="mlmodel"></a>Construir modelos no Azure máquina aprendizagem

Vamos agora conseguem avançar para criar um modelo e implementação do modelo no [Azure máquina aprendizagem](https://studio.azureml.net). Os dados estão prontos para-nos utilizar em endereçar os problemas de previsão identificados acima:

**1. classificação binária**: para prever ou não uma sugestão foi pagos para uma viagem.

**Aprendiz utilizado:** Duas classe logística regressão

um. Para este problema, os nossos etiqueta de destino (ou escolares) é "inclinada". Os nossos dataset reduzidos original tem algumas colunas que estejam fugas de destino para esta experiência de classificação. Em particular: Sugestão\_classe, sugestão\_quantidade e total\_montante revelar informações sobre a etiqueta de destino que não está disponível em testes de tempo. Vamos remover estas colunas de consideração utilizando as [Selecionar colunas no conjunto de dados] [ select-columns] módulo.

O instantâneo abaixo mostra os nossos experiência para prever ou não tiver sido paga uma sugestão para uma viagem determinada.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. Para este experiência, os nossos distribuições de etiqueta de destino foram aproximadamente 1:1.

O instantâneo abaixo mostra a distribuição de sugestão de etiquetas de classe para o problema de classificação binário.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

Como resultado, podemos-obter um AUC de 0.987, tal como apresentado na figura abaixo.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. classificação multiclass**: para prever o intervalo de valores de sugestão paga viagem, utilizando as classes definidas anteriormente.

**Aprendiz utilizado:** Multiclass regressão logística

um. Para este problema, o nossa etiqueta de destino (ou escolares) é "sugestão\_classe" que pode ter um dos cinco valores (0,1,2,3,4). Tal como o caso de classificação binário, podemos têm algumas colunas que estejam fugas de destino para esta experiência. Em particular: inclinado, sugestão\_montante, total\_montante revelar informações sobre a etiqueta de destino que não está disponível em testes de tempo. Vamos remover estas colunas utilizando as [Selecionar colunas no conjunto de dados] [ select-columns] módulo.

O instantâneo abaixo mostra os nossos experiência para prever em que posição uma sugestão é provável que se situar (classe 0: sugestão = $0, classe 1: Sugestão > $0 e sugestão < = $5, classe 2: Sugestão > $5 e sugestão < = $10, classe 3: Sugestão > $10 e sugestão < = $20, classe 4: Sugestão > $20)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

Mostramos agora o aspeto do nossa distribuição de classe de teste real. Vemos que, enquanto classe 0 e 1 de classe se encontram propagadas, outras classes são raros.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. Para este experiência, utilizamos uma matriz ambiguidade para veja os nossos precisão de previsão. Isto é mostrado abaixo.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

Tenha em atenção que enquanto o nosso precisão de classe nas classes predominante é bastante bom, o modelo não faz um bom trabalho de "formação" nas classes raros.


**3. tarefa de regressão**: para prever a quantidade de sugestão paga uma viagem.

**Aprendiz utilizado:** Árvore de decisões aumentado

um. Para este problema, o nossa etiqueta de destino (ou escolares) é "sugestão\_quantidade". Neste caso são os nossos fugas destino: inclinado, sugestão\_classe, total\_montante; todas as variáveis estes revelarem informações sobre a quantidade de sugestão que não está normalmente disponível em testes de tempo. Vamos remover estas colunas utilizando as [Selecionar colunas no conjunto de dados] [ select-columns] módulo.

O belows instantâneo mostra os nossos experiência para prever a quantidade da sugestão determinada.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. Problemas de regressão, podemos medir a precisão dos nossos predição verificando o erro ao quadrado nas previsões, o coeficiente de imagem e semelhantes. Mostramos-estes abaixo.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

Vemos que sobre o coeficiente de imagem é 0.709, conferir cerca de 71% da variância é explicado ao nossos coeficientes de modelo.

> [AZURE.IMPORTANT] Para saber mais sobre o Azure máquina aprendizagem e como aceder e utilizá-lo, consulte [o que é a aprendizagem máquina?](machine-learning-what-is-machine-learning.md). Um recurso muito útil para reproduzir com um conjunto de aprendizagem máquina experiências no Azure máquina formação é a [Galeria de informações da empresa Cortana](https://gallery.cortanaintelligence.com/). A Galeria de abrange uma gama de experiências e fornece uma introdução aprofundada para o intervalo das capacidades do Azure máquina aprendizagem.

## <a name="license-information"></a>Informações de licença

Este tutorial de exemplo e os seus scripts acompanhamento são partilhadas ao abrigo da licença MIT pela Microsoft. Selecione o ficheiro License no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências

• [Andrés Monroy pt veículo viagens de página de transferência](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing dados de viagem de veículo do pt por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Veículo pt e Limousine Comissão investigação e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
