<properties
    pageTitle="O processo de ciências de dados de equipa em ação: Utilizar armazém de dados SQL | Microsoft Azure"
    description="Tecnologia em ação e o processo de análise avançadas"  
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
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>O processo de ciências de dados de equipa em ação: Utilizar armazém de dados SQL

Neste tutorial, iremos guiá-através de criar e implementar uma modelo com armazém de dados SQL (SQL DW) de aprendizagem automática para um conjunto de dados publicamente disponível – o conjunto de dados de [Viagens de veículo pt](http://www.andresmh.com/nyctaxitrips/) . O modelo de classificação binário construído prevê, com uma sugestão é paga para uma viagem e modelos de classificação multiclass e regressão são também debatidos que a distribuição de previsão para os valores de sugestão pagos ou não.

O procedimento segue o fluxo de trabalho de [Equipa dados ciência processo (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) . Mostramos-como configurar um ambiente de ciências de dados, como carregar os dados para SQL DW e como utilizar o SQL DW ou um bloco de notas IPython para explorar os dados e engenharia funcionalidades ao modelo. Em seguida, podemos apresentar os como criar e implementar um modelo com Azure máquina aprendizagem.


## <a name="dataset"></a>O conjunto de dados de viagens de veículo pt

Os dados de viagem de veículo pt consiste em cerca de 20GB de ficheiros CSV comprimidos (~ 48GB não comprimido), viagens de mais de 173 milhão individuais e as tarifas gravação pagos para cada viagem. Cada registo de viagem inclui as localizações de recolha e remessa e horas, número de licenças ataque à anónimos (controlador) e o número de medallion (id exclusivo do veículo). Os dados abrange viagens de todos os do ano 2013 e são fornecidos em conjuntos de dados de duas seguintes para cada mês:

1. O ficheiro **trip_data.csv** contém detalhes de viagem, como o número de passageiros, recolha e dropoff pontos, duração de viagem e comprimento de viagem. Aqui estão alguns registos de exemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. O ficheiro **trip_fare.csv** contém detalhes de tarifa paga para cada viagem, tal como o tipo de pagamento, quantidade tarifa, sobretaxa e impostos, sugestões e portagens e o montante total pago. Aqui estão alguns registos de exemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A **chave exclusivo** utilizados para participar em viagem\_dados e viagem\_tarifa é composto por três seguintes campos:

- Medallion,
- ataque\_licença e
- recolha\_datetime.

## <a name="mltasks"></a>Três tipos de tarefas de previsão de endereços

Vamos elaborar três problemas de previsão com base na *Sugestão\_quantidade* para ilustrar três tipos de modelação tarefas:

1. **Classificação binária**: para prever ou não uma sugestão foi pagos para uma viagem, ou seja, um *Sugestão\_quantidade* que for maior que $0 é um exemplo positivo, enquanto um *Sugestão\_quantidade* de €0 é um exemplo negativo.

2. **Classificação multiclass**: para prever o intervalo de sugestão paga viagem. Vamos dividir o *Sugestão\_quantidade* para cinco posições ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tarefa de regressão**: para prever a quantidade de sugestão paga uma viagem.  


## <a name="setup"></a>Configurar o ambiente da ciência do Azure dados de análise avançadas

Para configurar o seu ambiente do Azure dados ciência, siga estes passos.

**Criar a sua própria conta de armazenamento de Blobs do Azure**

- Quando aprovisionar o seu próprio armazenamento de Blobs do Azure, selecione uma localização de geo para o armazenamento de Blobs do Azure no ou tanto quanto possível **Sul Central (EUA)**, que é onde os dados de veículo pt estão armazenados. Os dados serão copiados AzCopy do contentor de armazenamento de BLOBs público para um contentor a utilizar na sua própria conta de armazenamento. Quanto mais próximo o armazenamento de Blobs do Azure é sul Central-nos, mais rápido esta tarefa (passo 4) vai ser concluída.
- Para criar a sua própria conta Azure armazenamento, siga os passos descritos no [Azure sobre contas de armazenamento](../storage/storage-create-storage-account.md). Certifique-se de que tomar notas nos valores para introduzir as credenciais da conta do armazenamento seguintes conforme vai ser necessário posteriormente destas instruções.

  - **Nome da conta de armazenamento**
  - **Chave de conta de armazenamento**
  - **Nome do contentor** (que pretende que os dados sejam armazenados no armazenamento de Blobs do Azure)

**Aprovisione a instância DW de SQL Azure.**
Siga a documentação sobre em [criar um armazém de dados SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para Aprovisionar uma instância do armazém de dados SQL. Certifique-se de que faça científicas nas seguintes credenciais armazém de dados SQL que serão utilizadas nos passos posteriores.

  - **Nome do servidor**: <server Name>. database.windows.net
  - **Nome SQLDW (base de dados)**
  - **Nome de utilizador**
  - **Palavra-passe**

**Instale o Visual Studio 2015 e ferramentas de dados do SQL Server.** Para obter instruções, consulte o artigo [2015 para instalar o Visual Studio e/ou SSDT (ferramentas de dados do SQL Server) para armazém de dados SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Ligar a sua DW Azure SQL com o Visual Studio.** Para obter instruções, consulte os passos 1 e 2 em [ligar ao armazenamento de dados do SQL Azure com o Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

>[AZURE.NOTE] Execute a seguinte consulta SQL na base de dados que criou no seu armazém de dados SQL (em vez da consulta fornecida no passo 3 do tópico ligar,) para **criar uma chave de modelo global**.

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Crie uma área de trabalho do Azure máquina formação em sua subscrição do Azure.** Para obter instruções, consulte o artigo [criar uma área de trabalho do Azure máquina aprendizagem](machine-learning-create-workspace.md).

## <a name="getdata"></a>Carregue os dados no armazém de dados SQL

Abra uma consola do comando Windows PowerShell. Executar o PowerShell seguinte comandos para transferir o exemplo SQL ficheiros de script que recomendamos partilham no Github para um diretório local que especificar, com o parâmetro *- DestDir*. Pode alterar o valor do parâmetro *-DestDir* para qualquer directório local. Se não existir *- DestDir* , será criado pelo script do PowerShell.

>[AZURE.NOTE] Poderá ter de **Executar como administrador** ao executar o seguinte script do PowerShell se necessitar de diretório da sua *DestDir* privilégios de administrador para criar ou escrever na mesma.

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Após a execução com êxito, o seu diretório de trabalho atual muda para *- DestDir*. Deverá conseguir ver o ecrã como abaixo:

![][19]

No seu *-DestDir*, execute o seguinte script do PowerShell no modo de administrador:

    ./SQLDW_Data_Import.ps1

Quando executa o script do PowerShell pela primeira vez, lhe-á pedido para introduzir as informações do seu DW de SQL Azure e a sua conta de armazenamento de Blobs do Azure. Quando concluir este script do PowerShell executar pela primeira vez, as credenciais de entrada irá foram escrita um ficheiro de configuração SQLDW.conf no directório de trabalho apresentar. A executar futura deste ficheiro de script do PowerShell tem a opção para todas as necessárias parâmetros deste ficheiro de configuração de ler. Se precisar de alterar alguns parâmetros, pode escolher para os parâmetros no ecrã relativamente a linha de comandos de entrada ao eliminar este ficheiro de configuração e introduzir os valores de parâmetros quando lhe for pedido ou para alterar os valores de parâmetro ao editar o ficheiro SQLDW.conf no seu diretório *- DestDir* .

>[AZURE.NOTE] Para evitar conflitos de nome de esquema com aqueles que já existam no seu DW SQL Azure, durante a leitura de parâmetros diretamente a partir do ficheiro de SQLDW.conf, um número aleatório de 3 dígitos é adicionado ao nome do esquema do ficheiro SQLDW.conf como o nome do esquema predefinido para cada execução. O script do PowerShell poderá pedir-lhe um nome de esquema: o nome pode ser especificado no critério de utilizador.

Este ficheiro de **script do PowerShell** conclui as seguintes tarefas:

- **Transfere e instala AzCopy**, se AzCopy ainda não estiver instalado

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **Dados de cópias à sua conta de armazenamento blob privada** a partir do público blob com AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Dados de cargas utilizando Polybase (através da execução LoadDataToSQLDW.sql) para o seu DW de SQL Azure** da sua conta de armazenamento blob privada com os seguintes comandos.

    - Criar um esquema

            EXEC (''CREATE SCHEMA {schemaname};'');

    - Criar uma credencial de base de dados confinado

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - Criar uma origem de dados externa para uma BLOBs do Azure armazenamento

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - Crie um formato de ficheiro externo para um ficheiro csv. Os dados são comprimidos e campos são separados com o caráter de pipe.

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - Crie tarifa externa e tabelas de viagem de conjunto de dados do pt veículo no armazenamento de Blobs do Azure.

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Carregar os dados de tabelas externas no armazenamento de Blobs do Azure para armazém de dados SQL

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Criar uma tabela de dados de exemplo (NYCTaxi_Sample) e inserir dados à mesma a partir de selecionar consultas SQL nas tabelas de viagem e tarifa. (Alguns passos destas instruções tem de utilizar esta tabela de exemplo.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

A localização geográfica das suas contas de armazenamento afeta os tempos de carregamento.

>[AZURE.NOTE] Dependendo da localização geográfica da sua conta de armazenamento de BLOBs privado, o processo de copiar dados a partir de um público blob à sua conta de armazenamento privado pode demorar cerca de 15 minutos, ou ainda mais longa, e o processo de carregamento de dados da sua conta de armazenamento à sua DW de SQL Azure poderá demorar 20 minutos ou mais longa.  

Tem de decidir o que fazer se tiver ficheiros de origem e destino duplicados.

>[AZURE.NOTE] Se o ficheiro. csv ficheiros sejam copiados a partir do armazenamento de BLOBs público à sua conta de armazenamento blob privada já existentes na sua conta de armazenamento de BLOBs privado, AzCopy irá pedir-lhe se pretende substituí-las. Se não pretender substituí-las, **n** , quando lhe for pedido de entrada. Se pretender substituir **todos os** mesmos, entrada **por** quando lhe for pedido. Também pode entrada **y** para substituir os ficheiros. csv individualmente.

![Desenhar #21][21]

Pode utilizar os seus dados. Se os dados estiverem no seu computador no local na sua aplicação de vida real, ainda pode utilizar AzCopy para carregar dados no local para o seu armazenamento de Blobs do Azure privado. Só precisa de alterar a localização de **origem** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, no comando AzCopy do ficheiro de script PowerShell para o directório local que contenha os dados.


>[AZURE.TIP] Se os seus dados já estiverem no seu armazenamento de Blobs do Azure privado na sua aplicação de vida real, pode ignorar o passo AzCopy no PowerShell script e diretamente carregar os dados para DW de SQL Azure. Isto vai requerer adicionais edições do script a personalize-o para o formato dos dados.


Este script do Powershell também se ligue às informações do Azure SQL DW informações detalhadas exemplo ficheiros de dados de SQLDW_Explorations.sql, SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py para que estes três ficheiros estão prontos para ser tentou instantaneamente após completa o script do PowerShell.

Após a execução de um bem sucedida, irá ver ecrã como abaixo:

![][20]

## <a name="dbexplore"></a>Informações detalhadas de dados e engenharia de funcionalidade no armazém de dados do SQL Azure

Nesta secção, vamos executar exploração de dados e a funcionalidade geração ao executar consultas de SQL contra Azure SQL DW directamente utilizando **Ferramentas de dados do Visual Studio**. Podem encontrar no script de exemplo com o nome *SQLDW_Explorations.sql*todas as consultas SQL utilizadas nesta secção. Este ficheiro já foi transferido para o seu diretório local pelo script do PowerShell. Também pode recuperá-la a partir do [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mas o ficheiro no Github não tem as informações do Azure SQL DW ligadas.

Ligar a sua DW do SQL Azure utilizando o Visual Studio com o nome de início de sessão do SQL DW e a palavra-passe e abra o **Explorador do objeto SQL** para confirmar que foram importadas a base de dados e as tabelas. Obter o ficheiro *SQLDW_Explorations.sql* .

>[AZURE.NOTE] Para abrir um editor de consultas paralelas dados armazém PDW (), utilize o comando **Nova consulta** durante a sua PDW estiver selecionada no **Explorador do objeto SQL**. O editor de consultas SQL padrão não é suportado pelo PDW.

Seguem-se o tipo de dados das tarefas geração informações detalhadas e funcionalidade executado nesta secção:

- Explore as distribuições de dados de alguns campos no windows variados do tempo.
- Investigar a qualidade dos dados dos campos longitude e latitude.
- Gerar etiquetas de classificação binário e multiclass com base na **Sugestão\_quantidade**.
- Gerar funcionalidades e cluster/comparar distância de viagem.
- Participe em duas tabelas e extrair uma amostra aleatória que será utilizada para construir modelos.

### <a name="data-import-verification"></a>Verificação de importação de dados

Estas consultas fornecem uma verificação rápida do número de linhas e colunas nas tabelas povoadas anterior utilizando em paralelo volume do Polybase importar,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Saída:** Deve obter 173,179,759 linhas e 14 colunas.

### <a name="exploration-trip-distribution-by-medallion"></a>Informações detalhadas: Distribuição de viagem por medallion

Este exemplo de consulta identifica medallions (veículo números) concluídos mais de 100 viagens dentro do período de tempo especificado. A consulta seria beneficiem das vantagens dos aceder a tabela com partições uma vez que deve ser condicionado pelo esquema de partição de **recolha\_datetime**. Consultar o conjunto de dados completo também irá tornar utilizar da tabela com partições e/ou índice de pesquisa.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Saída:** A consulta deve devolver uma tabela com linhas especificando os 13,369 medallions (táxis) e o número de viagem efetuada por-los no 2013. Na última coluna contém a contagem do número de viagens concluída.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Informações detalhadas: Distribuição de viagem ao medallion e hack_license

Este exemplo identifica os medallions (veículo números) e hack_license números (controladores) que concluídas mais de 100 viagens dentro de um período de tempo especificado.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Saída:** A consulta deve devolver uma tabela com 13,369 linhas especificando o 13,369 carro/controlador de IDs de que tem de ser concluída mais esse viagens 100 no 2013. Na última coluna contém a contagem do número de viagens concluída.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação da qualidade de dados: verificar os registos com longitude incorreto e/ou latitude

Este exemplo investiga se qualquer um dos campos longitude e/ou latitude quer contêm um valor inválido (radiano graus devem estar entre -90 e 90), ou se tiver (0, 0) coordenadas.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Saída:** A consulta devolve 837,467 viagens que tenham campos longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Informações detalhadas: Inclinado vs. distribuição de viagens não Inclinado

Este exemplo localiza o número de viagens foram inclinado vs. o número que não foram inclinado num período de tempo especificado (ou o conjunto completo de dados se referente ao ano completo, tal como está definido para cima aqui). Esta distribuição reflete a distribuição de etiqueta binário para ser utilizado mais tarde para modelação de classificação binário.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Saída:** A consulta deve devolver a frequência de sugestão seguinte para o ano 2013: 90,447,622 inclinado e inclinado não 82,264,709.

### <a name="exploration-tip-classrange-distribution"></a>Informações detalhadas: Distribuição de classe/intervalo de sugestão

Este exemplo fórmula calcula a distribuição dos intervalos de sugestão de um determinado período de tempo (ou o conjunto completo de dados se referente ao ano completo). Esta é a distribuição das classes de etiqueta que será utilizada mais tarde para modelação de classificação multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Resultado:**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Informações detalhadas: Calcular e comparar a distância de viagem

Este exemplo converte a recolha e remessa longitude e latitude geografia SQL pontos, fórmula calcula a distância de viagem utilizando diferença de pontos de geografia SQL e devolve um exemplo aleatório de resultados da comparação. O exemplo limita os resultados para coordenadas válidas apenas utilizando a consulta de avaliação de qualidade de dados abrangida anterior.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Utilizar SQL funções de engenharia de funcionalidade

Por vezes, funções SQL podem ser uma opção eficiente de engenharia de funcionalidade. Neste tutorial, definido uma função SQL para calcular a distância entre as localizações de recolha e dropoff direta. Pode executar os seguintes scripts de SQL nas **Ferramentas de dados do Visual Studio**.

Eis o script de SQL que define a função distância.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

Eis um exemplo para ligar a esta função para gerar funcionalidades na sua consulta SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Saída:** Esta consulta gera uma tabela (com 2,803,538 linhas) com recolha e dropoff norte e longitudes e a distância direta correspondente em milhas. Seguem-se aos resultados pela primeira vez, 3 linhas:

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>Preparar dados para criar um modelo

A consulta seguinte associações a **nyctaxi\_viagem** e **nyctaxi\_tarifa** tabelas, gera uma classificação binário etiqueta **inclinado**, uma etiqueta de classificação de classe com várias **Sugestão\_classe**e extrai de uma amostra do conjunto de dados associado completo. As amostras são feita através da obtenção de um subconjunto de viagens com base no tempo escolha.  Esta consulta pode ser copiada, em seguida, colada diretamente na [Azure máquina aprendizagem Studio](https://studio.azureml.net) [Importar dados] [ import-data] módulo para ingestão dados direta da instância de base de dados do SQL no Azure. A consulta exclui registos com incorreto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Quando estiver pronto para avançar para a formação do Azure máquina, pode:  

1. Guardar a consulta SQL final para extrair e os dados de exemplo e copiar colar a consulta diretamente para um [Importar dados] de[ import-data] módulo no Azure formação de máquina, ou
2. Manter os dados de amostra e engenharia que pretenda utilizar para criar uma nova tabela do SQL DW de modelo e utilizar a nova tabela na [Importar dados] [ import-data] módulo no Azure máquina aprendizagem. O script do PowerShell no passo anterior tenha feito para si. Pode obter diretamente a partir desta tabela no módulo importar dados.


## <a name="ipnb"></a>Informações detalhadas de dados e engenharia de funcionalidade no IPython bloco de notas

Nesta secção, podemos fará exploração de dados e geração funcionalidade utilizar ambas as Python e consultas SQL contra a DW SQL criou anteriormente. Um bloco de notas de IPython exemplo com o nome **SQLDW_Explorations.ipynb** e um ficheiro de script Python **SQLDW_Explorations_Scripts.py** tiverem sido transferidas para o seu diretório local. Também estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Estes dois ficheiros são idênticos em Python scripts. O ficheiro de script Python é fornecido caso não tiver um servidor de IPython bloco de notas. Estes dois ficheiros de Python exemplo destinam-se em **Python 2.7**.

As informações de Azure SQL DW necessárias no exemplo IPython bloco de notas e o ficheiro de script Python são transferidas para o seu computador local foi ligadas ao script do PowerShell anteriormente. Estes são executáveis sem qualquer modificação.

Se já configurou uma área de trabalho AzureML, pode carregar a amostra IPython bloco de notas para o serviço de bloco de notas do AzureML IPython diretamente e iniciar executá-la. Eis os passos para carregar para o serviço de bloco de notas do AzureML IPython:

1. Inicie sessão na sua área de trabalho AzureML, clique em "Studio" na parte superior e clique em "Blocos de notas" no lado esquerdo da página web.

    ![Desenhar #22][22]

2. Clique em "Novo" no canto inferior esquerdo da página web e selecione "Python 2". Em seguida, forneça um nome ao bloco de notas e clique na marca de verificação para criar o bloco de notas IPython novo em branco.

    ![Desenhar #23][23]

3. Clique no símbolo "Jupyter" no canto superior esquerdo do bloco de notas novo IPython.

    ![Desenhar #24][24]

4. Arraste e largue a amostra IPython bloco de notas para a página de **árvore** do seu serviço de bloco de notas do AzureML IPython e clique em **carregar**. Em seguida, a amostra IPython bloco de notas será carregada para o serviço de bloco de notas do AzureML IPython.

    ![Desenhar #25][25]

Para executar o exemplo IPython bloco de notas ou o Python ficheiro de script, as seguintes Python pacotes são necessários. Se estiver a utilizar o serviço de bloco de notas do AzureML IPython, estes pacotes tem sido pré-instalados.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

A sequência recomendada durante a criação de soluções de análises avançadas no AzureML com dados de grandes dimensões é o seguinte procedimento:

- Ler uma amostra pequenas dos dados numa moldura na memória dados.
- Execute algumas visualizações e explorations a utilizar os dados de amostra.
- Experimentar a utilizar os dados de amostra de engenharia de funcionalidade.
- Para informações detalhadas de dados maior, manipulação de dados e engenharia de funcionalidade, utilize Python para emitir consultas SQL diretamente contra a DW de SQL.
- Decida o tamanho da amostra seja adequado para criar um modelo Azure máquina aprendizagem.

As seguintes são algumas informações detalhadas de dados, visualização de dados e funcionalidade engenharia exemplos. Podem encontrar mais explorations de dados no exemplo IPython bloco de notas e o ficheiro de script de Python de exemplo.

### <a name="initialize-database-credentials"></a>Iniciar credenciais base de dados

Iniciar as suas definições de ligação de base de dados no variáveis que se seguem:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Criar ligação de base de dados

Eis a cadeia de ligação que cria a ligação à base de dados.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Número de linhas e colunas na tabela < nyctaxi_trip > do relatório

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de linhas = 173179759  
- Número total de colunas = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Número de linhas e colunas na tabela < nyctaxi_fare > do relatório

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de linhas = 173179759  
- Número total de colunas = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Leitura-in uma amostra de pequenas dados a partir da base de dados do armazém de dados do SQL

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Data/hora a leitura que da tabela de exemplo é 14.096495 segundos.  
Número de linhas e colunas obtidos = (1000, 21).

### <a name="descriptive-statistics"></a>Estatística descritiva

Agora, está pronto para explorar os dados de amostra. Vamos começar com consultar alguns Estatística descritiva para o **viagem\_distância** (ou campos de optar por especificar).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de desenho de caixa

Seguinte olharmos para o desenho de caixa para a distância de viagem visualizar o quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Desenhar #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de desenho de distribuição

Representações visualizar a distribuição e um histograma para a distância entre viagem de amostra.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Desenhar #2][2]

### <a name="visualization-bar-and-line-plots"></a>Desenha a linha e de barras de visualização:

Neste exemplo, a distância de viagem em agrupamentos de cinco de agrupamentos e visualizar os resultados binning.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vamos pode desenhar a distribuição posição acima numa barra ou linha desenho com:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Desenhar #3][3]

e

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Desenhar #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualização: Exemplos de Scatterplot

Mostramos-desenho de gráficos de dispersão entre **viagem\_tempo\_no\_seg.** e **viagem\_distância** para ver se existe qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Desenhar #6][6]

Da mesma forma pode verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Desenhar #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Informações detalhadas de dados nos dados recolhidos através do consultas SQL IPython bloco de notas

Nesta secção, vamos explorar distribuições de dados a utilizar os dados recolhidos que são mantidos na nova tabela que criámos acima. Tenha em atenção que podem ser executadas explorations semelhantes utilizando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Informações detalhadas: Relatório o número de linhas e colunas na tabela de amostra

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Informações detalhadas: Inclinado/não convertidas distribuição

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Informações detalhadas: Distribuição de classe de sugestão

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Informações detalhadas: Desenhar a distribuição de sugestão por classe

    tip_class_dist['tip_freq'].plot(kind='bar')

![Desenhar #26][26]


#### <a name="exploration-daily-distribution-of-trips"></a>Informações detalhadas: Diária distribuição de viagens

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Informações detalhadas: Distribuição de viagem por medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Informações detalhadas: Distribuição de viagem pela medallion e ataque à licença

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Informações detalhadas: Distribuição de tempo de viagem

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Informações detalhadas: Distribuição de distância de viagem

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Informações detalhadas: Distribuição de tipo de pagamento

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verificar o formato da tabela featurized final

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Construir modelos no Azure máquina aprendizagem

Vamos agora está prontos avançar para criar um modelo e implementação do modelo no [Azure máquina aprendizagem](https://studio.azureml.net). Os dados estão prontos para ser utilizado em qualquer um dos problemas predição verificados anteriormente, nomeadamente:

1. **Classificação binária**: para prever ou não uma sugestão foi pagos para uma viagem.

2. **Classificação multiclass**: para prever o intervalo de sugestão paga, de acordo com as classes definidas anteriormente.

3. **Tarefa de regressão**: para prever a quantidade de sugestão paga uma viagem.  



Para iniciar o exercício modelação, iniciar sessão para a área de trabalho do **Azure máquina aprendizagem** . Se ainda não criou uma área de trabalho de aprendizagem automática, consulte o artigo [criar uma área de trabalho do Azure ML](machine-learning-create-workspace.md).

1. Para começar a trabalhar com Azure máquina formação, consulte o artigo [o que é o Azure máquina aprendizagem Studio?](machine-learning-what-is-ml-studio.md)

2. Iniciar sessão no [Azure máquina de formação Studio](https://studio.azureml.net).

3. A página de base Studio fornece vastos de informações, vídeos, tutoriais, ligações para a referência de módulos e outros recursos. Para mais informações sobre a formação do Azure máquina, consulte o [Centro de documentação de formação do Azure máquina](https://azure.microsoft.com/documentation/services/machine-learning/).

Uma experiência de formação típica é constituído pelos seguintes passos:

1. Crie uma experiência **+ Novo** .
2. Obtenha os dados para o Azure ML.
3. Preparação do processo, transformar e manipular os dados conforme necessário.
4. Gera funcionalidades conforme necessário.
5. Dividir os dados em conjuntos de dados formação / / teste de validação (ou tiver conjuntos de dados separados para cada).
6. Selecione um ou mais máquina formação dos algoritmos dependendo resolver o problema de aprendizagem. Por exemplo, classificação binária, classificação multiclass, regressão.
7. Prepare um ou mais modelos que utilizem o conjunto de dados de formação.
8. Pontuação o conjunto de dados de validação, utilizando os modelos de formação.
9. Avalie os modelos para calcular as métricas relevantes para o problema de aprendizagem.
10. Ajustar otimizar os modelos de e selecione o modelo de utilizações para implementar.

Neste exercício, vamos ter já explorou e engenharia os dados no armazém de dados SQL e decidido no tamanho da amostra ingerir esta última no Azure ML. Eis o procedimento para criar um ou mais dos modelos de previsão:

1. Colocar os dados no Azure ML utilizando os [Importar dados] [ import-data] módulo, disponível na secção **dados de entrada e saída** . Para obter mais informações, consulte o artigo [Importar dados] [ import-data] página de referência do módulo.

    ![Azure ML importar dados][17]

2. Selecione a **Base de dados do SQL Azure** como a **origem de dados** no painel de **Propriedades** .

3. Introduza o nome DNS de base de dados no campo **nome de servidor de base de dados** . Formato:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Introduza o **nome da base de dados** no campo correspondente.

5. Introduza o *nome de utilizador SQL* no **nome de conta de utilizador do servidor**e *palavra-passe* na **palavra-passe do conta de utilizador do servidor**.

6. Selecione a opção **aceitar qualquer certificado de servidor** .

7. Na área de **consulta de base de dados** editar texto, a consulta que extrai o necessário campos (incluindo quaisquer campos calculados como as etiquetas) de base de dados e para baixo de colar amostras os dados para o tamanho da amostra pretendida.

É um exemplo de uma experiência de classificação binário ler dados diretamente a partir da base de dados do armazém de dados SQL na figura abaixo (não se esqueça substituir a nyctaxi_trip de nomes de tabela e nyctaxi_fare ao nome do esquema e os nomes de tabela que utilizou no seu instruções passo a passo). Experiências semelhantes podem ser criadas para a classificação multiclass e problemas de regressão.

![Azure ML comboio][10]

> [AZURE.IMPORTANT] Os dados de modelação extração e amostragem exemplos de consulta fornecidos nas secções anteriores, **todas as etiquetas para os exercícios três modelação estão incluídas na consulta**. Um passo (obrigatório) importante em cada uma dos exercícios modelação é **Excluir** as etiquetas desnecessárias para os outros dois problemas e quaisquer outros **fugas de destino**. Por exemplo, ao utilizar classificação binária, utilize a etiqueta **inclinado** e excluir os campos **Sugestão\_classe**, **Sugestão\_quantidade**, e **total\_quantidade**. O último é destino fugas desde que implicam a sugestão paga.
>
> Para excluir qualquer colunas desnecessárias ou fugas de destino, poderá utilizar as [Selecionar colunas no conjunto de dados] [ select-columns] módulo ou os [Metadados editar][edit-metadata]. Para obter mais informações, consulte o artigo [Selecionar colunas no conjunto de dados] [ select-columns] e [Editar metadados] [ edit-metadata] páginas de referência.

## <a name="mldeploy"></a>Implementar modelos de formação de máquina do Azure

Quando o seu modelo estiver pronto, pode facilmente implementá-lo como um serviço web diretamente a partir da experiência. Para mais informações sobre como implementar o Azure ML web services, consulte o artigo [Implementar um serviço web de aprendizagem do Azure máquina](machine-learning-publish-a-machine-learning-web-service.md).

Para implementar um novo serviço web, tem de:

1. Crie uma experiência de pontuação.
2. Implemente o serviço web.

Para criar uma experiência de pontuação a partir de uma experiência de formação **terminado** , clique em **Criar PONTUAÇÃO EXPERIMENTAR** na barra de ação inferior.

![Pontuação Azure][18]

Formação de máquina Azure irá tentar criar uma experiência de pontuação com base nos componentes da experiência de formação. Em particular, irá:

1. Guarde o modelo de formação e remova os módulos de formação do modelo.
2. Identifique uma lógica **porta de entrada** para representar o esquema de dados de entrada esperado.
3. Identifique uma lógica **porta de saída** para representar o esquema de saída de serviço web esperado.

Quando é criada a experiência de pontuação, revê-la e criar ajuste conforme necessário. É um ajustamento típico substituir o conjunto de dados de entrada e/ou a consulta com um que exclui campos de etiqueta, tal como estes não estará disponíveis quando o serviço é denominado. Também é aconselhável para reduzir o tamanho do conjunto de dados de entrada e/ou a consulta a alguns registos, basta suficiente para indicar o esquema de teclado. Para a porta de saída, é comum para excluir a entrada todos os campos e incluir apenas a **Classificados etiquetas** e **Classificados probabilidades** na saída utilizando as [Selecionar colunas no conjunto de dados] [ select-columns] módulo.

Um exemplo de pontuação experiência é fornecido na figura abaixo. Quando estiver pronto para implementar o, clique no botão de **Publicar serviço WEB** na barra de ação inferior.

![Publicar Azure ML][11]


## <a name="summary"></a>Resumo
Para recap o que fizemos neste tutorial instruções passo a passo, ter criado um ambiente de ciências dados Azure, trabalhado com um conjunto de dados público grande, tendo-lo através do equipa dados ciência processo, tudo a partir do aquisição de dados para modelar formação e, em seguida, a implementação de um serviço web de aprendizagem do Azure máquina.

### <a name="license-information"></a>Informações de licença

Este tutorial de exemplo e a respectiva a que o acompanha scripts e IPython notebook(s) são partilhados pela Microsoft ao abrigo da licença MIT. Selecione o ficheiro License no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências

• [Andrés Monroy pt veículo viagens de página de transferência](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing dados de viagem de veículo do pt por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Veículo pt e Limousine Comissão investigação e estatísticas](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
