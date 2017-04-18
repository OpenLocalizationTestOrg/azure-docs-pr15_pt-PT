<properties
   pageTitle="Ciclos no armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para ciclos de Transact-SQL e substituir cursores no armazém de dados do SQL Azure para desenvolver soluções."
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

# <a name="loops-in-sql-data-warehouse"></a>Ciclos no armazém de dados SQL
Armazém de dados SQL suporta o loop [enquanto][] para executar repetidamente blocos declaração. Isto vai continuar para desde que as condições especificadas forem verdadeiras ou até o código termina especificamente o ciclo utilizando o `BREAK` palavra-chave. Ciclos são particularmente úteis para substituir os cursores definidas no código SQL. Felizmente, são quase todos os cursores escritas código SQL de avançar, leia apenas variedade. Por conseguinte, ciclos [enquanto] são uma excelente alternativa se encontrar o seu próprio ter de substituir um.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Tirar partido da ciclos e substituição de cursores no armazém de dados SQL
No entanto, antes de consultar pela primeira vez na cabeça deverá pedir a próprio a seguinte pergunta: "Poderia este cursor ser novamente escrito para utilizar as operações de conjunto baseado?". Em muitos casos a resposta será Sim e é, muitas vezes, a melhor abordagem. Uma operação de conjunto baseado frequentemente executa significativamente for mais rápido uma abordagem de iterativa, linha por linha.

Avançar cursores só de leitura podem ser substituídos facilmente com uma construção loop. Segue-se um exemplo simples. Este exemplo de código atualiza as estatísticas de todas as tabelas na base de dados. Por iteração as tabelas tudo estamos conseguir executar cada comando numa sequência.

Em primeiro lugar, crie uma tabela temporária que contém um número de linha exclusivo utilizado para identificar as declarações individuais:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Em segundo lugar, inicialização as variáveis necessárias para executar o ciclo:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora Repetir continuamente ao longo de declarações de executar um cada vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Por fim, largue da tabela temporária criada no primeiro passo

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[TEMPO]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
