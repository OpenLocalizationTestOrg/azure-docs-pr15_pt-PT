<properties
   pageTitle="Estruturar decisões e coding técnicas para o desenvolvimento de armazém de dados SQL | Microsoft Azure"
   description="Os conceitos de desenvolvimento, decisões de projeto, recomendações e coding técnicas para armazém de dados SQL."
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
   ms.date="08/16/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Decisões de estrutura e coding técnicas para armazém de dados SQL

Consulte o artigo através destes artigos de desenvolvimento para compreender melhor as decisões de chave do projeto, recomendações e coding técnicas para armazém de dados SQL.

## <a name="key-design-decisions"></a>Decisões de chave do projeto
Os seguintes artigos realçam alguns dos conceitos chave e decisões de projeto, que é necessário compreender para o desenvolvimento do seu armazém de dados distribuídos utilizando armazém de dados SQL:

- [ligações][]
- [simultaneidade][]
- [transações][]
- [esquemas definidos pelo utilizador][]
- [distribuição de tabela][]
- [índices de tabela][]
- [partições de tabela][]
- [CTAS][]
- [estatísticas][]

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações de desenvolvimento e coding técnicas
Estes artigos realçam técnicas coding específicas, sugestões e recomendações para desenvolver o seu armazém de dados SQL:

- [procedimentos armazenados][]
- [etiquetas][]
- [vistas][]
- [tabelas temporárias][]
- [SQL dinâmico][]
- [ciclo][]
- [Agrupar por opções][]
- [atribuição de variáveis][]

## <a name="next-steps"></a>Próximos passos
Depois de ter sido através de artigos de desenvolvimento explorá através da página de [referência Transact-SQL][] para obter mais detalhes sobre a sintaxe suportada para armazém de dados SQL.

<!--Image references-->

<!--Article references-->
[simultaneidade]: ./sql-data-warehouse-develop-concurrency.md
[ligações]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL dinâmico]: ./sql-data-warehouse-develop-dynamic-sql.md
[Agrupar por opções]: ./sql-data-warehouse-develop-group-by-options.md
[etiquetas]: ./sql-data-warehouse-develop-label.md
[ciclo]: ./sql-data-warehouse-develop-loops.md
[estatísticas]: ./sql-data-warehouse-tables-statistics.md
[procedimentos armazenados]: ./sql-data-warehouse-develop-stored-procedures.md
[distribuição de tabela]: ./sql-data-warehouse-tables-distribute.md
[índices de tabela]: ./sql-data-warehouse-tables-index.md
[partições de tabela]: ./sql-data-warehouse-tables-partition.md
[tabelas temporárias]: ./sql-data-warehouse-tables-temporary.md
[transações]: ./sql-data-warehouse-develop-transactions.md
[esquemas definidos pelo utilizador]: ./sql-data-warehouse-develop-user-defined-schemas.md
[atribuição de variáveis]: ./sql-data-warehouse-develop-variable-assignment.md
[vistas]: ./sql-data-warehouse-develop-views.md
[Referência de Transact-SQL]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
