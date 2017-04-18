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


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Carregar os dados do SQL Server para armazém de dados do SQL Azure (AZCopy)

Utilize bcp e AZCopy utilitários de linha de comandos para carregar os dados do SQL Server ao armazenamento de Blobs do Azure. Em seguida, utilize o PolyBase ou Azure dados fábrica para carregar os dados para armazém de dados do SQL Azure. 


## <a name="prerequisites"></a>Pré-requisitos

Para visualizar passo a passo neste tutorial, é necessário:

- Uma base de dados do armazém de dados SQL
- O utilitário de linha de comandos bcp instalado
- O utilitário de linha de comandos SQLCMD instalado

>[AZURE.NOTE] Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de transferências da Microsoft][].

## <a name="import-data-into-sql-data-warehouse"></a>Importar dados para armazém de dados SQL

Neste tutorial, irá criar uma tabela no armazém de dados do SQL Azure e importar dados para a tabela.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Passo 1: Criar uma tabela no armazém de dados do SQL Azure

A partir de uma linha de comandos, utilize sqlcmd para executar a consulta seguinte para criar uma tabela no seu instância:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

>[AZURE.NOTE] Consulte o artigo [Descrição geral de tabela][] ou [uma sintaxe criar tabela][] para obter mais informações sobre a criação de uma tabela no armazém de dados SQL e as opções disponíveis na cláusula com.

### <a name="step-2-create-a-source-data-file"></a>Passo 2: Criar um ficheiro de dados de origem

Abra o bloco de notas e copie as seguintes linhas de dados para um novo ficheiro de texto e, em seguida, guardar o ficheiro no seu diretório local temp, C:\Temp\DimDate2.txt.

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

> [AZURE.NOTE] É importante lembrar-se de que bcp.exe não suporta a codificação de ficheiro de UTF-8. Utilize ficheiros ASCII ou UTF-16 codificado ficheiros ao utilizar bcp.exe.

### <a name="step-3-connect-and-import-the-data"></a>Passo 3: Ligar e importar os dados
Utilizar bcp, pode ligar e importar os dados utilizando o seguinte comando substituição de valores conforme adequado:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Pode verificar os dados que foram carregados ao executar a consulta seguinte utilizando sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Isto deve devolver os seguintes resultados:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Passo 4: Criar estatísticas dos seus dados carregado recentemente

Armazém de dados SQL Azure é que ainda não suporte automática criar ou actualizar as estatísticas de automaticamente. Para obter o melhor desempenho de consultas, é importante que estatísticas de ser criada em todas as colunas de todas as tabelas após a primeira carregar ou quaisquer alterações substanciais ocorrerem dos dados. Para obter uma explicação detalhada das estatísticas, consulte o tópico de [Estatísticas][] no grupo desenvolver de tópicos. Abaixo está um exemplo rápido de como criar estatísticas sobre a tabela carregado neste exemplo

Execute os seguintes declarações estatísticas de criar a partir de um pedido de sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportar dados do armazém de dados SQL
Neste tutorial, irá criar um ficheiro de dados de uma tabela no armazém de dados SQL. Vamos irá exportar os dados que criámos acima para um novo ficheiro de dados denominado DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Passo 1: Exporte os dados

Utilizar o utilitário bcp, pode ligar e exportar dados utilizando o seguinte comando substituição de valores conforme adequado:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pode verificar que os dados foram exportados corretamente, ao abrir o novo ficheiro. Os dados no ficheiro devem corresponder o texto abaixo:

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

>[AZURE.NOTE] Devido à natureza distribuídas sistemas, a ordem de dados não poderá ser igual ao longo de bases de dados do armazém de dados do SQL. Outra opção é utilizar a função de **queryout** de bcp escrever extrair uma consulta em vez de exportar a tabela inteira.

## <a name="next-steps"></a>Próximos passos
Para obter uma descrição geral de carregamento, consulte o artigo [carregar dados para armazém de dados SQL][].
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL][].

<!--Image references-->

<!--Article references-->

[Carregar os dados para armazém de dados SQL]: ./sql-data-warehouse-overview-load.md
[Descrição geral do desenvolvimento armazém de dados SQL]: ./sql-data-warehouse-overview-develop.md
[Descrição geral de tabela]: ./sql-data-warehouse-tables-overview.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Sintaxe de criar tabela]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de transferências da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
