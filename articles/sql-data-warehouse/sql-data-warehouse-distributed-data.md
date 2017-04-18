<properties
   pageTitle="Dados são distribuídos normalmente e distribuído opções da tabela para os sistemas de previstos excederem consideravelmente paralelas processamento (MPP) dos armazém de dados SQL e Parallel Data Warehouse | Microsoft Azure"
   description="Saiba como os dados são distribuídos para previstos excederem consideravelmente paralelas processamento (MPP) e as opções de forma a distribuir tabelas no armazém de dados do SQL Azure e Parallel Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Dados distribuídos e distribuídas tabelas para previstos excederem consideravelmente paralelas processamento (MPP)

Saiba como os dados de utilizador são distribuídos no armazém de dados do SQL Azure e Parallel Data Warehouse, que são sistemas de previstos excederem consideravelmente paralelas processamento (MPP) da Microsoft. Estruturar o seu armazém de dados para utilizar os dados distribuídos eficazmente ajuda-o para atingir as benefícios da arquitectura MPP de processamento de consultas. Algumas opções de estrutura de base de dados podem ter um impacto significativo sobre como melhorar o desempenho da consulta.  

>[AZURE.NOTE] Azure armazém de dados SQL e Parallel Data Warehouse utilizam a mesma estrutura previstos excederem consideravelmente paralelas processamento (MPP), mas algumas diferenças que tenham devido a plataforma subjacente. Armazém de dados SQL é uma plataforma como um serviço (PaaS) que é executada no Azure. Parallel Data Warehouse é executado no sistema de plataforma Analytics (Apps), que é um aparelho no local que é executada no Windows Server.

## <a name="what-is-distributed-data"></a>O que é dados distribuídos?

No armazém de dados SQL e Parallel Data Warehouse, os dados distribuídos refere-se aos dados de utilizador que estão armazenados em várias localizações no sistema de MPP. Cada uma das localizações funciona como um armazenamento independente e a unidade de processamento que é executada consultas no seu parte dos dados. Dados distribuídos são fundamentais para executar consultas em paralelo para alcançar o desempenho da consulta alta.

Para distribuir dados, o armazenamento de dados atribui cada linha da tabela de um utilizador para uma localização distribuída.  Pode distribuir tabelas com um método de distribuição hash ou um método de round robin. O método de distribuição é especificado na instrução CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Distribuído hash tabelas
  
O diagrama seguinte ilustra como um inteiro (tabela que não sejam distribuídas) é armazenado como uma tabela distribuído hash. Uma função determinista atribui a cada linha pertencem a uma distribuição. A definição da tabela, uma das colunas está designada como a coluna de distribuição. A função hash utiliza o valor na coluna de distribuição para atribuir a cada linha a uma distribuição.

Existem considerações de desempenho para a seleção de uma coluna de distribuição, tais como distinção, distorção de dados e os tipos de consultas executar no sistema.
  
![Tabela de distribuído] (media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela de distribuído")  
  
-   Cada linha pertence a uma distribuição.  
  
-   Um algoritmo hash determinista atribui a cada linha a uma distribuição.  
  
-   O número de linhas de tabela por distribuição varia conforme apresentada pelas diferentes tamanhos de tabelas.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas round robin

Uma tabela distribuída round robin distribui as linhas, atribuindo cada linha a uma distribuição de uma forma sequencial. É rápido carregar os dados numa tabela round robin, mas o desempenho da consulta, poderá ser mais lento.  Aderir a uma tabela de round robin normalmente requer redistribuição as linhas para ativar a consulta produzir um resultado preciso, que demora tempo.

## <a name="distributed-storage-locations-are-called-distributions"></a>Localizações de armazenamento distribuído chamam-se as distribuições

Cada localização distribuída chama-se uma distribuição. Quando executa uma consulta em paralelo, cada distribuição executa uma consulta SQL na sua parte dos dados. Armazém de dados SQL utiliza a base de dados SQL para executar a consulta. Parallel Data Warehouse utiliza o SQL Server para executar a consulta. Este design de arquitectura nada partilhado é fundamental para alcançar a escala de saída paralelas computação.

### <a name="can-i-view-the-distributions"></a>Pode ver as distribuições?

Cada distribuição tem um ID de distribuição e está visível em vistas de sistema que pertencem aos armazém de dados SQL e Parallel Data Warehouse. Pode utilizar o ID de distribuição para resolver problemas de desempenho da consulta e outros problemas. Para obter uma lista das vistas de sistema, consulte o artigo a [vista do sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Diferença entre uma distribuição e um nó cluster

Uma distribuição é a unidade básica para armazenar os dados distribuídos e processamento de consultas paralelas. As distribuições são agrupadas em nós de cluster. Cada nó cluster controla as distribuições um ou mais.  

-   Sistema de plataforma de análise utiliza cluster nós como um componente central das capacidades de hardware arquitetura e a escala de saída. Utiliza sempre as oito distribuições por cluster nó, conforme mostrado no diagrama para distribuído hash tabelas. O número de nós de cluster e, consequentemente, o número de distribuições, são determinados pelo número de nós de cluster que comprar para o aparelho. Por exemplo, se comprar oito nós de cluster, obtém 64 distribuições (8 cluster nós x 8 distribuições/nó). 

-   Armazém de dados SQL tem um número fixo de 60 Distribuições e um número de nós de cluster flexível. Os nós de cluster são implementados com Azure recursos computação e de armazenamento. Pode alterar o número de nós de cluster de acordo com a carga de trabalho do serviço de back-end e a capacidade de informática (DWUs) especificado para o armazenamento de dados. Quando o número de nós de cluster alterada, o número de distribuições por nó cluster também é alterada. 

### <a name="can-i-view-the-compute-nodes"></a>Pode ver os nós de cluster?

Cada nó cluster tem um ID de nó e está visível em vistas de sistema que pertencem aos armazém de dados SQL e Parallel Data Warehouse.  Pode ver o nó cluster procurando a coluna node_id na cujos nomes começam por sys.pdw_nodes as vistas do sistema. Para obter uma lista das vistas de sistema, consulte o artigo a [vista do sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Replicadas tabelas para armazém, paralelas dados 
  
Aplica-se ao: Parallel Data Warehouse

Além de utilizar tabelas distribuídas, Parallel Data Warehouse oferece uma opção para criar uma réplica tabelas. Uma *tabela de replicadas* é uma tabela que está armazenada na sua totalidade em cada nó cluster. Replicação de uma tabela remove a necessidade de transferir as suas linhas de tabela entre nós de cluster antes de utilizar a tabela numa associação ou agregação. Tabelas replicadas só estão viáveis com tabelas pequenas devido a armazenamento extra necessário para armazenar a tabela inteira em cada nó cluster.  
  
O diagrama seguinte mostra uma tabela de replicadas que está armazenada no cada nó cluster. A tabela de replicadas está armazenada em todos os discos atribuídos ao nó cluster. Esta estratégia de disco é implementada utilizando os grupos de ficheiros do SQL Server.  
  
![Tabela de replicada] (media/sql-data-warehouse-distributed-data/replicated-table.png "Tabela de replicada") 
  
## <a name="next-steps"></a>Próximos passos
  
Para utilizar tabelas distribuídas eficazmente, consulte [tabelas de Distributing no armazém de dados SQL](sql-data-warehouse-tables-distribute.md)  
  



  
