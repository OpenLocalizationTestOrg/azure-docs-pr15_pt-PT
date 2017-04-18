<properties
   pageTitle="Migrar a sua solução para armazém de dados SQL | Microsoft Azure"
   description="Sugestões de migração para trazer a solução a plataforma armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Migrar a sua solução para armazém de dados SQL

Armazém de dados SQL é um sistema de base de dados distribuída que se adapta elastically para corresponder às suas necessidades. Para manter o desempenho e escala, nem todas as funcionalidades do SQL Server são implementadas dentro armazém de dados SQL. Os seguintes tópicos de migração toque em alguns dos elementos principais para migrar a sua solução para armazém de dados SQL. Estruturar armazém de dados para escala apresenta estrutura diferente padrões e abordagens tradicionais pelo que não estão sempre o melhor. Por conseguinte, poderá constatar que adaptar a sua solução existente garante que as que tira o máximo partido da plataforma distribuído fornecida pela armazém de dados SQL.

Também é importante lembrar-se de que armazém de dados SQL é uma plataforma com base no Microsoft Azure. Por conseguinte, parte da sua migração também pode incluir transferir os seus dados para a nuvem. Transferência de dados é um assunto na sua própria direita e deve ser considerada cuidadosamente; especialmente à medida que volumes aumentam. Transferência de dados e o carregamento de dados são tópicos descontínuos.

## <a name="migration-guidance"></a>Orientação de migração

Certifique-se de que leu através destes artigos para se certificar de que compreende algumas das diferenças de produto e conceitos fundamentais antes de embarque na sua migração.

- [Migrar o esquema][]
- [Migrar os seus dados][]
- [Migrar o seu código][]

## <a name="next-steps"></a>Próximos passos

O gato (cliente consultivo Team) também tem algumas orientações armazém de dados SQL excelente que que publicam através de blogues.  Veja o artigo, [migrar dados para armazém de dados do SQL Azure na prática][] para obter orientações adicionais sobre migração.

<!--Image references-->

<!--Article references-->
[Migrar o esquema]: sql-data-warehouse-migrate-schema.md
[Migrar os seus dados]: sql-data-warehouse-migrate-data.md
[Migrar o seu código]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrar dados para armazém de dados do SQL Azure na prática]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
