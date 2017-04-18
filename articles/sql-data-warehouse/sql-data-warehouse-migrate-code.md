<properties
   pageTitle="Migrar o seu código SQL para armazém de dados SQL | Microsoft Azure"
   description="Sugestões para migrar o seu código SQL para armazém de dados do SQL Azure para desenvolver soluções."
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
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrar o seu código SQL para armazém de dados SQL

Quando a migrar o seu código a partir de outra base de dados para armazém de dados SQL, provavelmente terá de efetuar alterações a sua base de código. Algumas funcionalidades de armazém de dados SQL significativamente podem melhorar o desempenho à medida que foram concebidos para trabalhar de forma distribuída. No entanto, para manter o desempenho e a escala, algumas funcionalidades também não estão disponíveis.

## <a name="common-t-sql-limitations"></a>Limitações de T-SQL comuns

A lista seguinte resume a funcionalidade mais comuns que não são suportadas no armazém de dados do SQL Azure. As ligações levá-lo soluções para a funcionalidade não suportada:

- [Associações ANSI atualizações][]
- [Associações ANSI no elimina][]
- [declaração de impressão em série][]
- associações entre bases de dados
- [cursores][]
- [SELECIONE... PARA][]
- [INSERIR... EXECUÇÃO][]
- cláusula de saída
- inline funções definidas pelo utilizador
- instrução com várias funções
- [expressões comuns de tabela](#Common-table-expressions)
- [recursiva comuns expressões de tabelas (CTE)] (#Recursive-common-table-expressions-(CTE)
- Funções CLR e procedimentos
- função $partition
- variáveis de tabela
- parâmetros de valor de tabela
- transações distribuídas
- consolidar / anulação trabalho
- Guardar da transação
- contextos execução (executar AS)
- [Agrupar por cláusula com rollup / cubo / conjuntos de opções de agrupamento][]
- [níveis de aninhamento para além de 8][]
- [Atualizar através de vistas][]
- [utilização de selecionar para a atribuição de variáveis][]
- [tipo de cadeias SQL dinâmicas sem dados máximo][]

Felizmente, a maior parte destas limitações pode ser trabalhou à volta. Explicações são fornecidas nos artigos desenvolvimento relevantes acima referenciados.

## <a name="supported-cte-features"></a>Funcionalidades CTE suportadas

Expressões comuns de tabela (CTEs) são parcialmente suportados no armazém de dados SQL.  As seguintes funcionalidades CTE atualmente são suportadas:

- Pode ser especificado um CTE numa instrução SELECT.
- Pode ser especificado um CTE numa instrução CREATE VIEW.
- Pode ser especificado um CTE numa instrução criar tabela como SELECIONAR (CTAS).
- Pode ser especificado um CTE numa instrução criar remoto tabela como SELECIONAR (CRTAS).
- Pode ser especificado um CTE numa instrução criar EXTERNOS tabela como SELECIONAR (CETAS).
- Uma tabela remota pode ser referenciada a partir de um CTE.
- Uma tabela externa pode ser referenciada a partir de um CTE.
- Definições de consulta múltiplos CTE podem ser definidas num CTE.

## <a name="cte-limitations"></a>Limitações de CTE

Expressões comuns de tabela têm algumas limitações armazém de dados SQL, incluindo:

- Um CTE deve ser seguido por uma única instrução SELECT. Inserir, ATUALIZAR, eliminar, e não são suportadas declarações de impressão em série.
- Uma expressão de tabela comuns que inclui referências para si próprio (recursiva comuns a expressão da tabela) não é suportada (consulte abaixo secção).
- Especificar mais do que um com cláusula num CTE não é permitida. Por exemplo, se um CTE_query_definition contiver uma subconsulta, esse subconsulta não pode conter um aninhadas com cláusula que define CTE outra.
- Uma cláusula ORDER BY não pode ser utilizada no CTE_query_definition, exceto quando uma cláusula TOP não é especificada.
- Quando um CTE é utilizada numa instrução que faz parte de um lote, a declaração de antes de ser deve ser seguida por um ponto e vírgula.
- Quando utilizada num declarações preparadas pelo sp_prepare, CTEs irá comportam-se da mesma forma que as outras instruções SELECT no PDW. No entanto, se CTEs são utilizados como parte do CETAS preparada pelo sp_prepare, o comportamento pode diferir a partir do SQL Server e outras demonstrações PDW devido a forma como o enlace é implementada para sp_prepare. Se SELECIONAR que referências CTE está a utilizar uma coluna errada que não existe no CTE, o sp_prepare passará sem detectar o erro, mas o erro será devolvido durante sp_execute em vez disso.

## <a name="recursive-ctes"></a>CTEs recursivas

Recursiva CTEs não são suportadas no armazém de dados do SQL.  Migraion de recursiva CTE pode ser um pouco concluído e o processo melhor é dividir a para vários passos. Normalmente, pode utilizar um ciclo e preencher uma tabela temporária como iteramos sobre as consultas provisório recursiva. Assim que a tabela temporária é preenchida, em seguida, pode devolver os dados como um conjunto único resultado. Uma abordagem semelhante foi utilizada para resolver `GROUP BY WITH CUBE` no artigo [Agrupar por cláusula com rollup / cubo / conjuntos de opções de agrupamento][] .

## <a name="unsupported-system-functions"></a>Funções de sistema não suportadas

Também existem algumas funções de sistema que não são suportadas. Algumas das principais aqueles que pode achar normalmente utilizados em armazenamento de dados são:

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Alguns destes problemas podem ser trabalhou à volta.

## <a name="rowcount-workaround"></a>@@ROWCOUNTsolução

Para contornar falta de suporte para @@ROWCOUNT, criar um procedimento armazenado que irá obter a contagem do último linha de sys.dm_pdw_request_steps e, em seguida, executar `EXEC LastRowCount` após uma declaração DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Próximos passos
Para obter uma lista completa de todos os demonstrações T-SQL suportadas, consulte os [Tópicos de Transact-SQL][].

<!--Image references-->

<!--Article references-->
[Associações ANSI atualizações]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Associações ANSI no elimina]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[declaração de impressão em série]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERIR... EXECUÇÃO]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Tópicos de Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[cursores]: ./sql-data-warehouse-develop-loops.md
[SELECIONE... PARA]: ./sql-data-warehouse-develop-ctas.md#selectinto
[Agrupar por cláusula com rollup / cubo / conjuntos de opções de agrupamento]: ./sql-data-warehouse-develop-group-by-options.md
[níveis de aninhamento para além de 8]: ./sql-data-warehouse-develop-transactions.md
[Atualizar através de vistas]: ./sql-data-warehouse-develop-views.md
[utilização de selecionar para a atribuição de variáveis]: ./sql-data-warehouse-develop-variable-assignment.md
[tipo de cadeias SQL dinâmicas sem dados máximo]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
