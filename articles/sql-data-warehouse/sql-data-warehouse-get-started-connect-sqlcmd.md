<properties
   pageTitle="Consulta armazém de dados do SQL Azure (sqlcmd) | Microsoft Azure"
   description="Consultar armazém de dados do SQL Azure com o sqlcmd utilitário de linha de comandos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Consulta armazém de dados do SQL Azure (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Formação de máquina Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Este tutorial utiliza o utilitário da linha de comandos [sqlcmd][] para consultar um armazém de dados do SQL Azure.  

## <a name="1-connect"></a>1. a ligar

Para começar a trabalhar com [sqlcmd][], abra a linha de comandos e introduza **sqlcmd** seguido da cadeia de ligação para a base de dados do armazém de dados SQL. A cadeia de ligação requer os parâmetros seguintes:

+ **Server (-S):** Servidor no formulário de `<`nome de servidor`>`. database.windows.net
+ **Base de dados (-d):** Nome da base de dados.
+ **Ativar entre aspas identificadores (-posso):** Identificadores entre aspas tem de estar ativadas para ligar a uma instância do armazém de dados SQL.

Para utilizar a autenticação do SQL Server, terá de adicionar os parâmetros de nome de utilizador/palavra-passe:

+ **Utilizador (-U):** No formulário de utilizador do servidor `<`utilizador`>`
+ **Palavra-passe (-P):** Palavra-passe associada ao utilizador.

Por exemplo, a cadeia de ligação poderá ser semelhante o seguinte procedimento:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para utilizar a autenticação Azure Active Directory integrada, terá de adicionar os parâmetros do Azure Active Directory:

+ **Autenticação do azure Active Directory (-G):** utilizar Azure Active Directory para autenticação

Por exemplo, a cadeia de ligação poderá ser semelhante o seguinte procedimento:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Tem de [Ativar a autenticação do Azure Active Directory](sql-data-warehouse-authentication.md) autenticar utilizando o Active Directory.

## <a name="2-query"></a>2. consulta de

Depois de ligação, pode emitir qualquer declarações Transact-SQL suportadas contra a instância.  Neste exemplo, as consultas são submetidas no modo interativo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Estes exemplos seguintes mostram como pode executar as suas consultas no modo de batch através da opção -Q ou tubagens SQL para sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Próximos passos

Consulte a [documentação sqlcmd][sqlcmd] para mais informações sobre os detalhes sobre as opções disponíveis no sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[SQLCMD]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
