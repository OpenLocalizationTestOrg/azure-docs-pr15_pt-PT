<properties
   pageTitle="Indexação tabelas no armazém de dados SQL | Microsoft Azure"
   description="Introdução ao tabela indexação no armazém de dados do SQL Azure."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexação tabelas no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice remissivo][]
- [Partição][]
- [Estatísticas][]
- [Temporária][]

Armazém de dados SQL oferece várias opções de indexação incluindo [agrupadas columnstore índices][], [índices agrupados e índices agrupados][].  Além disso, também oferece uma índice remissivo opção sem também conhecido como [pilha][].  Este artigo abrange os benefícios de cada tipo de índice remissivo, bem como sugestões para obter o desempenho a maior parte dos fora dos índices. Consulte o artigo [Criar sintaxe de tabela][] para obter mais detalhes sobre como criar uma tabela no armazém de dados SQL.

## <a name="clustered-columnstore-indexes"></a>Índices de columnstore agrupadas

Por predefinição, o armazém de dados SQL cria um índice de columnstore agrupadas quando sem opções de índice são especificadas numa tabela. Tabelas de columnstore agrupadas oferecem tanto o nível mais alto de compressão de dados, bem como o melhor desempenho de consulta global.  As tabelas columnstore agrupadas serão geralmente específico tabelas de índice remissivo ou na pilha agrupadas e normalmente, são a melhor escolha para tabelas grandes.  Para estes motivos, columnstore agrupada é o melhor local para começar quando tem a certeza de como indexar a sua tabela.  

Para criar uma tabela de columnstore agrupadas, basta especificar índice COLUMNSTORE AGRUPADO na cláusula com ou deixar a cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Existem alguns cenários onde columnstore agrupada pode não ser uma boa opção:

- Tabelas de Columnstore não suportam índices de colunas que não sejam agrupadas secundários.  Considere pilha ou tabelas de índice agrupado.
- Tabelas de Columnstore não suportam varchar (Max), tipo e varbinary (Max).  Considere pilha ou índice agrupado.
- Tabelas de Columnstore poderão ser menos eficaz para os dados breves.  Considere a área de dados dinâmicos e tabelas talvez mesmo temporárias.
- Pequenas tabelas com menos de 100 milhões de linhas.  Considere tabelas da pilha.

## <a name="heap-tables"></a>Tabelas de pilha

Quando são temporariamente fiquem dados no armazém de dados SQL, poderá constatar que a utilizando uma tabela de pilha irá tornar o processo de geral mais rápido.  Isto acontece porque cargas a pilhas são mais rápidas que, para tabelas de índice remissivo e em alguns casos, que pode ser feita a leitura subsequente cache.  Se estiver a carregar dados apenas a fase-lo antes de executar transformações mais, será muito mais rápido que ao carregar os dados a uma tabela de columnstore agrupadas carregar a tabela a tabela da pilha. Além disso, carregar os dados a uma [tabela temporária][temporário] irá também carregar mais rapidamente do que uma tabela em armazenamento permanente a ser carregados.  

Para tabelas de referência pequenas, menos de 100 milhões de linhas, muitas vezes as tabelas de pilha fazem sentido.  Cluster columnstore tabelas começam a alcançar compressão ideal quando existe mais de 100 milhões de linhas.

Para criar uma tabela de pilha, especifique simplesmente a pilha na cláusula com:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices agrupados e agrupados

Índices agrupados poderão específico columnstore agrupadas tabelas quando necessita de uma única linha a serem obtidos rapidamente.  Para consultas onde uma única ou muito poucos pesquisa de linha é necessária para um desempenho com velocidade extrema, considere um índice de cluster ou agrupado índice secundária.  A desvantagem para utilizar um índice agrupado é que serão vantajoso para apenas consultas que utilizam um filtro altamente selective à coluna de índice agrupado.  Para melhorar a filtro noutras colunas um índice agrupado pode ser adicionado às outras colunas.  No entanto, cada índice que é adicionado a uma tabela irá adicionar espaço e tempo de processamento cargas.

Para criar uma tabela de índice remissivo agrupadas, especifique simplesmente índice AGRUPADO na cláusula com:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice que não sejam agrupadas numa tabela, simplesmente utilize a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] É criado um índice que não sejam agrupadas por predefinição quando é utilizada CREATE INDEX. Além disso, um índice que não sejam agrupadas só é permitido numa tabela de armazenamento de linha (pilha ou agrupadas índice remissivo). Índices que não sejam agrupados na parte superior de um índice COLUMNSTORE AGRUPADO não é permitida neste momento.


## <a name="optimizing-clustered-columnstore-indexes"></a>Otimizar os índices columnstore agrupadas

Columnstore agrupadas tabelas estão organizadas em dados em segmentos.  Está com dificuldades de qualidade de segmento alta é fundamental alcançar o desempenho da consulta ideal numa tabela columnstore.  Qualidade de segmento pode ser medida pelo número de linhas num grupo de linha comprimido.  Qualidade de segmento é mais ideal onde existem, pelo menos, 100K linhas por linha comprimida agrupar e obtenha o no desempenho como o número de linhas por abordagem de grupo de linha 1.048.576 linhas, qual é as maioria das linhas que pode conter um grupo de linha.

A abaixo vista pode ser criada e utilizada no sistema de para calcular as linhas de média por fila de grupo e identificam os índices de columnstore cluster sub ideal.  Na última coluna nesta vista irá gerar como instrução SQL que pode ser utilizada para recriar os índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Agora que criou a vista, execute esta consulta para identificar tabelas com grupos de linha com menos de 100K linhas.  Obviamente, poderá querer aumentar o limiar de 100K se procura mais qualidade de segmento ideal. 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Assim que tiver executado a consulta que pode começar a analisar os dados e analisar os seus resultados. Esta tabela explica o que pretende procurar na sua análise de grupo de linha.


| Coluna                             | Como utilizar estes dados                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | Se a tabela é a partições, que pode esperar ver a conta do grupo de abrir linha superior. Cada partição na distribuição poderia teoricamente tem um grupo de linha abrir associado. Factor isto na sua análise. Uma pequena tabela que tenha sido criado a partições poderá ser otimizada ao remover completamente a partições como isto tornaria a compressão.                                                                        |
| [row_count_total]                  | Contagem de linha de total para a tabela. Por exemplo, pode utilizar este valor para calcular a percentagem de linhas no estado de comprimido.                                                                      |
| [row_count_per_distribution_MAX]   | Se todas as linhas são distribuídas uniformemente este valor seria o número de destino de linhas por distribuição. Compare este valor com o compressed_rowgroup_count.                                 |
| [COMPRESSED_rowgroup_rows]         | Número total de linhas no formato de columnstore para a tabela.                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | Se o número médio de linhas é significativamente inferior a # máximo de linhas para um grupo de linha, em seguida, considere utilizar CTAS ou ALTER RECONSTRUIR de índice remissivo para comprimir os dados                     |
| [COMPRESSED_rowgroup_count]        | Número de grupos de linha no formato de columnstore. Se este número for muito alto em relação a tabela é um indicador de que a densidade columnstore está baixa.                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | Linhas logicamente são eliminadas no formato de columnstore. Se o número é elevado relativamente às tamanho da tabela, considere recriar a partição ou o índice de a reconstruir como esta ação remove-los física. |
| [COMPRESSED_rowgroup_rows_MIN]     | Utilize esta opção em conjunto com as colunas de média e máximo para compreender o intervalo de valores para os grupos de linha na sua columnstore. Um número baixo sobre o limiar de carga (102,400 por partição alinhada à distribuição) sugere que otimizações estão disponíveis no carregamento de dados                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | Como acima                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | Os grupos de linha abertos são normais. Um razoável seria esperado um grupo de linha abertos por distribuição de tabela (60). Números excessivos sugerem carregamento através de partições de dados. Faça a partições estratégia para se certificar de que está a ser som                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | Cada grupo de linha pode ter 1.048.576 linhas-lo como um valor máximo. Utilizar este valor para ver como completo os grupos de linha abrir estão atualmente                                                                 |
| [OPEN_rowgroup_rows_MIN]           | Abrir grupos indicam que dados estão a ser são recebidas lentamente carregado para a tabela ou que a carga anterior derramarem restante linhas para este grupo de linha. Utilizar MIN, MAX, grupos de linha de colunas AVG para ver a quantidade de dados é sol aberta. Para tabelas pequenas dever-se a 100% de todos os dados! Neste caso ALTER RECONSTRUIR de índice remissivo para forçar os dados para columnstore.                                                                       |
| [OPEN_rowgroup_rows_MAX]           | Como acima                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | Como acima                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | Observe as linhas de grupo de linha fechada como uma marca de verificação sanidade.                                                                                                                                       |
| [CLOSED_rowgroup_count]            | O número de grupos de linha fechada deve ser baixo se qualquer forem vistos de todo. Grupos de linha fechada podem ser convertidos para roups rowg comprimido utilizando o índice ALTER... REORGANIZAR comandos. No entanto, esta não é normalmente necessária. Grupos fechados são automaticamente convertidas em grupos de linha columnstore pelo processo de "ponteiro de mover cadeia de identificação" de fundo.                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | Grupos de linha fechada devem ter uma taxa de preenchimento muito alta. Se a taxa de preenchimento de um grupo de linha fechada for baixa, é necessária uma análise aprofundada da columnstore.                                   |
| [CLOSED_rowgroup_rows_MAX]         | Como acima                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | Como acima                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL reconstruir columnstore índice para uma tabela                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas dos columnstore originar má qualidade de índice remissivo

Se identificou tabelas com a qualidade de segmento fraca, irá querer identificar o problema de raiz.  Seguem-se algumas outras causas comuns de baixa segmento quaility:

1. Pressão de memória quando foi criado o índice remissivo
2. Grande volume de operações de LMG
3. Pequenas ou são recebidas lentamente carregar operações
4. Demasiadas partições

Seguintes fatores podem causar um índice de columnstore ter significativamente menor do que as linhas de 1 milhão ideais por grupo de linhas.  Podem também causar linhas para ir para o grupo de linha delta em vez de um grupo de linha comprimido. 

### <a name="memory-pressure-when-index-was-built"></a>Pressão de memória quando foi criado o índice remissivo

O número de linhas por grupo de linhas comprimido estarem diretamente relacionados com a largura da linha e a quantidade de memória disponível para o grupo de linha do processo.  Quando são escritas linhas a tabelas columnstore em pressão de memória, sofrer de qualidade de segmento columnstore.  Por isso, é a melhor prática dar a sessão que está a escrever o acesso de tabelas de índice remissivo columnstore a quanto memória possível.  Uma vez que existe um compromisso entre memória e simultaneidade, as informações sobre a alocação de memória direita depende os dados em cada linha da sua tabela, a quantidade de DWU tenha atribuída ao seu sistema e a quantidade de faixas de simultaneidade que pode dar à sessão que está a escrever dados à sua tabela.  Como prática recomendada, recomendamos a ser iniciadas com xlargerc se estiver a utilizar DW300 ou menos, largerc se estiver a utilizar DW400 DW600 e mediumrc se estiver a utilizar DW1000 e acima.

### <a name="high-volume-of-dml-operations"></a>Grande volume de operações de LMG

Pode introduzir um grande volume de operações de LMG que atualizar e eliminar linhas ineficiência a columnstore. O que é especialmente verdade quando forem modificados a maioria das linhas num grupo de linha.

- Eliminar uma linha de um grupo de linha comprimido apenas logicamente marca a linha como eliminadas. A linha permanece no grupo linha comprimido até a partições ou a tabela é recriada.
- Inserir uma linha adiciona a linha para a uma tabela de rowstore interno designado por um grupo de linha delta. Linha inserida não é convertida columnstore até que o grupo de linha delta está cheio e está marcado como fechado. Grupos de linha são fechados quando chegar à capacidade máxima de 1.048.576 linhas. 
- Atualizar uma linha no formato de columnstore é processada como um lógico eliminar e, em seguida, um inserir. Linha inserida pode ser armazenada na loja delta.

Enviadas em batches atualização e inserir as operações que excedem o limiar de em volume de 102.400 linhas por partição de distribuição alinhada será escrita diretamente para o formato de columnstore. No entanto, partindo do princípio de uma distribuição uniforme, que precisa para ser modificar mais do que 6.144 milhões de linhas numa única operação para isto ocorrer. Se o número de linhas para uma determinada partição alinhada à distribuição é menor que 102,400, em seguida, as linhas serão direcionadas para o arquivo de delta e irão permanecer aí até linhas suficientes foram inseridas ou modificadas para fechar o grupo de linha ou o índice foram recompilado.

### <a name="small-or-trickle-load-operations"></a>Pequenas ou são recebidas lentamente carregar operações

Pequena carrega que fluxo para armazém de dados do SQL por vezes também são conhecidos como são recebidas lentamente cargas. Normalmente, que representam numa sequência constante próximo de dados a ser penetração pelo sistema. No entanto, tal como está nesta sequência contínua perto o volume de linhas não é particularmente grande. Mais frequentemente que não os dados não significativamente no limiar de necessários para uma carga direta para o formato de columnstore.

Nas seguintes situações, é frequentemente melhor terra os dados pela primeira vez no armazenamento de Blobs do Azure e reproduza-a se acumulem antes de carregamento. Esta técnica é muitas vezes conhecida como *micro de lotes*.

### <a name="too-many-partitions"></a>Demasiadas partições

Outra coisa a ter em conta é o impacto das partições no seu tabelas columnstore agrupadas.  Antes de criar a partições, armazém de dados SQL divide já os seus dados em 60 bases de dados.  Ainda mais partições divide os seus dados.  Se a partições os seus dados, em seguida, pretenderá a ter em conta que **cada** partição será necessário terem pelo menos de 1 milhão de linhas para beneficiar de um índice columnstore agrupadas.  Se a partições a tabela para a 100 partições, a sua tabela terão de ter, pelo menos, 6 mil milhões de linhas para beneficiar de um índice de columnstore agrupadas (linhas de *100 partições* 1 milhão de 60 distribuições). Se a tabela de 100 partições não tiver 6 mil milhões de linhas, reduza o número de partições ou considere utilizar uma tabela de pilha como alternativa.

Assim que tiverem sido carregadas suas tabelas com alguns dados, siga a seguir os passos para identificar e reconstruir tabelas com índices do cluster sub ideal columnstore.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>A reformulação repetida de índices para melhorar a qualidade do segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Passo 1: Identificar ou criar utilizador que utiliza a classe de recursos à direita

Uma forma rápida de imediatamente melhorar a qualidade de segmento é reconstruir o índice.  O SQL devolvido pela vista acima irá devolver uma instrução ALTER RECONSTRUIR de índice remissivo que pode ser utilizada para recriar os índices.  Quando a reformulação repetida os índices, certifique-se de que alocar memória suficiente para a sessão que irá reconstruir o índice remissivo.  Para executar esta tarefa, aumente a classe de recursos de um utilizador que tem permissões para reconstruir o índice nesta tabela ao mínimo recomendadas.  A classe de recursos do utilizador ao proprietário da base de dados não pode ser alterada, para que se não tiver criado um utilizador no sistema, é necessário para o fazer em primeiro lugar.  O mínimo que recomendamos é xlargerc se estiver a utilizar DW300 ou menos, largerc se estiver a utilizar DW400 DW600 e mediumrc se estiver a utilizar DW1000 e acima.

Abaixo está um exemplo de como atribuir mais memória a um utilizador ao aumentar a sua classe de recursos.  Para mais informações sobre recursos aulas e como criar um novo utilizador podem ser encontradas na [simultaneidade e carga de trabalho Gestão de] [ Concurrency] artigo.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Passo 2: Reconstruir índices columnstore agrupadas com mais elevado utilizador de classe de recursos
Início de sessão como o utilizador a partir do passo 1 (por exemplo, LoadUser), que é agora a utilizar uma classe recurso mais elevada e execute as instruções de índice remissivo ALTER.  Certifique-se de que este utilizador tem permissão de ALTER para as tabelas onde está a ser recompilado o índice.  Estes exemplos mostram como reconstruir o índice columnstore inteira ou como reconstruir uma única partição. Em tabelas grandes, é mais prática para reconstruir índices uma única partição cada vez.

Em alternativa, em vez de a reconstruir o índice remissivo, pode copiar a tabela para uma nova tabela utilizando [CTAS][].  Qual a forma mais são melhor? Para grandes volumes de dados, [CTAS][] é normalmente mais rápido que [ALTER índice][]. Para mais pequenos volumes de dados, [ALTER índice][] é mais fácil utilizar e não necessitam que trocar a tabela.  Consulte o artigo **Rebuilding índices com CTAS e mudança de partição** abaixo para obter mais detalhes sobre como reconstruir índices com CTAS.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

A reformulação repetida de um índice remissivo no armazém de dados SQL é uma operação offline.  Para mais informações sobre a reformulação repetida de índices, consulte a secção alterar RECONSTRUIR de índice remissivo [Columnstore índices desfragmentação][] e o tópico de sintaxe [ALTER índice][].
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Passo 3: Verificar a qualidade de segmento columnstore agrupadas melhorou
Voltar a executar a consulta à tabela de identificado com má segmentar qualidade e verificar a qualidade de segmento melhorou.  Se não melhorar a qualidade de segmento, dever-se que as linhas na tabela são extra largas.  Considere utilizar um superior classe de recursos ou DWU quando a reformulação repetida os índices.

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>A reformulação repetida de índices com CTAS e mudança de partição

Este exemplo utiliza [CTAS][] e partição mudar para recompilar uma partição de tabela. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para obter mais detalhes sobre a criação de voltar a partições utilizando `CTAS`, consulte o artigo [partição][] .

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os artigos no [Descrição geral de tabela da][Descrição geral], [Tipos de dados de tabela][Tipos de dados], [distribuir a uma tabela][distribuir], [criar a partições uma tabela][partição], [Mantendo estatísticas da tabela][Estatísticas] e [Tabelas temporárias][temporário].  Para saber mais sobre os procedimentos recomendados, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[Descrição geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice remissivo]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporária]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTERAR O ÍNDICE REMISSIVO]: https://msdn.microsoft.com/library/ms188388.aspx
[área de dados dinâmicos]: https://msdn.microsoft.com/library/hh213609.aspx
[índices agrupados e índices agrupados]: https://msdn.microsoft.com/library/ms190457.aspx
[Criar sintaxe de tabela]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore índices desfragmentação]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[índices de columnstore agrupadas]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
