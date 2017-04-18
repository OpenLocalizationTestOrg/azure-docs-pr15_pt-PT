<properties
   pageTitle="Utilizar etiquetas para consultas instrument no armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para utilizar etiquetas para consultas instrument no armazém de dados do SQL Azure para desenvolver soluções."
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

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Utilizar etiquetas para consultas instrument no armazém de dados do SQL
Armazém de dados SQL suporta um conceito denominado etiquetas de consulta. Antes da ser entregues qualquer profundidade vamos ver um exemplo de um:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Última linha etiquetas a cadeia 'Etiqueta da minha consulta' à consulta. Isto é particularmente útil tal como a etiqueta se consulta conseguir através de DMVs. Isto fornece-nos através de um para controlar as consultas de problema e também para ajudar a identificar curso através de um executar ETL.

Uma boa Convenção de nomenclatura realmente ajuda aqui. Por exemplo, algo como ' PROJETO: procedimento: declaração: Comentário ' seria ajudar a identificar exclusivamente a consulta a todos os códigos no controlo de origem.

Para procurar por etiqueta pode utilizar a seguinte consulta que utiliza as vistas de gestão de dinâmica:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] É essencial moldar entre parênteses Retos ou aspas em redor da etiqueta do word quando consultar. Etiqueta é uma palavra reservada e irá ocorrer um erro se não foi delimitado.


## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
