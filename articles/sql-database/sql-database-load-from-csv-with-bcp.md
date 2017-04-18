<properties
   pageTitle="Carregar dados a partir do ficheiro CSV para Azure SQL Databaase (bcp) | Microsoft Azure"
   description="Para um tamanho de dados pequenas utiliza bcp para importar dados para a base de dados do SQL Azure."
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>


# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>Carregar dados a partir de CSV para armazém de dados do SQL Azure (ficheiros simples)

Pode utilizar o utilitário da linha de comandos bcp para importar dados a partir de um ficheiro CSV para base de dados do SQL Azure.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para visualizar passo a passo neste tutorial, é necessário:

- Um servidor de base de dados do Azure SQL lógico e a base de dados
- O utilitário de linha de comandos bcp instalado
- O utilitário de linha de comandos sqlcmd instalado

Pode transferir os utilitários bcp e sqlcmd a partir do [Centro de transferências da Microsoft][].

### <a name="data-in-ascii-or-utf-16-format"></a>Dados no formato ASCII ou UTF-16

Se estiver a tentar este tutorial com os seus dados, os seus dados tem de utilizar a ASCII ou UTF-16 codificação de uma vez que bcp não suporta UTF-8. 

## <a name="1-create-a-destination-table"></a>1. a criar uma tabela de destino

Defina uma tabela numa base de dados do SQL como a tabela de destino. As colunas na tabela tem de corresponder aos dados em cada linha do seu ficheiro de dados.

Para criar uma tabela, abra uma linha de comandos e utilizar sqlcmd.exe para executar o seguinte comando:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
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


## <a name="next-steps"></a>Próximos passos

Para migrar uma base de dados do SQL Server, consulte o artigo [migração de base de dados do SQL Server](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de transferências da Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433
