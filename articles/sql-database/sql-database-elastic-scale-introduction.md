<properties
    pageTitle="Dimensionamento fora com base de dados do SQL Azure | Microsoft Azure"
    description="Software como um programadores de serviço (SaaS) pode facilmente criar bases de dados flexível, dimensionáveis na nuvem utilizando estas ferramentas"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>Dimensionamento saída com a base de dados do SQL Azure

Pode facilmente Dimensionar saída bases de dados do Azure SQL com as ferramentas de **Base de dados flexível** . Estas ferramentas e funcionalidades permitem-lhe utilizar os recursos de base de dados praticamente ilimitado da **Base de dados do SQL Azure** para criar soluções para transaccionais das cargas de trabalho e, sobretudo Software como um aplicações de serviço (SaaS). Funcionalidades de bases de dados flexível são compostos por dos seguintes procedimentos:

* [Biblioteca de cliente de base de dados flexível](sql-database-elastic-database-client-library.md): A biblioteca do cliente é uma funcionalidade que permite-lhe criar e manter sharded bases de dados.  Consulte o artigo [Introdução às ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md).
* [Ferramenta de intercalação de divisão de bases de dados flexível](sql-database-elastic-scale-overview-split-and-merge.md): mover dados entre sharded bases de dados. Isto é útil para mover dados a partir de uma base de dados do inquilino com várias para uma base de dados do inquilino único (ou vice versa). Consulte o artigo [flexível base de dados tutorial de ferramenta dividido em série](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Tarefas de base de dados flexível](sql-database-elastic-jobs-overview.md) (pré-visualização): utilizam tarefas para gerir grandes quantidades de bases de dados Azure SQL. Facilmente execute operações administrativas, tais como alterações de esquema, gestão de credenciais, atualizações de dados de referência, recolha de dados de desempenho ou da coleção de telemetria do inquilino (cliente) utilizando processos.
* [Consulta de base de dados flexível](sql-database-elastic-query-overview.md) (pré-visualização): permite-lhe executar uma consulta de Transact-SQL que abrange várias bases de dados. Isto permite a ligação para elaboração de relatórios ferramentas, como o Excel, obter, quadro, etc.
* [Transações flexível](sql-database-elastic-transactions-overview.md): esta funcionalidade permite-lhe executar as transações que aparecem em várias bases de dados na base de dados do SQL Azure. As transações de base de dados flexível estão disponíveis para aplicações do .NET utilizando o ADO .NET e integram com a experiência familiar programação, utilizando as [System.Transaction classes](https://msdn.microsoft.com/library/system.transactions.aspx).

O gráfico abaixo apresenta uma arquitectura que inclui as **funcionalidades de base de dados flexível** em relação uma coleção de bases de dados.

Neste gráfico, as cores da base de dados representam esquemas. Bases de dados com a mesma cor partilham o mesmo esquema.

1. Um conjunto de **bases de dados Azure SQL** estão alojados no Azure utilizando a arquitetura de sharding.
2. A **biblioteca de base de dados flexível do cliente** é utilizada para gerir um conjunto de shard.
3. Um subconjunto de bases de dados sejam colocados num **conjunto de base de dados flexível**. (Consulte o artigo [o que é um conjunto de dados?](sql-database-elastic-pool.md)).
4. Uma **base de dados flexível tarefa** é executada agendada ou ad-hoc T-SQL scripts contra todas as bases de dados.
5. A **ferramenta de divisão em série** é utilizada para mover dados a partir de um shard para outro.
6. A **consulta de base de dados flexível** permite-lhe escrever uma consulta que abrange todas as bases de dados no conjunto de shard.
7. **Transações flexível** permite-lhe executar as transações que aparecem em várias bases de dados. 


![Ferramentas de base de dados flexível][1]


## <a name="why-use-the-tools"></a>Porquê utilizar as ferramentas?

Uma elasticidade e a escala para aplicações na nuvem que foram simples para VMs e armazenamento de BLOBs – basta adicionar ou subtrair unidades ou aumentar power. Mas permaneceu um desafio para processamento de dados com estado nas bases de dados relacionais. Desafios eclodidas nestes cenários:

* Aumentar e diminuir capacidade para a parte da base de dados relacional da sua carga de trabalho.
* Gerir pontos que podem surgir afetar um subconjunto específico de dados – como um nomeadamente ocupado-cliente final (inquilino).

Normalmente, cenários como estes foram resolvidos pelo investir em servidores de grande escala base de dados para suportar a aplicação. No entanto, esta opção é limitada na nuvem onde todo o processamento acontece em hardware do produto importante predefinido. Em vez disso, distribuição de dados e processamento através de muitas bases de dados estruturadas idêntico (um padrão de escala-out conhecido como "sharding") fornece uma alternativa a tradicionais abordagens de escala de segurança, tanto em termos de custos e elasticidade.

## <a name="horizontal-and-vertical-scaling"></a>Dimensionamento horizontal e vertical

A figura seguinte mostra as dimensões horizontais e verticais do dimensionamento da, que são formas básicas que as bases de dados flexível podem ser dimensionados.

![Horizontal versus Vertical Scaleout][2]

Dimensionamento horizontal refere-se a adição ou remoção de bases de dados para ajustar o desempenho geral ou capacidade. Isto é também chamado "dimensionamento". Sharding, na qual são divididos dados ao longo de uma coleção de bases de dados estruturadas idênticos, é uma forma comum para implementar o dimensionamento horizontal.  

Dimensionamento vertical que se refere a aumentar ou diminuir o nível de desempenho de uma base de dados individual — esta é também conhecida como "escalar para cima."

A maioria das aplicações de base de dados de escala nuvem irá utilizar uma combinação destas duas estratégias. Por exemplo, um Software de uma aplicação de serviço poderá utilizar dimensionamento vertical e horizontal dimensionamento aprovisionamento de novos de clientes de fim para permitir que a base de dados do cliente cada extremidade aumentar ou encolher recursos, conforme necessário, a carga de trabalho.

* Dimensionamento horizontal é gerido utilizando a [biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md).

* Dimensionamento vertical é feito utilizar cmdlets do Azure PowerShell para alterar a camada de serviço, ou colocando bases de dados num conjunto de dados flexível.

## <a name="sharding"></a>Sharding

*Sharding* é uma técnica para distribuir grandes quantidades de dados estruturadas idêntico ao longo de um número de bases de dados independentes. É especialmente popular com os programadores de nuvem criação de Software para os clientes de fim ou empresas como um ofertas de serviço (SAAS). Estes clientes de fim são, muitas vezes como "inquilinos". Sharding podem ser necessários para qualquer número das razões:  

* A quantidade total de dados é demasiado grande para se ajustar dentro dos limites de uma base de dados simples
* O débito da transação a carga de trabalho geral exceda as capacidades de uma base de dados simples
* Inquilinos precisem de isolamento físico uns dos outros, para que são necessários bases de dados separadas para cada inquilino
* Secções diferentes de uma base de dados poderão ter de residem na geografias diferentes para conformidade, desempenho ou motivos geopolíticos.

Em outras situações, tal como ingestão de dados a partir de dispositivos distribuídos, sharding pode ser utilizado para preencher um conjunto de bases de dados que estão organizados amplificada. Por exemplo, pode ser dedicado uma base de dados separada para cada dia ou da semana. Nesse caso, a tecla sharding pode ser um número inteiro que representa a data (presente em todas as linhas das tabelas sharded) e consultas a obter informações para um intervalo de datas devem ser encaminhadas pela aplicação para o subconjunto de bases de dados que abrange que o intervalo em questão.

Sharding funciona melhor quando cada transação numa aplicação pode ser restringida a um único valor de uma chave de sharding. Que assegura que todas as operações será locais para uma base de dados.

## <a name="multi-tenant-and-single-tenant"></a>Inquilino único e com várias inquilino

Algumas aplicações utilizam a abordagem mais simples de criação de uma base de dados separada para cada inquilino. Este é o **padrão de sharding inquilino único** que fornece isolamento, a capacidade de cópia de segurança/restauro e recursos de dimensionamento na granularidade do inquilino. Com sharding única inquilino, cada base de dados está associada a um valor de ID de inquilino específico (ou valor de chave do cliente), mas essa tecla não sempre é necessário presente nos dados própria. Cabe a aplicação para encaminhar cada pedido para a base de dados adequado – e a biblioteca de cliente pode simplificar esta.

![Inquilino única versus com várias inquilino][4]

As outras pessoas cenários pack múltiplos inquilinos em conjunto para bases de dados, em vez de isolá-los em bases de dados separados. Este é um **padrão de inquilino com várias sharding** típica – e pode ser movida pelo facto de uma aplicação gere a grandes quantidades de inquilinos muito pequenos. No inquilino com várias sharding, as linhas nas tabelas da base de dados são todas concebidas para executar uma chave que identifica o ID do inquilino ou chave de sharding. Novamente, é responsável por encaminhamento de pedido de um inquilino para a base de dados adequado a camada de aplicação e isto pode ser suportado pela biblioteca do cliente flexível da base de dados. Além disso, a segurança de nível de linha pode ser utilizado para filtrar as linhas de cada inquilino pode aceder – para obter detalhes, consulte o artigo [as aplicações do inquilinos com várias ferramentas de base de dados flexível e segurança de nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuir dados entre as bases de dados pode ser necessárias com o padrão de inquilino com várias sharding e esta é facilitada pela ferramenta flexível da base de dados dividida em série. Para saber mais sobre os padrões de estrutura para aplicações SaaS utilizando flexível conjuntos de dados, consulte o artigo [Padrões de Design para inquilino com várias SaaS aplicações com base de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Mover os dados de várias a única arrendamento bases de dados

Ao criar uma aplicação de SaaS, é típico a oferecer potenciais clientes uma versão de avaliação do software. Neste caso, é rentável para utilizar uma base de dados do inquilino com várias para os dados. No entanto, quando um potencial cliente se torna um cliente, uma base de dados do inquilino único é aconselhável uma vez que fornece um melhor desempenho. Se o cliente criado dados durante o período de avaliação, utilize a [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md) para mover os dados do inquilino com várias para a nova base de dados do inquilino único.

## <a name="next-steps"></a>Próximos passos

Para uma aplicação de exemplo que mostra a mudança a biblioteca do cliente, consulte o artigo [Introdução às ferramentas Datababase flexível](sql-database-elastic-scale-get-started.md).

Para converter bases de dados existentes para utilizar as ferramentas, consulte o artigo [Migrar bases de dados existentes fora de escala](sql-database-elastic-convert-to-use-elastic-tools.md).

Para ver os detalhes do agrupamento de base de dados flexível, consulte o artigo [preço e desempenho considerações para um agrupamento de base de dados flexível](sql-database-elastic-pool-guidance.md)ou criar um novo conjunto de dados com o [tutorial](sql-database-elastic-pool-create-portal.md).  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

