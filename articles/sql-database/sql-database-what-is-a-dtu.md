<properties
    pageTitle="Base de dados SQL: O que é um DTU? | Microsoft Azure"
    description="Noções sobre que Azure base de dados SQL unidade da transação é."
    keywords="Opções de base de dados, o desempenho da base de dados"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Explicar unidades da transação da base de dados (DTUs) e flexível da base de dados as unidades da transação (eDTUs)

Este artigo explica as unidades da transação da base de dados (DTUs) e flexível unidades da transação da base de dados (eDTUs) e o que acontece quando clicar na DTUs ou eDTUs máximo.  

## <a name="what-are-database-transaction-units-dtus"></a>O que são unidades da transação da base de dados (DTUs)

Um DTU é uma unidade de medida dos recursos que garante a estar disponível para uma base de dados do Azure SQL autónomo num nível de desempenho específicos dentro de uma [camada de serviços de base de dados autónomo](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Um DTU é uma medida mistura de dados e/s e da transação registo e/s um rácio determinada por uma carga de trabalho de referência OLTP concebida para ser típica da reais OLTP das cargas de trabalho, memória e CPU. Duplicar os DTUs, aumentando o nível de desempenho de uma base de dados equivale a duplicar o conjunto de recursos disponíveis para essa base de dados. Por exemplo, uma base de dados Premium P11 com 1750 DTUs fornece 350 x DTU mais calcular power que uma base de dados básico com 5 DTUs. Para compreender a metodologia de carga de trabalho de referência OLTP, utilizada para determinar a mistura DTU, consulte o artigo [Descrição geral de referência de base de dados SQL](sql-database-benchmark-overview.md).

![Introdução à base de dados SQL: individual DTUs de base de dados por camadas e nível](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Pode [Alterar camadas de serviços](sql-database-scale-up.md) em qualquer altura com indisponibilidade mínima para a sua aplicação (geralmente calcular a média de menos de quatro segundos). Para muitos e apps de empresas, ser capaz de criar bases de dados e fazer a marcação desempenho única base de dados para cima ou para baixo a pedido é suficiente, sobretudo se padrões de utilização são relativamente previsíveis. Mas, se tiver padrões de utilização inesperados,-pode torná-la disco rígido gerir custos e o seu modelo de negócio. Para este cenário, utilize um agrupamento de flexível com um determinado número de eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>O que são flexível unidades da transação da base de dados (eDTUs)

Um eDTU é uma unidade de medida do conjunto de recursos (DTUs) que pode ser partilhado entre um conjunto de bases de dados num servidor Azure SQL - designado por um [conjunto de flexível](sql-database-elastic-pool.png). Conjuntos de dados flexível fornecem uma solução rentável simple para gerir os objetivos do desempenho para várias bases de dados que tenham amplamente com diversas larguras e padrões de utilização inesperados. Consulte o artigo [flexível agrupamentos e camadas de serviço](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) para obter mais informações.

![Introdução à base de dados SQL: eDTUs pelos camadas e nível](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Um conjunto de dados é dado um determinado número de eDTUs, por um preço em conjunto. Dentro de agrupamento, bases de dados individuais estão atribuídos a flexibilidade para dimensionar automaticamente dentro de definir parâmetros. Em caso de carga muito carregado, uma base de dados pode consumir mais eDTUs para cumprir o pedido. Bases de dados em cargas simplificadas consumam menor e bases de dados em caso de carga sem consumam sem eDTUs. Recursos para todo o agrupamento em vez de para bases de dados simples de aprovisionamento simplifica tarefas de gestão de. Para além disso tem um orçamento para o conjunto de previsível.

EDTUs adicionais pode ser adicionada a um conjunto existente com sem tempo de inatividade da base de dados ou nenhum impacto nas bases de dados no conjunto de flexível. Da mesma forma, se já não forem necessárias extra eDTUs podem ser removidas de um conjunto existente em qualquer ponto no tempo. Pode adicionar ou subtrair bases de dados para o conjunto de dados. Se uma base de dados é seja previsível em-recorrendo recursos, movê-lo a.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Como posso determinar o número de DTUs necessário ao meu carga de trabalho?

Se estiver à procura de migrar uma existente no local ou do SQL Server máquina virtual carga de trabalho para a base de dados do SQL Azure, pode utilizar a [DTU Calculadora](http://dtucalculator.azurewebsites.net/) de aproximação do número de DTUs conforme necessário. Para uma carga de trabalho de base de dados do Azure SQL existente, pode utilizar [Conhecimentos aprofundados do desempenho de consulta de base de dados do SQL](sql-database-query-performance.md) para compreender o consumo de recursos base de dados (DTUs) para obter mais aprofundada visão como otimizar a sua carga de trabalho. Também pode utilizar [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) das DMV obter as informações de consumo de recursos para a última hora uma. Em alternativa, a vista de catálogo [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) também podem ser consultados para obter os mesmos dados para os últimos 14 dias, apesar da uma fidelidade inferior das médias de cinco minutos.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Como sei se pôde beneficiar a partir de um conjunto de recursos flexível?

Conjuntos de dados são adequados para um grande número de bases de dados com os padrões de utilização específico. Para uma determinada base de dados, este padrão é caracterizado por baixa utilização média com picos relativamente pouco frequente utilização. Base de dados SQL automaticamente avalia a utilização de recursos históricas bases de dados de um servidor de base de dados SQL existente e recomenda a configuração do conjunto adequadas no portal do Azure. Para obter mais informações, consulte o artigo [quando a um agrupamento de base de dados flexível deve ser utilizado?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>O que acontece quando posso acertos na minha DTUs máximos

Níveis de desempenho são calibrados e regulados para fornecer os recursos necessários para executar a sua carga de trabalho de base de dados até max dos limites permitidos para o seu nível de camada/desempenho serviço selecionado. Se a sua carga de trabalho está de atingir os limites de uma das limites de CPU/dados IO/registo IO, continuar a receber os recursos no máximo permitido nível, mas provavelmente ver um aumento latências das suas consultas. Estes limites não resultar em erros, mas preferir num abrandamento da carga de trabalho, a menos que o abrandamento torna-se por isso, extremas consultas começar a temporização. Se está a atingir limites máximos permitidos em simultâneo sessões/de pedidos de utilizador (threads de trabalho), será apresentada explícitos erros. Consulte o artigo [limites de recursos base de dados do SQL Azure](sql-database-resource-limits.md) para obter informações sobre o limite de recursos que não seja CPU, memória, dados e/s e registo da transação e/s.

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [camada de serviços](sql-database-service-tiers.md) para obter informações sobre a DTUs e eDTUs disponível para autónomo bases de dados e flexível conjuntos de dados.
- Consulte o artigo [limites de recursos base de dados do SQL Azure](sql-database-resource-limits.md) para obter informações sobre o limite de recursos que não seja CPU, memória, dados e/s e registo da transação e/s.
- Consulte o artigo [Conhecimentos aprofundados do desempenho de consulta de base de dados do SQL](sql-database-query-performance.md) para compreender o consumo (DTUs).
- Aceder a [Descrição geral de referência de base de dados SQL](sql-database-benchmark-overview.md) para compreender a metodologia de carga de trabalho de referência OLTP, utilizada para determinar a mistura DTU.