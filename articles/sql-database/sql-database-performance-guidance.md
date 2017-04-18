<properties
    pageTitle="Base de dados SQL Azure e o desempenho para bases de dados única | Microsoft Azure"
    description="Este artigo pode ajudar a determinar qual camada de serviços para escolher para a sua aplicação. Recomenda também formas para a aplicação para obter o máximo partido da base de dados do SQL Azure o gráfico."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Base de dados SQL Azure e o desempenho para bases de dados simples

Base de dados SQL Azure oferece três [camadas de serviços](sql-database-service-tiers.md): Basic, padrão e Premium. Cada camada de serviço estritamente isola os recursos que pode utilizar a base de dados do SQL e garantias desempenho previsível para esse nível de serviço. Neste artigo, oferecemos orientações que podem ajudar a escolher a camada para a sua aplicação de serviço. Vamos discutir também as formas que pode ajustar a sua aplicação para tirar o máximo partido da base de dados do SQL Azure.

>[AZURE.NOTE] Este artigo foca-se em desempenho seta de quatro pontas única bases de dados na base de dados do SQL Azure. Para uma orientação de desempenho relacionados com agrupamentos de base de dados flexível, consulte o artigo [preço e desempenho considerações para agrupamentos de base de dados flexível](sql-database-elastic-pool-guidance.md). No entanto, Note que pode aplicar muitas das recomendações optimização neste artigo para bases de dados num conjunto de dados da base de dados flexível e obter semelhantes vantagens de desempenho.

Estas são as três camadas de serviço de base de dados do Azure SQL que pode escolher a partir de (o desempenho é medido em unidades de débito de base de dados ou [DTUs](sql-database-what-is-a-dtu.md):

- **Básica**. O serviço básico camada ofertas bom desempenho previsibilidade para cada base de dados, a hora hora. Numa base de dados básico, recursos suficientes suportam bom desempenho numa base de dados pequenas que não tenha várias pedidos em simultâneo.
- **Padrão**. A camada de serviço padrão Deteta a barra para bases de dados que tenham vários pedidos em simultâneo, como as aplicações do grupo de trabalho e web e oferece previsibilidade desempenho melhorado. Quando escolher uma base de dados do serviço padrão de camadas, pode dimensionar a sua aplicação de base de dados com base no desempenho previsível, minuto ao longo do minuto.
- **Premium**. A camada de serviço Premium fornece previsível desempenho, segunda sobre em segundo lugar, para cada base de dados de Premium. Quando escolhe a camada de serviço Premium, pode dimensionar a sua aplicação de base de dados com base na carga de pico para essa base de dados. O plano remove casos em que desempenho variância pode causar pequenas consultas para demorar mais tempo do que o esperado no sensível a latência operações. Este modelo substancialmente pode simplificar os ciclos de validação de desenvolvimento e produto para as aplicações que precisa de efetuar fortes declarações sobre necessidades de recursos de pico, desvio de desempenho ou latência da consulta.

Em cada camada de serviço, defina o nível de desempenho, pelo que tem a flexibilidade de poder pagar apenas a capacidade de que precisar. Pode [Ajustar a capacidade de](sql-database-scale-up.md), para cima ou para baixo, como alterações de carga de trabalho. Por exemplo, se a sua carga de trabalho de base de dados é elevada durante a época compras anterior à escola, poderá aumentar o nível de desempenho para a base de dados para um período definido, Julho através de Setembro de. Pode reduzir quando termina o seu época pico. Pode minimizar a pagar por otimizar o seu ambiente de nuvem para a sazonalidade da sua empresa. Este modelo também funciona bem para ciclos de lançamento do produto de software. Uma equipa de teste poderá atribuir capacidade enquanto teste será executado e, em seguida, solte dessa capacidade quando concluir a testar. Num modelo de pedido capacidade, pode paga a capacidade à medida que precise da mesma e evitar dedicados recursos que raramente poderá utilizar as despesas.

## <a name="why-service-tiers"></a>Por que motivo camadas de serviço?

Apesar de cada carga de trabalho de base de dados pode ser, é o objetivo do camadas de serviço fornecer previsibilidade de desempenho a vários níveis de desempenho. Os clientes com os requisitos de recursos de base de dados em grande escala podem trabalhar num ambiente mais dedicado de computação.

### <a name="common-service-tier-use-cases"></a>Camada de serviços comuns casos de utilização

#### <a name="basic"></a>Básicas

- O **está a dar os primeiros passos com base de dados do SQL Azure**. Aplicações que se encontram no desenvolvimento de muitas vezes não precisam de níveis de alto desempenho. Bases de dados básicos são um ambiente ideal para o desenvolvimento de base de dados, num ponto do preço baixa.
- **Tiver uma base de dados com um único utilizador**. As aplicações que associar um único utilizador com uma base de dados, normalmente, não têm requisitos de simultaneidade do e desempenho alto. Estas aplicações são ideais para a camada de serviço básico.

#### <a name="standard"></a>Padrão

- A **sua base de dados tiver vários pedidos em simultâneo**. Aplicações de serviço mais do que um utilizador uma vez, normalmente, precisam de mais elevados níveis de desempenho. Por exemplo, os Web sites que obter tráfego moderado ou departamentais aplicações que requerem mais recursos são bons candidatos para a camada de serviço padrão.

#### <a name="premium"></a>Premium

A maioria dos casos de utilização do camada serviço Premium tem um ou mais dos seguintes características:

- **Pico alto carregar**. Numa aplicação que requer muitas CPU, a memória ou entrada/saída (e/s) para concluir as suas operações requer um nível de dedicada e de alto desempenho. Por exemplo, uma operação de base de dados conhecida consumir várias núcleos de CPU para uma data/hora adicional é um candidato para a camada de serviço Premium.
- **Muitos pedidos em simultâneo**. Algumas aplicações de base de dados serviço muitos pedidos em simultâneo, por exemplo, quando servir um Web site que tem um volume tráfego alta. Básicos e padrão camadas de serviços limitar o número de pedidos em simultâneo por base de dados. As aplicações que requerem mais ligações seriam necessário escolher um tamanho de reserva adequado para processar o número máximo de pedidos de conforme seja necessários.
- **Latência baixa**. Algumas aplicações tem de garantir uma resposta da base de dados em tempo mínimo. Se um procedimento armazenado específico chama-se como parte de uma operação de cliente mais amplo, poderá ter um requisito para ter um partir dessa chamada de retorno em milissegundos mais do que 20, 99% das vezes. Vantagens de camada de serviço Premium, certifique-se de que a capacidade de informática necessária está disponível para este tipo de aplicação.

O nível de serviço que precisa para a base de dados do SQL depende os requisitos de carga de pico para cada dimensão de recursos. Algumas aplicações de utilizam uma quantidade comum de um único recurso, mas têm requisitos significativos para outros recursos.

## <a name="service-tier-capabilities-and-limits"></a>Capacidades de camada de serviço e os limites
Cada nível de serviço camadas e o desempenho está associado com limites de diferentes e características de desempenho. Esta tabela descreve estas características para uma base de dados simples.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

As secções seguintes contêm mais informações sobre como ver utilize relacionadas com estes limites.

### <a name="maximum-in-memory-oltp-storage"></a>Armazenamento de máximo na memória OLTP

Pode utilizar a vista de **sys.dm_db_resource_stats** para monitorizar a utilização de armazenamento do Azure na memória. Para mais informações sobre como monitorizar, consulte o artigo [Monitor na memória OLTP armazenamento](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] Atualmente, processamento pré-visualização do (OLTP) de transações online Azure na memória são suportada apenas para bases de dados simples. Não pode utilizá-lo em bases de dados em agrupamentos de base de dados flexível.

### <a name="maximum-concurrent-requests"></a>Pedidos em simultâneo máximos

Para ver o número de pedidos em simultâneo, execute esta consulta Transact-SQL na sua base de dados do SQL:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Para analisar a carga de trabalho de uma base de dados do SQL Server no local, modificar esta consulta para filtrar na base de dados específico que pretende analisar. Por exemplo, se tiver uma base de dados no local com o nome minhabasededados, esta consulta Transact-SQL devolve a contagem de pedidos em simultâneo nessa base de dados:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Este é apenas um instantâneo num único ponto no tempo. Para obter uma melhor compreensão dos seus carga de trabalho e os requisitos de pedido em simultâneo, terá de recolher amostras muitos ao longo do tempo.

### <a name="maximum-concurrent-logins"></a>Máximos inícios de sessão em simultâneo

Pode analisar os seus padrões de utilizador e a aplicação para obter uma ideia da frequência dos inícios de sessão. Também pode executar cargas reais num ambiente de teste para se certificar de que não está a atingir este ou outros limites que discutimos neste artigo. Não existe uma única consulta ou a vista de gestão de dinâmica (das DMV) que pode mostrar-lhe em simultâneo conta de início de sessão ou o histórico de.

Se vários clientes utilizam a mesma cadeia de ligação, o serviço autentica cada início de sessão. Se 10 utilizadores em simultâneo ligarem a uma base de dados utilizando o mesmo nome de utilizador e palavra-passe, não existem seria 10 inícios de sessão em simultâneo. Este limite só se aplica a duração de início de sessão e autenticação. Se os mesmos 10 utilizadores ligarem à base de dados sequencialmente, o número de inícios de sessão em simultâneo nunca seria maior do que 1.

>[AZURE.NOTE] Atualmente, este limite não se aplica a bases de dados em agrupamentos de base de dados flexível.

### <a name="maximum-sessions"></a>Máximo de sessões

Para ver o número de sessões activas atuais, execute esta consulta Transact-SQL na sua base de dados do SQL:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Se está a analisar uma carga de trabalho do SQL Server no local, modificar a consulta focar-se numa base de dados específico. Esta consulta ajuda-o a determinar as necessidades de sessão possíveis para a base de dados se estiver a considerar movê-lo para a base de dados do SQL Azure.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Novamente, estas consultas devolvem uma contagem em qualquer altura. Se recolher amostras vários ao longo do tempo, terá a compreensão melhor da sua sessão de utilizar.

Para uma análise de base de dados SQL pode obter históricas estatísticas sobre sessões. Consultar **sys.resource_stats**e utilize a coluna **active_session_count** . Consulte a secção seguinte para obter mais informações sobre como utilizar esta vista.

## <a name="monitor-resource-use"></a>Monitorizar a utilização de recursos
Duas vistas podem ajudá-lo monitorizar a utilização de recursos para uma base de dados do SQL relativamente às sua camada de serviços:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Pode utilizar a vista de [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) cada base de dados do SQL. A vista de **sys.dm_db_resource_stats** mostra dados de utilização de recursos recentes em relação a camada de serviço. Média percentagens para CPU, dados e/s, registo escritas e memória são registadas cada 15 segundos e são mantidas para 1 hora.

Uma vez que esta vista fornece um olhar mais granular utilização de recursos, utilize **sys.dm_db_resource_stats** primeiro para uma análise de estado actual qualquer ou resolução de problemas. Por exemplo, esta consulta mostra a utilização de recursos média e máximo para a base de dados atual através de última hora:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Para outras consultas, consulte os exemplos [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats

A vista de [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) na base de dados **principal** tem informações adicionais que podem ajudá-lo monitorizar o desempenho da sua base de dados do SQL no seu nível de camadas e o desempenho do serviço específico. Os dados são recolhidos em 5 minutos e são mantidos durante cerca de 35 dias. Esta vista é útil para uma análise histórica longo prazo da forma como a base de dados do SQL utiliza recursos.

O gráfico seguinte mostra a utilização de recursos CPU para uma base de dados Premium com o nível de desempenho P2 para cada hora numa semana. Este gráfico começa na segunda-feira, mostra 5 dias de trabalho e, em seguida, mostra um fim de semana, quando muito menos acontece sobre a aplicação.

![Utilização de recursos de base de dados SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

A partir de dados, esta base de dados tem atualmente uma carga de CPU pico de apenas mais de 50 por cento utilização da CPU relativamente ao nível de desempenho P2 (meio-dia na Terça-feira). Se o factor dominante no perfil do recurso a aplicação não for CPU, em seguida, pode decidir que P2 é o nível de desempenho direita para garantir que a carga de trabalho sempre se adequa a. Se pretender uma aplicação para aumentar as ao longo do tempo, é uma boa ideia ter um intervalo de tempo de recursos adicionais para que a aplicação não nunca atingir o limite de nível de desempenho. Se aumentar o nível de desempenho, pode ajudar a evitar erros de cliente visíveis que poderão ocorrer quando uma base de dados não tem potência suficiente para processar pedidos de forma eficaz, sobretudo no sensível a latência ambientes. Um exemplo é uma base de dados compatível com uma aplicação que tintas páginas Web com base nos resultados de base de dados de chamadas.

Tenha em atenção que outros tipos de aplicação poderão interpretar o mesmo gráfico diferente. Por exemplo, se uma aplicação tenta a processar cada dia os dados de folhas de pagamentos e tem o mesmo gráfico, este tipo de modelo de "rotina" poderá fazê-ajustar a um nível de desempenho P1. O nível de desempenho P1 tem 100 DTUs comparadas comparados o 200 DTUs ao nível de desempenho de P2. O nível de desempenho P1 fornece metade o desempenho do nível de desempenho P2. Por isso, 50 por cento da utilização da CPU no P2 é igual a utilização da CPU de 100 por cento no P1. Se a aplicação não tiver tempos limite, poderá não interessa se uma tarefa leva-o até 2 horas ou 2.5 horas para concluir, se obtém processada hoje. Uma aplicação nesta categoria provavelmente pode utilizar um nível de desempenho P1. Pode tirar partido do facto de que não existem períodos de tempo durante o dia quando a utilização de recursos é inferior, para que qualquer "pico grande" poderá ajustar numa só os lavatórios colectivos mais tarde no final do dia. O nível de desempenho P1 poderá boa para esse tipo de aplicação (e guardar dinheiro), desde que podem concluir as tarefas de tempo de cada dia.

Expõe de base de dados SQL Azure consumida informação de recurso para cada base de dados ativo na vista **sys.resource_stats** da base de dados **principal** em cada servidor. Os dados na tabela são agregados para intervalos de 5 minutos. Com as camadas de serviço Basic, padrão e Premium, os dados podem demorar mais de 5 minutos seja apresentado na tabela, para que estes dados são mais útil para análise histórica em vez de análise de perto em tempo real. Consulta na vista de **sys.resource_stats** para ver o histórico de uma base de dados recente e para validar se a reserva de optar por entregue o desempenho pretendida quando for necessário.

>[AZURE.NOTE] Tem de estar ligado à base de dados **principal** do seu servidor de base de dados SQL lógico para consultar **sys.resource_stats** nos exemplos seguintes.

Este exemplo mostra como os dados nesta vista expostos:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![A vista de catálogo sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

O exemplo seguinte mostra-lhe formas diferentes que pode utilizar a vista de catálogo **sys.resource_stats** para obter informações sobre como a base de dados do SQL utiliza recursos:

1. Para ver recursos da semana passada Utilize para userdb1 a base de dados, pode executar esta consulta:

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. Para avaliar como bem sua carga de trabalho se ajuste o nível de desempenho, tem de desagregar em cada aspecto métricas recurso: CPU, lê, escritas, número de colaboradores na área e número de sessões. Eis uma consulta revista utilizando **sys.resource_stats** para comunicar os valores de média e máximo de métricas estes recursos:

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Estas informações sobre os valores média e máximo de cada métrica de recursos, pode avaliar como bem sua carga de trabalho se adequa o nível de desempenho que escolheu. Normalmente, os valores médios de **sys.resource_stats** dar-lhe uma boa do plano base para utilizar contra o tamanho de destino. Deverá ser joystick sua medida principal. Por exemplo, poderá estar a utilizar a camada de serviço padrão com S2 nível de desempenho. A média utilizar percentagens para lê CPU e e/s e escritas são descritos a seguir 40 por cento, o número médio de trabalhadores for inferior a 50 e o número médio de sessões for inferior a 200. Sua carga de trabalho poderá ajustá-las para o nível de desempenho S1. É fácil ver se a base de dados se ajusta em limites de trabalho e de sessão. Para ver se se ajusta uma base de dados para um nível de desempenho inferior no que diz respeito CPU, lê e escritas, dividir o número DTU do nível de desempenho inferior pelo número DTU do seu nível de desempenho atual e, em seguida, multiplique o resultado por 100:

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 * *

    O resultado é a diferença de desempenho relativa entre os níveis de duas desempenho em percentagem. Se a utilização de recursos não excede este valor, sua carga de trabalho poderá ajustá-las para o nível de desempenho inferior. No entanto, tem de ver todos os intervalos de valores de utilização de recursos e determinar, por percentagem, com que frequência sua carga de trabalho de base de dados seria ajustam o nível de desempenho inferior. A seguinte consulta exporta a percentagem ajustar por dimensão de recurso, com base no limiar de 40%, que são calculadas neste exemplo:

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Com base no seu objectivo de nível de serviço de base de dados (SLO), pode decidir se sua carga de trabalho se adequa o nível de desempenho inferior. Se a sua carga de trabalho para a base de dados SLO for 99,9 por cento e a consulta precedente devolve valores maior 99,9 por cento para todas as dimensões de três recurso, a sua carga de trabalho provável que cabem para o nível de desempenho inferior.

    Consultar a percentagem de ajustar também dá-lhe visão tiver que mover para o próximo nível de desempenho mais elevado para cumprir o seu SLO. Por exemplo, userdb1 mostra a utilização da CPU seguinte semana passada:

  	| Percentagem de CPU média | Percentagem de CPU máxima |
  	|---|---|
  	| 24.5 | 100,00 |

    É a média CPU sobre um trimestre do limite do nível de desempenho, teria se ajustam bem o nível de desempenho da base de dados. No entanto, o valor máximo mostra que a base de dados atinge o limite do nível de desempenho. É necessário deslocar-se para o próximo nível superior do desempenho? Tem de observe como número de vezes atinge a carga de trabalho 100 por cento e, em seguida, compará-la para sua carga de trabalho para a base de dados SLO.

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Se esta consulta devolve um valor menor que 99,9 por cento para qualquer uma das dimensões três recurso, considere quer mover para o próximo nível superior do desempenho ou utilizar técnicas de aplicação de sintonização para reduzir a carga da base de dados do SQL.

4. Este exercício considera também a aumentar a carga de trabalho prevista no futuro.

## <a name="tune-your-application"></a>Ajustar a sua aplicação

No SQL Server do tradicional no local, o processo de planeamento de capacidade inicial frequentemente está separado do processo de execução de uma aplicação de produção. Licenças de hardware e produto sejam compradas pela primeira vez e Otimização do desempenho é concluída mais tarde. Ao utilizar a base de dados do SQL Azure, é uma boa ideia interweave o processo de execução de uma aplicação e Otimização do mesmo. Com o modelo de pagamentos para capacidade a pedido, pode otimizar a aplicação para utilizar os recursos mínimos necessários agora, em vez de overprovisioning em hardware com base em fundamentadas dos planos de futuro crescimento para uma aplicação, que frequentemente estão incorretas. Alguns clientes poderão optar por não sintonizar uma aplicação e optar por overprovision recursos de hardware. Esta abordagem poderá ser uma boa ideia se não quiser alterar uma aplicação de chave durante um período de ocupado. No entanto, uma aplicação de sintonização pode minimizar requisitos de recursos e contas a pagar mensal inferior ao utilizar as camadas do serviço na base de dados do SQL Azure.

### <a name="application-characteristics"></a>Características da aplicação

Apesar de camadas de serviços de base de dados do Azure SQL foram concebidas para melhorar a estabilidade de desempenho e previsibilidade para uma aplicação, algumas das melhores práticas podem ajudar a otimizar a aplicação para melhor tirar partido dos recursos a um nível de desempenho. Apesar de muitas aplicações têm significativos ganhos de desempenho simplesmente ao mudar para um nível de desempenho superior ou camada de serviço, algumas aplicações precisam de otimização adicional para beneficiar a partir de um nível superior do serviço. Para um melhor desempenho, considere optimização aplicações adicionais para as aplicações que tem as seguintes características:

- **Aplicações que tenham um desempenho lento devido a comportamento "chatty"**. Aplicações chatty certifique operações de acesso de excesso de dados que são sensíveis a latência da rede. Poderá ter de modificar estes tipos de aplicações para reduzir o número de acesso de operações de dados à base de dados SQL. Por exemplo, pode melhorar o desempenho da aplicação ao utilizar técnicas como lotes consultas ad-hoc ou mover as consultas para procedimentos armazenados. Para mais informações, consulte o artigo [lote consultas](#batch-queries).
- **Bases de dados com uma carga de trabalho intensa que não sejam suportadas por um único computador inteiro**. Bases de dados que excedam os recursos do nível de desempenho Premium mais alto poderão beneficiar escalar para fora a carga de trabalho. Para mais informações, consulte o artigo [sharding entre bases de dados](#cross-database-sharding) e a [partições funcionais](#functional-partitioning).
- **Aplicações que possuem consultas reduzidas**. As aplicações, especialmente aqueles na camada de acesso a dados, que tem mal optimizada consultas não poderão beneficiar a partir de um nível superior do desempenho. Isto inclui consultas que possuem uma cláusula WHERE, tem em falta índices ou tem Desatualizadas estatísticas. Estas aplicações beneficiam técnicas de otimização do desempenho da consulta padrão. Para mais informações, consulte o artigo [índices em falta](#missing-indexes) e a [consulta de sintonização e dicas](#query-tuning-and-hinting).
- **Acedem a aplicações que possuem dados reduzidos estrutura**. As aplicações que têm problemas de simultaneidade do access dados inerentes, deadlocking por exemplo, não poderão beneficiar a partir de um nível superior do desempenho. Considere a redução de viagens round contra a base de dados do SQL Azure por colocação em cache dos dados do lado do cliente com o serviço do Azure colocação em cache ou outra tecnologia de colocação em cache. Consulte o artigo [Colocação em cache de camadas de aplicação](#application-tier-caching).

## <a name="tuning-techniques"></a>Otimização do técnicas
Nesta secção, vamos ver algumas técnicas que pode utilizar para o gráfico base de dados do SQL Azure para obter o melhor desempenho para a sua aplicação e executá-la ao nível do desempenho possível mais baixo. Algumas destas técnicas correspondem tradicional do SQL Server melhores práticas de sintonização, mas outras pessoas são específicas a base de dados do SQL Azure. Em alguns casos, pode examinar os recursos consumidos para uma base de dados encontrar áreas para otimizar e expandir tradicionais do SQL Server técnicas para trabalhar numa base de dados do SQL Azure ainda mais.

### <a name="azure-portal-tools"></a>Ferramentas de portais Azure
Encontrará duas ferramentas no portal do Azure pode ajudá-lo analisar e corrigir problemas de desempenho com a sua base de dados do SQL:

- [Conhecimentos aprofundados de desempenho de consulta](sql-database-query-performance.md)
- [Advisor de base de dados SQL](sql-database-advisor.md)

Portal do Azure tem mais informações sobre as duas situações estas ferramentas e como utilizá-las. A forma eficiente diagnosticar e corrigir problemas, recomendamos que tenta as ferramentas no portal do Azure pela primeira vez. Recomendamos que utilize o manual de sintonização abordagens discutimos em seguida, em falta índices e optimização da consulta, em casos especiais.

### <a name="missing-indexes"></a>Índices em falta
Um problema comuns no desempenho da base de dados OLTP está relacionada com a estrutura de base de dados física. Muitas vezes, esquemas de base de dados são concebidos e enviados sem teste de escala (quer numa carga ou volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável numa escala mais pequena mas diminuir substancialmente em volumes de dados de nível de produção. A fonte mais comuns deste problema é a falta de índices adequados para satisfazer filtros ou outras restrições numa consulta. Muitas vezes, em falta índices manifestos como uma tabela pesquisa quando foi suficiente atingir um índice remissivo.

Neste exemplo, o plano de consulta selecionada utiliza uma pesquisa quando um atingir seria suficiente:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![Um plano de consulta com índices em falta](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Base de dados SQL Azure pode ajudá-lo a localizar e corrigir comuns em falta indexar condições. DMVs que foram criadas para a base de dados do SQL Azure observe compilações de consulta em que um índice remissivo significativamente iria reduzir o custo estimado para executar uma consulta. Durante a execução de consulta, base de dados SQL regista o número de vezes que cada plano de consulta é executado e controla o intervalo estimado entre o plano de execução de consulta e a imaginado onde existia desse índice. Pode utilizar estes DMVs rapidamente adivinhar alterações para a estrutura da base de dados física poderão melhorar o custo de carga de trabalho geral para uma base de dados e a sua carga de trabalho real.

Pode utilizar esta consulta para avaliar índices em falta potenciais:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Neste exemplo, a consulta resultou nesta sugestão:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Após ter sido criada, esse mesmo instrução SELECT escolhe um plano diferente, que utiliza uma atingir em vez de uma pesquisa e, em seguida, executa o plano de forma mais eficiente:

![Um plano de consulta com corrigido índices](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

O conhecimentos aprofundados chave são que a capacidade de e/s de um sistema de produto importante partilhada, é limitada mais do que uma máquina de servidor dedicado. Existe um prémio no minimizar e/s desnecessários para tirar o máximo partido do sistema no DTU de cada nível de desempenho das camadas de serviço de base de dados do Azure SQL. Estrutura de base de dados adequado física escolhas podem melhorar significativamente a latência para consultas individuais, a melhorar o débito de pedidos em simultâneo geridos por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para mais informações sobre o índice em falta DMVs, consulte o artigo [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Consulta de sintonização e dicas
O otimizador de consulta numa base de dados do Azure SQL é semelhante ao otimizador de consulta do SQL Server tradicional. A maior parte das melhores práticas para consultas de sintonização e compreender o raciocínio limitações de modelo para o otimizador de consulta também se aplicam a base de dados do SQL Azure. Se sintonizar consultas de base de dados do SQL Azure, poderá receber o benefício adicional de como reduzir recursos agregados exigências. A aplicação poderá conseguir executar um custo inferior ou superior um equivalente untuned porque pode executar de um nível de desempenho inferior.

Um exemplo que é comuns no SQL Server e que também se aplica à base de dados do SQL Azure é como o otimizador de consulta "sniffs" parâmetros. Durante a compilação, o otimizador de consulta avalia o valor atual de um parâmetro para determinar se o que pode gerar um plano de consulta mais ideal. Apesar desta estratégia com frequência pode levar a um plano de consulta que é significativamente mais rápido que um plano compilado sem valores de parâmetros conhecidos, atualmente funciona imperfectly ambos no SQL Server e na base de dados do SQL Azure. Por vezes, o parâmetro não é inalado e, por vezes, o parâmetro é inalado mas o plano de gerado é reduzido para o conjunto completo de valores de parâmetros numa carga de trabalho. A Microsoft inclui as sugestões de consulta (diretivas do) para que possa especificar mais deliberadamente intenção e substituir o comportamento predefinido da consulta de parâmetros. Muitas vezes, se utilizar sugestões, é possível corrigir casos em que o comportamento predefinido de SQL Server ou base de dados do SQL Azure é imperfeito para uma carga de trabalho do cliente específico.

O exemplo seguinte demonstra como o processador de consultas pode gerar um plano que é reduzido, tanto para o desempenho e requisitos de recursos. Este exemplo mostra também que se utilizar uma sugestão de consulta, pode reduzir consulta executar o tempo e requisitos de recursos para a base de dados do SQL:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

O código de configuração cria uma tabela que tem distorcida distribuição dos dados. O plano de consulta ideal difere que parâmetro está seleccionado. Infelizmente, o plano para o comportamento de colocação em cache não sempre recompilar a consulta baseada o valor do parâmetro mais comuns. Por isso, é possível para um plano ser em cache e utilizada para muitos valores, mesmo quando um plano diferente poderá ser uma boa opção de plano de em média reduzido. Em seguida, o plano de consulta cria dois procedimentos armazenados são idênticos, exceto que um tem uma sugestão de consulta especial.

**Exemplo, parte 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Exemplo, parte 2**

(Recomendamos que aguarde pelo menos de 10 minutos antes de começar a parte 2 de exemplo, para que os resultados são distintos dos dados de telemetria resultante.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Cada parte deste exemplo tenta executar uma instrução insert parametrizadas 1000 vezes (para gerar um carregamento suficiente para utilizar como um conjunto de dados de teste). Quando executa-procedimentos armazenados, o processador de consultas examina o valor do parâmetro que lhe é transmitido o procedimento durante a sua primeira compilação (parâmetro "consulta"). Processador coloca em cache o plano resultante e utiliza-exe posterior, mesmo se o valor do parâmetro é diferente. O plano ideal não pode ser utilizado em todos os casos. Por vezes necessário para o orientar o otimizador de para selecionar um plano que é melhor para as maiúsculas/minúsculas média em vez das maiúsculas/minúsculas específica a partir do quando a consulta pela primeira vez foi compilada. Neste exemplo, o plano inicial gera um plano de "pesquisa" lê todas as linhas para localizar a cada valor que corresponde ao parâmetro da:

![Otimização ao utilizar um plano de pesquisa de consulta](./media/sql-database-performance-guidance/query_tuning_1.png)

Porque é executado o procedimento, utilizando o valor 1, o plano resultante foi ideal para o valor 1, mas foi reduzido para todos os outros valores na tabela. O resultado provável que não são aquilo iria pretende se fosse selecionar cada plano aleatoriamente, porque o plano de desempenho mais lento e utiliza mais recursos.

Se executar o teste com `SET STATISTICS IO` definido para `ON`, o trabalho de pesquisa lógicos neste exemplo é feito nos bastidores. Pode ver que não existem 1.148 lê efetuado pelo plano (que é ineficaz, se for o caso média devolver apenas uma linha):

![Consulta de sintonização utilizando uma verificação de lógica](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo utiliza uma sugestão de consulta para onde está o otimizador de utilizar um valor específico durante o processo de compilação. Neste caso, força-o processador de consulta para ignorar o valor que lhe é transmitido como o parâmetro, e, em vez disso, a assumir `UNKNOWN`. Refere-se um valor que tem a frequência média na tabela (ignorando distorção). O plano resultante é um plano de atingir com base no que é mais rápido e utiliza menos recursos, em média, o plano na parte 1 neste exemplo:

![Consulta de sintonização utilizando uma sugestão de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Pode ver o efeito na tabela **sys.resource_stats** (existe um atraso de tempo que executar o teste e quando os dados preenchem a tabela). Para este exemplo, parte 1 executada durante a janela de tempo 22:25:00 e parte 2 executada em 22:35:00. Nota Se a janela de tempo anterior utilizadas mais recursos essa janela de tempo que a posterior (devido a melhoramentos da eficiência plano).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Consulta de sintonização resultados de exemplo](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Apesar do volume neste exemplo é intencionalmente pequeno, pode ser substancial, especialmente em bases de dados maiores o efeito dos parâmetros reduzidos. A diferença, em representarem casos, pode ser entre segundos casos rápida e as horas de casos lentas.

Pode examinar **sys.resource_stats** para determinar se o recurso para um teste utiliza mais ou menos recursos que outro teste. Quando comparar dados, separe a temporização dos testes para que não estiverem na mesma janela na vista **sys.resource_stats** 5 minutos. O objetivo do exercício é para minimizar a quantidade total de recursos utilizados e não para minimizar os recursos de pico. Em geral, otimizar um elemento de código para latência também reduz o consumo de recursos. Certifique-se de que as alterações que efetuar à aplicação são necessárias e que as alterações negativa não afetam a experiência do cliente para alguém que possa estar a utilizar as sugestões de consulta na aplicação.

Se uma carga de trabalho tiver um conjunto de consultas de repetição, geralmente faz sentido para capturar e validar optimização das opções de plano porque orienta a unidade de tamanho de recurso mínimo necessária para a base de dados do anfitrião. Depois de validar, ocasionalmente reexamina os planos para o ajudar a certificar-se de que não tenham degradado. Pode obter mais informações sobre [as sugestões de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Sharding entre bases de dados
Uma vez que a base de dados do SQL Azure é executada no hardware do produto importante, os limites de capacidade para uma base de dados única são inferiores para uma instalação do SQL Server tradicional no local. Alguns clientes utilizam técnicas de sharding distribuídas operações de base de dados através de várias bases de dados quando as operações não caber dentro dos limites de uma base de dados simples base de dados do SQL Azure. A maioria dos clientes que utilizam técnicas sharding base de dados do SQL Azure dividir os respetivos dados numa única dimensão através de várias bases de dados. Esta abordagem, terá de compreender que aplicações OLTP executam com frequência transações que se apliquem a apenas uma linha ou num pequeno grupo de linhas no esquema.

>[AZURE.NOTE] Base de dados SQL agora fornece uma biblioteca para ajudá-lo a sharding. Para mais informações, consulte o artigo [Descrição geral de biblioteca de cliente flexível da base de dados](sql-database-elastic-database-client-library.md).

Por exemplo, se a uma base de dados tiver o nome do cliente, ordem e detalhes da encomenda (como exemplo tradicional base de dados Adamastor que vem incluído com o SQL Server), pode dividir estes dados em várias bases de dados ao agrupar um cliente com a ordem relacionado e informações de detalhes da encomenda. Pode garantir que os dados do cliente permanecem na base de dados único. A aplicação seria dividir diferentes clientes através de bases de dados, de forma eficaz propagação a carga entre várias bases de dados. Com sharding, clientes não só podem evitar o limite de tamanho máximo da base de dados, mas base de dados do SQL Azure também pode processar das cargas de trabalho são significativamente maiores do que os limites dos níveis de desempenho diferentes, desde que cada base de dados individual se adequa a sua DTU.

Apesar de sharding de base de dados não reduzir a capacidade de agregação de recursos para obter uma solução, é altamente eficaz a apoiar muito grandes soluções que são propagadas sobre várias bases de dados. Pode executar cada base de dados de um nível de desempenho diferentes para suportar muito grandes, "eficaz" bases de dados com os requisitos de recursos alta.

### <a name="functional-partitioning"></a>Criar a partições funcional
Utilizadores do SQL Server frequentemente combinam várias funções numa única base de dados. Por exemplo, se uma aplicação tiver lógica para gerir inventário de um arquivo, essa base de dados poderá ter lógica associada ao inventário, encomendas de compra, procedimentos armazenados e vistas ou não indexados ocorrência que gerir relatórios de fim do mês de controlo. Esta técnica, é mais fácil administrar a base de dados para operações como cópia de segurança, mas também requer que dimensionar o hardware para processar a carga de pico através de todas as funções de uma aplicação.

Se utilizar uma arquitetura de saída de escala de base de dados do SQL Azure, é uma boa ideia para dividir diferentes funções de uma aplicação para bases de dados diferentes. Ao utilizar esta técnica, cada aplicação escalas forma independente. Tal como uma aplicação torna-se os (e a carga da base de dados aumenta), o administrador pode escolher os níveis de cada função performance independente na aplicação. No limite, com esta arquitectura, uma aplicação pode ser maior do que uma máquina de produto importante única pode processar porque a carga for distribuída através de múltiplos computadores.

### <a name="batch-queries"></a>Consultas de batch
Para as aplicações que aceder aos dados utilizando o volume de alta, frequente ad hoc consultar, um substancial período de tempo de resposta é gasto em comunicação de rede entre a camada de aplicação e a camada de base de dados do Azure SQL. Mesmo quando a aplicação e a base de dados do SQL Azure são no Centro de dados do mesmo, a latência de rede entre os dois poderá estar ampliada por um grande número de dados operações de acesso. Para reduzir a rede para arredondar turismo para as operações de acesso de dados, considere utilizar a opção para quer batch as consultas ad hoc ou para compilá-los procedimentos armazenados como. Se o lote as consultas ad hoc, pode enviar várias consultas como cada lote grande numa viagem simples base de dados do SQL Azure. Se compilar consultas ad hoc num procedimento armazenado, é alcançar o mesmo resultado como se batch-los. Utilizar um procedimento armazenado também dá-lhe o benefício de aumentar as hipóteses de colocação em cache os planos de consulta numa base de dados do Azure SQL para que possa utilizar o procedimento armazenado novamente.

Algumas aplicações são com muitos escrita. Por vezes, pode reduzir a carga e/s total numa base de dados ponderando como batch escritas em conjunto. Muitas vezes, este é tão simple como utilizando transações explícitas em vez das transações de consolidação automática no procedimentos armazenados e lotes ad hoc. Para uma avaliação da técnicas diferentes que pode utilizar, consulte o artigo [Batching técnicas para aplicações de base de dados SQL Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimentar o seu próprio carga de trabalho para encontrar o modelo certo para lotes. Certifique-se de que compreender que um modelo pode ter garantias consistência transaccional ligeiramente diferente. Localizar a carga de trabalho à direita que minimiza a utilização de recursos requer localizar a combinação à direita de consistência e desempenho compromissos.

### <a name="application-tier-caching"></a>Colocação em cache de camadas de aplicação
Algumas aplicações de base de dados têm leitura grossa cargas de trabalho. Colocação em cache de camadas poderá reduza a carga da base de dados e potencialmente poderá reduzir o nível de desempenho necessário para suportar a uma base de dados ao utilizar a base de dados do SQL Azure. Com o [Azure Redis Cache](https://azure.microsoft.com/services/cache/), se tiver uma carga de trabalho grossa de leitura, pode ler os dados de uma vez (ou talvez uma vez por máquina de camada de aplicação, dependendo de como está configurado) e, em seguida, armazenar esses dados fora da sua base de dados do SQL. Esta é uma forma para reduzir a carga de base de dados (CPU e leitura e/s), mas não há um efeito de consistência transaccional porque os dados serem lidos da cache de poderão ser dessincronizados com os dados na base de dados. Apesar de muitas aplicações algum nível de inconsistência for aceitável, que não é verdade para todas as cargas de trabalho. Totalmente deve compreender qualquer requisitos da aplicação antes de implementar uma estratégia de cache camada de aplicação.

## <a name="next-steps"></a>Próximos passos

- Para mais informações sobre camadas de serviços, consulte o artigo [Opções de base de dados SQL e desempenho](sql-database-service-tiers.md)
- Para obter mais informações acerca de agrupamentos de base de dados flexível, consulte o artigo [o que é um agrupamento de base de dados flexível Azure?](sql-database-elastic-pool.md)
- Para obter informações sobre o desempenho e agrupamentos de base de dados flexível, consulte o artigo [quando a considerar um agrupamento de base de dados flexível](sql-database-elastic-pool-guidance.md)
