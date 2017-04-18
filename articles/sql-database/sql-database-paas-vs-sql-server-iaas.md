<properties
    pageTitle="SQL (PaaS) da base de dados vs. do SQL Server na nuvem no VMs (IaaS) | Microsoft Azure"
    description="Saber qual a opção do SQL Server nuvem se adequa a aplicação: base de dados SQL Azure (PaaS) ou do SQL Server na nuvem no máquinas virtuais do Azure."
    services="sql-database, virtual-machines"
    keywords="Nuvem do SQL Server, SQL Server na nuvem, base de dados PaaS, SQL Server, DBaaS em nuvem"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Selecione uma opção do SQL Server nuvem: base de dados SQL Azure (PaaS) ou do SQL Server no Azure VMs (IaaS)

Azure tem duas opções para alojamento das cargas de trabalho do SQL Server no Microsoft Azure:

* [Base de dados do SQL Azure](https://azure.microsoft.com/services/sql-database/): A base de dados SQL nativo na nuvem, também conhecido como uma plataforma como uma base de dados do serviço (PaaS) ou uma base de dados como um serviço (DBaaS) que está optimizado para programação da aplicações software-como-a-service (SaaS). Compatibilidade com a maioria das funcionalidades do SQL Server que este oferece. Para mais informações sobre PaaS, consulte o artigo [o que é PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server instalado e alojados na nuvem no Windows Server máquinas virtuais (VMs) em execução no Azure, também conhecido como uma infraestrutura como um serviço (IaaS).
SQL Server em máquinas virtuais Azure está optimizado para migrar as aplicações do SQL Server existentes. Todas as versões e edições do SQL Server estão disponíveis. Compatibilidade de 100% com o SQL Server, permitindo-lhe alojar quantas bases de dados como conforme seja necessárias e executar operações de base de dados de publicação em que este oferece. Oferece controlo total do SQL Server e Windows.

Saiba como cada opção se adequa a plataforma de dados da Microsoft e obter ajuda com a opção da direita para os seus requisitos empresariais de correspondência. Se atribuir prioridades reduções de custo ou administração mínima antes da data tudo o que mais preciso, este artigo pode ajudar a decidir qual das abordagens oferece contra os requisitos de empresas que lhe interessa.


## <a name="microsofts-data-platform"></a>Plataforma de dados da Microsoft

Uma das coisas primeiros para compreender qualquer debate do Azure versus bases de dados do SQL Server no local é que pode utilizar tudo. Plataforma de dados da Microsoft tira partido de tecnologia SQL Server e disponibiliza-o ao longo máquinas físico no local, ambientes de nuvem privado, ambientes de terceiros alojado de privado na nuvem e nuvem pública. SQL Server no Azure virtual mchines permite-lhe satisfazer as necessidades da empresa único e diversificados através de uma combinação de no local e implementações alojado na nuvem, ao utilizar o mesmo conjunto de produtos de servidor, ferramentas de desenvolvimento e conhecimentos nestes ambientes.

   ![Opções do SQL Server em nuvem: base de dados do SQL server no IaaS ou SaaS SQL na nuvem.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Conforme visto no diagrama, cada oferta pode caracterizada pelo nível de administração que têm sobre a infraestrutura (no eixo X) e, pelo grau de custo eficácia realizado pelos consolidação de nível de base de dados e automatização (no eixo do Y).

Quando a estruturar uma aplicação, quatro opções básicas estão disponíveis para a parte do SQL Server da aplicação de alojamento:

- SQL Server em máquinas físicas não foi virtualizado
- SQL Server em máquinas no local foi virtualizado (privada nuvem)
- SQL Server no Azure Máquina Virtual (Microsoft cloud pública)
- Base de dados SQL Azure (Microsoft cloud pública)

Nas secções seguintes, saber sobre o SQL Server na nuvem pública Microsoft: base de dados do SQL Azure e o SQL Server no Azure VMs. Além disso, é explorar motivators de negócio comuns para determinar qual a opção funciona melhor para a sua aplicação.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Um olhar na base de dados do SQL Azure e o SQL Server no Azure VMs

**Base de dados do SQL Azure** é um relacionais da base de dados-como-a-serviço (DBaaS) alojado na nuvem Azure determinado para as categorias de indústria de *Software-como-a-Service (SaaS)* e a *Plataforma-como-a-Service (PaaS)*. [Base de dados SQL](sql-database-technical-overview.md) é criada com base em padronizado hardware e software que é proprietário, alojado e mantida pela Microsoft. Base de dados SQL, podem desenvolver diretamente no serviço utilizando funcionalidades incorporadas e a funcionalidade. Ao utilizar a base de dados SQL, que repartição com opções para dimensionar para cima ou para fora para maior power com sem interrupções.

**SQL Server em máquinas virtuais do Azure (VMs)** se enquadra a categoria de indústria *Infraestrutura-como-a-Service (IaaS)* e permite-lhe executar o SQL Server dentro de uma máquina virtual na nuvem. Semelhante à base de dados SQL, é criada com base em hardware padronizado que é proprietário, alojado e mantida pela Microsoft. Quando utilizar o SQL Server numa VM, pode quer pagar-como-ir para uma licença do SQL Server já incluída numa imagem do SQL Server ou utilizar facilmente uma licença existente. Pode também facilmente para escala-cima/baixo e colocar em pausa/retomar a VM conforme necessário.

Em geral, estas duas opções de SQL estão otimizadas para fins diferentes:

- **Base de dados SQL** está optimizada para reduzir custos gerais ao mínimo para aprovisionamento e gerir muitas bases de dados. Reduz o custos de administração em curso, uma vez que não possui que gerir qualquer máquinas virtuais, o sistema operativo ou o software de base de dados. Não possui gerir atualiza os, elevada disponibilidade ou [cópias de segurança](sql-database-automated-backups.md). Em geral, base de dados do SQL Azure pode aumentar significativamente o número de bases de dados gerido por um único IT ou desenvolvimento recurso.
- **SQL Server em execução no Azure VMs** está optimizada para migrar aplicações existentes para Azure ou expandir aplicações no local existentes para a nuvem em implementações híbridas. Além disso, pode utilizar o SQL Server numa máquina virtual para desenvolver e testar aplicações do SQL Server tradicionais. Com o SQL Server no Azure VMs, terá os direitos administrativos totais através de uma instância do SQL Server dedicada e uma VM baseado na nuvem. É uma escolha perfeita quando uma organização já tem disponíveis para manter as máquinas virtuais de recursos de TI. Estas funcionalidades permitem-lhe construir um sistema para lidar com a aplicação desempenho específicos e requisitos de disponibilidade altamente personalizado.

A tabela seguinte resume as características principais da base de dados SQL e o SQL Server no Azure VMs:

|       | Base de dados SQL | SQL Server em máquinas uma Máquina Virtual Azure|
| -------------- | ------------ | ---------------------- |
| **Melhor para:** | Novas concebido nuvem aplicações que têm constrangimentos de tempo em desenvolvimento e marketing. |Aplicações existentes que requerem migração rápida para a nuvem com alterações mínimas. Rápido desenvolvimento e teste cenários quando não quiser comprar hardware de SQL Server no local não sejam de produção. |
|| Equipas de que precisa de incorporados elevada disponibilidade, recuperação de falhas e atualização para a base de dados. |Equipas que podem configurar e gerir elevada disponibilidade, a recuperação de falhas e a aplicação de patches para SQL Server. Algumas fornecidas funcionalidades automatizadas simplificar a isto significativamente. |
||Equipas que não pretende gerir as definições de configuração e o sistema operativo subjacente.| Se precisar de um ambiente personalizado com direitos administrativos completa.|
||Bases de dados de até 1 TB ou bases de dados maiores que podem ser [horizontal ou verticalmente a partições](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) com um padrão de escala de saída.|Instâncias de SQL Server com até 64 TB de armazenamento. A instância pode suportar quantas bases de dados, conforme necessário. |
||[Aplicações do edifício Software-como-a-Service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Migrar e criação de aplicações empresariais e híbrido.|
|||||
|**Recursos:**|Não pretende que seja empregam recursos de TI para configuração e gestão infraestrutura do subjacente, mas pretende manter o foco a camada de aplicação.|Ter alguns recursos de TI para gestão e configuração. Algumas fornecidas funcionalidades automatizadas simplificar a isto significativamente.|
|**Custo total de propriedade:**|Elimina os custos de hardware e reduz custos administrativos.|Elimina os custos de hardware.|
|**Continuidade do negócio:**|Para além de capacidades de infraestrutura de tolerância a falhas incorporados, base de dados do SQL Azure disponibiliza funcionalidades, como [cópias de segurança automatizadas](sql-database-automated-backups.md), [Restaurar em qualquer altura](sql-database-recovery-using-backups.md#point-in-time-restore), [Geo restaurar](sql-database-recovery-using-backups.md#geo-restore)e a [Replicação do Geo Active](sql-database-geo-replication-overview.md) para aumentar a continuidade do negócio. Para mais informações, consulte o artigo [Descrição geral de continuidade do negócio base de dados SQL](sql-database-business-continuity.md).|SQL Server no Azure VMs permite-lhe configurar uma solução de recuperação de disponibilidade e falhas alta para necessidades específicas da sua base de dados. Por conseguinte, pode ter um sistema de que está optimizado para a sua aplicação. Pode testar e executar activações pós-falha por si mesmo quando for necessário. Para mais informações, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Nuvem de híbrido:**|A aplicação no local pode aceder aos dados na base de dados do SQL Azure.|Com o SQL Server no Azure VMs, pode ter aplicações que são executadas parcialmente na nuvem e parcialmente no local. Por exemplo, pode expandir a sua rede no local e o domínio do Active Directory na nuvem através da [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md). Além disso, pode armazenar ficheiros de dados no local no armazenamento Azure através de [Ficheiros de dados do SQL Server no Azure](http://msdn.microsoft.com/library/dn385720.aspx). Para mais informações, consulte o artigo [Introdução ao SQL Server 2014 híbrido nuvem](http://msdn.microsoft.com/library/dn606154.aspx).|
||Suporta a [replicação transaccional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) como um subscritor para criar uma réplica dados.|Suporta totalmente [replicação transaccional do SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Grupos de Disponibilidade AlwaysOn](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md), os serviços de integração e registo de envio para criar uma réplica dados. Além disso, são totalmente suportadas tradicionais cópias de segurança do SQL Server|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivações de negócio para escolher a base de dados do SQL Azure ou do SQL Server no Azure VMs

### <a name="cost"></a>Custo

Se tiver um arranque que é strapped para dinheiro ou uma equipa de uma empresa estabelecida que funciona em restrições de orçamento justa, fundos limitado são frequentemente controlador principal quando decidir como aloja as bases de dados. Nesta secção, saiba mais sobre a faturação e licenciamento Noções básicas no Azure no que diz respeito estas duas opções de base de dados relacional: base de dados SQL e o SQL Server no Azure VMs. Saiba também como calcular o custo total de aplicação.

#### <a name="billing-and-licensing-basics"></a>Faturação e licenciamento Noções básicas

**Base de dados SQL** é vendido a clientes como um serviço, não com uma licença.  [SQL Server no Azure VMs](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) é vendido com uma licença de incluído pagar por minuto. Se tiver uma licença existente, também pode utilizá-lo.  

Atualmente, a **Base de dados SQL** está disponível em várias camadas de serviço, as quais faturadas por hora com uma taxa fixa com base na camada de serviços e nível de desempenho que escolher. Além disso, são faturada para o tráfego de saída de Internet no normal [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/). Camadas de serviços Basic, padrão e Premium foram concebidas para disponibilizar previsível desempenho com vários níveis de desempenho para que correspondam aos requisitos de pico da sua aplicação. Pode alterar entre camadas de serviços e os níveis de desempenho para que correspondam às suas necessidades de débito variada a aplicação. Se a sua base de dados tiver grande volume transaccional e necessidades para suportar a vários utilizadores em simultâneo, recomendamos que a camada de serviço Premium. Para informações mais recentes nas camadas atual do serviço de suportado, consulte o artigo [Camadas de serviços de base de dados de SQL Azure](sql-database-service-tiers.md). Também pode criar [conjuntos de dados da base de dados flexível](sql-database-elastic-pool.md) para partilhar recursos de desempenho entre instâncias de base de dados.

Com a **Base de dados SQL**, o software de base de dados é automaticamente configurado, corrigido e, atualizado pela Microsoft, o que reduz os custos de administração. Além disso, as suas capacidades de [cópia de segurança incorporada](sql-database-automated-backups.md) ajudá-lo alcançar reduções de custo significativa, especialmente quando tem um grande número de bases de dados.

Com o **SQL Server no Azure VMs**, pode utilizar qualquer uma das imagens de SQL Server desde de plataforma (que inclui uma licença) ou trazer a licença do SQL Server. Todas as versões suportadas do SQL Server (2008R2, 2012, 2014, 2016) e edições (programador, Express, Web, padrão, Enterprise) estão disponíveis. Além disso, trazer-Your-proprietário-licenças de versões (BYOL) das imagens estão disponíveis. Quando utilizar o Azure fornecido imagens, o custo operacional depende o tamanho da memória virtual e a edição do SQL Server que escolher. Independentemente do tamanho da memória virtual ou edição do SQL Server, pode pagar por minuto custo licenciamento do SQL Server e Windows Server, juntamente com o custo de armazenamento do Windows Azure de discos VM. A opção de faturação por minuto permite-lhe utilizar o SQL Server para desde que precisar sem compra adição do SQL Server licenças. Se cumprir o seu próprio licença do SQL Server para Azure, são cobrados para Windows Server e apenas os custos de armazenamento. Para mais informações sobre trazer-your-o proprietário do licenciamento, consulte o artigo [Licença mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Calcular o custo total de aplicação

Quando começar a utilizar uma plataforma na nuvem, o custo de executar a aplicação inclui os custos de administração e desenvolvimento, bem como os custos de serviço da plataforma de nuvem público.

Eis o cálculo do custo detalhadas para a sua aplicação ser executado numa base de dados SQL e o SQL Server no Azure VMs:

**Quando utilizar a base de dados do Azure SQL:**

*Custo da aplicação total = custos de administração altamente minimizado + custos de desenvolvimento de software + custos de serviço de base de dados SQL*

**Quando utilizar o SQL Server Azure VMs:**

*Custo da aplicação total = custo de desenvolvimento de software altamente minimizado + custos de administração + os custos de licenciamento do SQL Server e Windows Server + custos de armazenamento Azure*

Para mais informações sobre preços, consulte os seguintes recursos:

- [Preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Preços de Máquina Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Existe um pequeno subconjunto de funcionalidades no SQL Server que não são aplicáveis ou não está disponível com a base de dados SQL. Para mais informações, consulte [limitações geral de base de dados do SQL e diretrizes](sql-database-general-limitations.md) e [informações de base de dados de SQL Transact-SQL](sql-database-transact-sql-information.md) . Se está a mover uma solução existente do SQL Server para a nuvem, consulte o artigo [migrar uma base de dados do SQL Server para a base de dados do SQL Azure](sql-database-cloud-migrate.md). Quando migrar uma aplicação do SQL Server no local existente para a base de dados SQL, considere atualizar a aplicação para tirar partido das capacidades de oferta de serviços em nuvem. Por exemplo, pode considerar utilizar o [Serviço do Azure Web App](https://azure.microsoft.com/services/app-service/web/) ou [Serviços em nuvem Azure](https://azure.microsoft.com/services/cloud-services/) para alojar o seu camada de aplicação para aumentar as vantagens de custo.

### <a name="administration"></a>Administração

Para muitas empresas, a decisão de transição para um serviço na nuvem é muito sobre descarregar complexidade da administração tal como é custo. Com a **Base de dados SQL**, Microsoft instituição o hardware subjacente. Microsoft automaticamente replica todos os dados para fornecer elevada disponibilidade, configura e aplicar o software de base de dados, gere balanceamento de carga e não activação pós-falha transparente se existe uma falha do servidor. Pode continuar a administrar a sua base de dados, mas que não precisa para gerir o motor de base de dados, o sistema operativo do servidor ou o hardware.  Exemplos de itens, que pode continuar a administrar incluem as bases de dados e inícios de sessão, índice e optimização da consulta e auditoria e segurança.

Com o **SQL Server no Azure VMs**, tem de controlo total sobre o sistema operativo e configuração da instância do SQL Server. Com uma VM, é para que possa decidir quando deve/actualizar o sistema operativo e o software de base de dados e quando deve instalar qualquer software adicional, tal como anti-virus. Algumas funcionalidades automatizadas são fornecidas para simplificar significativamente a disponibilidade de patch, cópia de segurança e elevada. Além disso, pode controlar o tamanho da VM, o número de discos e suas configurações de armazenamento. Azure permite-lhe alterar o tamanho de uma VM conforme necessário. Para obter informações, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure](../virtual-machines/virtual-machines-linux-sizes.md). 

### <a name="service-level-agreement-sla"></a>Contrato de nível de serviço (SLA)

Para os vários departamentos de TI, hora de cima obrigações de um contrato de nível do serviço (SLA) da reunião é uma prioridade superior. Nesta secção, podemos veja o que SLA aplica-se para cada base de dados que aloja a opção.

Para **Base de dados SQL** Basic, padrão e Premium camadas de serviços Microsoft fornece uma disponibilidade SLA de 99,99%. Para informações mais recentes, consulte o [Contrato de nível de serviço](https://azure.microsoft.com/support/legal/sla/sql-database/). Para informações mais recentes no camadas de serviços de base de dados SQL e os planos de continuidade do negócio suportados, consulte o artigo [Camadas de serviços](sql-database-service-tiers.md).

Para o **SQL Server em execução no Azure VMs**, a Microsoft fornece uma disponibilidade SLA de % 99.95 que abrange apenas a Máquina Virtual. Este SLA não aborda os processos (como SQL Server) em execução na VM e requer que aloja, pelo menos, duas instâncias VM num conjunto de disponibilidade. Para informações mais recentes, consulte o [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para base de dados elevada disponibilidade (HA) dentro VMs, deve configurar uma das opções de disponibilidade de alta suportados no SQL Server, tal como [Grupos de Disponibilidade AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Utilizar uma opção suportada elevada disponibilidade não fornece uma SLA adicional, mas permite-lhe alcançar > 99,99% de disponibilidade de base de dados.

### <a name="market"></a>Tempo de mercado

**Base de dados SQL** é a solução direita para aplicações concebidas nuvem quando produtividade do programador e rápido tempo para mercado são críticas. Com uma funcionalidade semelhante DBA programação, é perfeito para arquitectura na nuvem e os programadores como diminui a necessidade de gerir o sistema operativo e base de dados subjacentes. Por exemplo, pode utilizar a [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) e [Os cmdlets do PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) para automatizar e gerir operações administrativas para milhares de bases de dados. Funcionalidades como [Flexível agrupamentos de base de dados](sql-database-elastic-pool.md) permitem-lhe concentrar-na camada de aplicação e transmitir a sua solução no mercado mais rápido.

**SQL Server em execução no Azure VMs** é perfeito se as aplicações existentes ou novas necessitam de acesso a todas as funcionalidades no SQL Server ou no Windows, bases de dados interligadas ou grandes bases de dados. Também é uma boa opção quando quiser migrar existente no local aplicações e bases de dados para Azure como-é. Uma vez que não precisar de alterar a apresentação, de aplicações e camadas de dados, pode poupa tempo e de orçamento no rearchitecting a solução existente. Em vez disso, pode concentrar-se sobre como migrar todas as suas soluções para Azure e ao fazê-lo algumas otimizações de desempenho que poderão ser necessário pela plataforma do Azure. Para mais informações, consulte o artigo [Desempenho melhores práticas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Resumo

Este artigo explorou base de dados SQL e o SQL Server em máquinas virtuais do Azure (VMs) e outros fabricantes referidos motivators de negócio comuns que afetam a sua decisão. Segue-se um resumo das sugestões de a ter em conta:

Selecione a **base de dados Azure SQL** se:

- Está a criar novas aplicações baseadas na nuvem para tirar partido das poupanças de custos e Otimização do desempenho que serviços em nuvem fornecem. Esta abordagem fornece as vantagens de um serviço na nuvem totalmente geridas, ajuda-o a inferior tempo-para-mercado inicial e pode fornecer a longo prazo otimização de custo.

- Pretende que o Microsoft executar operações de gestão comuns nas bases de dados e exigir mais forte disponibilidade SLA para bases de dados.



Escolha o **SQL Server no Azure VMs** se:

- Tiver aplicações existentes no local que pretende migrar ou expandir na nuvem, ou se pretender criar aplicações empresariais maiores do que 1 TB. Esta abordagem fornece o benefício de compatibilidade SQL de 100%, a capacidade de base de dados grandes, controlo total sobre o SQL Server e Windows e seguro túnel para no local. Esta abordagem minimiza os custos para o desenvolvimento e modificações das aplicações existentes.

- Possui recursos de TI existentes e, finalmente, podem ser proprietários de aplicação de patches, cópias de segurança e disponibilidade de alto de base de dados. Tenha em atenção que algumas funcionalidades automatizadas simplificam significativamente estas operações. 


## <a name="next-steps"></a>Próximos passos
- Consulte o artigo [tutorial de base de dados SQL: criar uma base de dados do SQL no minutos através do portal Azure](sql-database-get-started.md) para começar a base de dados SQL.
- Consulte o artigo [preços de base de dados SQL] (https://azure.microsoft.com/pricing/details/sql-database/).
- Consulte o artigo [aprovisionar uma máquina de virtual do SQL Server no Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) para começar a utilizar com o SQL Server no Azure VMs.
- Consulte o artigo [do SQL Server num computador Virtual Azure: caminho de formação](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).
