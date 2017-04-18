<properties
   pageTitle="Gerir power cluster no armazém de dados do SQL Azure (descrição geral) | Microsoft Azure"
   description="Escala de desempenho saída funcionalidades no armazém de dados do SQL Azure. Dimensionar saída, ajustando DWUs ou interromper e retomar cluster recursos poupar nos custos."
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
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gerir power cluster no armazém de dados do SQL Azure (descrição geral)

> [AZURE.SELECTOR]
- [Descrição geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

A arquitetura do armazém de dados SQL separa armazenamento e cluster, permitindo que cada um deles dimensionar de forma independente. Como resultado, é possível dimensionar desempenho ao guardar custos ao pagar apenas para um desempenho quando precisar dele. 

Esta descrição geral descreve as seguintes capacidades de escala de saída do desempenho do armazém de dados do SQL e fornece-recomendações sobre como e quando utilizá-los. 

- Escala calcular power, ajustando [unidades (DWUs) do armazém de dados][]
- Interromper ou retomar cluster de recursos

<a name="scale-performance-bk"></a>

## <a name="scale-performance"></a>Desempenho de escala

No armazém de dados do SQL, pode rapidamente Dimensionar desempenho saída ou voltar ao crescentes ou decrescentes recursos de cluster de largura de banda e/s, memória e CPU. Para dimensionar o desempenho, tudo o que precisa de fazer é ajustar o número de [unidades (DWUs) do armazém de dados][] que o armazém de dados SQL atribui a sua base de dados. Armazém de dados SQL rapidamente faz com que a alteração e alças de todas as alterações subjacentes ao software ou hardware.

Desaparece são os dias onde precisa de pesquisar que tipo de processadores, quantidade memória ou que tipo de armazenamento tem de ter o desempenho excelente no seu armazém de dados. Ao colocar o seu armazém de dados na nuvem, já não tem de lidar com problemas de hardware feixe. Em vez disso, armazém de dados SQL pede-lhe esta pergunta: como a mesma rapidez com que pretende analisar os seus dados? 

### <a name="how-do-i-scale-performance"></a>Como posso dimensionar o desempenho?

Para elastically aumentar ou diminuir o seu cluster power, basta altere a definição de [unidades (DWUs) do armazém de dados][] para a base de dados. Desempenho aumentará linear à medida que adiciona DWU mais.  Níveis superiores DWU, tem de adicionar mais de 100 DWUs para Repare uma melhoria significativa no desempenho. Para ajudar a seleccionar saltos significativos no DWUs, oferecemos os níveis DWU que irão dar-os melhores resultados.
 
Para ajustar DWUs, pode utilizar qualquer um dos seguintes métodos individuais.

- [Escala calcular power com o Azure portal][]
- [Escala calcular power com PowerShell][]
- [Energia de cluster de escala com os REST APIs][]
- [Escala calcular power com TSQL][]

### <a name="how-many-dwus-should-i-use"></a>Quantos DWUs devo utilizar?
 
Desempenho no armazém de dados do SQL escalas linear e alterar a partir de um cluster escala para outra (Apresentamos a partir de 100 DWUs para 2000 DWUs) acontece em segundos. Isto dá-lhe a flexibilidade para experimentar as diferentes definições DWU até determinar o melhor ajuste do seu cenário.

Para compreender o que é o seu valor DWU ideal, experimente dimensionamento cima e para baixo e executar consultas de alguns depois de carregar os seus dados. Uma vez que dimensionamento rápida, que pode experimentar um número de níveis diferentes de desempenho numa hora ou menos. Tenha em atenção, que armazém de dados SQL foi concebido para processar grandes quantidades de dados e para ver as suas capacidades verdadeiras para escalar, especialmente nas escalas maiores que oferecemos, irá querer utilizar um conjunto de dados grande que abordagens ou excede 1 TB.

Recomendações para localizar o melhor DWU para sua carga de trabalho:

1. Para um armazém de dados em desenvolvimento, comece por selecionar um pequeno número de DWUs.  Um bom ponto de partida é DW400 ou DW200.
2. Monitorizar o desempenho da aplicação, observar que o número de DWUs seleccionado em comparação com o desempenho que é observar.
3. Determine a quantidade desempenho mais rápido ou mais lento deve ser por si alcançar o nível de um desempenho ideal para os seus requisitos de por partindo do princípio de escala linear.
4. Aumentar ou diminuir o número de DWUs proporcionalmente como muito mais rápido ou mais lento pretende que a sua carga de trabalho para executar. O serviço será responder rapidamente e ajustar os recursos de cluster para cumprir os requisitos de DWU novos.
5. Continue a fazer ajustes até chegar um nível de um desempenho ideal para os seus requisitos empresariais.

### <a name="when-should-i-scale-dwus"></a>Quando posso dimensionar os DWUs?

Quando precisar de resultados mais rapidamente, aumentar a sua DWUs e pagar para obter um desempenho maior.  Quando precisar de menos calcular power, diminuir os DWUs e pagar apenas para o que precisa. 

Recomendações para quando dimensionar DWUs:

1. Se a aplicação tiver uma carga de trabalho flutuante, escala DWU níveis para cima ou para baixo para acomodar picos e pontos de baixos. Por exemplo, se a sua carga de trabalho picos normalmente no fim do mês, planeia adicionar mais DWUs durante a esses dias de pico, em seguida, dimensionar para baixo, uma vez período de pico.
2. Antes de executar uma operação de carregamento ou transformação de dados muito carregado, dimensione o DWUs para que os dados estiverem disponíveis mais rapidamente.

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cluster de colocar em pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para interromper uma base de dados, utilize qualquer um dos seguintes métodos individuais.

- [Colocar em pausa cluster com o Azure portal][]
- [Colocar em pausa cluster com o PowerShell][]
- [Colocar em pausa cluster com REST APIs][]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cluster de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para retomar a uma base de dados, utilize qualquer um dos seguintes métodos individuais.

- [Cluster de currículo com o Azure portal][]
- [Cluster de currículo com PowerShell][]
- [Cluster de currículo com REST APIs][]

## <a name="permissions"></a>Permissões

Dimensionamento da base de dados necessitam as permissões descritas [ALTER DATABASE][].  Interromper e retomar irão necessitar a permissão de [SQL DB contribuinte][] , especificamente Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximos passos
Consulte os artigos seguintes para ajudá-lo a compreender alguns conceitos de chave de desempenho adicionais:

- [Gestão de carga de trabalho e de simultaneidade do][]
- [Descrição geral de estrutura de tabela][]
- [Distribuição de tabela][]
- [Tabela de indexação][]
- [Tabela de partição][]
- [Estatísticas da tabela][]
- [Melhores práticas][]

<!--Image reference-->

<!--Article references-->
[unidades de armazém de dados (DWUs)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Escala calcular power com o Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Escala calcular power com PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Energia de cluster de escala com os REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Escala calcular power com TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Colocar em pausa cluster com o Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Colocar em pausa cluster com o PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Colocar em pausa cluster com REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Cluster de currículo com o Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Cluster de currículo com PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Cluster de currículo com REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Gestão de carga de trabalho e de simultaneidade do]: ./sql-data-warehouse-develop-concurrency.md
[Descrição geral de estrutura de tabela]: ./sql-data-warehouse-tables-overview.md
[Distribuição de tabela]: ./sql-data-warehouse-tables-distribute.md
[Tabela de indexação]: ./sql-data-warehouse-tables-index.md
[Tabela de partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas da tabela]: ./sql-data-warehouse-tables-statistics.md
[Melhores práticas]: ./sql-data-warehouse-best-practices.md 
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB contribuinte]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTERAR A BASE DE DADOS]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
