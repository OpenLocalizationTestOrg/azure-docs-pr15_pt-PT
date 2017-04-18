<properties
   pageTitle="Base de dados Azure SQL constrói inquilinos com várias aplicações com isolamento e eficiência"
   description="Saiba como base de dados SQL constrói inquilinos com várias aplicações"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Constrói inquilinos com várias aplicações com base de dados do Azure SQL com isolamento e eficiência

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>Tirar partido flexível conjuntos de dados e criar aplicações de inquilinos com várias mais eficientes

Se for um Dev Center SaaS, escrever uma aplicação do inquilino com várias e que processam muitos clientes, muitas vezes efetue concessões no cliente desempenho, gestão e segurança. Com os Azure SQL da base de dados flexível da base de dados agrupamentos, já não tem de fazer essa compromisso. Estes conjuntos de dados ajudam a gerir e monitorizar aplicações do inquilinos com várias e obtenha benefícios de isolamento da base de um cliente por dados. Consulte o artigo [padrões de Design para aplicações do inquilino com várias SaaS com base de dados Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![Compilar multi-inquilino aplicações](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>Dimensionamento atendedores controlam

Conjuntos de dados Dimensionar automaticamente desempenho e capacidade de armazenamento para as bases de dados flexível no momento. Pode controlar o desempenho atribuído a um conjunto de dados, adicionar ou remover flexível bases de dados a pedido e definir o desempenho do flexível bases de dados sem afetar o custo total do conjunto de. Isto significa que não tem de se preocupar com gerir a utilização de bases de dados individuais.

[Leia a documentação](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>Inteligente gestão do seu ambiente

Recomendações de redimensionamento incorporados importante identificam bases de dados que iria beneficiem das vantagens dos conjuntos de dados. Estas recomendações permitem "-" análise de hipóteses para rápida otimização cumprir os seus objetivos desempenho. Monitorização e dashboards de resolução de problemas de desempenho Rich ajudá-lo a visualizar a utilização de agrupamento histórico.

[Leia a documentação](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>Desempenho e preço para corresponder às suas necessidades

Básicas, Standard, e agrupamentos de Premium fornecem-lhe uma vasta gama de desempenho, armazenamento e opções de preços. Conjuntos de dados podem conter até 400 bases de dados flexível. Bases de dados flexível podem Dimensionar automaticamente até 1000 unidades da transação da base de dados flexível (eDTU).

[Leia a documentação](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>Ferramentas flexível

Para além de agrupamentos flexível, existem funcionalidades de base de dados SQL para o ajudar a gerir actividades operacionais através de várias bases de dados:

**Execute consultas de base de dados de cruz e relatórios.**  
[Consulta de base de dados flexível](sql-database-elastic-query-overview.md) permite-lhe executar consultas ou relatórios em várias bases de dados no seu conjunto flexível e aceder aos dados remotos armazenados no muitas bases de dados do conjunto de ao mesmo tempo.

**Execute as transações de base de dados cruzada.**  
[As transações de base de dados flexível](sql-database-elastic-transactions-overview.md) permitem-lhe executar as transações que abrangem várias bases de dados nas bases de dados do SQL e executam operações (isto é, quando processamento operações financeiras através de bases de dados ou ao atualizar inventário uma base de dados e encomendas).

**Execute as mesmas operações em várias bases de dados.**  
[Tarefas de base de dados flexível](sql-database-elastic-jobs-overview.md) executar operações administrativas, tais como a reformulação repetida de índices ou atualizar esquemas através de cada base de dados no seu conjunto flexível.

Aceda à home page para ver o que mais preciso base de dados do SQL tem para oferecer.
[Dar saída do mesmo](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>Próximos passos

Obter uma [subscrição gratuita do Azure](https://azure.microsoft.com/get-started/) e, em seguida, [criar a sua primeira base de dados do Azure SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

Explore todas as [capacidades da base de dados SQL](https://azure.microsoft.com/services/sql-database/).
 
Rever a [Descrição geral técnica de base de dados SQL](sql-database-technical-overview.md).  
