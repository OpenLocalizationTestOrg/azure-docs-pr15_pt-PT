<properties
   pageTitle="SQL dinâmico no armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para utilizar o SQL dinâmico no armazém de dados do SQL Azure para desenvolver soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinâmico no armazém de dados SQL
Quando desenvolver código da aplicação para o armazém de dados SQL poderá ter de utilizar sql dinâmico para ajudar a fornecer as soluções flexíveis, genéricas e modulares. SQL Data Warehouse não suporta tipos de dados de BLOBs neste momento. Isto pode limitar o tamanho da sua cadeias como tipos de BLOBs incluem tipos varchar (Max) e tipo. Se tiver utilizado estes tipos no código da aplicação durante a criação de cadeias muito grandes, terá de dividir o código em blocos e utilize a declaração de execução como alternativa.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia for breve pode utilizar [sp_executesql][] como habitualmente.

> [AZURE.NOTE] Instruções executadas como SQL dinâmico continuarão a ter sujeito a todas as regras de validação TSQL.

## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
