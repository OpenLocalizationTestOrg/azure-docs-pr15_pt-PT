<properties
    pageTitle="Dimensionáveis ciência de dados no Azure dados Lake: um tutorial de ponto a ponto | Microsoft Azure"
    description="Como utilizar o Azure dados Lake para efetuar tarefas de classificação de informações detalhadas e binário dados num conjunto de dados."  
    services="machine-learning"
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
    ms.author="bradsev;weig"/>


# <a name="scalable-data-science-in-azure-data-lake-an-end-to-end-walkthrough"></a>Dimensionáveis ciência de dados no Azure dados Lake: um tutorial de ponto a ponto

Este tutorial mostra como utilizar o Azure dados Lake para efetuar tarefas de classificação binário uma amostra da viagem de veículo pt e informações detalhadas de dados e conjunto de dados para prever ou não uma sugestão serão pagos por uma tarifa a importância a pagar. Orienta-o através dos passos do [Processo de ciências de dados de equipa](http://aka.ms/datascienceprocess), fim-para-end, a partir do aquisição de dados para modelar formação e, em seguida, para a implementação de um serviço web que publica o modelo.


### <a name="azure-data-lake-analytics"></a>A análise de dados Azure Lake

O [Microsoft Azure dados Lake](https://azure.microsoft.com/solutions/data-lake/) tem todas as capacidades necessárias para facilitar a cientistas de dados para armazenar dados de qualquer tamanho, a forma e a velocidade de e para realizar processamento de dados, e de análise avançadas aprendizagem modelação com elevada escalabilidade de forma rentável automática.   Pode pagar numa base por tarefa, apenas quando dados realmente está a ser processados. Azure dados Lake Analytics inclui U SQL, um idioma que mistura da natureza declarativa do SQL com a potência modelar de c# para fornecer dimensionáveis distribuído capacidade de consulta. Permite-lhe processar dados não estruturados aplicando esquema no leitura, inserir lógica personalizada e funções definidas pelo utilizador (UDFs) e inclui expansão para ativar o controlo ajustar detalhado sobre como executar na escala. Para saber mais sobre a filosofia estrutura atrás U SQL, consulte o [blogue do Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

A análise de dados Lake também é uma chave parte do conjunto de aplicações de análise de Cortana e funciona com armazém de dados do SQL Azure, do Power BI e fábrica de dados. Isto dá-lhe uma dados grande concluída na nuvem e a plataforma de análise avançadas.

Neste tutorial, comece por que descrevem os pré-requisitos e recursos que são necessários para concluir as tarefas com dados Lake Analytics que formam o processo de ciências de dados e como instalá-los. Em seguida, -descreve os passos de processamento de dados utilizando o U-SQL e conclui mostrando como utilizar Python e ramo com o Azure máquina aprendizagem Studio para criar e implementar os modelos de aspeto do Office. 


### <a name="u-sql-and-visual-studio"></a>U SQL e o Visual Studio

Este tutorial recomenda a utilização do Visual Studio para editar scripts U SQL para processar o conjunto de dados. Os scripts U SQL estão descritos aqui e fornecidos num ficheiro separado. O processo inclui ingesting, explorar e amostragem os dados. Também mostra como executar uma tarefa de U-SQL de script a partir do portal do Azure. Ramo de tabelas são criadas para os dados num cluster HDInsight associado para facilitar a criação e implementação de um modelo de classificação binário no Azure máquina aprendizagem Studio.  


### <a name="python"></a>Python

Este tutorial também contém uma secção que mostra como criar e implementar um modelo de aspeto do Office com Python com o Azure máquina aprendizagem Studio.  Fornecemos um bloco de notas Jupyter os scripts Python para que estes passos deste processo. O bloco de notas inclui código para algumas funcionalidades adicionais engenharia passos e modelos de construção como classificação multiclass e regressão modelação para além de modelo de classificação binário descrito aqui. A tarefa de regressão é prever a quantidade de sugestão com base em outras funcionalidades de sugestão. 


### <a name="azure-machine-learning"></a>Formação de máquina Azure
Azure máquina aprendizagem Studio é utilizada para criar e implementar os modelos de aspeto do Office. Isto é feito utilizando duas abordagens: pela primeira vez com Python scripts e, em seguida, com tabelas de ramo num cluster HDInsight (Hadoop).


### <a name="scripts"></a>Scripts

Apenas os passos principais são destacados destas instruções. Pode transferir o completo **script U SQL** e **Jupyter bloco de notas** do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar estes tópicos, tem de ter o seguinte procedimento:

- Uma subscrição do Azure. Se não já possui um, consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- [Recomendado] Visual Studio 2013 ou 2015. Se não já possui uma destas versões instaladas, pode transferir uma edição de Comunidade gratuita do [aqui](https://www.visualstudio.com/visual-studio-homepage-vs.aspx). Clique no botão **Transferir 2015 da Comunidade** na secção do Visual Studio. 

>[AZURE.NOTE] Em vez do Visual Studio, também pode utilizar o Portal do Azure para submeter Azure dados Lake consultas. Irá fornecemos instruções sobre como fazê-lo com tanto o Visual Studio e no portal de na secção intitulado **dados de processo com U-SQL**. 

- Inscrição para pré-visualização do Azure dados Lake

>[AZURE.NOTE] Que precisa para obter aprovação para utilizar o Azure dados Lake loja (ADLS) e Azure dados Lake Analytics (ADLA) como estes serviços estão na pré-visualização. Vai ser-lhe para se inscrever quando criar o seu ADLS primeiro ou ADLA. Para sigh para cima, clique em **Inscrever-se para a pré-visualizar**, leia o contrato e clique em **OK**. Por exemplo, eis o ADLS de inscrição página:

 ![2](./media/machine-learning-data-science-process-data-lake-walkthrough/2-ADLA-preview-signup.PNG)


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Preparar o ambiente de ciências de dados para Azure dados Lake
Para preparar o ambiente de ciências de dados para este tutorial, crie os seguintes recursos:

- Azure dados Lake loja (ADLS) 
- Dados Azure Lake Analytics (ADLA)
- Conta de armazenamento de BLOBs Azure
- Conta de computador aprendizagem Studio Azure
- Dados Azure Lake ferramentas para o Visual Studio (recomendado)

Esta secção fornece instruções sobre como criar cada um destes recursos. Se optar por utilizar tabelas ramo com o Azure máquina formação, em vez de Python, para criar um modelo, também terá de aprovisionamento de um cluster de HDInsight (Hadoop). Este procedimento alternativo no descrito na secção adequada abaixo.
<br/>
>AZURE. Tenha em atenção o **Azure dados Lake loja** podem ser criadas separadamente ou quando cria o **Azure dados Lake Analytics** como o armazenamento de predefinido. As instruções são referenciadas para a criação de cada um dos seguintes recursos separadamente abaixo, mas a conta de armazenamento de dados Lake não precisa de ser criada em separado.
<br/>
### <a name="create-an-azure-data-lake-store"></a>Criar um arquivo de Lake de dados do Azure

Crie um ADLS a partir do [Azure Portal](http://portal.azure.com). Para obter detalhes, consulte o artigo [criar um cluster de HDInsight com o arquivo de dados de Lake utilizando o Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que configurar a identidade de AAD Cluster no pá a **origem de dados** de pá a **Configuração opcional** descrito aí. 

 ![3](./media/machine-learning-data-science-process-data-lake-walkthrough/3-create-ADLS.PNG)


### <a name="create-an-azure-data-lake-analytics-account"></a>Criar uma conta Azure dados Lake Analytics
Crie uma conta ADLA a partir do [Azure Portal](http://portal.azure.com). Para obter mais detalhes, consulte o artigo [Tutorial: introdução ao Azure dados Lake Analytics através do Portal do Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/machine-learning-data-science-process-data-lake-walkthrough/4-create-ADLA-new.PNG)


### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure
Crie uma conta de armazenamento de Blobs do Azure a partir do [Azure Portal](http://portal.azure.com). Para obter detalhes, consulte o artigo criar uma secção de conta de armazenamento no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md).
    
 ![5](./media/machine-learning-data-science-process-data-lake-walkthrough/5-Create-Azure-Blob.PNG)


### <a name="set-up-an-azure-machine-learning-studio-account"></a>Configurar uma conta Azure máquina aprendizagem Studio
Inicie sessão para cima/em Azure máquina aprendizagem Studio a partir da página de [Aprendizagem do Azure máquina](https://azure.microsoft.com/services/machine-learning/) . Clique no botão **começar agora** e, em seguida, selecione um "Gratuito área de trabalho" ou "Área de trabalho padrão". Após estes serão poderá criar experiências no Azure ML Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Instalar ferramentas de Lake dados Azure [Recomendado]
Instale ferramentas de Lake Azure dados para a sua versão do Visual Studio a partir do [Azure dados Lake ferramentas para o Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Após a instalação concluir com êxito, abra o Visual Studio. Deverá visualizar a Lake de dados no menu na parte superior do separador. Os recursos do Azure deverão aparecer no painel da esquerda quando iniciar sessão na sua conta Azure.

 ![7](./media/machine-learning-data-science-process-data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)


## <a name="the-nyc-taxi-trips-dataset"></a>O conjunto de dados de viagens de veículo pt
O conjunto de dados que utilizámos aqui está um conjunto de dados publicamente disponível – o [conjunto de dados de viagens de veículo pt](http://www.andresmh.com/nyctaxitrips/). Os dados de viagem de veículo pt consiste em cerca de 20GB de ficheiros CSV comprimidos (~ 48GB não comprimido), viagens de mais de 173 milhão individuais e as tarifas gravação pagos para cada viagem. Cada registo de viagem inclui as localizações de recolha e remessa e horas, número de licenças ataque à anónimos (controlador) e o número de medallion (id exclusivo do veículo). Os dados abrange viagens de todos os do ano 2013 e são fornecidos em conjuntos de dados de duas seguintes para cada mês:

 - O 'trip_data CSV contém detalhes de viagem, como o número de passageiros, recolha e dropoff pontos, duração de viagem e comprimento de viagem. Aqui estão alguns registos de exemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - O trip_fare CSV contém detalhes de tarifa paga para cada viagem, tal como o tipo de pagamento, quantidade tarifa, sobretaxa e impostos, sugestões e portagens e o montante total pago. Aqui estão alguns registos de exemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para participar viagem\_dados e viagem\_tarifa é composto por três seguintes campos: medallion, ataque à\_licença e recolha\_datetime. Os ficheiros CSV observou podem ser acedidos a partir de um blob armazenamento Azure público. O script U SQL para esta associação é na secção de [tabelas de viagem ou tarifas de associação](#join) .

## <a name="process-data-with-u-sql"></a>Dados de processo com U SQL

As tarefas de processamento de dados ilustradas nesta secção incluem ingesting, verificar a qualidade, explorar e amostragem os dados. Mostramos também como participar tabelas viagem e tarifa. A secção final mostra executar uma tarefa em script U SQL a partir do portal Azure. Aqui estão ligações para cada subsecção:

- [Ingestão de dados: ler dados a partir do blob público](#ingest)
- [Verificações de qualidade de dados](#quality)
- [Informações detalhadas de dados](#explore)
- [Associe as tabelas de viagem e corrida](#join)
- [Recolha de dados](#sample)
- [Executar tarefas U SQL](#run)

Os scripts U SQL estão descritos aqui e fornecidos num ficheiro separado. Pode transferir os **scripts U SQL** de completo do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para executar o U-SQL, abrir Visual Studio, clique em **ficheiro--> novo--> projeto**, escolha **Projeto U SQL**, nome e guardá-lo para uma pasta.

![8](./media/machine-learning-data-science-process-data-lake-walkthrough/8-create-USQL-project.PNG)

>[AZURE.NOTE] É possível utilizar o Portal do Azure executar U-SQL em vez do Visual Studio. Pode navegar para o recurso Azure dados Lake Analytics no portal do e submeter consultas diretamente como ilustrado na figura seguinte.

![9](./media/machine-learning-data-science-process-data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Dados ingestão: Ler dados a partir do blob público

A localização dos dados de Blobs do Azure referenciada como **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** e podem ser extraídas utilizando **Extractors.Csv()**. Substitua seu próprio nome para o contentor e o nome de conta de armazenamento no seguintes scripts para container_name@blob_storage_account_name o endereço de wasb. Dado que são os nomes de ficheiro no formato mesmo, pode utilizamos **viagem\_data_ {\*\}. csv** para ler em todos os ficheiros de viagem 12. 

    ///Read in Trip data
    @trip0 =
        EXTRACT 
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Uma vez que existem cabeçalhos na primeira linha, precisamos de remover os cabeçalhos e alterar tipos de coluna para aqueles adequado. Podemos quer guardar os dados processados ao armazenamento de Lake de dados do Azure utilizando **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ ou ao armazenamento de Blobs do Azure conta utilizando **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

    // change data types
    @trip =
        SELECT 
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

Da mesma forma podemos leia os conjuntos de dados tarifa. Clique com o botão direito do rato em Azure dados Lake loja, pode optar por ver os dados no **Portal do Azure--> Explorador de dados** ou no **Explorador de ficheiros** do Visual Studio. 

 ![10](./media/machine-learning-data-science-process-data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/machine-learning-data-science-process-data-lake-walkthrough/11-data-in-ADL.PNG)


### <a name="quality"></a>Verificações de qualidade de dados

Depois de tabelas viagem e tarifa tem sido lida, controlos de qualidade de dados podem ser feitos da seguinte forma. Os ficheiros CSV resultantes podem ser exportados para o armazenamento de Blobs do Azure ou Azure dados Lake loja. 

Encontre o número de medallions e o número exclusivo de medallions:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;
    
    @ex_1 =
        SELECT
        pickup_month, 
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv(); 

Localize essas medallions que tinha mais de 100 viagens:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv(); 

Localize os registos inválidos em termos pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

Localize valores em falta para algumas variáveis:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;
    
    @trip_summary6 =
        SELECT 
            vendor_id,
        SUM(missing_medallion) AS medallion_empty, 
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique  
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>Informações detalhadas de dados

Podemos fazer algumas informações detalhadas de dados para obter uma melhor compreensão dos dados.

Localize a distribuição dos viagens inclinadas e que não sejam inclinado:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;
    
    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv(); 

Localizar a distribuição de montante de sugestão com valores de corte: 0,5,10 e euros 20.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv(); 

Encontre estatísticas de base da distância de viagem:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT 
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance 
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Localize os percentis da distância de viagem:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv(); 


### <a name="join"></a>Associe as tabelas de viagem e corrida

Tabelas de viagem e tarifa podem ser associadas ao medallion, hack_license e pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 

    ////output data to ADL
    OUTPUT @model_data_full   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 


Para cada nível de contagem de passageiros, calcule o número de registos, quantidade de sugestão de média, variância da quantidade de sugestão, percentagem de viagens inclinadas.

    // contigency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>Recolha de dados

Em primeiro lugar podemos aleatoriamente selecione % de 0,1 dos dados a partir da tabela associada:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;
    
    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;
    
    OUTPUT @model_data_random_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv(); 

Em seguida, podemos efetue amostragem estratificada por binário tip_class variável:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;
    
    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="run"></a>Executar tarefas U SQL

Quando terminar a edição de scripts U SQL, pode enviá-las no servidor com a sua conta Azure dados Lake Analytics. Clique em **Dados Lake**, **Submeter a tarefa**, selecione a sua **Conta de análise**, selecione **paralelismo**e clique em botão **Submeter** .  

 ![12](./media/machine-learning-data-science-process-data-lake-walkthrough/12-submit-USQL.PNG)

Quando a tarefa está satisfeita com êxito, o estado da tarefa de será apresentado no Visual Studio para monitorização. Depois da tarefa estiver concluída, também pode repetir o processo de execução de tarefas e saber os passos de congestionamento para melhorar a eficácia da tarefa. Também pode aceder ao Portal do Azure para verificar o estado dos seus trabalhos U-SQL.

 ![13](./media/machine-learning-data-science-process-data-lake-walkthrough/13-USQL-running-v2.PNG)


 ![14](./media/machine-learning-data-science-process-data-lake-walkthrough/14-USQL-jobs-portal.PNG)


Agora pode verificar os ficheiros de saída no armazenamento de Blobs do Azure ou Azure Portal. Vamos irá utilizar os dados de exemplo estratificado para os nossos modelação no próximo passo.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/machine-learning-data-science-process-data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)


## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Criar e implementar modelos de formação de máquina do Azure

Vamos demonstram duas opções disponíveis para si importar dados para o Azure máquina formação para criar e 

- Na primeira opção, pode utiliza os dados recolhidos que foi escritos para um BLOBs do Azure (no passo de **recolha de dados** acima) e utilizar Python para criar e implementar modelos da partir do Azure máquina aprendizagem. 
- Na segunda opção, consultar os dados no Azure dados Lake diretamente utilizando uma consulta de ramo. Esta opção requer que crie um novo cluster de HDInsight ou utilize um cluster de HDInsight existente onde as tabelas de ramo apontam para os dados de veículo NY no Azure dados Lake armazenamento.  Vamos discutir ambas as seguintes opções abaixo. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opção 1: Python utilizar para criar e implementar modelos de formação de computador

Para criar e implementar modelos de formação de máquina utilizando Python, crie um bloco de notas Jupyter no seu computador local ou no Azure máquina de formação Studio. O bloco de notas Jupyter fornecido na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) contém o código completo para explorar, visualizar dados, engenharia de funcionalidade, modelação e implementação. Neste artigo, mostramos-apenas a modelação e implementação. 

### <a name="import-python-libraries"></a>Importar Python bibliotecas

Para executar o exemplo Jupyter bloco de notas ou o Python ficheiro de script, as seguintes Python pacotes são necessários. Se estiver a utilizar o serviço de AzureML bloco de notas, estes pacotes tem sido pré-instalados.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Leia os dados a partir do blob

- Cadeia de ligação   

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    
- Ler como texto

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![17](./media/machine-learning-data-science-process-data-lake-walkthrough/17-python_readin_csv.PNG)    
 
- Adicionar nomes de colunas e separe colunas

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
    


- Alterar algumas colunas para numérico

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Construir modelos de aprendizagem automática

Aqui vamos construa um modelo de classificação binário para prever se uma viagem é inclinada ou não. No bloco de notas Jupyter pode encontrar outros dois modelos: multiclass classificação e os modelos de regressão.

- Em primeiro lugar precisamos de criar fictícias variáveis que podem ser utilizadas no scikit-obter informações sobre modelos

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- Crie moldura de dados para a modelação

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
        
        X = data.iloc[:,1:]
        Y = data.tipped

- Formação e testes de divisão de 60-40

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- Regressão logística num conjunto de formação

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/machine-learning-data-science-process-data-lake-walkthrough/c1-py-logit-coefficient.PNG)

- Pontuação teste conjunto de dados

        Y_test_pred = logit_fit.predict(X_test)

- Calcular métricas de avaliação

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train
        
        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
        print fpr_test, tpr_test, thresholds_test
        
        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)
        
        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/machine-learning-data-science-process-data-lake-walkthrough/c2-py-logit-evaluation.PNG)


 
### <a name="build-web-service-api-and-consume-it-in-python"></a>Construir a API do serviço Web e consuma-lo no Python

Pretendemos operationalize máquina aprendizagem modelo após foi criado. Aqui vamos utilizar o modelo de logístico binário de exemplo. Certifique-se a scikit-saiba versão no seu computador local é 0.15.1. Não tem de se preocupar com isso se utilizar o serviço do Azure ML studio.

- Encontre as suas credenciais de área de trabalho a partir das definições do Azure ML studio. No Azure máquina aprendizagem Studio, clique em **Definições** --> **nome** --> **Tokens de autorização**. 

    ![C3](./media/machine-learning-data-science-process-data-lake-walkthrough/c3-workspace-id.PNG)


        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- Criar o serviço Web

        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)

- Obter as credenciais de serviço web

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
        
        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass

- Ligar a API do serviço Web. Tem de aguardar 5-10 segundos após o passo anterior.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/machine-learning-data-science-process-data-lake-walkthrough/c4-call-API.PNG)


## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opção 2: Criar e implementar modelos de diretamente no Azure máquina aprendizagem

Azure máquina aprendizagem Studio pode ler os dados diretamente a partir do Azure dados Lake arquivo e, em seguida, ser utilizado para criar e implementar modelos. Esta abordagem utiliza uma tabela Hive que aponta na loja Azure dados Lake. Isto requer que ser aprovisionado um cluster de Azure HDInsight em separado, no qual a tabela Hive é criada. As secções seguintes mostram como o fazer. 

### <a name="create-an-hdinsight-linux-cluster"></a>Criar um Cluster de Linux HDInsight

Crie um Cluster de HDInsight (Linux) a partir do [Azure Portal](http://portal.azure.com). Para obter detalhes, consulte a secção **criar um cluster de HDInsight com acesso ao Azure dados Lake arquivo** no [criar um cluster de HDInsight com o arquivo de dados de Lake utilizando o Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/machine-learning-data-science-process-data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Criar tabela Hive no HDInsight

Agora criamos ramo de tabelas para ser utilizado no Azure máquina aprendizagem Studio no cluster HDInsight com os dados armazenados na loja do Azure dados Lake no passo anterior. Aceda ao cluster HDInsight acabou de criar. Clique em **Definições** --> **Propriedades** --> **Cluster AAD identidade** --> **ADLS acesso**, certifique-se a sua conta Azure dados Lake loja é adicionada na lista com ler, escrever e executar direitos. 

 ![19](./media/machine-learning-data-science-process-data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)


Em seguida, clique em **Dashboard** junto ao botão **Definições** e uma janela irá pop-up. Clique em **Ver Hive** no canto superior canto direito da página e irá ver o **Editor de consultas**.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/machine-learning-data-science-process-data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)


Cole os seguintes scripts de Hive para criar uma tabela. A localização da origem de dados é a referência de arquivo do Azure dados Lake desta forma: **adl://data_lake_store_name.azuredatalakestore.net:443/nome da pasta/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Quando a consulta estiver concluída, irá ver os resultados da seguinte forma:

 ![22](./media/machine-learning-data-science-process-data-lake-walkthrough/22-Hive-Query-results.PNG)



### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Criar e implementar modelos no Azure máquina aprendizagem Studio

Vamos agora está prontos para criar e implementar um modelo que prevê, com ou não uma sugestão é paga com Azure máquina aprendizagem. Os dados de exemplo estratificado estão prontos para ser utilizada nesta classificação binário (sugestão ou não) problema. Os modelos de aspeto do Office utilizando a classificação multiclass (tip_class) e regressão (tip_amount) também podem ser criados e implementados com o Azure máquina aprendizagem Studio, mas aqui apenas mostramos-como lidar com as maiúsculas/minúsculas utilizando o modelo de classificação binário.

1. Obtenha os dados para o Azure ML utilizar o módulo de **Importar dados** , disponível na secção **dados de entrada e saída** . Para obter mais informações, consulte a página de referência do [módulo importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) .
2. Selecione **Hive consulta** como a **origem de dados** no painel de **Propriedades** .
3. Cole o seguinte script do ramo no editor de **ramo de consulta de base de dados**

        select * from nyc_stratified_sample;

4. Introduza o cluster de URI de HDInsight (Isto pode ser encontrado no Portal do Azure), a Hadoop credenciais, a localização de dados de saída e nome de contentor/chave/nome da conta de armazenamento Azure.

 ![23](./media/machine-learning-data-science-process-data-lake-walkthrough/23-reader-module-v3.PNG)  

Um exemplo de uma experiência de classificação binário ler dados a partir da tabela Hive é apresentado na figura abaixo.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthrough/24-AML-exp.PNG)

Depois da experiência é criada, clique em **Configurar o serviço Web** --> **Serviço Web aspeto do Office**

 ![25](./media/machine-learning-data-science-process-data-lake-walkthrough/25-AML-exp-deploy.PNG)

Executar criada automaticamente pontuação experiência, quando tiver terminado, clique em **Implementar o serviço Web**

 ![26](./media/machine-learning-data-science-process-data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

O dashboard de serviço web será apresentado em breve:

 ![27](./media/machine-learning-data-science-process-data-lake-walkthrough/27-AML-web-api.PNG)


## <a name="summary"></a>Resumo

Preenchendo este tutorial ter criado um ambiente de ciências de dados para construção de soluções de ponto a ponto dimensionáveis no Azure dados Lake. Este ambiente foi utilizado para analisar um conjunto de dados público grande, tendo-la através dos passos do processo de ciências de dados, a partir de aquisição de dados através de formação do modelo e, em seguida, para a implementação do modelo, como um serviço web canónicos. U SQL foi utilizado para processar, explorar e os dados de exemplo. Python e ramo eram utilizadas com o Azure máquina aprendizagem Studio para criar e implementar modelos de aspeto do Office.

## <a name="whats-next"></a>O que se segue?

Caminho de formação para o [Processo de ciências de dados de equipa (TDSP)](http://aka.ms/datascienceprocess) fornece ligações para tópicos que descrevem cada passo no processo de análise avançadas. Existem uma série de tutoriais discriminadas na página [Tutoriais do processo de ciências de dados de equipa](data-science-process-walkthroughs.md) que ilustram como utilizar recursos e serviços em diversos cenários de análise de aspeto do Office:

- [O processo de ciências de dados de equipa em ação: Utilizar armazém de dados SQL](machine-learning-data-science-process-sqldw-walkthrough.md)
- [O processo de ciências de dados de equipa em ação: utilizar HDInsight Hadoop clusters](machine-learning-data-science-process-hive-walkthrough.md)
- [O processo de ciências de dados de equipa: utilizar o SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [Descrição geral do processo de ciências de dados com motores no Azure HDInsight](machine-learning-data-science-spark-overview.md)

