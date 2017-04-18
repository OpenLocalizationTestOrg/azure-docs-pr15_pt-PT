<properties
   pageTitle="Procedimentos armazenados no SQL Data Warehouse | Microsoft Azure"
   description="Sugestões para implementar o procedimentos armazenados no armazém de dados do SQL Azure para desenvolver soluções."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="stored-procedures-in-sql-data-warehouse"></a>Procedimentos armazenados no armazém de dados SQL

Armazém de dados SQL suporta muitos das funcionalidades de Transact-SQL que se encontram no SQL Server. Mais nenhumas existem escala saída funcionalidades específicas que pretendemos tirar partido para maximizar o desempenho da sua solução.

No entanto, para manter a escala e o desempenho do armazém de dados do SQL não existem também estão algumas funcionalidades e que têm diferenças comportamentais e outras pessoas que não são suportadas.

Este artigo explica como implementar o procedimentos armazenados dentro armazém de dados do SQL.

## <a name="introducing-stored-procedures"></a>Introdução aos procedimentos armazenados
Procedimentos armazenados são uma excelente forma para capsular o código SQL; armazenar-fechar para os seus dados no armazém de dados. Por encapsulating o código para fácil unidades procedimentos armazenados ajudam os programadores aplicar as respetivas soluções; facilitar a maior reutilização de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-las ainda mais flexível.

Armazém de dados SQL fornece uma implementação do procedimento armazenado simplificado e simplificada. A diferença maior comparadas com o SQL Server é que o procedimento armazenado não é código previamente compilado. Pedimos no armazém de dados são geralmente menos preocupem com a hora de compilação. É mais importante que o código do procedimento armazenado está corretamente optimizado quando operativo contra grandes volumes de dados. O objetivo é guardar as horas, minutos e segundos não milissegundos. Por conseguinte, é mais útil pensar procedimentos armazenados como contentores para a lógica SQL.     

Quando o armazenamento de dados SQL executa o procedimento armazenado as instruções SQL estão analisadas, traduzido e otimizada em tempo de execução. Durante este processo cada instrução é convertida em consultas distribuídas. O código SQL que realmente é executado nos dados é diferente para a consulta submetida.

## <a name="nesting-stored-procedures"></a>Procedimentos armazenado de aninhamento
Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executar dinâmico sql, em seguida, a invocação de código ou procedimento armazenada interna é disse para ser aninhadas.

SQL Data Warehouse suporta um máximo de níveis de aninhamento 8. Este é ligeiramente diferente para o SQL Server. O nível de ninho no SQL Server é 32.

A chamada de procedimento armazenado de nível superior equivale a aninhar nível 1

```sql
EXEC prc_nesting
```
Se o procedimento armazenado também torna execução e outra chamada, em seguida, isto irá aumentar o nível de ninho para 2
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se o procedimento segundo, em seguida, executa algumas dinâmico sql, em seguida, isto irá aumentar o nível de ninho para 3
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Nota SQL Data Warehouse não suporta atualmente @@NESTLEVEL. Terá de manter uma faixa do seu nível de ninho de si. Não é provável será visitas o limite de nível do 8 ninho mas se fizer terá de trabalhar novamente o seu código e "aplanar"-lo para que se encontra dentro deste limite.

## <a name="insertexecute"></a>INSERIR... EXECUTAR
Armazém de dados SQL não lhe permite consumir o conjunto de resultados de um procedimento armazenado com uma declaração inserir. No entanto, existe uma abordagem alternativa, que pode utilizar.

Consulte o seguinte artigo sobre [tabelas temporárias] para ver um exemplo de como o fazer.

## <a name="limitations"></a>Limitações

Existem alguns aspetos de procedimentos armazenados Transact-SQL que não são implementados no armazém de dados do SQL.

São descritos abaixo:

- procedimentos armazenados temporários
- procedimentos armazenados numerados
- procedimentos armazenados expandidos
- Procedimentos armazenado do CLR
- opção de encriptação
- opção de replicação
- parâmetros de valor de tabela
- parâmetros só de leitura
- parâmetros predefinidos
- contextos de execução
- devolver instrução

## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[tabelas temporárias]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Descrição geral do desenvolvimento]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
