<properties 
   pageTitle="Copiar dados entre o arquivo de dados de Lake e base de dados do Azure SQL utilizando Sqoop | Microsoft Azure"
   description="Utilizar Sqoop para copiar dados entre base de dados do SQL Azure e o arquivo de dados de Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copiar dados entre o arquivo de dados de Lake e base de dados do Azure SQL utilizando Sqoop

Saiba como utilizar Apache Sqoop para importar e exportar dados entre a base de dados do SQL Azure e o arquivo de Lake de dados.
 

## <a name="what-is-sqoop"></a>O que é Sqoop?

Aplicações de dados grande são uma escolha natural para processamento de dados não estruturados e semiestruturados, como registos e ficheiros. No entanto, também poderá uma necessidade de dados estruturados que estão armazenados no bases de dados relacionais do processo.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) é uma ferramenta concebida para transferir dados entre bases de dados relacionais e um repositório de dados grande, tal como arquivo de dados de Lake. Pode utilizá-lo para importar dados a partir de um sistema de gestão de base de dados relacional (RDBMS) como base de dados do Azure SQL para o arquivo de dados de Lake. Pode, em seguida, transformar e analisar os dados utilizando cargas de trabalho de grande dados e, em seguida, exporte os dados novamente para uma RDBMS. Neste tutorial, utilize uma base de dados do SQL Azure como a base de dados relacional para importar/exportar de.
 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Ativar a sua subscrição do Azure** para pré-visualização de público do arquivo de Lake de dados. Consulte as [instruções](data-lake-store-get-started-portal.md#signup). 
- **Cluster azure HDInsight** com acesso a uma conta do arquivo de Lake de dados. Consulte o artigo [criar um cluster de HDInsight com dados Lake arquivo](data-lake-store-hdinsight-hadoop-use-portal.md). Este artigo assume que tem um cluster de HDInsight Linux com o access de arquivo de Lake de dados.
- **Base de dados azure SQL**. Para obter instruções sobre como criar uma, consulte o artigo [criar uma base de dados do Azure SQL](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Saiba rapidamente com vídeos?

[Veja este vídeo](https://mix.office.com/watch/1butcdjxmu114) sobre como copiar dados entre o Azure armazenamento de Blobs e arquivo de dados de Lake utilizando DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Criar tabelas de exemplo na base de dados do SQL Azure

1. Para começar, crie duas tabelas de exemplo na base de dados do SQL Azure. Utilize o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio para ligar à base de dados do SQL Azure e, em seguida, executar consultas que se seguem.

    **Criar tabela1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **Criar tabela2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. Na **tabela1**, adicione alguns dados de exemplo. Deixe **tabela2** vazia. Vamos irá importar dados de **tabela1** para dados Lake loja. Em seguida, podemos irá exportar dados a partir do arquivo de dados de Lake para **tabela2**. Execute o seguinte fragmento.

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Utilizar Sqoop a partir de um cluster de HDInsight com acesso ao arquivo de dados de Lake

Um cluster de HDInsight já tem os pacotes de Sqoop disponíveis. Se tiver configurado o cluster HDInsight para utilizar o arquivo de dados de Lake como um armazenamento adicional, pode utilizar Sqoop (sem alterações à configuração) para importar/exportar dados entre uma base de dados relacional (neste exemplo, a base de dados do SQL Azure) e uma conta do arquivo de Lake de dados. 

1. Para neste tutorial, vamos assumir que criou um cluster de Linux, pelo que deve utilizar SSH para ligar ao cluster. Consulte o artigo [ligar a um cluster de baseado em Linux HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Verifique se pode aceder a conta de arquivo de Lake dados a partir do cluster. Execute o seguinte comando da linha de comandos do SSH:

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Isto deve fornecer uma lista de ficheiros/pastas na conta de arquivo de Lake de dados.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importar dados a partir da base de dados do Azure SQL para o arquivo de dados de Lake

3. Navegue para o diretório onde Sqoop pacotes estão disponíveis. Normalmente, isto irá estar `/usr/hdp/<version>/sqoop/bin`. 

4. Importe os dados de **tabela1** na conta do arquivo de Lake de dados. Utilize a seguinte sintaxe:

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Tenha em atenção que esse marcador de posição **de base de dados--nome do sql server** representa o nome do servidor onde a base de dados do Azure SQL está em execução. Marcador de posição do **nome da base de dados de SQL** representa o nome da base de dados real.

    Por exemplo,

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Certifique-se de que foi transferidos os dados para a conta de arquivo de Lake de dados. Execute o seguinte comando:

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Deverá ver o resultado seguinte.

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Cada **Peça-m -** * ficheiro corresponde a uma linha na tabela de origem, * *tabela1**. Pode ver os conteúdos da peça-m -* ficheiros para confirmar.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exportar dados do arquivo de dados de Lake numa base de dados do Azure SQL

6. Exporte os dados a partir da conta do arquivo de Lake de dados para a tabela vazia, **tabela2**, da base de dados do SQL Azure. Utilize a seguinte sintaxe.

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Por exemplo,

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Certifique-se de que os dados foram carregados para a tabela de base de dados SQL. Utilize o [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio para ligar à base de dados do SQL Azure e, em seguida, execute a seguinte consulta.

        
        SELECT * FROM TABLE2

    Isto deve ter o seguinte resultado.

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>Consulte também

- [Copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
