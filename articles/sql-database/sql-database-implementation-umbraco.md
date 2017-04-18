<properties
   pageTitle="Base de dados do Azure SQL Azure caso prático da Microsoft - Umbraco | Microsoft Azure"
   description="Saiba mais sobre como Umbraco utiliza a base de dados SQL para aprovisionar rapidamente e serviços de escala para milhares de inquilinos na nuvem"
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
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco utiliza a base de dados do SQL Azure para rapidamente aprovisionar escala de serviços de e para milhares de inquilinos na nuvem

![Logótipo de Umbraco](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco é um popular abrir origem gestão de conteúdos sistema (CMS) que pode executar tudo a partir de sites de campanha ou uma brochura pequenas para aplicações complexas para revista Fortune 500 empresas e sites de multimédia global. 

> "Temos bastante uma grande Comunidade de programadores que utilizam o sistema, com mais de 100.000 programadores nossos fóruns e em mais do que 350.000 sites que estão direto, executar Umbraco."

> — Manuel Christensen, oportunidade potencial técnico, Umbraco

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

Para simplificar a implementações do cliente, Umbraco adicionado Umbraco-como-a-Service (UaaS): uma oferta de software-como-a-service (SaaS) que elimina a necessidade de para implementações no local, fornece dimensionamento incorporados e remove a gestão de custos gerais ao ativar os programadores focar-se em gestão de inovação em vez de solução do produto. Umbraco é forneçam todas essas vantagens por depender de flexível plataforma-como-a-service (PaaS) modelo de oferecidos pelo Microsoft Azure.

UaaS permite aos clientes SaaS utilizar as funcionalidades do Umbraco CMS que estavam anteriormente fora do seu alcance. Configurações para esses clientes com um ambiente do CMS trabalho que inclui uma base de dados de produção. Os clientes podem adicionar até duas bases de dados adicionais para o desenvolvimento e ambientes de teste, dependendo das suas necessidades. Quando um novo ambiente é pedido, um processo automatizado atribui esse cliente uma base de dados automaticamente. A nova base de dados está pronto em segundos, uma vez que a base de dados já ter sido previamente aprovisionada por Umbraco a partir de um conjunto de flexível Azure de bases de dados disponíveis (consulte figura 1).


![Ciclo de vida de aprovisionamento Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Ciclo de vida de aprovisionamento para Umbraco como um serviço (UaaS)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Conjuntos de dados flexível Azure e automatização simplificam híbridas

Com base de dados do SQL Azure e outros serviços do Azure, Umbraco clientes personalizada podem aprovisionar o seus ambientes e Umbraco facilmente pode monitorizar e gerir bases de dados como parte de um fluxo de trabalho intuitivo:

1.  Aprovisionar

    Umbraco mantém uma capacidade de 200 disponíveis previamente aprovisionadas bases de dados de agrupamentos de flexível. Quando um cliente novo se inscreve para UaaS, Umbraco fornece o cliente com um ambiente do CMS novo em tempo real próximo, atribuindo-lhes uma base de dados a partir do conjunto de disponibilidade.

    Quando um agrupamento de disponibilidade atinge respectivo limiar, é criado um novo conjunto de flexível e novas bases de dados são previamente aprovisionadas a serem atribuídas à clientes, conforme necessário.

    Pós-implementação está totalmente automatizada utilizar bibliotecas de gestão de c# e filas Azure Service Bus.

2.  Utilizam

    Os clientes utilizar um a três ambientes (para produção, teste e/ou desenvolvimento), cada com a sua própria base de dados. Bases de dados de cliente estão agrupamentos de base de dados flexível, que permite aos Umbraco fornecer eficiente dimensionamento sem ter de aprovisionamento de excesso.

    ![Descrição geral do projeto Umbraco](./media/sql-database-implementation-umbraco/figure2.png)

    ![Detalhes do projecto Umbraco](./media/sql-database-implementation-umbraco/figure3.png)

    Figura 2. Web site de cliente Umbraco-como-a-Service (UaaS), a mostrar a descrição geral do projeto e detalhes

    Base de dados SQL Azure utiliza unidades da transação da base de dados (DTUs) para representar a potência relativa necessária para as transações de base de dados reais. Para clientes UaaS, normalmente operam bases de dados a DTUs cerca de 10, mas cada tem elasticidade para dimensionar a pedido. Isto significa que UaaS pode garantir que os clientes tenham sempre recursos necessários, mesmo durante as horas de ponta. Por exemplo, durante um evento de desporto Domingo noite recente, UaaS cliente experiente base de dados picos até 100 DTUs para a duração do jogo. Azure agrupamentos flexível proporcionou Umbraco suportar essa procura alta sem degradação do desempenho.

3.  Monitor

    Umbraco monitores da base de dados atividade utilizar dashboards no interior do portal Azure, juntamente com os alertas de correio eletrónico personalizado.

4.  Recuperação de falhas

    Azure fornece duas opções de recuperação de falhas (DR): Geo-a replicação do Active e Geo restaurar. A opção de DR numa empresa deve seleccionar depende seus [objetivos continuidade do negócio](sql-database-business-continuity.md).

    Replicação do Geo Active fornece o nível mais rápido de resposta trabalho tempo de inatividade. Utilizar replicação de Geo ativa, pode criar até quatro secundários legíveis em servidores de diferentes regiões e, em seguida, pode iniciar activação pós-falha a qualquer uma dos secundários em caso de falha.

    Umbraco não necessita de replicação Geo, mas -lo a tirar partido do Azure Geo-restauro para ajudar a garantir tempo de inatividade mínimo trabalho uma falha. Restaurar geo depende das cópias de segurança da base de dados no armazenamento Azure geo redundantes. Que permite aos utilizadores restaurar a partir de uma cópia de segurança quando existe uma falha na região principal.

5.  A aprovisionar

    Quando é eliminado um ambiente do projeto, quaisquer bases de dados associadas (desenvolvimento, transição, ou ao vivo) são removidos durante a limpeza do Azure Service Bus fila de espera. Este processo automatizado restaura as bases de dados não utilizadas ao agrupamento de flexível de disponibilidade de base de dados do Umbraco, disponibilizando-as para aprovisionar futuras mantendo utilização máxima.

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Conjuntos de dados flexível permitem UaaS dimensionar com facilidade

Tirando partido da agrupamentos de base de dados flexível Azure, Umbraco pode otimizar o desempenho para os seus clientes sem ter de cargo ou prestação insuficiente. Umbraco tem atualmente quase 3.000 bases de dados ao longo 19 agrupamentos de base de dados flexível, com a capacidade de facilmente dimensionar, conforme necessário para acomodar qualquer um dos seus 325,000 clientes existentes ou novos clientes que estão prontos para implementar um CMS na nuvem.

Na verdade, de acordo com Christensen Manuel, conduzir técnico na Umbraco, "UaaS é agora ter e em crescimento de cerca de 30 novos clientes por dia. Dos nossos clientes estão contente com a conveniência da ser conseguir aprovisionar novos projectos em segundos, publicar instantaneamente atualizações para os seus sites direto a partir de um ambiente de desenvolvimento com 'com um clique implementação' e efetuar alterações tal como rapidamente se a pessoa encontrar erros."

Se um cliente não requer um ambiente do segundo e/ou terceiro deixem, basta-pode remover esses ambientes. Que liberta recursos que podem ser utilizados para outros clientes como parte do conjunto de flexível de disponibilidade de base de dados de Umbraco.

![Arquitetura de implementação de Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Arquitetura de implementação de UaaS no Microsoft Azure

##<a name="the-path-from-datacenter-to-cloud"></a>O caminho a partir do Centro de dados para o cloud

Quando os programadores de Umbraco inicialmente efetuado decisão para mover a um modelo de SaaS, eles Sabia que necessitam uma forma rentável e dimensionável construir o serviço.

> "Agrupamentos de base de dados flexível são uma opção perfeita para os nossos oferecer de SaaS porque iremos marcar capacidade de cima e para baixo, conforme necessário. Aprovisionamento é fácil e com o nosso programa de configuração, podemos mantê-utilização num máxima."

> — Manuel Christensen, oportunidade potencial técnico, Umbraco

"Queremos perder o nosso tempo para resolver problemas dos nossos clientes, não a gerir infraestrutura. Queremos tornam mais fácil para os nossos clientes obter o valor a maior parte dos,"diz Niels Hartvig, fundador de Umbraco. "Pedimos inicialmente considerado que aloja os servidores de estabelecidos, mas planeamento da capacidade teria sido um pesadelo em termos de." Coincidentally, Umbraco não empregam quaisquer administradores de base de dados, que modo, uma proposta de valor de chave para utilizar UaaS.

Um objetivo importante para os programadores de Umbraco era fornece uma maneira de clientes UaaS aprovisionar ambientes rapidamente e sem limitações de capacidade. Mas fornecer um serviço alojado dedicado no Umbraco centros de dados seria ter necessários muitas excesso de capacidade para processar rajada no processamento. Que iria ter queria dizer adicionar infraestrutura de cluster consideráveis que iria ter sido subutilizada regularmente.

Além disso, a equipa de desenvolvimento Umbraco pretendia numa solução que permitirá-las para reutilização do respectivo código existente possível quanto. Como programador Umbraco dos Estados Mikkel chaves-Ferreira, "Pedimos foram satisfeitos com as ferramentas de desenvolvimento da Microsoft já foram familiarizados, como o Microsoft SQL Server, base de dados do Microsoft Azure SQL, ASP.net e serviços de informação Internet (IIS). Antes de investir uma IaaS ou um PaaS na nuvem solução, queremos para se certificar de que o mesmo seria suportar nosso ferramentas do Microsoft e plataformas, para que não seria temos que fazer alterações grandes nosso código base. "

Para cumprir todos os critérios, Umbraco procurava um parceiro de nuvem com as seguintes qualificações:

-   Capacidade e fiabilidade suficientes
-   Suporte de ferramentas de desenvolvimento da Microsoft, para que os engenheiros Umbraco seriam não ser forçados completamente ajude o seu ambiente de desenvolvimento
-   Presença em todos os mercados geográficos na qual UaaS compete (empresas é Certifique-se de que podem aceder aos seus dados rapidamente e que os respetivos dados estão armazenados numa localização que cumpre os respetivos requisitos de regulamentação regionais)

##<a name="why-umbraco-chose-azure-for-uaas"></a>Por que motivo o Umbraco escolher Azure para UaaS

De acordo com Manuel Christensen "após a considerar todas as nossas opções, podemos selecionada Azure porque-cumprido todos os nossos critérios, a partir de capacidade de gestão e escalabilidade familiaridade e rentabilidade. Vamos configurar os ambientes no Azure VMs e cada ambiente tem as suas próprias instância de base de dados do Azure SQL, com todas as instâncias agrupamentos de base de dados flexível. Separando bases de dados entre desenvolvimento, teste e direto ambientes, pode oferecemos dos nossos clientes isolamento de desempenho robusto correspondido à escala — um win enorme. "

Manuel persistir, "antes, tivemos aprovisionamento de servidores para bases de dados web manualmente. Agora, não temos refletir acerca do mesmo. Tudo o que está a automatizado — a partir de aprovisionamento para limpeza. "

Manuel também está satisfeito com as capacidades de dimensionamento fornecidas pela Azure. "Agrupamentos de base de dados flexível são uma opção perfeita para os nossos oferecer de SaaS porque iremos marcar capacidade de cima e para baixo, conforme necessário. Aprovisionamento é fácil e com o nosso programa de configuração, podemos mantê-utilização num máxima." Manuel Unidos, "simplificar de flexível conjuntos de dados, juntamente com assurance de serviço camada são baseados DTUs, dá-na potência aprovisionar o novo agrupamentos de recursos a pedido. Recentemente, um dos nossos clientes maiores subiram para 100 DTUs no seu ambiente direto. Utilizar o Azure, os nossos agrupamentos de flexível fornecido bases de dados do cliente com os recursos que eles necessários em tempo real sem ter de prever requisitos DTU. Resumindo, dos nossos clientes obter o tempo de rotação esperava e podemos pode cumprir os nossos acordos do nível de serviço de desempenho."

Mikkel chaves-Ferreira soma-la: "Pedimos tenha embraced o algoritmo Azure poderoso que liga um cenário de SaaS comum (ativação novos clientes em tempo real na escala) ao nosso padrão de aplicação (previamente aprovisionamento bases de dados, ambos os desenvolvimento e live) na parte superior a tecnologia subjacente (utilizando filas Azure Service Bus em conjunto com a base de dados SQL Azure)."

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Com o Azure, UaaS é excedem as expectativas do cliente

Desde a escolher Azure como seu parceiro de nuvem, Umbraco foi forneçam UaaS clientes com o desempenho otimizado de gestão de conteúdos, sem investimento IT recursos necessário a partir de uma solução personalizada alojada. Como Manuel diz, "Pedimos adora a conveniência de programador e escalabilidade Azure dá-nos e dos nossos clientes são thrilled com as funcionalidades e fiabilidade. Resumindo, passaram uma excelente win para-nos!"
 
## <a name="more-information"></a>Obter mais informações

- Para saber mais acerca de agrupamentos de base de dados flexível Azure, consulte o artigo [agrupamentos de base de dados flexível](sql-database-elastic-pool.md).

- Para saber mais sobre Azure Service Bus, consulte o artigo [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).

- Para saber mais sobre as funções de Web e funções de trabalho, consulte [funções de trabalho](../fundamentals-introduction-to-azure.md#compute). 

- Para saber mais sobre o funcionamento em rede virtual, consulte o artigo [rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/).    

- Para saber mais sobre a cópia de segurança e recuperação, consulte o artigo [continuidade do negócio](sql-database-business-continuity.md).  

- Para saber mais sobre como monitorizar ppols, consulte o artigo [monitorizar os conjuntos de dados](sql-database-elastic-pool-manage-portal.md). 

- Para saber mais sobre Umbraco como um serviço, consulte o artigo [Umbraco](https://umbraco.com/cloud).

