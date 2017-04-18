<properties
   pageTitle="Migrar o seu esquema para armazém de dados SQL | Microsoft Azure"
   description="Sugestões para migrar o seu esquema para armazém de dados do SQL Azure para desenvolver soluções."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Migrar o seu esquema para armazém de dados SQL#

Os resumos dos seguintes ajudá-lo a compreender as diferenças entre o SQL Server e armazém de dados SQL para o ajudar a migrar a base de dados.

## <a name="table-migration"></a>Migração de tabela

Quando migrar as suas tabelas, irá querer para se familiarizar com as funcionalidades de tabela de tabelas de SQL armazém de dados.  [Descrição geral de tabela][] é uma ótima maneira para começar.  Este artigo apresenta-lhe para considerações mais importantes ao criar uma tabela como estatísticas da tabela, distribuição, a partições e indexação.  Também abrange algumas [funcionalidades de tabela não suportadas][] e soluções.

Armazém de dados SQL suporta os tipos de dados de negócio comuns.  Consulte o artigo [tipos de dados][] para uma lista de suportados e [tipos de dados não suportadas][].  O artigo [tipos de dados][] também contém uma consulta para identificar [os tipos de dados não suportadas][].  Ao converter tipos de dados, certifique-se de que observe as [melhores práticas do tipo de dados][].

## <a name="next-steps"></a>Próximos passos
Assim que tiver migradas com êxito o esquema da base de dados para armazém de dados do SQL, continue para um dos seguintes artigos:

- [Migrar os seus dados][]
- [Migrar o seu código][]

Para mais informações sobre práticas recomendadas do armazém de dados SQL, consulte o artigo [melhores práticas][] .

<!--Image references-->

<!--Article references-->
[Migrar o seu código]: ./sql-data-warehouse-migrate-code.md
[Migrar os seus dados]: ./sql-data-warehouse-migrate-data.md
[melhores práticas]: ./sql-data-warehouse-best-practices.md
[Descrição geral de tabela]: ./sql-data-warehouse-tables-overview.md
[funcionalidades de tabela não suportadas]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[tipos de dados não suportadas]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[melhores práticas do tipo de dados]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
