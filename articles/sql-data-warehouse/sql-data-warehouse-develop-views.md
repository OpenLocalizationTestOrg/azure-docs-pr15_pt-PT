<properties
   pageTitle="Vistas no armazém de dados SQL | Microsoft Azure"
   description="Sugestões para utilizar vistas Transact-SQL no armazém de dados do SQL Azure para desenvolver soluções."
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
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>Vistas no armazém de dados SQL

Vistas são particularmente úteis no armazém de dados do SQL. Podem ser utilizadas num número de formas diferentes para melhorar a qualidade da sua solução.  Este artigo realça alguns exemplos de como enriquecer a sua solução com vistas, bem como as limitações que precisam de ser consideradas.

> [AZURE.NOTE] Sintaxe para `CREATE VIEW` não é abordado neste artigo. Consulte o artigo [Criar vista][] no MSDN para obter estas informações de referência.

## <a name="architectural-abstraction"></a>Produção de arquitectura
É um padrão de aplicação comuns muito criar tabelas de criar tabela como SELECIONAR (CTAS) seguido de um objeto mudar o nome padrão, embora o carregamento de dados.

O exemplo abaixo adiciona registos nova data para uma dimensão de data. Tenha em atenção como um novo tabble, DimDate_New, é criada pela primeira vez e, em seguida, cujo nome foi mudado para substituir a versão original da tabela.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

No entanto, esta abordagem pode resultar em tabelas a aparecer e desaparecer a partir da vista de um utilizador, bem como mensagens de erro "a tabela não existe". Vistas podem ser utilizadas para fornecer aos utilizadores uma camada de apresentação consistente, embora os objectos subjacentes são alterados. Fornecendo aos utilizadores acesso aos dados através de um vistas, significa que os utilizadores não têm de ter visibilidade das tabelas subjacentes. Isto fornece uma experiência de utilizador consistente enquanto garantir que os dados armazém estruturadores pode evoluir o modelo de dados e maximizar o desempenho ao utilizar CTAS durante o processo de carregamento dos dados.    

## <a name="performance-optimization"></a>Otimização do desempenho
Vistas também podem ser utilizadas para impor associações de desempenho otimizado entre tabelas. Por exemplo, uma vista pode incorporar uma chave de distribuição redundantes como parte dos critérios de junção para minimizar movimento de dados.  Outra vantagem de uma vista dever-se forçar uma consulta específica ou sugestão unir. Utilizar vistas desta forma garante que as associações sempre são executadas de uma forma ideal evitando a necessidade de para os utilizadores não se esqueça de construção de correta para os respetivos associações.

## <a name="limitations"></a>Limitações
As vistas no armazém de dados do SQL são metadados apenas.  Consequentemente, não estão disponíveis as seguintes opções:

-   Existe uma opção de encadernação de esquema
-   Tabelas de base não podem ser atualizadas através da vista
-   Não não possível criar vistas sobre tabelas temporárias
-   Não existe suporte para o expandir / sugestões de NOEXPAND
-   Não existem indexadas vistas no armazém de dados SQL


## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL][].
Para `CREATE VIEW` sintaxe, consulte [Criar vista][].

<!--Image references-->

<!--Article references-->
[Descrição geral do desenvolvimento armazém de dados SQL]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CRIAR VISTA]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
