<properties
   pageTitle="Atribuir as variáveis nos armazém de dados SQL | Microsoft Azure"
   description="Sugestões para atribuir Transact-SQL as variáveis nos armazém de dados do SQL Azure para desenvolver soluções."
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

# <a name="assign-variables-in-sql-data-warehouse"></a>Atribuir as variáveis nos armazém de dados SQL
As variáveis nos armazém de dados SQL são definidas utilizando o `DECLARE` declaração ou o `SET` declaração.

Todas as ações seguintes algumas formas perfeitamente válidas para definir o valor de uma variável:

## <a name="setting-variables-with-declare"></a>Definição de variáveis com DECLARE

A inicialização variáveis com DECLARE é uma das formas mais flexíveis para definir o valor de uma variável no armazém de dados do SQL.

```sql
DECLARE @v  int = 0
;
```

Também pode utilizar DECLARE para definir mais do que uma variável de cada vez. Não é possível utilizar `SELECT` ou `UPDATE` para fazer isto:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Não é possível inicialização e utilizar uma variável na mesma instrução DECLARE. Para ilustrar o ponto de exemplo abaixo, **não** é permitido como @p1 são tanto inicializado e são utilizadas na mesma instrução DECLARE. Isto irá resultar num erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Definição de valores com conjunto
Conjunto é um método muito comuns sobre a configuração de uma única variável.

Todos os exemplos abaixo são válidas formas de definir uma variável com conjunto de:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Apenas pode definir uma variável de cada vez com conjunto. No entanto, tal como podem ser vistos acima operadores compostos são permitido.

## <a name="limitations"></a>Limitações
Não pode utilizar SELECT ou UPDATE para atribuição de variáveis.


## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
