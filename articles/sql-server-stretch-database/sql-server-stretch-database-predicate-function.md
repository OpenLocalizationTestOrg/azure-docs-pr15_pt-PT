<properties
    pageTitle="Selecione as linhas para migrar utilizando uma função de filtro (base de dados esticar) | Microsoft Azure"
    description="Saiba como selecionar linhas para migrar utilizando uma função de filtro."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Selecione as linhas para migrar utilizando uma função de filtro (Esticar base de dados)

Se armazenar frios dados numa tabela separada, pode configurar esticar base de dados para migrar toda a tabela. Se a tabela contiver dados quentes e frios, outro lado, pode especificar uma função de filtro para selecionar as linhas que pretende migrar. O predicado filtro é uma tabela de inline\-avaliar a função. Este tópico descreve como escrever uma tabela de inline\-avaliar a função para seleccionar linhas que pretende migrar.

>   [AZURE.NOTE] Se fornecer uma função de filtro que efetua mal, migração de dados também executa a mal. Base de dados esticar aplica-se a função filter à tabela utilizando o operador CROSS aplicar.

Se não especificar uma função de filtro, toda a tabela é migrada.

Quando executa a base de dados ativar para o Assistente de opções esticar, pode migrar uma tabela inteira ou pode especificar uma função de filtro simples no assistente. Se pretender utilizar um tipo diferente da função de filtro para selecionar linhas para migrar, efetue um dos seguintes procedimentos.

-   Sair do assistente e execute a instrução ALTER TABLE para ativar Esticar para a tabela e para especificar uma função de filtro.

-   Execute a instrução ALTER TABLE para especificar uma função de filtro depois de sair do assistente.

A sintaxe de ALTER TABLE para adicionar uma função é descrita mais adiante.

## <a name="basic-requirements-for-the-filter-function"></a>Requisitos básicos para a função filter
A tabela inline\-função avaliada necessária para um predicado de filtro de base de dados esticar aspeto semelhante ao exemplo seguinte.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
Os parâmetros para a função tem de ser identificadores para colunas da tabela.

Ligação de esquemas é necessária para impedir que colunas que são utilizadas pela função filter contra largadas ou alteradas.

### <a name="return-value"></a>Valor devolvido
Se a função devolve um não\-resultado vazio, a linha é elegível para ser migrado. Caso contrário, \- ou seja, se a função não devolve um resultado \- a linha não é elegível para ser migrado.

### <a name="conditions"></a>Condições
O &lt; *predicado* &gt; podem ter de numa condição, ou de várias condições que aderiu com o operador ' e lógico.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Cada condição sucessivamente pode consistir em numa condição primitiva ou de várias condições primitivos associadas com o operador ou lógico.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Condições primitivos
Uma condição primitiva pode efetuar um dos serão as comparações seguintes.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Compare um parâmetro de função para uma expressão constante. Por exemplo, `@column1 < 1000`.

    Eis um exemplo que verifica se o valor de uma coluna de *data* é &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Aplica o operador é nulo ou não é nulo um parâmetro de função.

-   Utilize o operador IN para comparar um parâmetro de função para uma lista de valores constantes.

    Eis um exemplo que verifica se o valor de um *envio\_Estado* coluna é `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>Operadores de comparação
São suportados os seguintes operadores de comparação.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Constantes expressões
As constantes que utiliza uma função de filtro podem ser qualquer expressão determinista que possa ser avaliado quando define a função. Expressões constantes podem conter as seguintes coisas.

-   Literais. Por exemplo, `N’abc’, 123`.

-   Expressões algébricas. Por exemplo, `123 + 456`.

-   Funções determinista. Por exemplo, `SQRT(900)`.

-   Conversões determinista que utilizam CAST ou converter. Por exemplo, `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Outras expressões
Pode utilizar a entre e não os operadores entre se a função resultante está em conformidade com as regras descritas aqui depois de substituir o operador entre e não entre os operadores com o equivalente e e ou expressões.

Não é possível utilizar subconsultas ou não\-determinista funções como rand () ou GETDATE ().

## <a name="add-a-filter-function-to-a-table"></a>Adicionar uma função de filtro a uma tabela
Adicionar uma função de filtro a uma tabela executando a instrução **ALTER TABLE** e especificando uma tabela existente do inline\-avaliar a função como o valor da **filtro\_PREDICADO** parâmetro. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Depois de vincular a função para a tabela como um predicado, as seguintes coisas são verdadeiras.

-   Ocorre a migração de dados de tempo seguinte, apenas as linhas para o qual a função devolve um não\-são migrados valor vazio.

-   As colunas utilizadas pela função são dependente de esquema. Não é possível alterar estes colunas como uma tabela estiver a utilizar a função como predicado seu filtro.

Não é possível largar a tabela inline\-avaliar a função como uma tabela estiver a utilizar a função como predicado seu filtro.

>   [AZURE.NOTE] Para melhorar o desempenho da função filter, crie um índice nas colunas utilizadas pela função.

### <a name="passing-column-names-to-the-filter-function"></a>Os nomes das colunas a passar para a função filter
Ao atribuir uma função de filtro a uma tabela, especifique os nomes das colunas transferido para a função de filtro com um nome de uma peça. Se especificar um nome de três partes quando passar com a coluna nomes, as consultas subsequentes contra a esticar\-tabela activada irá falhar.

Por exemplo, se especificar um nome de coluna de três partes conforme mostrado no seguinte exemplo, a declaração de será executado com êxito, mas as consultas subsequentes contra a tabela irão falhar.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

Em vez disso, especifique a função filter com um nome de coluna de uma peça, conforme mostrado no seguinte exemplo.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Adicionar uma função de filtro após executar o Assistente  

Se pretender utilizar uma função que não é possível criar no Assistente de **Ativar a base de dados para esticar** , pode executar a instrução ALTER TABLE para especificar uma função depois de sair do assistente. Antes de poder aplicar uma função, no entanto, tem de interromper a migração de dados que já está em curso e trazer dados migrados de volta. (Para obter mais informações sobre porque é que isto é necessário, consulte o artigo [substituir uma função de filtro existentes](#replacePredicate).  

1. Inverter a direção de migração e colocá anterior que os dados já migrados. Não pode cancelar esta operação depois de ser iniciado. Implicam também os custos Azure para transferências de dados de saída \(saída\). Para obter mais informações, consulte o artigo [como Azure preços funciona](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Aguarde para migração concluir. Pode verificar o estado no **Monitor do esticar base de dados** a partir do SQL Server Management Studio ou pode consultar a vista de **sys.dm_db_rda_migration_status** . Para obter mais informações, consulte o artigo [Monitor e resolver problemas de migração de dados](sql-server-stretch-database-monitor.md) ou [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  

3. Crie a função de filtro que pretende aplicar à tabela.  

4. Adicionar a função para a tabela e reinicie a migração de dados para o Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Linhas do filtro por data
O exemplo seguinte migra linhas onde a coluna **data** contém um valor anteriores a 1 de Janeiro de 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrar as linhas por valor numa coluna de estado
O exemplo seguinte migra linhas onde a coluna **Estado** contém um dos valores especificados.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrar linhas utilizando uma janela deslizante
Para filtrar linhas utilizando uma janela deslizante, tenha em atenção os seguintes requisitos para a função filter.

-   A função tem de ser determinista. Por conseguinte, não é possível criar uma função que efetua automaticamente a janela deslizante como fases de tempo.

-   A função utiliza encadernação de esquema. Por conseguinte, não pode simplesmente atualizar a função "num local" diariamente ao contactar o suporte **ALTER função** para mover a janela deslizante.

Comece com uma função de filtro como o exemplo seguinte, migra linhas onde a coluna **systemEndTime** contiver um valor anteriores a 1 de Janeiro de 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Aplica a função filter à tabela.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Quando pretende atualizar a janela deslizante, efetue as seguintes coisas.

1.  Crie uma nova função, que especifica a nova janela deslizante. O exemplo seguinte seleciona datas anteriores a 2 de Janeiro de 2016, em vez de 1 de Janeiro de 2016.

2.  Substitua a função filter anterior pelo novo por chamada **ALTER TABLE**, conforme mostrado no seguinte exemplo.

3. Opcionalmente, largue a anterior função filter que já não estiver a utilizar pela **Função LARGAR**de chamada. (Este passo não é mostrado no exemplo.)

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Obter mais exemplos de funções de filtro válido

-   O exemplo seguinte combina duas condições primitivos utilizando o operador ' e lógico.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   O exemplo seguinte utiliza várias condições e uma conversão determinista com converter.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   O exemplo seguinte utiliza os operadores matemáticos e funções.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   O exemplo seguinte utiliza o entre e não os operadores entre. Esta utilização é válida porque a função resultante está em conformidade com as regras descritas aqui depois de substituir o operador entre e não entre os operadores com o equivalente e e ou expressões.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    A função anterior é equivalente à função seguinte depois de substituir a entre e não os operadores entre com o equivalente e e ou expressões.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Exemplos de funções de filtro que não são válidas

-   A função seguinte não é válida porque contém um não\-determinista conversão.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   A função seguinte não é válida porque contém um não\-chamada de função determinista.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   A função seguinte não é válida porque contém uma subconsulta.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   As seguintes funções não são válidas porque expressões que utilizam operadores algébricas ou compilado\-nas funções têm de ser avaliados uma constante quando define a função. Não pode incluir referências de coluna em expressões algébricas ou chamadas de função.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   A função seguinte não é válida, porque-viola as regras descritas aqui depois substitua o operador entre a expressão e equivalente.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    A função anterior é equivalente à função seguinte depois de substituir o operador entre com a expressão e equivalente. Esta função não é válida porque condições primitivos só podem utilizar o operador ou lógico.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>Como base de dados esticar aplica-se a função filter
Base de dados esticar aplica-se a função filter para a tabela e determina elegíveis linhas utilizando o operador CROSS aplicar. Por exemplo:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Se a função devolve um não\-resultado vazio para a linha, a linha é elegível para ser migrado.

## <a name="replacePredicate"></a>Substituir uma função filter existente
Pode substituir uma função de filtro anteriormente especificado executando a instrução **ALTER TABLE** novamente e especificar um novo valor para o **filtro\_PREDICADO** parâmetro. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
A nova tabela inline\-função avaliada tem os seguintes requisitos.

-   A nova função tem de ser menos restritivas do que a função anterior.

-   Todos os operadores que existia na função antiga tem de existir a nova função.

-   A nova função, não pode conter operadores que não existem na função antiga.

-   Não pode alterar a ordem dos argumentos de operador.

-   Apenas os valores constantes que fazem parte de um `<, <=, >, >=` comparação pode ser alterada de uma forma que faz com que a função menos restritivo.

### <a name="example-of-a-valid-replacement"></a>Exemplo de substituição válida
Partem do princípio de que a função seguinte é o predicado filtro atual.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
A função seguinte é válida substituição porque a constante nova data (que especifica uma data posterior corte) faz com que a função menos restritivo.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Exemplos de substituição que não é válidas
A função seguinte não válido porque a constante nova data (que especifica uma data anterior limite) não efetuar a função menos restritivo.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
A função seguinte não válida uma vez que um dos operadores de comparação foi removido.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
A função seguinte não válida uma vez que foi adicionada uma nova condição com o operador ' e lógico.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Remover uma função de filtro de uma tabela
Para migrar a tabela inteira em vez de linhas selecionadas, remova a função existente, definindo **filtro\_PREDICADO** para null. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Depois de remover a função filter, todas as linhas da tabela são elegíveis para migração. Como resultado, não é possível especificar uma função de filtro para a mesma tabela mais tarde, a menos que mostrá novamente todos os dados remotos para a tabela a partir do Azure pela primeira vez. Esta restrição existe para evitar a situação onde linhas que não são elegíveis para migração quando fornecer uma função de filtro novo já foram migradas para Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Verificar a função de filtro aplicada a uma tabela
Para verificar a função de filtro aplicada a uma tabela, abra a vista de catálogo **sys.remote\_dados\_arquivo\_tabelas** e verificar o valor da **filtro\_predicado** coluna. Se o valor é nulo, toda a tabela é elegível para o arquivo. Para obter mais informações, consulte o artigo [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Notas de segurança para funções de filtro  
Uma conta com privilégios de proprietário comprometida, pode efetuar as seguintes coisas.  

-   Criar e aplicar uma função de valor de tabela que consome grandes quantidades de recursos do servidor ou aguarda por um determinado período que resulta em negação de serviço.  

-   Criar e aplicar uma função de valor de tabela que torna possível inferir o conteúdo de uma tabela para a qual o utilizador tem foram explicitamente negado acesso de leitura.  

## <a name="see-also"></a>Consulte também

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
