A unidade da transação da base de dados (DTU) é a unidade de medida na base de dados do SQL que representa a potência relativa de bases de dados com base numa medida de reais: a transação da base de dados. Vamos tirou com um conjunto de operações que são típicas para uma transação online processamento pedido (OLTP) e, em seguida, medido quantas transações foi concluídas por segundo em totalmente carregado condições (que é a versão abreviada, pode ler os detalhes da categoria na [Descrição geral de referência](../articles/sql-database/sql-database-benchmark-overview.md)). 

Por exemplo, uma base de dados Premium P11 com 1750 DTUs fornece 350 x DTU mais calcular power que uma base de dados básico com 5 DTUs. 

![Introdução à base de dados SQL: individual DTUs de base de dados por camadas e nível.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Se estiver a migrar uma base de dados existente do SQL Server, pode utilizar uma ferramenta de terceiros, [A Calculadora de DTU de base de dados de SQL Azure](http://dtucalculator.azurewebsites.net/), para obter uma estimativa do nível de desempenho e serviço camada que a base de dados pode necessitar de base de dados do SQL Azure.

### <a name="dtu-vs-edtu"></a>DTU vs. eDTU

Converte DTU para bases de dados única diretamente para o eDTU para bases de dados flexível. Por exemplo, uma base de dados num conjunto de base de dados flexível básicas oferece eDTUs até 5. Esse é o desempenho do mesmo como um único básica da base de dados. A diferença é que a base de dados flexível não consumir qualquer eDTUs do conjunto de até que esta tenha para. 

![Introdução à base de dados SQL: flexível conjuntos de dados por camadas.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Um exemplo simples ajuda. Tirar um conjunto de base de dados flexível básica com 1000 DTUs e largue 800 bases de dados no mesmo. Desde que apenas 200 das bases de 800 dados estão a ser utilizados em qualquer ponto no tempo (5 DTU X 200 = 1000), não visitas capacidade do conjunto de e desempenho de base de dados não diminui. Neste exemplo, se simplificadas para maior clareza. Os cálculos matemáticos real são um pouco mais complexo. O portal é que os cálculos matemáticos por si e faz com que uma recomendação com base na utilização de histórico da base de dados. Consulte o artigo [preço e desempenho considerações para um agrupamento de base de dados flexível](../articles/sql-database/sql-database-elastic-pool-guidance.md) para saber como funcionam as recomendações ou para faça os cálculos matemáticos si mesmo. 
