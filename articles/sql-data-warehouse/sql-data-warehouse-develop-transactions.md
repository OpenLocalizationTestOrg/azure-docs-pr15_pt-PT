<properties
   pageTitle="Transações no armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para implementar o transações no armazém de dados do SQL Azure para desenvolver soluções."
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>Transações no armazém de dados SQL

Como seria de esperar, armazém de dados SQL suporta transações como parte da carga de trabalho do armazém de dados. No entanto, para garantir que o desempenho do armazém de dados SQL é mantido na escala algumas funcionalidades estão limitadas quando comparado com o SQL Server. Este artigo realça as diferenças e as outras listas. 

## <a name="transaction-isolation-levels"></a>Níveis de isolamento da transação
Armazém de dados SQL implementa transações ÁCIDAS. No entanto, o isolamento do suporte do transaccional está limitado à `READ UNCOMMITTED` e esta não pode ser alterada. Pode implementar um número de codificação métodos para impedir que lê dirty de dados se se tratar de uma preocupações por si. Os métodos mais populares tirar partido CTAS e mudar de partição de tabela (muitas vezes conhecido como o padrão de janela deslizante) para impedir que os utilizadores consultar dados que ainda está a ser preparados. As vistas que previamente filtram os dados também é uma abordagem popular.  

## <a name="transaction-size"></a>Tamanho da transação
Uma transação da modificação de dados simples é limitada em tamanho. O limite de hoje é aplicado "por distribuição". Por conseguinte, a alocação total pode ser calculada ao multiplicar: o limite pela contagem de distribuição. Para aproximada o número máximo de linhas na transação divida o remate de distribuição ao tamanho total de cada linha. Para as colunas de comprimento variável considere demorar um comprimento médio coluna em vez de utilizar o tamanho máximo.

Na tabela abaixo os pressupostos seguintes foram efetuadas:

* Ocorreu uma distribuição par dos dados 
* O comprimento de linha de média é 250 bytes

| [DWU][]    | Maiúscula por distribuição (da chaveta) | Número de distribuições | Tamanho máximo da transação (da chaveta) | # Linhas por distribuição | Linhas de máximo da transação |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  1,5                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2.25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  juros 3,75                      | 60                      |  225                       |  15,000,000             |    900,000,000           |
| DW600  |  4,5                       | 60                      |  270                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30,000,000             |  1,800,000,000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11,25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60.000.000             |  3,600,000,000           |
| DW3000 | 22.5                       | 60                      |  1,350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2.700                     | 180,000,000             | 10,800,000,000           |

O limite de tamanho da transação é aplicado por transação ou operação. Não é aplicada ao longo de todas as operações em simultâneo. Por conseguinte, cada transação é permitida para escrever este quantidade de dados para o registo. 

Para otimizar e minimizar a quantidade de dados escritos no registo, consulte o artigo [transações melhores práticas][] .

> [AZURE.WARNING] O tamanho máximo da transação apenas pode ser atingido para HASH ou onde está a propagação dos dados de tabelas de ROUND_ROBIN distribuído mesmo. Se a transação está a escrever dados de uma forma não linear as distribuições o limite é é provável que seja possível alcançar antes do tamanho máximo da transação.
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Estado da transação
Armazém de dados SQL utiliza a função XACT_STATE() para comunicar uma transação falha utilizando o valor -2. Isto significa que a transação falhou e está marcada para anulação apenas

> [AZURE.NOTE] A utilização de -2 pela função XACT_STATE para designar uma transação falha representa um comportamento diferente para o SQL Server. SQL Server utiliza o valor -1 para representar uma barra committable transação. SQL Server pode tolerar alguns erros dentro de uma transação sem ele ter de ser marcada como barra committable. Por exemplo `SELECT 1/0` seria originar um erro, mas não forçar uma transação para uma fase de barra committable. SQL Server também permite que lê na barra committable operação. No entanto, armazém de dados SQL não permitem-lhe faça o seguinte. Se ocorre um erro dentro de uma transação armazém de dados SQL introduz automaticamente o estado de-2 e não conseguir tornar ainda mais selecione declarações até a declaração de foi revertida. Por conseguinte, é importante verificar que o código da aplicação para ver se utiliza XACT_STATE() à medida que poderá ter de fazer modificações código.

Por exemplo, no SQL Server poderá ver uma transação que tem este aspeto:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Se deixar o seu código tal como está acima, em seguida, obterá a seguinte mensagem de erro:

Msg 111233, nível de 16, estado 1, linha 1 111233; A transação atual foi interrompida e quaisquer alterações pendentes foram revertidas. Problema: Uma transação um Estado só de anulação não foi explicitamente revertida antes de um DDL, LMG ou instrução SELECT.

Também irá obter o resultado das funções ERROR_ * não.

No armazém de dados do SQL o código tem de ser ligeiramente alterado:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

O comportamento esperado agora é observado. O erro na transação é gerido e as funções ERROR_ * fornecem os valores tal como é esperado.

Tudo o que foi alterada é que o `ROLLBACK` da transação tinha deverá acontecer antes da leitura das informações da erro a `CATCH` bloco.

## <a name="errorline-function"></a>Função Error_Line()
Também é notar que armazém de dados SQL não implementar ou a função ERROR_LINE() de suporte. Se tiver isto no seu código terá de removê-lo de estar em conformidade com armazém de dados do SQL. Utilize etiquetas de consulta no seu código para implementar a funcionalidade equivalente. Consulte o artigo [etiqueta][] para obter mais detalhes sobre esta funcionalidade.

## <a name="using-throw-and-raiserror"></a>Utilizar o lançamento e RAISERROR
LANÇAMENTO é a aplicação mais moderna para aumentar as exceções no armazém de dados do SQL mas RAISERROR também é suportada. Existem algumas diferenças que estão património com atenção para contudo.

- Mensagens de erro não podem ser números no intervalo 100,000 150.000 para lançamento definidas pelo utilizador
- Mensagens de erro RAISERROR são corrigidas na 50.000
- Utilização das sys.messages não é suportada

## <a name="limitiations"></a>Limitiations
Armazém de dados SQL tem algumas outras restrições que se relacionam com as transações.

Estes são os seguintes:

- Sem transações distribuídas
- Sem transações aninhadas permitidas
- Não guardar pontos permitido
- Sem transações com nome
- Sem transações marcadas
- Não existe suporte para DDL tal como `CREATE TABLE` dentro de um utilizador definidos da transação

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre como optimizar as transações, consulte o artigo [transações melhores práticas][].  Para saber mais sobre outras práticas recomendadas do armazém de dados SQL, consulte o artigo [melhores práticas do armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[Melhores práticas de transações]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md
[ETIQUETA]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
