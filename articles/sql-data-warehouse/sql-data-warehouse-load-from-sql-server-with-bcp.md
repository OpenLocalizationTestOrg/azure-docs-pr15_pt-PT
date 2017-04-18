<properties
   pageTitle="Carregar os dados do SQL Server para armazém de dados do SQL Azure (bcp) | Microsoft Azure"
   description="Para um tamanho de dados pequenas utiliza bcp para exportar dados do SQL Server para ficheiros simples e importar os dados diretamente para armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Carregar os dados do SQL Server para armazém de dados do SQL Azure (ficheiros simples)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Para pequenas conjuntos de dados, pode utilizar o utilitário da linha de comandos bcp para exportar dados do SQL Server e, em seguida, carregue-o diretamente ao armazém de dados do SQL Azure.

Neste tutorial, irá utilizar bcp para:

- Exportar uma tabela a partir do SQL Server utilizando o bcp comando (ou criar um ficheiro de exemplo simples)
- Importe a tabela a partir de um ficheiro simples para armazém de dados SQL.
- Crie estatísticas nos dados carregados.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para visualizar passo a passo neste tutorial, é necessário:

- Uma base de dados do armazém de dados SQL
- O utilitário de linha de comandos bcp instalado
- O utilitário de linha de comandos sqlcmd instalado

Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de transferências da Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Dados no formato ASCII ou UTF-16

Se estiver a tentar este tutorial com os seus dados, os seus dados tem de utilizar a ASCII ou UTF-16 codificação de uma vez que bcp não suporta UTF-8. 

PolyBase suporta UTF-8 mas ainda não suporta UTF-16. Tenha em atenção que, se pretender combinar bcp com PolyBase terá de transformar os dados para UTF-8 depois de ter é exportado do SQL Server. 


## <a name="1-create-a-destination-table"></a>1. a criar uma tabela de destino

Defina uma tabela no armazém de dados do SQL que será a tabela de destino para a carregar. As colunas na tabela tem de corresponder aos dados em cada linha do seu ficheiro de dados.

Para criar uma tabela, abra uma linha de comandos e utilizar sqlcmd.exe para executar o seguinte comando:


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


## <a name="2-create-a-source-data-file"></a>2. Crie um ficheiro de dados de origem

Abra o bloco de notas e copie as seguintes linhas de dados para um novo ficheiro de texto e, em seguida, guardar o ficheiro no seu diretório local temp, C:\Temp\DimDate2.txt. Estes dados estão no formato de ASCII.

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

(Opcional) Para exportar os seus próprios dados a partir de uma base de dados do SQL Server, abra uma linha de comandos e execute o seguinte comando. Substitua a tabela, nomedoservidor, DatabaseName, nome de utilizador e palavra-passe pelas suas próprias informações.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. carregar os dados
Para carregar os dados, abra uma linha de comandos e execute o seguinte comando, substituindo os valores para o nome do servidor, nome da base de dados, nome de utilizador e palavra-passe pelas suas próprias informações.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Utilize este comando para verificar se que os dados foram carregados corretamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Os resultados devem ter este aspeto:

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

## <a name="4-create-statistics"></a>4. Crie estatísticas

Armazém de dados SQL é que ainda não suporte criação automática ou as estatísticas de actualização automática. Para obter o melhor desempenho de consulta, é importante criar estatísticas em todas as colunas de todas as tabelas após a primeira carregar ou após a quaisquer alterações substanciais ocorrerem dos dados. Para obter uma explicação detalhada das estatísticas, consulte o artigo [Estatísticas][]. 

Execute o seguinte comando para criar estatísticas na sua tabela carregada recentemente.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. exportar dados do armazém de dados SQL
Para diversão, pode exportar os dados que carregou novamente fora do armazém de dados SQL.  O comando Exportar exatamente é igual a exportação do SQL Server.

No entanto, existe uma diferença nos resultados de. Uma vez que os dados são armazenados nas localizações distribuídas dentro armazém de dados do SQL, quando exporta dados cada nó cluster escreve-dados para o ficheiro de exportação. A ordem dos dados no ficheiro de saída é provável que seja diferente a ordem dos dados no ficheiro de entrada.

### <a name="export-a-table-and-compare-exported-results"></a>Exportar uma tabela e comparar os resultados exportados

Para ver os dados exportados, abra uma linha de comandos e execute este comando utilizando os seus próprios parâmetros. Nomedoservidor é o nome do seu servidor de SQL Azure lógicos.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pode verificar que os dados foram exportados corretamente, ao abrir o novo ficheiro. Os dados no ficheiro devem corresponder o texto abaixo, mas provavelmente serão ordenados numa ordem diferente:

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

### <a name="export-the-results-of-a-query"></a>Exporte os resultados de uma consulta

Pode utilizar a função **queryout** de bcp para exportar os resultados de uma consulta em vez de exportar a tabela inteira. 

## <a name="next-steps"></a>Próximos passos
Para obter uma descrição geral de carregamento, consulte o artigo [carregar dados para armazém de dados SQL][].
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL][].
Consulte o artigo [Descrição geral de tabela][] ou [uma sintaxe criar tabela][] para obter mais informações sobre a criação de uma tabela no armazém de dados SQL.

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
