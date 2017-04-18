<properties
   pageTitle="Carregar os dados de exemplo para armazém de dados SQL | Microsoft Azure"
   description="Carregar os dados de exemplo para armazém de dados SQL"
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
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>Carregar os dados de exemplo para armazém de dados SQL

Siga estes passos simples para carregar e consultar a base de dados de exemplo do Adventure Works. Estes scripts utilizam pela primeira vez sqlcmd para executar SQL que irá criar tabelas e vistas. Depois de tem sido criadas tabelas, os scripts irão utilizar bcp para carregar os dados.  Se ainda não tiver sqlcmd e bcp instalada, siga estas ligações para [instalar bcp][] e instalar o [sqlcmd][].

##<a name="load-sample-data"></a>Carregar os dados de exemplo

1. Transferir o ficheiro zip de [Scripts de exemplo da Adventure Works para armazém de dados SQL][] .

2. Extrai os ficheiros de postal transferido para um diretório no seu computador local.

3. Editar o ficheiro extraída aw_create.bat e definir as seguintes variáveis que se encontram na parte superior do ficheiro.  Certifique-se de que deixar sem espaços em branco entre o "=" e o parâmetro.  Abaixo estão exemplos de como as edições podem apresentar aspeto.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. A partir de uma linha de comandos de cmd do Windows, execute o aw_create.bat editada.  Certifique-se de que estão no diretório onde o guardou sua versão editado do aw_create.bat.
Este script irá...
    * Largar Adventure Works tabelas ou vistas já existentes na sua base de dados
    * Criar tabelas de Adventure Works e vistas
    * Carregar a cada tabela de Adventure Works utilizando bcp
    * Valide as contagens de linha para cada tabela Adventure Works
    * Recolher estatísticas de cada coluna para cada tabela Adventure Works


##<a name="query-sample-data"></a>Dados de exemplo de consulta

Assim que tiver carregada alguns dados de exemplo para o seu armazém de dados SQL, pode executar consultas de alguns rapidamente.  Para executar uma consulta, ligar à base de dados da Adventure Works recentemente criado no Azure SQL DW utilizando o Visual Studio e SSDT, conforme descrito no documento [de consulta com Visual Studio][] .

Exemplo da instrução select simple para obter todas as informações dos empregados:

```sql
SELECT * FROM DimEmployee;
```

Exemplo de uma consulta mais complexa utilizando construções como agrupar por para ver a quantidade total para todas as vendas em cada dia:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exemplo de um seleccione com uma cláusula WHERE para filtrar as encomendas a partir de anteriores a determinada data:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Armazém de dados SQL suporta quase todas as construções de T-SQL que suporta o SQL Server.  Quaisquer diferenças encontram-se documentados na nossa documentação [migrar código][] .

## <a name="next-steps"></a>Próximos passos
Agora que tenha tido a oportunidade de experimentar algumas consultas com dados de exemplo, consulte o artigo como [desenvolver][], [carregar][]ou [Migrar][] para armazém de dados do SQL.

<!--Image references-->

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[consulta com o Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrar código]: sql-data-warehouse-migrate-code.md
[instalar bcp]: sql-data-warehouse-load-with-bcp.md
[instalar sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Aventura Lavor Scripts de exemplo para armazém de dados SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
