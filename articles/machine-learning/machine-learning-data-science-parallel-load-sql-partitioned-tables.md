<properties 
    pageTitle="Paralelo a importação de dados em massa utilizando tabelas de partição SQL | Microsoft Azure" 
    description="Importação de dados em massa paralela utilizando tabelas de partição SQL" 
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
    ms.author="bradsev" /> 

# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Importação de dados em massa paralela utilizando tabelas de partição SQL

Este documento descreve como criar tabelas com partições para a importação de rápida paralela em massa de dados para uma base de dados do SQL Server. Para grande carga/transferência de dados numa base de dados de SQL, pode ser melhorada a importar dados para a base de dados de SQL e consultas subsequentes utilizando _tabelas de partições e vistas_. 


## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Criar uma nova base de dados e um conjunto dos grupos de ficheiros

- [Criar uma nova base de dados](https://technet.microsoft.com/library/ms176061.aspx) (se não existir)
- Adicionar os grupos de ficheiros de base de dados na base de dados que vai conter os ficheiros físicos com partições

  Nota: Isto pode ser feito com [Criar base de dados](https://technet.microsoft.com/library/ms176061.aspx) se novos ou [Alterar a base de dados](https://msdn.microsoft.com/library/bb522682.aspx) se a base de dados já existente

- Adicionar um ou mais ficheiros (conforme necessário) para cada grupo de ficheiros de base de dados

 > [AZURE.NOTE] Especificar o grupo de ficheiros de destino que contém os dados para esta partição e os nomes de ficheiro de base de dados física onde serão armazenados os dados do grupo de ficheiros.
 
O exemplo seguinte cria uma nova base de dados com três os grupos de ficheiros que não seja o principal e grupos de registo, que contém um ficheiro físico em cada um. Os ficheiros de base de dados são criados na pasta de dados do SQL Server predefinido, conforme configurado na instância do SQL Server. Para mais informações sobre as localizações predefinidas de ficheiros, consulte [Localizações de ficheiro predefinido e o nome de instâncias do SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
        CREATE DATABASE <database_name>
        ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
        LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## <a name="create-a-partitioned-table"></a>Criar uma tabela com partições

Crie tabelas com partições definidas de acordo com o esquema de dados mapeado para os base de dados os grupos de ficheiros criados no passo anterior. Quando dados em massa importada para a tabela de partição (s), registos serão distribuídos entre os grupos de ficheiros em conformidade com um esquema de partição, conforme descrito abaixo.

**Para criar uma tabela de partição, necessita de:**

- [Criar uma função de partição](https://msdn.microsoft.com/library/ms187802.aspx) que define o intervalo de valores/limites que deseja incluir em cada tabela de partição individuais, por exemplo, para limitar partições por mês (alguns\_data/hora\_campo) do ano de 2013:

        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

- [Criar um esquema de partição](https://msdn.microsoft.com/library/ms179854.aspx) que mapeia cada intervalo de partição na função de partição para um grupo de ficheiros físico, por exemplo:

        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  Sugestão: Para verificar se os intervalos em vigor em cada partição de acordo com o esquema/função, execute a seguinte consulta:

        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

- [Criar tabela de partição](https://msdn.microsoft.com/library/ms174979.aspx) (s) acordo para o esquema de dados, e especifique o campo de esquema e restrição de partição utilizado para criar partições da tabela, por exemplo:

        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Para mais informações, consulte [criar tabelas de partições e os índices](https://msdn.microsoft.com/library/ms188730.aspx).


## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Os dados para cada tabela de partição individuais de importação em massa

- Pode utilizar BCP, inserção em massa ou outros métodos como [O Assistente de migração do SQL Server](http://sqlazuremw.codeplex.com/). O exemplo fornecido utiliza o método BCP.

- [Alterar a base de dados](https://msdn.microsoft.com/library/bb522682.aspx) para alterar o esquema de registo de transacções para BULK_LOGGED para minimizar os custos gerais de início de sessão, por exemplo:

        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- Para acelerar o carregamento de dados, inicie as operações de importação em massa em paralelo. Para obter sugestões sobre acelerar em massa a importação de dados grandes em bases de dados do SQL Server, consulte a [carregar 1 TB em menos de 1 hora](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

O seguinte script PowerShell é um exemplo de dados paralelos a carregar utilizando BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Criar índices para optimizar o desempenho de consulta e de associações

- Se vai extrair dados de modelação de várias tabelas, crie índices nas teclas de associação para melhorar o desempenho de associação.

- [Criar índices](https://technet.microsoft.com/library/ms188783.aspx) (em cluster ou sem clusters) especificado o mesmo grupo de ficheiros para cada partição, por exemplo, para:

        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
ou,

        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] Pode optar por criar os índices antes de importar os dados de em massa. Criação de índices antes da importação em massa irá diminuir os carregamento de dados.


## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Tecnologia no exemplo de acção e o processo de Analytics Avançado

Para obter um exemplo de instruções passo a passo a-ponto utilizando o processo de Analytics Cortana com um conjunto de dados público, consulte [Cortana Analytics processo em acção: utilizar o SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
 
