 <properties
    pageTitle="Como utilizar lotes para melhorar o desempenho da aplicação de base de dados do Azure SQL"
    description="O tópico fornece provas esse lotes operações de base de dados imroves significativamente a velocidade e escalabilidade das suas aplicações de base de dados do Azure SQL. Apesar destas técnicas lotes ideal para quaisquer bases de dados do SQL Server, o foco do artigo estiver no Azure."
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Como utilizar lotes para melhorar o desempenho da aplicação de base de dados SQL

Operações de base de dados do SQL Azure de lotes significativamente melhora o desempenho e escalabilidade das suas aplicações. Para compreender as vantagens, a primeira parte deste artigo abrange alguns resultados de teste de exemplo que comparam sequenciais e por lotes pedidos para uma base de dados do SQL. O resto do artigo mostra a técnicas, cenários e considerações para ajudá-lo para utilizar com êxito de lotes nas suas aplicações Azure.

**Autores**: João Roth, Silvano Coriani, Trent Swanson (escala completa 180 Inc)

**Os revisores**: Conor Cunningham, Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>Por que razão é de lotes importantes para a base de dados SQL?
As chamadas para um serviço remoto de lotes são uma estratégia de famoso para aumentar o desempenho e escalabilidade. Não existem são processamento custos fixos para qualquer interações com um serviço remoto, como serialização, a transferência de rede e desserialização. Embalagem quantas transações separadas para um único lote minimiza estes custos.

Neste documento técnico, podemos pretende examinar a base de dados do SQL vários lotes estratégias e cenários. Apesar destas estratégias também são importantes para as aplicações no local que utilizam o SQL Server, existem vários motivos para realçar a utilização de lotes para a base de dados SQL:

- Há latência da rede potencialmente maior no aceder a base de dados SQL, especialmente se estiver a aceder a base de dados SQL do fora o Centro de dados do Microsoft Azure mesmo.
- As características multi-inquilino da base de dados SQL significa que a eficiência dos dados access camada correlaciona com para a escalabilidade geral da base de dados. Base de dados SQL tem impedir que qualquer utilizador/inquilino único monopolizar recursos de base de dados em detrimento dos outros inquilinos. Em resposta à utilização que excedam quotas predefinidas, base de dados SQL pode reduzir débito ou responder com limitação exceções. Eficiência, tal como lotes, permite-lhe fazer mais trabalho numa base de dados SQL antes de atingir estes limites. 
- Lotes também são eficaz para arquitecturas que utilizam várias bases de dados (sharding). A eficiência da sua interação com cada unidade de base de dados ainda é um fator de chave no seu escalabilidade geral. 

Uma das vantagens da utilização de base de dados SQL é que não tem de gerir os servidores que alojam a base de dados. No entanto, esta infraestrutura gerida também significa que tem a pensar otimizações de base de dados de forma diferente. Já não pode procurar para melhorar a infraestrutura de rede ou hardware de base de dados. Microsoft Azure controla os ambientes. A área principal que pode controlar é como a sua aplicação interage com base de dados SQL. Lotes são um dos seguintes otimizações. 

A primeira parte do papel examina várias técnicas lotes para aplicações do .NET que utilizam a base de dados SQL. As dois últimos secções abrangem lotes diretrizes e cenários.

## <a name="batching-strategies"></a>Estratégias de lotes

### <a name="note-about-timing-results-in-this-topic"></a>Nota sobre os resultados de temporização neste tópico
>[AZURE.NOTE] Os resultados não forem referências mas destinam-se para mostrar o **Desempenho relativo**. Temporizações são baseadas em média, mínimo de 10 teste será executado. As operações são insere para uma tabela vazia. Estes testes foram medido pre-V12 e não seja necessariamente correspondem ao débito que se poderá deparar numa base de dados V12 utilizando as novas [camadas de serviços](sql-database-service-tiers.md). A vantagem relativa da técnica lotes deve ser semelhante.

### <a name="transactions"></a>Transações
Parece estranho para começar uma revisão de lotes por debater transações. No entanto, a utilização de transações do lado do cliente tem um efeito de lotes do lado do servidor ténue que melhora o desempenho. E transações podem ser adicionadas com apenas algumas linhas de código, para que fornecem uma forma rápida para melhorar o desempenho de operações sequenciais.

Considere o seguinte c# código que contém uma sequência de inserir e atualizar operações numa tabela simples.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

O seguinte código ADO.NET sequencialmente executa estas operações.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

É a melhor forma de otimizar este código implementar o alguma forma de lado do cliente de lotes destas chamadas. Mas existe uma forma simple para aumentar o desempenho deste código por moldagem simplesmente a sequência de chamadas de uma transação. Eis o mesmo código que utiliza uma transação.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

Transações realmente estão a ser utilizadas nas duas estes exemplos. No primeiro exemplo, cada chamada individual é uma transação implícita. No segundo exemplo, uma transação explícita é moldado todas as chamadas. Pela documentação para o [escrita automáticas registo de transações](https://msdn.microsoft.com/library/ms186259.aspx), registos de registo são descarregados para o disco quando a transação consolida. Por isso, incluindo chamadas mais de uma transação, escrita para o registo de transações pode atrasar até que a operação de é consolidada. Na verdade, são activação de lotes para escritas para o registo de transações do servidor.

A tabela seguinte mostra algumas resultados dos testes de ad-hoc. Os testes executado o mesmo inserir sequencial com e sem transações. Para obter mais perspetiva, o primeiro conjunto de testes correu remotamente a partir de um computador portátil para a base de dados no Microsoft Azure. O segundo conjunto de testes executou a partir de um serviço na nuvem e a base de dados que ambos residiam dentro o Centro de dados do Microsoft Azure mesmo (Ocidental e.u.a.). A tabela seguinte mostra a duração em milissegundos do insere sequenciais com e sem transações.

**No local para Azure**:

| Operações | Sem transação (ms) | Transação (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Azure para Azure (mesmo centro de dados)**:

| Operações | Sem transação (ms) | Transação (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] Resultados não são referências. Consulte a [Nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).

Com base nos resultados de teste anterior, a moldagem de uma única operação uma transação diminui realmente desempenho. Mas à medida que aumenta o número de operações dentro de uma única transação, a melhoria de desempenho torna-se mais marcada. A diferença de desempenho também é mais evidente quando todas as operações ocorrem dentro o Centro de dados do Microsoft Azure. A latência um aumento de utilização de base de dados SQL do fora o Centro de dados do Microsoft Azure torna demasiado sombreado de ganhos de desempenho de utilização de transações.

Apesar da utilização de transações pode melhorar o desempenho, continue a [observar melhores práticas para transações e ligações](https://msdn.microsoft.com/library/ms187484.aspx). Manter a transação tão curta quanto possível e feche a ligação de base de dados, depois de concluir o trabalho. O utilizando instrução no exemplo anterior assegura que a ligação é fechada quando concluir o bloco de código subsequentes.

O exemplo anterior demonstra que pode adicionar uma transação local a qualquer código ADO.NET com duas linhas. Transações fornecem uma forma rápida de melhorar o desempenho do código que considera inserir sequencial, atualizar e eliminar operações. No entanto, para o melhor desempenho, considere alterar o código na sequência tirar partido do lado do cliente de lotes, como parâmetros de valor de tabela.

Para mais informações sobre as transações no ADO.NET, consulte o artigo [Transações Local no ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### <a name="table-valued-parameters"></a>Parâmetros de valor de tabela
Valor de tabela parâmetros suportam tipos de tabela definido pelo utilizador como parâmetros na instruções Transact-SQL, procedimentos armazenados e funções. Esta técnica lotes do lado do cliente permite-lhe enviar várias linhas de dados dentro o parâmetro do valor de tabela. Para utilizar parâmetros de valor de tabela, primeiro defina um tipo de tabela. A seguinte instrução Transact-SQL cria um tipo de tabela com o nome **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

No código, crie uma **DataTable** com os mesmos nomes exatos e tipos do tipo de tabela. Passar esta **DataTable** um parâmetro numa consulta de texto ou chamada de procedimento armazenado. O exemplo seguinte mostra esta técnica:

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

No exemplo anterior, o objeto **SqlCommand** insere linhas a partir de um parâmetro do valor de tabela, **@TestTvp**. O objeto **DataTable** criado anteriormente está atribuído a este parâmetro com o método de **SqlCommand.Parameters.Add** . Insere uma chamada de lotes significativamente aumentam o desempenho ao longo do insere sequenciais.

Para melhorar a ainda mais o exemplo anterior, utilize um procedimento armazenado em vez de um comando baseado em texto. O seguinte comando Transact-SQL cria um procedimento armazenado que leva-o até o parâmetro do valor de tabela **SimpleTestTableType** .

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Em seguida, altere a declaração de objeto **SqlCommand** no exemplo anterior código ao seguinte.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Na maioria dos casos, o valor de tabela parâmetros ter equivalente ou um melhor desempenho que outras técnicas lotes. Valor de tabela parâmetros são geralmente preferíveis, uma vez que são mais flexíveis do que as outras opções. Por exemplo, outras técnicas, tal como cópia de em volume SQL, só permitem a inserção de novas linhas. Mas com parâmetros de valor de tabela, pode utilizar a lógica no procedimento armazenado para determinar quais as linhas são atualizações e que são insere. O tipo de tabela também pode ser modificado para conter uma coluna de "A operação" que indica se a linha especificada deve ser inserida, atualizada ou eliminada.

A tabela seguinte mostra os resultados de teste do ad-hoc para a utilização dos parâmetros de valor de tabela em milissegundos.

| Operações | No local para Azure (ms)  | Azure mesmo centro de dados do (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] Resultados não são referências. Consulte a [Nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).

O lucro de desempenho a partir de lotes é evidente imediatamente. No teste sequencial anterior, operações de 1000 demoraram segundos 129 exterior o Centro de dados e, 21 segundos a partir do Centro de dados. Mas com valor de tabela parâmetros, operações de 1000 demorar apenas 2.6 alguns segundos fora o Centro de dados e 0,4 segundos dentro o Centro de dados.

Para mais informações sobre os parâmetros de valor de tabela, consulte o artigo [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Copiar em volume SQL
Copiar em volume SQL é outra forma de inserir grandes quantidades de dados para uma base de dados de destino. Aplicações do .NET podem utilizar a classe de **SqlBulkCopy** para efetuar operações de inserir em volume. **SqlBulkCopy** é uma função semelhante a ferramenta de linha de comandos, **Bcp.exe**ou a instrução Transact-SQL, **Inserir em volume**. Exemplo de código que se segue mostra como em volume copiar as linhas na origem de **DataTable**, tabela, para a tabela de destino no SQL Server, AMinhaTabela.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Existem alguns casos, onde cópia em massa é preferida através de parâmetros de valor de tabela. Consulte a tabela de comparação de valor de tabela parâmetros versus operações de inserção em massa no tópico [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

Os seguintes resultados de teste do ad-hoc mostram o desempenho do lotes com **SqlBulkCopy** em milissegundos.

| Operações | No local para Azure (ms)  | Azure mesmo centro de dados do (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] Resultados não são referências. Consulte a [Nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).

Em tamanho mais pequeno do lote, os parâmetros de valor de tabela de utilização outperformed a classe de **SqlBulkCopy** . No entanto, **SqlBulkCopy** efetuadas 12 – 31% mais rápido do que o valor de tabela parâmetros para os testes de 1.000 e 10.000 linhas. Como o valor de tabela parâmetros, **SqlBulkCopy** é uma boa opção para por lotes inserções, especialmente quando comparado com o desempenho das operações que não sejam enviadas em batches.

Para mais informações sobre copiar em volume no ADO.NET, consulte o artigo [Operações de copiar em massa no SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Declarações de parametrizadas Inserir linha múltiplos
É uma alternativa para pequenos lotes ao construir uma instrução de INSERT parametrizada grande que insere várias linhas. Exemplo de código seguinte demonstra esta técnica.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

Este exemplo se destinar a mostrar o conceito básico. Um cenário mais real seria loop as entidades necessárias para construir a cadeia de consulta e os parâmetros do comando em simultâneo. Está limitado a um total de parâmetros de consulta 2100, para que isto limita o número total de linhas que podem ser processadas desta forma.

Os seguintes resultados de teste do ad-hoc mostram o desempenho deste tipo de declaração inserir em milissegundos.

| Operações | Parâmetros de valor de tabela (ms) | Inserir instrução simples (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] Resultados não são referências. Consulte a [Nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).

Esta abordagem pode ser ligeiramente mais rápida para secções que estão menos de 100 linhas. Apesar da melhoria for pequena, esta técnica é outra opção que poderá funcionar corretamente no seu cenário de aplicação específica.

### <a name="dataadapter"></a>DataAdapter
A classe de **DataAdapter** permite-lhe modificar um objeto de **conjunto de dados** e, em seguida, submeter as alterações como inserir, ATUALIZAR e eliminar operações. Se estiver a utilizar o **DataAdapter** desta forma, é importante ter em atenção que são efetuadas chamadas separadas para cada operação distinta. Para melhorar o desempenho, utilize a propriedade **UpdateBatchSize** para o número de operações que devem ser enviadas em batches cada vez. Para mais informações, consulte o artigo [Efetuar lote operações utilizando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Arquitetura de entidade
Arquitetura de entidade não suporta atualmente lotes. Os programadores diferentes na Comunidade do tem tentado demonstrar soluções, tal como o método de **SaveChanges** de substituição. Mas as soluções são normalmente complexo e personalizada para a aplicação e o modelo de dados. O projeto de codeplex entidade Framework tem atualmente uma página de debate este pedido de funcionalidade. Para ver este debate, consulte o artigo [Notas de reunião de Design – 2 de Agosto de 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Para integridade, podemos sentir que é importante falar sobre XML como uma estratégia de lotes. No entanto, a utilização de XML tem sem vantagens em relação a outros métodos e desvantagens várias. A abordagem é semelhante aos parâmetros de valor de tabela, mas um ficheiro XML ou cadeia lhe é transmitida um procedimento armazenado em vez de uma tabela definido pelo utilizador. O procedimento armazenado analisa os comandos no procedimento armazenado.

Existem várias desvantagens para esta abordagem:

- Trabalhar com XML pode ser complicado e sujeito a erros.
- XML da base de dados de análise pode consumir CPU.
- Na maioria dos casos, este método é mais lento do que o valor de tabela parâmetros.

Por estas razões, a utilização de XML para consultas lote não é recomendada.

## <a name="batching-considerations"></a>Considerações de lotes
As secções seguintes fornecem mais orientação para a utilização de lotes nas aplicações de base de dados SQL.

### <a name="tradeoffs"></a>Responsabilidades
Dependendo da sua arquitetura de lotes podem envolver uma solução de compromisso entre o desempenho e RDP. Por exemplo, considere o cenário onde ao seu cargo vai inesperadamente para baixo. Se perder a uma linha de dados, o impacto for menor que o impacto das perder um lote de linhas não submetidas grandes dimensões. Existe um risco maior quando memória intermédia linhas antes de enviar-lhes para a base de dados numa janela de tempo especificado.

Devido a esta solução de compromisso, avalie o tipo de operações desse lote. Batch mais agressivamente (lotes maiores e mais tempo windows) com os dados que são menos críticos.

### <a name="batch-size"></a>Tamanho de batch
Nos nossos testes, normalmente havido sem partido para dividir secções de grandes dimensões em blocos mais pequenos. Na verdade, este subdivisões frequentemente resultaram um desempenho mais lento que submeter um lote de grandes dimensões único. Por exemplo, considere um cenário em que pretende inserir linhas de 1000. A tabela seguinte mostra quanto tempo demora para utilizar o valor de tabela parâmetros para inserir linhas de 1000 quando dividido em secções mais pequenas.

| Tamanho de batch | Iterações | Parâmetros de valor de tabela (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] Resultados não são referências. Consulte a [Nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).

Pode ver que o melhor desempenho para as linhas de 1000 é submetê-los ao mesmo tempo. Em outros testes (não apresentadas aqui) havido um ganho desempenho pequena para dividir um lote de 10000 linha em dois lotes de 5000. Mas o esquema da tabela para estes testes é relativamente simple, pelo que deve efetuar testes no seu dados específicos e tamanhos de lote para verificar estas conclusões.

Outro factor a ter em conta é que se o lote total ficar demasiado grande, base de dados SQL poderá optimizar e recusar o lote de consolidação. Para obter melhores resultados, teste o seu cenário específico para determinar se existe um tamanho ideal lote. Tornar o tamanho do lote configuráveis o tempo de execução para ativar ajustes rápidas com base no desempenho ou erros.

Por fim, equilibre o tamanho do lote de com os riscos associados lotes. Se existem erros breves ou a função falha, considere as consequências de repetir a operação ou de perda de dados no lote de.

### <a name="parallel-processing"></a>Processamento paralelo
E se tenha apontado a abordagem de como reduzir o tamanho do lote mas utilizados vários threads para executar o trabalho? Novamente, nossos testes mostrava que de várias secções mais pequeno multithreaded executado normalmente pior um único lote maior. O seguinte teste tenta inserir linhas de 1000 em lotes paralelas um ou mais. Este teste mostra como mais secções simultâneas desempenho realmente diminuíram.

| Tamanho do lote [iterações] | Dois threads (ms) | Quatro threads (ms) | Seis threads (ms) |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] Resultados não são referências. Consulte a [Nota sobre os resultados de temporização neste tópico](#note-about-timing-results-in-this-topic).

Existem vários motivos possíveis para a degradação no desempenho devido ao paralelismo:

- Existem várias chamadas de rede em simultâneo em vez de um.
- Várias operações contra uma única tabela podem resultar em contenção e bloquear.
- Existem transparências associadas multithreading.
- As despesas de abertura de várias ligações ultrapassa o benefício de processamento paralelo.

Se destinar bases de dados ou de tabelas diferentes, é possível ver algumas desempenho ganhar com esta estratégia. Sharding de base de dados ou profissionais seria um cenário para esta abordagem. Sharding utiliza várias bases de dados e encaminha os dados diferentes para cada base de dados. Se cada lote pequeno for para outra base de dados, em seguida, executar as operações em paralelo pode ser mais eficaz. No entanto, o lucro de desempenho não está suficientemente significativo para utilizar como a base de decisão para utilizar sharding de base de dados na sua solução.

Em algumas estruturas, paralela execução dos mais pequenos lotes pode resultar em débito melhorado de pedidos de num sistema em caso de carga. Neste caso, mesmo que seja mais rápido processar um único lote maior, processamento de múltiplas secções em paralelo poderá ser mais eficiente.

Se utiliza o execução paralela, considere a controlar o número máximo de threads de trabalho. Um número mais pequeno poderá resultar em menos contenção e um tempo de execução mais rápido. Além disso, considere a carga adicional que este coloca da base de dados de destino nas ligações e transações.

### <a name="related-performance-factors"></a>Fatores de desempenho relacionados
Típica orientações sobre o desempenho da base de dados também afeta lotes. Por exemplo, insira o desempenho é reduzido para tabelas que tenham uma chave primária grande ou muitos índices agrupados.

Se utilizam o valor de tabela parâmetros um procedimento armazenado, pode utilizar o comando **SET NOCOUNT ON** no início do procedimento. Esta declaração de suprime o retorno da contagem das linhas afetadas no procedimento. No entanto, nos nossos testes, a utilização de **SET NOCOUNT ON** ou não teve efeitos ou desempenho diminuído. O procedimento de teste armazenado foi simple com um único **Inserir** comando a partir do parâmetro do valor de tabela. É possível que os procedimentos armazenados mais complexos seriam beneficiar esta declaração de. Mas não partem do princípio de que adicionar **SET NOCOUNT ON** automaticamente para o procedimento armazenado melhora o desempenho. Para compreender o efeito, teste o procedimento armazenado com e sem a instrução **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Cenários de lotes
As secções seguintes descrevem como utilizar parâmetros de valor de tabela no três cenários de aplicação. O primeiro cenário mostra como a memória intermédia e de lotes podem funcionar juntos. O segundo cenário melhora o desempenho através da execução de operações de modelo global de detalhe numa chamada de procedimento armazenado único. O cenário final mostra como utilizar o valor de tabela parâmetros numa operação de "UPSERT".

### <a name="buffering"></a>Memória intermédia
Apesar de existirem alguns cenários que são óbvios candidatos para lotes, existem muitos cenários que poderiam tirar partido das adiada processamento de lotes. No entanto, processamento adiado também executa um risco maior do que os dados são perdidos trabalho uma falha inesperada. É importante compreender este risco e ter em consideração as consequências.

Por exemplo, considere uma aplicação web que controla o histórico de navegação de cada utilizador. No pedido de cada página, a aplicação pode fazer uma chamada para gravar a vista de página do utilizador de base de dados. Mas mais elevado desempenho e escalabilidade podem ser realizados pelos memória intermédia de atividades de navegação dos utilizadores e, em seguida, enviar estes dados para a base de dados em lotes. Pode acionar a atualização de base de dados por tempo decorrido e/ou o tamanho da memória intermédia. Por exemplo, uma regra poderia especificar que o lote deve ser processado depois de 20 segundos ou quando a memória intermédia atinge 1000 itens.

Exemplo de código seguinte utiliza [Extensões reactivar - recepção](https://msdn.microsoft.com/data/gg577609) para processar eventos na memória intermédia elevados por uma classe de monitorização. Quando a memória intermédia preenchida ou um tempo limite é atingido, o lote de dados do utilizador é enviado para a base de dados com um parâmetro do valor de tabela.

A seguinte classe NavHistoryData modelos os detalhes de navegação do utilizador. Contém informações básicas, como o identificador de utilizador, o URL acedido e o tempo de acesso.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

A classe de NavHistoryDataMonitor é responsável pela utilização da memória intermédia os dados de navegação do utilizador à base de dados. Contém um método, RecordUserNavigationEntry, que responde aumentando um evento **OnAdded** . O código seguinte mostra a lógica do construtor que utiliza recepção para criar uma coleção de observável com base no evento. -Em seguida, subscreve a esta coleção observável com o método de memória intermédia. A Sobrecarga indica que a memória intermédia deve ser enviada cada 20 segundos ou entradas de 1000.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

O processador converte todos os itens na memória intermédia um tipo de valor de tabela e, em seguida, transmite este tipo de um procedimento armazenado que processa o lote de. O código seguinte mostra a definição de concluída para o NavHistoryDataEventArgs e as classes NavHistoryDataMonitor.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

Para utilizar esta classe na memória intermédia, a aplicação cria um objecto NavHistoryDataMonitor estático. Sempre que um utilizador acede a uma página, a aplicação chama o método de NavHistoryDataMonitor.RecordUserNavigationEntry. A lógica na memória intermédia avança para encarregam-se de enviar estas entradas à base de dados em lotes.

### <a name="master-detail"></a>Detalhe principal
Valor de tabela parâmetros são úteis para cenários de inserir simples. No entanto, pode ser mais difícil para insere lote que envolvam mais de uma tabela. O cenário "mestre/detalhe" é um bom exemplo. A tabela mestra identifica entidade primária. Uma ou mais tabelas de detalhe armazenam dados mais sobre a entidade. Neste cenário, relações de chave externa impõem a relação entre os detalhes para uma entidade mestra exclusiva. Considere a hipótese de uma versão simplificada de uma tabela de PurchaseOrder e a sua tabela OrderDetail associada. A seguinte Transact-SQL cria a tabela de PurchaseOrder com quatro colunas: CódigoDaEncomenda, DataDaEncomenda, CustomerID e estado.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Cada encomenda contém um ou mais compras do produto. Esta informação é capturada na tabela PurchaseOrderDetail. A seguinte Transact-SQL cria a tabela PurchaseOrderDetail com cinco colunas: CódigoDaEncomenda, OrderDetailID, Iddoproduto, PreçoUnitário e OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

A coluna CódigoDaEncomenda na tabela PurchaseOrderDetail tem de referenciar uma ordem a partir da tabela PurchaseOrder. A seguinte definição de uma chave externa impõe este restrição.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Para poder utilizar o valor de tabela parâmetros, tem de ter um tipo de tabela definido pelo utilizador para cada tabela de destino.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Em seguida, defina um procedimento armazenado que aceita tabelas um destes tipos. Este procedimento permite que uma aplicação localmente batch um conjunto de encomendas e detalhes da encomenda numa única chamada. Transact-SQL seguinte fornece a declaração de procedimento armazenado completa para este exemplo de encomenda de compra.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

Neste exemplo, definidas localmente @IdentityLink tabela armazena os valores reais do CódigoDaEncomenda das linhas inseridas recentemente. Estes identificadores ordem são diferentes dos valores CódigoDaEncomenda temporários na @orders e @details parâmetros de valor de tabela. Por esta razão, o @IdentityLink tabela, em seguida, liga CódigoDaEncomenda os valores a partir de @orders parâmetro para os valores reais do CódigoDaEncomenda para as novas linhas na tabela PurchaseOrder. Após neste passo, o @IdentityLink tabela pode facilitar a inserir os detalhes da encomenda com a CódigoDaEncomenda real que satisfaz a restrição de chave externa.

Este procedimento armazenado pode ser utilizado a partir de código ou de outras chamadas Transact-SQL. Consulte a secção de parâmetros de valor de tabela deste documento para um exemplo de código. Transact-SQL que se segue mostra como ligar o sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

Esta solução permite cada lote para utilizar um conjunto de valores de CódigoDaEncomenda que começam por 1. Estes valores CódigoDaEncomenda temporários descrevem as relações no lote de, mas os valores reais do CódigoDaEncomenda são determinados no momento da operação de inserir. Pode executar repetidamente as mesmas instruções no exemplo anterior e gerar encomendas exclusivas na base de dados. Por esta razão, recomendamos adicionar lógica de código ou base de dados mais que impede que encomendas duplicadas ao utilizar esta técnica de lotes.

Este exemplo demonstra que ainda mais complexas operações de base de dados, tais como operações de modelo global de detalhe, podem ser enviadas em batches utilizando os parâmetros do valor de tabela.

### <a name="upsert"></a>UPSERT
Outro cenário lotes envolve em simultâneo atualizar linhas existentes e inserir novas linhas. Esta operação é referida por vezes, como uma operação de "UPSERT" (actualização + insert). Em vez de efetuar chamadas em separado para inserir e ATUALIZAR, a instrução de impressão em série é mais adequada para esta tarefa. A declaração de impressão em série pode executar ambas as opções inserir e atualizar operações numa única chamada.

Parâmetros de valor de tabela podem ser utilizados com a instrução de impressão em série para executar atualizações e inserções. Por exemplo, considere uma tabela de empregado simplificada que contém as seguintes colunas: o campo IDdeEmpregado, NomePróprio, apelido, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
Neste exemplo, pode utilizar o facto da SocialSecurityNumber exclusivo para executar uma impressão em série de vários empregados. Primeiro, crie o tipo de tabela definido pelo utilizador:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Em seguida, crie um procedimento armazenado ou escrever código que utiliza a instrução de impressão em série para efetuar a atualização e inserir. O exemplo seguinte utiliza a instrução de impressão em série num parâmetro de valor de tabela, @employees, tipo EmployeeTableType. Os conteúdos do @employees tabela não são apresentados aqui.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Para obter mais informações, consulte a documentação e exemplos para a declaração de impressão em série. Apesar do trabalho mesmo pôde ser executado num passo de vários armazenados chamada de procedimento com separar inserir e operações de ATUALIZAÇÃO, a declaração de impressão em série é mais eficiente. Código de base de dados também pode construir chamadas Transact-SQL que utilizem a instrução de impressão em série diretamente sem necessidade de duas chamadas de base de dados para inserir e ATUALIZAR.

## <a name="recommendation-summary"></a>Recomendação resumo

A lista seguinte fornece um resumo das recomendações lotes outros fabricantes referidos neste tópico:

- Utilize a memória intermédia e de lotes para aumentar o desempenho e escalabilidade das aplicações de base de dados SQL.
- Compreenda as concessões entre lotes/a memória intermédia e RDP. Durante uma falha de função, o risco de perder um lote de dados de negócio crítica não transformado poderá compensar o benefício de desempenho de lotes.
- Tentativa de manter todas as chamadas para a base de dados do Centro de dados único para reduzir a latência.
- Se optar por uma única técnica lotes, o valor de tabela parâmetros oferecem o melhor desempenho e flexibilidade.
- Para obter o desempenho de inserir mais rápido, siga estas Diretrizes gerais mas testar o seu cenário:
    - Para < 100 linhas, utilizar um único parametrizadas comando Inserir.
    - Para as linhas de < 1000, utilize o valor de tabela parâmetros.
    - Para > = 1000 linhas, utilize SqlBulkCopy.
- Para atualizar e eliminar operações, utilize o valor de tabela parâmetros com lógica de procedimento armazenado que determina a operação correta em cada linha no parâmetro de tabela.
- Diretrizes de tamanho do lote:
    - Utilize os tamanhos lote maior adequadas para a aplicação e os requisitos de negócio.
    - Saldo de ganhos de desempenho de grandes lotes com os riscos da falhas temporários ou grave. O que é a consequência de tentativas ou perda de dados no lote de? 
    - Teste o tamanho máximo do lote para verificar que base de dados SQL não rejeitar.
    - Crie definições de configuração desse controlo de lotes, tal como o tamanho do lote ou a janela de tempo na memória intermédia. Estas definições fornecem flexibilidade. Pode alterar o comportamento lotes de produção sem Reimplementar o serviço de nuvem.
- Evite paralela execução dos lotes que funcionam numa única tabela numa base de dados. Se optar por dividir um único lote através de vários threads de trabalho, execute testes para determinar o número de threads ideal. Depois de um limite de desconhecidas threads mais irão diminuir o desempenho em vez de aumentá-la.
- Considere a utilização da memória intermédia no tamanho e a hora como uma forma de execução de lotes para obter mais cenários.

## <a name="next-steps"></a>Próximos passos

Este artigo aborda como estrutura de base de dados e técnicas relacionados com lotes de codificação podem melhorar o desempenho da aplicação e escalabilidade. Mas este é apenas uma factor na sua estratégia de geral. Para mais formas de melhorar o desempenho e escalabilidade, consulte o artigo [desempenho de base de dados do SQL Azure orientação para bases de dados única](sql-database-performance-guidance.md) e [preço e desempenho considerações para um agrupamento de base de dados flexível](sql-database-elastic-pool-guidance.md).
