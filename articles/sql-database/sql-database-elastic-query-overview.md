<properties
    pageTitle="Azure descrição geral do consulta de base de dados flexível base de dados SQL | Microsoft Azure"
    description="Descrição geral da funcionalidade flexível consulta"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Azure SQL base de dados da base de dados flexível descrição geral das consultas (pré-visualização)

A funcionalidade de consulta de base de dados flexível (na pré-visualização) permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados na base de dados de SQL Azure (SQLDB). Permite-lhe executar consultas de base de dados de cruz para aceder a tabelas remotas e para se ligar a Microsoft e terceiros ferramentas (Excel, obter, quadro, etc.) para consulta em camadas de dados com várias bases de dados. Utilizar esta funcionalidade, pode dimensionar saída consultas para camadas de dados grandes na base de dados SQL e visualizar os resultados em relatórios do business intelligence (BI).

## <a name="documentation"></a>Documentação

* [Começar a trabalhar com consultas de base de dados de cruz](sql-database-elastic-query-getting-started-vertical.md)
* [Comunicar através de bases de dados de saída dimensionada nuvem](sql-database-elastic-query-getting-started.md)
* [Consulta em nuvem sharded bases de dados (horizontalmente divididos)](sql-database-elastic-query-horizontal-partitioning.md)
* [Consulta em bases de dados de nuvem com diferentes esquemas (verticalmente divididos)](sql-database-elastic-query-vertical-partitioning.md)
* [SP\_executar \_remoto](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>Porquê utilizar consultas flexível?

**Base de dados Azure SQL**

Consulta em bases de dados do Azure SQL completamente na T-SQL. Isto permite só de leitura consultar das bases de dados remotos. Este procedimento fornece uma opção para os clientes do SQL Server no local atuais migrar aplicações utilizando nomes de três e quatro parcial ou servidor ligado à base de dados do SQL.

**Disponível na camada padrão** Consulta flexível é suportada na camada desempenho padrão para além da camada de desempenho Premium. Consulte a secção sobre limitações de pré-visualização abaixo das limitações de desempenho para camadas de desempenho inferiores.

**Notificações push para bases de dados remotos**

Consultas flexível agora podem transmitir parâmetros de SQL para as bases de dados remotos execução.

**Execução do procedimento armazenado**

Executar o procedimento armazenado remoto chamadas ou utilizar as funções de remoto [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714).

**Flexibilidade**

Tabelas externas com o query flexível agora podem referir-se para tabelas remotas com um esquema diferente ou o nome da tabela.

## <a name="elastic-database-query-scenarios"></a>Cenários de consulta de base de dados flexível

O objetivo é facilitar a consultar cenários onde várias bases de dados contribuam linhas num único resultado geral. Quer pode ser composto pela consulta ao utilizador ou aplicação diretamente ou indiretamente através de ferramentas que estão ligadas à base de dados. Este é particularmente útil quando criar relatórios, utilizando ferramentas de integração de BI ou dados comercial — ou em qualquer aplicação que não pode ser alterada. Com uma consulta de flexível, pode consultar em várias bases de dados utilizando a experiência de conectividade do SQL Server familiar nas ferramentas de como o Excel, obter, quadro ou Cognos.
Uma consulta flexível permite um acesso fácil a uma coleção de bases de dados através de consultas emitidas por SQL Server Management Studio ou Visual Studio toda e facilita a base de dados em várias consultar a partir do quadro entidade ou outros ambientes ORM. Figura 1 mostra um cenário onde cria uma aplicação de nuvem existente (que utiliza a [biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md)) numa camada de dados de saída dimensionada e uma consulta flexível é utilizada para elaboração de relatórios entre bases de dados.

**Figura 1** Consulta de base de dados flexível utilizada na camada de dados de saída dimensionada

![Consulta de base de dados flexível utilizada na camada de dados de saída dimensionada][1]

Cenários de cliente de consulta flexível são caracterizados por seguintes topologias:

* **Criar partições vertical – consultas de base de dados de cruz** (Topologia 1): os dados são divididos verticalmente entre um número de bases de dados de uma camada de dados. Normalmente, conjuntos de tabelas diferentes se encontram bases de dados diferentes. Isto significa que o esquema é diferente em bases de dados diferentes. Por exemplo, todas as tabelas para o inventário estão numa base de dados enquanto todas as tabelas relacionadas com a gestão de contas estão numa base de dados segundo. Casos de utilização comuns com esta topologia necessitam de uma consulta em ou compilar relatórios através de tabelas no várias bases de dados.
* **Criar partições horizontal – Sharding** (Topologia 2): dados estiver dividido em partições horizontalmente para distribuir linhas por de dados com escala saída camada. Com esta abordagem, o esquema é idêntico em todas as bases de dados de participantes. Esta abordagem é também chamada "sharding". Pode ser efetuada sharding e geridos utilizando (1) a base de dados flexível ferramentas (2) self-sharding ou bibliotecas. Uma consulta flexível é utilizada para consulta ou compilar relatórios através de vários shards.

> [AZURE.NOTE] Consulta de base de dados flexível funciona melhor para ocasionais cenários de elaboração de relatórios, onde pode ser efetuada a maior parte da transformação na camada dados. Para o das cargas de trabalho de elaboração de relatórios grossas ou cenários de armazenamento de dados com consultas mais complexas, considere também utilizar [Armazém de dados do SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).


## <a name="elastic-database-query-topologies"></a>Flexível topologias de consulta de base de dados

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>Topologia 1: Criar de partições Vertical – entre bases de dados consultas

Para começar a codificação, consulte o artigo [Introdução à consulta cruzada-base de dados (vertical de criação de partições)](sql-database-elastic-query-getting-started-vertical.md).

Uma consulta flexível pode ser utilizada para tornar os dados localizados numa base de dados SQLDB disponível para outras bases de dados SQLDB. Esta opção permite-consultas a partir de uma base de dados para consultar as tabelas no quaisquer outros SQLDB base de dados remota. O primeiro passo é definir a origem de dados externa para cada base de dados remota. Origem de dados externa é definida na base de dados local a partir do qual pretende ter acesso às tabelas localizada na base de dados remota. Não são necessárias alterações na base de dados remota. Para verticais partições cenários típicos que bases de dados diferentes têm esquemas diferentes, flexível consultas podem ser utilizadas para implementar comuns casos de utilização, como o acesso aos dados de referência e entre bases de dados consultar.

**Dados de referência**: topologia 1 é utilizada para gestão de dados de referência. Na imagem abaixo, duas tabelas (T1 e T2) com dados de referência são mantidas numa base de dados dedicada. Utilizar uma consulta flexível, já pode aceder a tabelas T1 e T2 remotamente a partir de outras bases de dados, tal como apresentado na figura. Utilizar topologia 1 se tabelas de referência são pequenas ou remotos consultas numa tabela de referência ter predicados selectivos.

**Figura 2** Criação de partições - utilizando dados de referência de consulta para consulta flexível vertical

![Criação de partições - utilizando dados de referência de consulta para consulta flexível vertical][3]

**Consultar entre bases de dados**: elásticos consultas casos de utilização de ativar que requerem consultar através de vários SQLDB as bases de dados. Figura 3 mostra quatro bases de dados diferentes: CRM, inventário, HR e produtos. Consultas executadas de uma das bases de dados também precisam de acesso a uma ou todas as outras bases de dados. Utilizar uma consulta flexível, pode configurar a base de dados para esta caso ao executar alguns instruções DDL simples em cada uma das quatro bases de dados. Após esta configuração única, acesso a uma tabela remota é tão simple como que fazem referência a uma tabela local a partir das suas consultas T SQL ou de ferramentas de BI. Esta abordagem é recomendada se as consultas remotas não devolvem resultados grandes.

**Figura 3** Vertical a partições - usar elásticos consulta para consulta em diferentes bases de dados

![Vertical a partições - usar elásticos consulta para consulta em diferentes bases de dados][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>Topologia de 2: Horizontal de criação de partições – sharding

Camada de dados através do query flexível para efetuar tarefas de elaboração de relatórios sobre sharded, ou seja, horizontalmente divididas, necessita de um [mapa de shard de base de dados flexível](sql-database-elastic-scale-shard-map-management.md) para representar as bases de dados da camada dados. Normalmente, é utilizado apenas um mapa de shard única neste cenário e uma base de dados com as capacidades de consulta flexível dedicada serve como o ponto de entrada para elaboração de relatórios de consultas. Apenas esta base de dados dedicada precisa de aceder ao mapa de shard. Figura 4 ilustra esta topologia e a respectiva configuração com o mapa de base de dados e shard consulta flexível. As bases de dados na camada dados podem ser de qualquer versão da base de dados do Azure SQL ou edição. Para mais informações sobre a biblioteca do cliente flexível da base de dados e criar mapas shard, consulte o artigo [Shard mapear gestão](sql-database-elastic-scale-shard-map-management.md).

**Figura 4** Criação de partições - através do query flexível para elaboração de relatórios sobre camadas dados sharded horizontal

![Criação de partições - através do query flexível para elaboração de relatórios sobre camadas dados sharded horizontal][5]

> [AZURE.NOTE] A base de dados de consulta de base de dados flexível dedicada tem de ser uma base de dados do SQL DB v12. Não existem restrições sobre os shards próprios.

Para começar a codificação, consulte o artigo [Introdução à consulta de base de dados flexível para a partições horizontal (sharding)](sql-database-elastic-query-getting-started.md).

## <a name="implementing-elastic-database-queries"></a>Consultas de base de dados flexível execução

Os passos para implementar o query flexível para os cenários de criação de partições verticais e horizontais são explicados as secções seguintes. Também consultar a documentação mais detalhada para os cenários de criação de partições diferentes.

### <a name="vertical-partitioning---cross-database-queries"></a>A partições vertical - entre bases de dados consultas

Os passos seguintes configurar consultas de base de dados flexível para cenários de criação de partições verticais que necessitam de acesso a uma tabela localizada no remotos SQLDB as bases de dados com o mesmo esquema:

*    [Criar o modelo global de chave](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Criar base de dados CONFINADOS CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [Origem de dados externa criar/LARGAR](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource tipo **RDBMS**
*    [Tabela externa criar/LARGAR](https://msdn.microsoft.com/library/dn935021.aspx) AMinhaTabela

Depois de executar as instruções de DDL, pode aceder a tabela remota "AMinhaTabela" como se estivesse numa tabela local. Base de dados SQL Azure automaticamente abre uma ligação à base de dados remota, processa o seu pedido na base de dados remota e devolve o resultado.
Podem encontrar mais informações sobre os passos necessários para o cenário de criação de partições vertical na [consulta flexível para criar a partições vertical](sql-database-elastic-query-vertical-partitioning.md).  

### <a name="horizontal-partitioning---sharding"></a>A partições horizontal - sharding

Os passos seguintes configurar consultas de base de dados flexível para horizontais partições cenários que necessitam de acesso a um conjunto de tabela que sejam encontram na (normalmente) várias remotos SQLDB bases de dados:

*    [Criar o modelo global de chave](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Criar base de dados CONFINADOS CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Crie um [mapa de shard](sql-database-elastic-scale-shard-map-management.md) que representa a camada de dados utilizando a biblioteca de cliente flexível da base de dados.   
*    [Origem de dados externa criar/LARGAR](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource tipo **SHARD_MAP_MANAGER**
*    [Tabela externa criar/LARGAR](https://msdn.microsoft.com/library/dn935021.aspx) AMinhaTabela

Assim que tiver executado estes passos, pode aceder a tabela horizontalmente com a partições "AMinhaTabela" como se estivesse numa tabela local. Base de dados SQL Azure abre múltiplas ligações paralelas para as bases de dados remotos onde as tabelas estão armazenadas os física automaticamente, processa pedidos nas bases de dados remotos e devolve o resultado.
Podem encontrar mais informações sobre os passos necessários para o cenário de criação de partições horizontal na [consulta flexível para criar a partições horizontal](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="t-sql-querying"></a>Consultar T-SQL
Assim que tiver definido as origens de dados externas e suas tabelas externas, pode utilizar cadeias de ligação normais do SQL Server para ligar para as bases de dados onde definidos pelo seu tabelas externas. Em seguida, pode executar instruções T SQL sobre suas tabelas externas nessa ligação com as limitações descritas abaixo. Pode encontrar mais informações e exemplos de consultas T SQL os tópicos de documentação para [criar a partições horizontal](sql-database-elastic-query-horizontal-partitioning.md) e [vertical a partições](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas
Pode utilizar cadeias de ligação normais do SQL Server para ligar as aplicações e ferramentas de integração de BI ou dados para bases de dados que tenham tabelas externas. Certifique-se de que é suportado do SQL Server como uma origem de dados de uma ferramenta. Quando estiver ligado, consulte a base de dados de consulta flexível e as tabelas na base de dados externas tal como faria com qualquer outra do SQL Server base de dados que se ligam com a ferramenta.

> [AZURE.IMPORTANT] Autenticação utilizando o Azure Active Directory com consultas flexível atualmente não é suportada.

## <a name="cost"></a>Custo

Consulta flexível é incluída para o custo de bases de dados da base de dados do Azure SQL. Tenha em atenção que topologias onde as bases de dados remotos estejam num centro de dados diferentes que o ponto final de consulta flexível são suportadas, mas saída de dados a partir de bases de dados remotos são cobrados normal [taxas Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitações de pré-visualização
* Executar a consulta flexível primeira pode demorar alguns minutos na camada desempenho padrão. Desta vez é necessária para carregar a funcionalidade de consulta flexível; carregar o desempenho melhora com mais elevadas camadas de desempenho.
* Ainda, o scripting de origens de dados externas ou tabelas externas a partir do SSMS ou SSDT não é suportada.
* Importar/exportar para SQL DB ainda não suporta origens de dados externas e tabelas externas. Se precisar de utilizar Importar/exportar, largue estes objectos antes de exportar e, em seguida, recriá-las após importar.
* Base de dados flexível query atualmente apenas suporta o acesso só de leitura para tabelas externas. No entanto, pode utilizar características T-SQL na base de dados onde é definida a tabela externa. Isto pode ser útil para, por exemplo, persistirem resultados temporários a utilizar, por exemplo, SELECIONE < column_list > para < local_table > ou para definir os procedimentos armazenados da base de dados de consulta flexível que se referem a tabelas externas.
* Exceto tipo, tipos LOB não são suportados nas definições de tabela externa. Como solução, pode criar uma vista numa base de dados remota que converte o tipo LOB para o tipo, definem a sua tabela externa através da vista em vez de à tabela base e, em seguida, convertê-la novamente para o tipo de LOB original nas suas consultas.
* Coluna estatísticas sobre tabelas externas atualmente não são suportadas. Estatísticas de tabelas são suportadas, mas necessita de ser criada manualmente.

## <a name="feedback"></a>Comentários
Partilhe comentários sobre a sua experiência com consultas flexível com-no Disqus abaixo, os fóruns do MSDN, ou no Stackoverflow. Estamos a interessado em todos os tipos de comentários sobre o serviço (defeito, margens irregulares, lacunas funcionalidade).

## <a name="more-information"></a>Obter mais informações

Pode encontrar mais informações sobre a base de dados em várias consultar e cenários de criação de partições verticais nos seguintes documentos:

* [Consultar entre bases de dados e descrição geral da criação de partições vertical](sql-database-elastic-query-vertical-partitioning.md)
* Experimente a nossa tutorial passo a passo de ter um total exemplo de trabalho em execução no minutos: [Introdução à consulta cruzada-base de dados (vertical de criação de partições)](sql-database-elastic-query-getting-started-vertical.md).


Obter mais informações sobre cenários de criação de partições e sharding horizontais estão disponíveis aqui:

* [Descrição geral horizontal de criação de partições e sharding](sql-database-elastic-query-horizontal-partitioning.md)
* Experimente a nossa tutorial passo a passo de ter um total exemplo de trabalho em execução no minutos: [Introdução à consulta de base de dados flexível para a partições horizontal (sharding)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
