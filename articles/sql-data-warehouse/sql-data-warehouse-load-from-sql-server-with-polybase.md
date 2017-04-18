<properties
   pageTitle="Carregar os dados do SQL Server para armazém de dados do SQL Azure (PolyBase) | Microsoft Azure"
   description="Utiliza bcp para exportar dados do SQL Server para ficheiros simples, AZCopy para importar dados para o armazenamento de Blobs do Azure e PolyBase para ingerir esta última os dados para armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Carregar os dados com PolyBase no armazém de dados SQL

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Este tutorial mostra como carregar os dados para armazém de dados SQL utilizando AzCopy e PolyBase. Quando tiver terminado, saberá como:

- Utilizar AzCopy para copiar dados para o armazenamento de Blobs do Azure
- Criar objectos de base de dados para definir os dados
- Executar uma consulta T SQL para carregar os dados

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## <a name="prerequisites"></a>Pré-requisitos

Para visualizar passo a passo neste tutorial, é necessário

- Uma base de dados do SQL armazém de dados.
- Uma conta de armazenamento Azure do tipo localmente redundantes armazenamento padrão (LRS padrão), armazenamento Geo Redundant padrão (padrão GRS) ou padrão acesso de leitura Geo Redundant armazenamento (padrão RAGRS).
- Utilitário de linha de comandos AzCopy. Transfira e instale a [versão mais recente do AzCopy][] que é instalado com as ferramentas de armazenamento do Microsoft Azure.

    ![Ferramentas de armazenamento Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Passo 1: Adicionar dados de exemplo para o armazenamento de Blobs do Azure

Para carregar os dados, precisamos de colocar alguns dados de exemplo num armazenamento de Blobs do Azure. Neste passo, podemos preencher um blob de armazenamento do Windows Azure com dados de exemplo. Mais tarde, utilizamos PolyBase para carregar estes dados de exemplo para a base de dados do SQL armazém de dados.

### <a name="a-prepare-a-sample-text-file"></a>RESPOSTAS. Preparar um ficheiro de texto de exemplo

Para preparar um ficheiro de texto de exemplo:

1. Abra o bloco de notas e copie as seguintes linhas de dados para um novo ficheiro. Guardá-lo para o seu diretório local temp como % temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Localizar o seu ponto final de serviço de BLOBs

Para localizar o seu ponto final de serviço de BLOBs:

1. A partir do Portal do Azure selecione **Procurar** > **Contas de armazenamento**.
2. Clique na conta de armazenamento que pretende utilizar.
3. No pá de conta de armazenamento, clique em Blobs

    ![Clique em Blobs](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Guarde o seu URL de ponto final de serviço de BLOBs para utilização posterior.

    ![Ponto final de serviço blob](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Localizar a sua chave de armazenamento Azure

Para localizar a chave de armazenamento Azure:

1. A partir do Portal do Azure, selecione **Procurar** > **Contas de armazenamento**.
2. Clique na conta de armazenamento que pretende utilizar.
3. Selecione **todas as definições de** > **teclas de acesso**.
4. Clique na caixa copiar para copiar uma das teclas de acesso para a área de transferência.

    ![Copiar a chave de armazenamento Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Copie o ficheiro de exemplo para o armazenamento de Blobs do Azure

Para copiar os dados ao armazenamento de Blobs do Azure:

1. Abra uma linha de comandos e altere directórios para o diretório de instalação AzCopy. Este comando muda para o directório de instalação predefinido de um cliente do Windows de 64 bits.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Execute o seguinte comando para carregar o ficheiro. Especificar o URL de ponto final de serviço de BLOBs para <blob service endpoint URL> e a sua chave de conta de armazenamento Azure para < azure_storage_account_key >.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Consulte também [Introdução ao utilitário de linha de comandos do AzCopy][versão mais recente do AzCopy].

### <a name="e-explore-your-blob-storage-container"></a>E. Explorar o contentor de armazenamento de BLOBs

Para ver os ficheiros carregados ao armazenamento blob:

1. Volte à sua pá de serviço de Blobs.
2. Em contentores, faça duplo clique **datacontainer**.
3. Para explorar o caminho para os seus dados, clique na pasta **datedimension** e irá ver o seu ficheiro carregado **DimDate2.txt**.
4. Para visualizar as propriedades, clique em **DimDate2.txt**.
5. Tenha em atenção que no pá de propriedades Blob, pode transferir ou elimine o ficheiro.

    ![BLOBs do Azure armazenamento de vista](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Passo 2: Criar uma tabela externa para os dados de exemplo

Nesta secção, podemos criar uma tabela externa que define os dados de exemplo.

PolyBase utiliza tabelas externas para aceder a dados no armazenamento de Blobs do Azure. Uma vez que os dados não estiver armazenados dentro armazém de dados do SQL, PolyBase processa a autenticação aos dados externos utilizando uma credencial confinados de base de dados.

O exemplo neste passo utiliza estas instruções Transact-SQL para criar uma tabela externa.

- [Criar o modelo global de chave (Transact-SQL)][] para encriptar o segredo da base de dados delimitada credenciais.
- [Criar base de dados no âmbito das credenciais (Transact-SQL)][] para especificar informações de autenticação para a sua conta de armazenamento Azure.
- [Criar origem de dados externa (Transact-SQL)][] para especificar a localização do seu armazenamento de Blobs do Azure.
- [Criar externos formato de ficheiro (Transact-SQL)][] para especificar o formato dos dados.
- [Criar tabela externa (Transact-SQL)][] para especificar a definição da tabela e localização dos dados.

Execute esta consulta contra a sua base de dados do SQL armazém de dados. -Lo irá criar uma tabela externa com o nome DimDate2External no esquema de dbo que aponta para os dados de exemplo DimDate2.txt no armazenamento de Blobs do Azure.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


No Explorador de objeto de servidor do SQL no Visual Studio, pode ver o formato de ficheiro externos, origem de dados externa e a tabela DimDate2External.

![Tabela externa da vista](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Passo 3: Carregar os dados para armazém de dados SQL

Quando a tabela externa estiver criada, pode carregar os dados para uma nova tabela ou pode inseri-lo numa tabela existente.

- Para carregar os dados para uma nova tabela, execute a criar tabela como (Transact-SQL) instrução [SELECT][] . A nova tabela terá colunas nomeadas na consulta. Os tipos de dados das colunas devolvida correspondem os tipos de dados na definição de tabela externa.
- Para carregar os dados para uma tabela já existente, utilize a inserir [... SELECIONE (Transact-SQL)][] declaração.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Passo 4: Criar estatísticas dos seus dados carregado recentemente

Armazém de dados SQL não criação automática ou as estatísticas de actualização automática. Por conseguinte, para alcançar o desempenho da consulta alta, é importante criar estatísticas em cada coluna de cada tabela após a primeira carregar. Também é importante atualizar as estatísticas após alterações substanciais dos dados.

Este exemplo cria uma coluna estatísticas a nova tabela DimDate2.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Para saber mais, consulte o artigo [Estatísticas][].  


## <a name="next-steps"></a>Próximos passos
Consulte o [Guia de PolyBase][] para obter mais informações que deve saber como desenvolver uma solução que utiliza PolyBase.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Guia de PolyBase]: ./sql-data-warehouse-load-polybase-guide.md
[versão mais recente do AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CRIAR a origem de dados externa (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CRIAR o formato de ficheiro externo (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[Criar tabela externa (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[Criar tabela como SELECIONAR (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERIR... SELECIONE (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CRIAR a chave de modelo global de (o Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CRIAR a base de dados SCOPED CREDENCIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
