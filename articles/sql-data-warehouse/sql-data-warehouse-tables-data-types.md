<properties
   pageTitle="Tipos de dados para tabelas no armazém de dados SQL | Microsoft Azure"
   description="Começar a trabalhar com tipos de dados para tabelas armazém de dados do SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Tipos de dados para tabelas no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice remissivo][]
- [Partição][]
- [Estatísticas][]
- [Temporária][]

Armazém de dados SQL suporta os mais utilizados tipos de dados.  Abaixo encontra uma lista dos tipos de dados suportados pelo armazém de dados SQL.  Para detalhes adicionais sobre o tipo de dados de suporte, consulte [Criar tabela][].

|**Tipos de dados suportados**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[em binário][]|[flutuante][]|[smallmoney][]|
[bit][]|[Int][]|[sysname][]|
[CARÁCT][]|[dinheiro][]|[tempo][]|
[data][]|[nchar][]|[tinyint][]|
[data/hora][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[datetimeoffset][]|[smalldatetime][]|[varchar][]|


## <a name="data-type-best-practices"></a>Melhores práticas do tipo de dados

 Quando definir tipos de coluna, com o tipo de dados menor que irá suportar os seus dados irá melhorar o desempenho da consulta. Isto é particularmente importante para colunas CARÁCT e VARCHAR. Se o valor mais longo numa coluna de 25 caracteres, em seguida, defina a sua coluna como VARCHAR(25). Evite definir todas as colunas de carateres para um comprimento de grandes dimensões predefinido. Além disso, defina colunas como VARCHAR quando que é tudo o que é necessário em vez de utilizar [NVARCHAR][].  Utilize NVARCHAR(4000) ou VARCHAR(8000) sempre que possível em vez de tipo ou varchar (Max).

## <a name="polybase-limitation"></a>Limitação de Polybase

Se estiver a utilizar Polybase para carregar as suas tabelas, defina as tabelas para que o tamanho máximo de linhas possíveis, incluindo o comprimento total de colunas de comprimento variável, não ultrapasse 32.767 bytes.  Enquanto pode definir uma linha com dados de comprimento variável que podem exceder este largura e carregar linhas com BCP, não conseguir utilizar Polybase para carregar estes dados.  Suporte de Polybase para linhas e uma altura será adicionado mais rapidamente.

## <a name="unsupported-data-types"></a>Tipos de dados não suportadas

Se estiver a migrar a base de dados a partir de outra plataforma SQL como base de dados SQL Azure, tal como migrar, que poderá encontrar alguns tipos de dados que não são suportados no armazém de dados SQL.  Abaixo estão tipos de dados não suportadas, bem como algumas alternativas, que pode utilizar em vez de tipos de dados não suportadas.

|Tipo de dados|Solução|
|---|---|
|[geometria][]|[varbinary][]|
|[Geografia][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][] (4000)|
|[imagem][ntext,text,image]|[varbinary][]|
|[texto][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|Dividir coluna em várias colunas vivamente escritas.|
|[tabela][]|Converta tabelas temporárias.|
|[data/hora][]|Reformule as código para utilizar [datetime2][] e `CURRENT_TIMESTAMP` função.  Apenas as constantes são suportadas como predefinições, por conseguinte, current_timestamp não podem ser definidas como uma restrição predefinida. Se precisar de migrar os valores da versão de linha de uma coluna de escrito de data/hora, em seguida, utilize [binário][](8) ou [VARBINARY][binário](8) para valores de versão de linha não nulo ou de valores nulos.|
|[XML][]|[varchar][]|
|[tipos de definidas pelo utilizador][]|converter novamente para os tipos de nativos sempre que possível|
|valores predefinidos|valores predefinidos suportam literais e constantes apenas.  Expressões não determinista ou funções, tais como `GETDATE()` ou `CURRENT_TIMESTAMP`, não são suportadas.|

O SQL abaixo podem ser executados em atual base de dados do SQL para identificar as colunas que não sejam suportadas pelo armazém de dados do SQL Azure:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os artigos no [Descrição geral de tabela da][Descrição geral], [distribuir a uma tabela][distribuir], [indexação de uma tabela]de[índice remissivo], [criar a partições uma tabela][partição], [Mantendo estatísticas da tabela][Estatísticas] e [Tabelas temporárias][temporário].  Para mais informações sobre procedimentos recomendados, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[Descrição geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice remissivo]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporária]: ./sql-data-warehouse-tables-temporary.md
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[Criar tabela]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[em binário]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[CARÁCT]: https://msdn.microsoft.com/library/ms176089.aspx
[data]: https://msdn.microsoft.com/library/bb630352.aspx
[data/hora]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[flutuante]: https://msdn.microsoft.com/library/ms173773.aspx
[geometria]: https://msdn.microsoft.com/library/cc280487.aspx
[Geografia]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[Int]: https://msdn.microsoft.com/library/ms187745.aspx
[dinheiro]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[tabela]: https://msdn.microsoft.com/library/ms175010.aspx
[tempo]: https://msdn.microsoft.com/library/bb677243.aspx
[data/hora]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[XML]: https://msdn.microsoft.com/library/ms187339.aspx
[tipos de definidas pelo utilizador]: https://msdn.microsoft.com/library/ms131694.aspx
