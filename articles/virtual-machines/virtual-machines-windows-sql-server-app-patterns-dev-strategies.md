<properties
    pageTitle="Os padrões de aplicação do SQL Server de VMs | Microsoft Azure"
    description="Este artigo abrange os padrões de aplicação para o SQL Server no Azure VMs. Fornece arquitectura solução tanto os programadores um foundation para arquitetura de aplicação boa e estrutura."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Padrões de aplicação e estratégias de desenvolvimento para SQL Server em máquinas virtuais do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>Resumo:
Para determinar qual padrão de aplicação ou padrões para utilizar para as suas aplicações baseadas no servidor SQL no Azure ambiente é uma decisão estrutura importantes e requer uma cor sólida compreensão das como SQL Server e cada componente de infraestrutura do Azure funcionam em conjunto. Com o SQL Server nos serviços de infraestrutura do Azure, pode facilmente migrar, manter e monitorizar as aplicações do SQL Server existentes criadas no Windows Server para máquinas virtuais no Azure.

O objetivo deste artigo é fornecer arquitectura solução e os programadores um foundation arquitetura de aplicação boa e estrutura, que pode seguir a quando estiver a migrar aplicações existentes para Azure, bem como desenvolver aplicações novas no Azure.

Para cada padrão de aplicação, irá encontrar um cenário no local, sua solução respetivas com capacidade de nuvem e as recomendações técnicas relacionadas. Além disso, o artigo aborda estratégias de desenvolvimento do Azure específicas, de modo a que possa estruturar as aplicações corretamente. Devido a vários padrões de aplicação possíveis, é recomendável que arquitectura tanto os programadores deverão escolher o padrão de mais adequado para as suas aplicações e utilizadores.

**Contribuintes técnicos:** Luís Carlos Martins arenque, Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey areeiros, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Frederico Schackow, Miguel Hickey, Miguel Wieman, Xin Jin

## <a name="introduction"></a>Introdução

Pode desenvolver muitos tipos de aplicações de camadas, separando os componentes das camadas aplicação diferente em computadores diferentes, assim como nos componentes em separado. Por exemplo, pode colocar a aplicação de cliente e empresas regras componentes de uma máquina, camada web front-end e componentes de camada de acesso de dados no outro computador e uma camada de base de dados back-end no outro computador. Este tipo de estruturação ajuda isolar cada camada uns dos outros. Se mudar de onde vêm dados, não precisa de alterar a aplicação de cliente ou web mas apenas os componentes de níveis de acesso dados.

Uma aplicação de típica *camadas* inclui a camada de apresentação, a camada de negócio e a camada de dados:


| Camada              | Descrição                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Apresentação** | A *camada de apresentação* (camada web, camada front-end) é a camada na qual os utilizadores interagir com uma aplicação.                                                                      |
| **Empresas**     | A *camada de negócio* (camada) é a camada que a apresentação camada e a camada de dados utilizar para comunicar com os outros e inclui as funcionalidades principais do sistema. |
| **Dados**         | A *camada de dados* é basicamente o servidor que armazena dados de uma aplicação (por exemplo, um servidor a executar o SQL Server).                                                             |

Camadas de aplicação descrevem os lógicos agrupamentos da funcionalidade e os componentes de uma aplicação; enquanto que camadas descrevem a distribuição física da funcionalidade e componentes no separado servidores físicos, computadores, redes ou localizações remotas. As camadas de uma aplicação poderão residem no mesmo computador físico (a mesma camada) ou podem ser distribuídas ao longo do computadores separados (n-camada) e os componentes de cada camada comunicam com componentes em outras camadas através de interfaces bem definidas. Poderá pensar da camada termos como se referir à padrões de distribuição física como duas camadas, três camadas e camadas. Um **padrão de aplicação de 2 camadas** contém duas camadas de aplicação: application server e o servidor de base de dados. Comunicação direta acontece entre o servidor da aplicação e o servidor de base de dados. O servidor da aplicação contém componentes web camadas e camadas de negócio. **Padrão de aplicação de 3-camadas**, existem três camadas de aplicação: servidor, servidor de aplicações, que contém a camada de lógica de negócio e/ou componentes do access de dados do business camada e o servidor de base de dados da web. A comunicação entre o servidor web e no servidor de base de dados acontece ao longo do servidor da aplicação. Para obter informações detalhadas sobre camadas e camadas de aplicação, consulte o artigo [Guia de arquitectura de aplicações do Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de iniciar ao ler este artigo, deve possuir um conhecimento os conceitos fundamentais do SQL Server e Azure. Para obter informações, consulte o artigo [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e [Azure.com](https://azure.microsoft.com/).

Este artigo descreve várias padrões de aplicação que podem ser adequados para as aplicações simples, bem como as aplicações de empresarial altamente complexos. Antes de com detalhes sobre cada padrão, recomendamos que deverá familiarizar-se com os serviços de armazenamento de dados disponíveis no Azure, como o [Armazenamento do Windows Azure](../storage/storage-introduction.md), [Base de dados do SQL Azure](../sql-database/sql-database-technical-overview.md)e [SQL Server uma Máquina Virtual no Azure](virtual-machines-windows-sql-server-iaas-overview.md). Para tornar as melhores decisões de estrutura para as suas aplicações, compreenda quando utilizar claramente qual serviço de armazenamento de dados.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Escolher o SQL Server numa máquina Virtual Azure, quando:

- Precisa de controlo no SQL Server e Windows. Por exemplo, podem incluir a versão do SQL Server, correcções especiais, configuração de desempenho, etc.

- Precisar de uma compatibilidade total com SQL Server no local e pretende mover aplicações existentes para Azure como-é.

- Pretende tirar partido das capacidades do ambiente do Azure mas base de dados do Azure SQL não suporta todas as funcionalidades que requer a aplicação. Isto pode incluir as seguintes áreas:

    - **Tamanho da base de dados**: momento este artigo foi actualizado, base de dados SQL suporta uma base de dados de até 1 TB de dados. Se a sua aplicação requer mais do que 1 TB de dados e não pretende implementar soluções sharding personalizado, é recomendado que utiliza o SQL Server uma Máquina Virtual no Azure. Para informações mais recentes, consulte o artigo [Dimensionamento saída Azure SQL bases de dados](https://msdn.microsoft.com/library/azure/dn495641.aspx) e [camadas de serviços de base de dados de SQL Azure e níveis de desempenho](../sql-database/sql-database-service-tiers.md).
    - **Conformidade HIPAA**: clientes cuidados de saúde e independentes Software MVPs poderão escolher o [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) em vez de [Base de dados do SQL Azure](../sql-database/sql-database-technical-overview.md) porque o SQL Server uma Máquina Virtual no Azure é abrangido pelo HIPAA empresas associar contrato (BAA). Para obter informações sobre a conformidade, consulte o artigo [Microsoft Azure Trust Center: conformidade](https://azure.microsoft.com/support/trust-center/compliance/).
    - **Funcionalidades de instância nível**: neste momento, base de dados SQL não suporta funcionalidades que live fora da base de dados (tais como servidores ligadas, agente de tarefas, FileStream, corretor de serviço, etc.). Para obter mais informações, consulte [diretrizes de base de dados do SQL Azure e limitações](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>camadas de 1 (simples): única máquina virtual

No padrão aplicação, implementar a aplicação do SQL Server e a base de dados para uma máquina de virtual autónomo no Azure. Mesma máquina virtual contém a aplicação de cliente/web, componentes de negócio, camada de acesso a dados e o servidor de base de dados. A apresentação, empresas e o código de acesso de dados são separados logicamente mas física estão localizados numa máquina de servidor único. A maioria dos clientes iniciar com este padrão de aplicação e, em seguida, eles Dimensionar saída ao adicionar mais funções da web ou máquinas virtuais ao seu sistema.

Este padrão de aplicação é útil quando:

- Pretende realizar uma migração de simple para plataforma Azure avaliar se a plataforma responde a requisitos da sua aplicação ou não.

- Pretende manter todas as camadas de aplicação alojadas no mesmo computador virtual no Centro de dados Azure mesmo para reduzir a latência entre camadas.

- Pretende rapidamente aprovisionar desenvolvimento e testar o ambientes para períodos de tempo curtos.

- Que pretende efetuar limite testes de vários níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietário e manter muitas máquinas físicas sempre.

O diagrama seguinte demonstra uma alteração simples no local cenário e como pode implementar a sua solução nuvem ativada numa única máquina virtual no Azure.

![padrão de aplicação de camadas de 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Implementar a camada de negócio (lógica empresarial e aceder a componentes de dados) na mesma camada física como a camada de apresentação pode maximizar o desempenho da aplicação, a menos que tem de utilizar uma camada separada devido a preocupações escalabilidade ou segurança.

Uma vez que este é um padrão muito comum para começar, poderá ser útil o seguinte artigo migração para mover os seus dados para o seu VM de servidor de SQL: [migrar uma base de dados para o SQL Server numa VM Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-camada (simples): várias máquinas virtuais

Este padrão de aplicação, implementar uma aplicação de 3 camadas no Azure ao colocar cada camada de aplicação numa máquina virtual diferente. Isto fornece um ambiente flexível para uma fácil cenários de escala de segurança e escala-out. Quando uma máquina virtual contém a sua aplicação de cliente/web, outros aquele aloja os componentes de negócio e outros aquele aloja o servidor de base de dados.

Este padrão de aplicação é útil quando:

- Que pretende executar uma migração de aplicações de base de dados complexos para máquinas virtuais do Azure.

- Camadas de aplicação diferente à alojado em diferentes regiões que pretende. Por exemplo, poderá ter partilhado bases de dados que são implementados em várias regiões para efeitos de relatórios.

- Que pretende mover aplicações empresariais de plataformas no local foi virtualizado para máquinas virtuais do Azure. Para mais informações detalhadas sobre aplicações empresariais, consulte o artigo [o que é uma aplicação empresarial](https://msdn.microsoft.com/library/aa267045.aspx).

- Pretende rapidamente aprovisionar desenvolvimento e testar o ambientes para períodos de tempo curtos.

- Que pretende efetuar limite testes de vários níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietário e manter muitas máquinas físicas sempre.

O diagrama seguinte demonstra como pode colocar uma aplicação de 3 camadas simples no Azure ao colocar cada camada de aplicação numa máquina virtual diferente.

![padrão de aplicação de 3-camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Este padrão de aplicação, existe apenas uma máquina virtual (VM) em cada camada. Se tiver várias VMs no Azure, recomendamos que configurar uma rede virtual. [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) cria um limite de segurança fidedignos e também lhe permite VMs comunicar entre si através do endereço IP privado. Além disso, certifique-se sempre que todas as ligações de Internet apenas Ir para a camada de apresentação. Quando seguir este padrão de aplicação, gerir as regras de grupo de segurança de rede para controlar o acesso. Para obter mais informações, consulte [Permitir o acesso externo ao seu VM através do portal Azure](virtual-machines-windows-nsg-quickstart-portal.md).

No diagrama, protocolos Internet pode ser TCP, UDP, HTTP ou HTTPS.

>[AZURE.NOTE] Configurar uma rede virtual no Azure é gratuito. No entanto, que lhe ser cobrada para o gateway VPN que se liga no local. Este encargo baseia-se a quantidade de tempo que a ligação é aprovisionada e está disponível.

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>camada de 2 e 3-camada, com camadas de apresentação escala de saída

Este padrão de aplicação, implementar a aplicação de base de dados de camada de 2 ou 3-camada para máquinas virtuais do Azure ao colocar cada camada de aplicação numa máquina virtual diferente. Além disso, dimensionar saída a camada de apresentação devido ao aumento do volume de pedidos recebidos de cliente.

Este padrão de aplicação é útil quando:

- Que pretende mover aplicações empresariais de plataformas no local foi virtualizado para máquinas virtuais do Azure.

- Que quer Dimensionar saída a camada de apresentação devido ao aumento do volume de pedidos recebidos de cliente.

- Pretende rapidamente aprovisionar desenvolvimento e testar o ambientes para períodos de tempo curtos.

- Que pretende efetuar limite testes de vários níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietário e manter muitas máquinas físicas sempre.

- Pretende proprietário de um ambiente de infraestrutura pode dimensionar para cima e para baixo a pedido.

O diagrama seguinte demonstra como pode colocar as camadas de aplicação no várias máquinas virtuais no Azure ao escalar para fora da camada de apresentação devido ao aumento do volume de pedidos recebidos de cliente. Conforme visto no diagrama, Balanceador de carga Azure é responsável por distribuir o tráfego através de várias máquinas virtuais e também para determinar qual o servidor web para ligar à. Está a ter várias instâncias dos servidores web atrás de um balanceador de carga garante a disponibilidade da camada apresentação elevada.

![Padrão de aplicação - escala de camada de apresentação saída](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Práticas recomendadas para padrões camada de 2, 3 camada ou camadas no que têm múltiplas VMs uma camada

É recomendado que coloque as máquinas virtuais que pertencem a mesma camada no serviço na nuvem mesmo e na mesma a disponibilidade definir. Por exemplo, coloque um conjunto de servidores web na **CloudService1** e **AvailabilitySet1** e um conjunto de servidores de base de dados no **CloudService2** e **AvailabilitySet2**. Disponibilidade de definir no Azure permite-lhe colocar os nós de elevada disponibilidade domínios falhas separadas e actualizar domínios.

Para tirar partido várias instâncias VM de uma camada, tem de configurar Balanceador de carga Azure entre camadas de aplicação. Para configurar Balanceador de carga em cada camada, crie um ponto final de balanceamento de carga em VMs cada camada separadamente. Para uma camada específica, crie VMs no serviço de nuvem do mesmo. Este procedimento garante que têm o mesmo endereço Virtual IP público. Em seguida, crie um ponto final de uma das máquinas virtuais nessa camada. Em seguida, atribua o ponto final da mesma para as outras máquinas virtuais essa camada para balanceamento de carga. Ao criar um conjunto de balanceamento de carga, distribuir o tráfego entre várias máquinas virtuais e também lhe permitem Balanceador de carga determinar qual o nó para estabelecer ligação quando um nó VM back-end falhar. Por exemplo, ter várias instâncias dos servidores web atrás de um balanceador de carga garante a disponibilidade da camada apresentação elevada.

Como prática recomendada, certifique-se sempre que todas as ligações de internet de aceder primeiro à camada de apresentação. A camada de apresentação acede a camada de negócio e, em seguida, a camada de empresas acede a camada de dados. Para obter mais informações sobre como permitir o acesso à camada de apresentação, consulte [Permitir o acesso externo ao seu VM através do portal Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Tenha em atenção que Balanceador de carga no Azure funciona semelhante ao carregar balanceadores num ambiente no local. Para mais informações, consulte o artigo [balanceamento de carga para serviços de infraestrutura Azure](virtual-machines-windows-load-balance.md).

Além disso, recomendamos que configurou uma rede privada para máquinas virtuais utilizando rede Virtual Azure. Esta opção permite-lhes comunicar entre si através do endereço IP privado. Para mais informações, consulte o artigo [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>camada de 2 e 3-camada, com camadas de empresas escala de saída

Este padrão de aplicação, implementar a aplicação de base de dados de camada de 2 ou 3-camada para máquinas virtuais do Azure ao colocar cada camada de aplicação numa máquina virtual diferente. Além disso, poderá pretender distribuir os componentes de servidor de aplicação para várias máquinas virtuais devido a complexidade da sua aplicação.

Este padrão de aplicação é útil quando:

- Que pretende mover aplicações empresariais de plataformas no local foi virtualizado para máquinas virtuais do Azure.

- Pretende distribuir os componentes de servidor de aplicação para várias máquinas virtuais devido a complexidade da sua aplicação.

- Que pretende mover grossa de lógica de negócio no local aplicações de (linha de negócio) LOB para máquinas virtuais do Azure. Aplicações LOB são um conjunto de aplicações crítico do computador que são o essencial para executar uma empresa, tais como contabilidade, recursos humanos (HR), folha de pagamentos, gestão da cadeia de fornecimento e aplicações de planeamento de recursos.

- Pretende rapidamente aprovisionar desenvolvimento e testar o ambientes para períodos de tempo curtos.

- Que pretende efetuar limite testes de vários níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietário e manter muitas máquinas físicas sempre.

- Pretende proprietário de um ambiente de infraestrutura pode dimensionar para cima e para baixo a pedido.

O diagrama seguinte demonstra a um cenário no local e a sua solução nuvem ativada. Neste cenário, coloque as camadas de aplicação no várias máquinas virtuais no Azure por escalar para fora a camada de empresas, que contém a camada de lógica de negócio e dados componentes do access. Conforme visto no diagrama, Balanceador de carga Azure é responsável por distribuir o tráfego através de várias máquinas virtuais e também para determinar qual o servidor web para ligar à. Está a ter várias instâncias dos servidores de aplicações atrás de um balanceador de carga garante a disponibilidade de alta da camada empresas. Para mais informações, consulte o artigo [melhores práticas para a camada de 2, 3-níveis ou padrões de aplicação de n camadas que tenham várias máquinas virtuais numa camada](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Padrão de aplicação com escala de camada empresas saída](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>camadas de 2 e 3-camada com apresentação e camadas de empresas escala de saída e HADR

Este padrão de aplicação, implementar a aplicação de base de dados de camada de 2 ou 3-camada para máquinas virtuais do Azure através da distribuição a camada de apresentação (servidor web) e os componentes de (servidor de aplicação) camada de negócio para várias máquinas virtuais. Além disso, é implementar soluções de recuperação de alta disponibilidade e falhas para as bases de dados em máquinas virtuais do Azure.

Este padrão de aplicação é útil quando:

- Que pretende mover aplicações empresariais de plataformas virtualizado no local para Azure através da implementação de elevada disponibilidade do SQL Server e capacidades de recuperação de falhas.

- Que quer dimensionar a camada de apresentação e a camada de empresas devido ao aumento do volume de pedidos recebidos de clientes e complexidade da aplicação.

- Pretende rapidamente aprovisionar desenvolvimento e testar o ambientes para períodos de tempo curtos.

- Que pretende efetuar limite testes de vários níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietário e manter muitas máquinas físicas sempre.

- Pretende proprietário de um ambiente de infraestrutura pode dimensionar para cima e para baixo a pedido.

O diagrama seguinte demonstra a um cenário no local e a sua solução nuvem ativada. Neste cenário, dimensionar a camada de apresentação e os componentes de camada de negócio em máquinas virtuais do vários no Azure. Além disso, implementar alta disponibilidade e falhas (HADR) técnicas de recuperação para bases de dados do SQL Server Azure.

Executar várias cópias de uma aplicação no diferentes VMs Certifique-se de que estão balanceamento de carga pedidos através de lhes. Quando tiver várias máquinas virtuais, é necessário para se certificar de que todos os seus VMs estão acessíveis e a ser executado em uma ponto altura. Se configurar balanceamento de carga, Balanceador de carga Azure controla o estado de funcionamento da VMs e encaminha as chamadas recebidas para os nós VM saudáveis funcionais corretamente. Para obter informações sobre como configurar o balanceamento de carga das máquinas virtuais, consulte o artigo [balanceamento de carga para serviços de infraestrutura Azure](virtual-machines-windows-load-balance.md). Está a ter várias instâncias de servidores web e aplicação atrás de um balanceador de carga garante a disponibilidade das camadas apresentação e empresas alta.

![Escala de saída e elevada disponibilidade](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Práticas recomendadas para os padrões de aplicação que exige o HADR de SQL

Quando configurar soluções de recuperação de falhas em máquinas virtuais do Azure e disponibilidade de elevada do SQL Server, configurar uma rede virtual para máquinas virtuais utilizando [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) é obrigatório.  Máquinas virtuais dentro de uma rede Virtual terá um endereço IP privado estável mesmo após uma interrupção de serviço, pelo que pode evitar o tempo de atualização é necessário para a resolução do nome DNS. Além disso, a rede virtual permite-lhe expandir a sua rede no local para Azure e cria um limite de segurança fidedigna. Por exemplo, se a sua aplicação tiver restrições de domínio empresarial (como autenticação do Windows, do Active Directory), configurar a [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) é necessário.

Maior parte dos clientes, o que estiver a executar o código de produção no Azure, é manter réplicas principais e secundárias no Azure.

Para informações completas e tutoriais no elevada disponibilidade e técnicas de recuperação de falhas, consulte [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>camadas de 2 e 3-camada utilizando Azure VMs e serviços em nuvem

No padrão aplicação, implementa camada de 2 ou 3-camada aplicação Azure utilizando ambos os [Serviços em nuvem Azure](../cloud-services/cloud-services-choose-me.md#tellmecs) (web e trabalhador funções - plataforma como um serviço (PaaS)) e [máquinas virtuais do Azure](virtual-machines-windows-about.md) (infraestrutura como um serviço (IaaS)). Utilizar o [Azure serviços em nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para a camada de camada/empresas de apresentação e o SQL Server em [máquinas virtuais do Azure](virtual-machines-windows-about.md) para a camada de dados é útil para a maioria das aplicações em execução no Azure. O motivo é que está a ter uma instância de cluster em execução no serviços em nuvem fornece uma gestão fácil, implementação, monitorização e escala de saída.

Serviços em nuvem, Azure mantém a infraestrutura de para si, executa a manutenção de rotina, correcções os sistemas operativos e tenta recuperar a partir do serviço e hardware falhas. Quando a aplicação necessita de escala de saída, opções de saída escala manual e automáticas estão disponíveis para o seu projeto do serviço de nuvem ao aumentar ou diminuir o número de instâncias ou máquinas virtuais que são utilizadas pela aplicação. Além disso, pode utilizar no local Visual Studio para implementar a aplicação a um projeto de serviço de nuvem no Azure.

Em resumo, se não pretender proprietário extenso tarefas administrativas para apresentação/empresas camada e a aplicação não necessitam de qualquer configuração complexa de software ou o sistema operativo, utilize serviços em nuvem Azure. Se a base de dados do Azure SQL não suporta todas as funcionalidades que procura, utilize do SQL Server uma Máquina Virtual no Azure para a camada de dados. Executar uma aplicação no Azure serviços em nuvem e armazenar os dados em máquinas virtuais do Azure combina as vantagens de ambos os serviços. Para uma comparação detalhada, consulte a secção neste tópico no [estratégias de desenvolvimento comparar no Azure](#comparing-web-development-strategies-in-azure).

No padrão aplicação, a camada de apresentação inclui uma função da web, que é um componente de serviços em nuvem em execução no ambiente de execução Azure e são personalizada para a programação de aplicações web como suportadas pelo IIS e ASP.NET. A camada de negócio ou back-end inclui uma função de trabalho, que é um componente de serviços em nuvem em execução no ambiente de execução Azure e é útil para o desenvolvimento GRG e pode executar processamento em segundo plano para uma função da web. A camada de base de dados encontra-se numa máquina de virtual do SQL Server no Azure. A comunicação entre a camada de apresentação e a camada de base de dados acontece diretamente ou sobre a camada de negócio – componentes de função de trabalho.

Este padrão de aplicação é útil quando:

- Que pretende mover aplicações empresariais de plataformas virtualizado no local para Azure através da implementação de elevada disponibilidade do SQL Server e capacidades de recuperação de falhas.

- Pretende proprietário de um ambiente de infraestrutura pode dimensionar para cima e para baixo a pedido.

- Base de dados SQL Azure não suporta todas as funcionalidades que necessita de ser sua aplicação ou base de dados.

- Que pretende efetuar limite testes de vários níveis de carga de trabalho, mas ao mesmo tempo que não pretende proprietário e manter muitas máquinas físicas sempre.

O diagrama seguinte demonstra a um cenário no local e a sua solução nuvem ativada. Neste cenário, coloque a camada de apresentação em funções da web, a camada de negócio em funções de trabalho, mas a camada de dados em máquinas virtuais no Azure. Executar várias cópias da camada apresentação nas funções da web diferente assegura para carregar os pedidos de saldo através de lhes. Quando combinar serviços em nuvem Azure com máquinas virtuais do Azure, recomendamos que configurou [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) também. Com a [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md), pode ter persistentes e estáveis endereços IP privados dentro do serviço na nuvem mesmo na nuvem. Depois de definir uma rede virtual para máquinas virtuais e dos serviços em nuvem, pode começar a comunicar entre si sobre o endereço IP privado. Além disso, tendo máquinas virtuais e funções web/trabalhador Azure na mesma [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) fornece latência baixa e conectividade mais segura. Para mais informações, consulte o artigo [o que é um serviço na nuvem](../cloud-services/cloud-services-choose-me.md).

Conforme visto no diagrama, Balanceador de carga Azure distribui o tráfego pelos várias máquinas virtuais e também determina quais servidor web ou application server para ligar a. Está a ter várias instâncias dos servidores web e de aplicação atrás de um balanceador de carga garante a disponibilidade de alta da camada de apresentação e a camada de empresas. Para mais informações, consulte o artigo [melhores práticas para padrões de aplicação que exige o HADR de SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Padrões de aplicação, serviços em nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Outra abordagem para implementar o padrão esta aplicação é utilizar uma função de web consolidados contém camada de apresentação e componentes de camada business conforme mostrado no seguinte diagrama. Este padrão de aplicação é útil para as aplicações que requerem estrutura com estado. Dado que Azure fornece nós de cluster sem estado nas funções da web e trabalhador, recomendamos que implementar uma lógica para armazenar o estado da sessão utilizando um dos seguintes tecnologias: [Azure colocação em cache](https://azure.microsoft.com/documentation/services/redis-cache/), [Armazenamento de tabela do Azure](../storage/storage-dotnet-how-to-use-tables.md) ou [Base de dados do SQL Azure](../sql-database/sql-database-technical-overview.md).

![Padrões de aplicação, serviços em nuvem](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Padrão com Azure VMs, base de dados Azure SQL e Azure de aplicação de serviço (Web Apps)

O objetivo principal do padrão aplicação é mostrar-lhe como combinar infraestrutura Azure como um componentes de serviço (IaaS) com Azure componentes plataforma-como-a-service (PaaS) na sua solução. Este padrão é focado numa base de dados do Azure SQL para o armazenamento de dados relacionais. -Não inclui o SQL Server numa máquina virtual Azure, que faz parte infraestrutura do Azure como uma oferta de serviço.

Este padrão de aplicação, implementar uma aplicação de base de dados para Azure colocando as camadas apresentação e empresas na mesma máquina virtual e aceder a uma base de dados em servidores de base de dados do Azure SQL (base de dados SQL). Pode implementar a camada de apresentação utilizando soluções web baseadas em IIS tradicional. Em alternativa, pode implementar uma apresentação combinada e camadas de negócio ao utilizar o [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Este padrão de aplicação é útil quando:

- Já tem um servidor de base de dados SQL existente configurado no Azure e que pretende testar a sua aplicação rapidamente.

- Que pretende testar as funcionalidades do ambiente do Azure.

- Pretende rapidamente aprovisionar desenvolvimento e testar o ambientes para períodos de tempo curtos.

- Os componentes de acesso de lógica e dados empresas podem ser personalizada contidos dentro de uma aplicação web.

O diagrama seguinte demonstra a um cenário no local e a sua solução nuvem ativada. Neste cenário, coloque as camadas de aplicação numa única máquina virtual no Azure e aceder aos dados na base de dados do SQL Azure.

![Padrão de aplicação misto](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Se optar por implementar uma web combinado e camada de aplicação utilizando Azure Web Apps, recomendamos que mantenha a camada camada ou da aplicação como bibliotecas de ligação dinâmica (DLL) no contexto de uma aplicação web.

Além disso, reveja as recomendações indicadas na secção de [Comparar estratégias de desenvolvimento web no Azure](#comparing-web-development-strategies-in-azure) no final deste artigo para saber mais sobre técnicas de programação.

## <a name="n-tier-hybrid-application-pattern"></a>Padrão de aplicação híbrida de camadas

No padrão de aplicação híbrida de camadas, implementar a aplicação em várias camadas distribuído entre no local e Azure. Por conseguinte, cria um sistema de híbrido flexível e reutilizável, que pode modificar ou adicionar uma camada específica sem alterar das outras camadas. Para alargar a rede da sua empresa na nuvem, utilize o serviço de [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) .

Este padrão de aplicação híbrida é útil quando:

- Pretende criar aplicações que são executadas parcialmente na nuvem e parcialmente no local.

- Que pretende migrar alguns ou todos os elementos de uma aplicação no local existente para a nuvem.

- Que pretende mover aplicações empresariais de plataformas no local foi virtualizado para Azure.

- Pretende proprietário de um ambiente de infraestrutura pode dimensionar para cima e para baixo a pedido.

- Pretende rapidamente aprovisionar desenvolvimento e testar o ambientes para períodos de tempo curtos.

- Uma forma rentável tirar cópias de segurança para aplicações de base de dados empresariais que pretende.

O diagrama seguinte demonstra um padrão de aplicação de camadas híbrido abrange no local e Azure. Como é mostrado no diagrama, infraestrutura no local inclui controlador de domínio do [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) para suportar a autenticação de utilizador e autorização. Tenha em atenção que o diagrama demonstra um cenário, onde algumas partes da camada dados residem num centro de dados no local Considerando que algumas partes da camada dados live no Azure. Dependendo necessidades da sua aplicação, pode implementar várias outras situações híbrido. Por exemplo, pode manter a camada de apresentação e a camada de negócio num ambiente no local, mas a camada de dados no Azure.

![Padrão de aplicação de camadas](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

No Azure, pode utilizar o Active Directory como um diretório de nuvem autónomo para a sua organização ou, também pode integrar o existente no local do Active Directory com o [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Conforme visto no diagrama, os componentes de camada empresas podem aceder a várias origens de dados, tais como para [SQL Server no Azure](virtual-machines-windows-sql-server-iaas-overview.md) através de um endereço IP interno privado, para de SQL Server no local através da [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)ou para a [Base de dados SQL](../sql-database/sql-database-technical-overview.md) utilizando as tecnologias de fornecedor de dados .NET Framework. Neste diagrama, base de dados do SQL Azure é um serviço de armazenamento de dados opcionais.

No padrão de aplicação híbrida de camadas, pode implementar o fluxo de trabalho seguinte na ordem especificada:

1. Identifique aplicações de base de dados empresariais que precisam de ser movido para cima para o cloud utilizando o [Microsoft avaliação e Toolkit de planeamento (mapa de)](http://microsoft.com/map). O Toolkit de mapa reúne inventário e dados de desempenho de computadores que esteja a considerar para virtualização e fornece recomendações sobre capacidade e planeamento de avaliação.

1. Planear a recursos e configuração necessária no plataforma do Azure, tais como contas de armazenamento e máquinas virtuais.

1. Configure a conectividade da rede entre a rede da empresa no local e a [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md). Para configurar a ligação entre o rede da empresa no local e uma máquina virtual no Azure, utilize um dos seguintes dois métodos:

    1. Estabelecer uma ligação entre no local e Azure através de pontos finais públicos numa máquina virtual no Azure. Este método fornece uma configuração fácil e permite-lhe utilizar autenticação do SQL Server no seu computador virtual. Além disso, configurar o seu regras de grupo de segurança de rede para controlar o tráfego público para a VM. Para obter mais informações, consulte [Permitir o acesso externo ao seu VM através do portal Azure](virtual-machines-windows-nsg-quickstart-portal.md).

    1. Estabelecer uma ligação entre no local e Azure através do Azure Virtual Private túnel de rede (VPN). Este método permite-lhe expandir políticas de domínio para uma máquina virtual no Azure. Além disso, pode configurar regras de firewall e utilize a autenticação do Windows no seu máquina virtual. Atualmente, o Azure suporta VPN segura do site para o site e as ligações de VPN ponto-para-site:

        - Com a ligação de site para o site segura, pode estabelecer conectividade de rede entre a sua rede no local e a sua rede virtual no Azure. Recomenda-se para ligar o seu ambiente do Centro de dados no local à Azure.

        - Com uma ligação ponto-para-site segura, pode estabelecer conectividade de rede entre a rede virtual no Azure e os computadores individuais em execução em qualquer lugar. Recomenda-se principalmente para fins de desenvolvimento e teste.

    Para obter informações sobre como ligar ao SQL Server no Azure, consulte o artigo [ligar a uma SQL Server Máquina Virtual no Azure](virtual-machines-windows-classic-sql-connect.md).

1. Configure alertas que agregar dados no local no disco máquina virtual no Azure e as tarefas agendadas. Para mais informações, consulte o artigo [cópia de segurança do SQL Server e restaurar com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx) e [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

1. Dependendo necessidades da sua aplicação, que possa implementar um dos seguintes três cenários comuns:

    1. Pode manter o servidor web, servidor de aplicações e dados um servidor de base de dados no Azure Considerando que manter o dados sensíveis a maiúsculas e no local.

    1. Pode manter o seu servidor web e do application server no local Considerando que o servidor de base de dados numa máquina virtual no Azure.

    1. Pode manter o seu servidor de base de dados, o servidor web e do application server no local Considerando que manter as réplicas de base de dados em máquinas virtuais no Azure. Esta definição permite a servidores no local da web ou aplicações de relatório para aceder as réplicas de base de dados no Azure. Por isso, é possível alcançar para diminuir a carga de trabalho numa base de dados no local. Recomendamos que implementar este cenário para grossa ler das cargas de trabalho e os efeitos de desenvolvimento. Para obter informações sobre como criar réplicas de base de dados no Azure, consulte o artigo grupos de Disponibilidade AlwaysOn [elevada disponibilidade](virtual-machines-windows-sql-high-availability-dr.md)e recuperação de falhas para SQL Server em máquinas virtuais do Azure.

## <a name="comparing-web-development-strategies-in-azure"></a>Comparar estratégias de desenvolvimento da web no Azure

Para implementar e implementar uma aplicação baseada em SQL Server no Azure de várias camadas, pode utilizar um dos seguintes métodos programação dois:

- Configure um servidor web tradicional (IIS - serviços de informação Internet) no Azure e acesso bases de dados do SQL Server em máquinas virtuais do Azure.

- Implementar e implementar um serviço na nuvem para Azure. Em seguida, certifique-se de que este serviço em nuvem pode aceder a bases de dados do SQL Server em máquinas virtuais do Azure. Um serviço na nuvem, pode incluir várias funções web e trabalhador.

A tabela seguinte fornece uma comparação de desenvolvimento da web tradicional com serviços em nuvem Azure e Azure Web Apps relativamente às SQL Server em máquinas virtuais do Azure. A tabela inclui Azure Web Apps, tal como é possível utilizar os do SQL Server Azure VM como uma origem de dados para aplicações Web do Azure através do seu endereço IP público virtual ou o nome de DNS.

||Desenvolvimento da web tradicional em máquinas virtuais do Azure|Serviços em nuvem no Azure|Alojamento Azure Web Apps na Web|
|---|---|---|---|
|**Aplicação migração no local**|Aplicações existentes como-é.|Aplicações necessitam funções web e trabalhador.|Aplicações existentes como-é mas adequado para aplicações web autónomo e serviços web que requerem escalabilidade rápida.|
|**Desenvolvimento e implementação**|Visual Studio, WebMatrix, Programador de Visual Web, WebDeploy, FTP, TFS, Gestor de IIS, PowerShell.|Visual Studio, SDK Azure, TFS, PowerShell. Cada serviço em nuvem tem dois ambientes ao qual pode implementar o pacote de serviço e de configuração: teste e de produção. Pode implementar um serviço na nuvem para o ambiente de teste para testá-lo antes de promover produção.|Visual Studio, WebMatrix, Programador de Visual Web, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub,, TFS, Web implementar, PowerShell.|
|**Administração e o programa de configuração**|For o responsável da tarefas administrativas relativo à aplicação, dados, as regras de firewall, rede virtual e sistema operativo.|For o responsável da tarefas administrativas na aplicação, dados, as regras de firewall e rede virtual.|For o responsável da tarefas administrativas na aplicação e apenas os dados.|
|**Disponibilidade de alta e recuperação de falhas (HADR)**|Recomendamos que coloca máquinas virtuais no mesmo conjunto de disponibilidade e no serviço de nuvem do mesmo. Conjunto de disponibilidade manter o seu VMs na mesma permite que o Azure colocar os nós de elevada disponibilidade domínios falhas separadas e actualizar domínios. Da mesma forma, manter o seu VMs no serviço na nuvem mesmo permite balanceamento de carga e VMs possam comunicar diretamente com um do outro através da rede local dentro de um centro de dados Azure.<br/><br/>For o responsável da execução de um elevada disponibilidade e a solução de recuperação de falhas para SQL Server em máquinas virtuais do Azure para evitar qualquer tempo de inatividade. Para tecnologias HADR suportadas, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>For o responsável da cópias de segurança seus próprios dados e de aplicações.<br/><br/>Azure pode mover as máquinas virtuais se o computador anfitrião no Centro de dados falhar devido a problemas de hardware. Além disso, podem existir planeado tempo de inatividade da sua VM quando o computador anfitrião é atualizado para software de segurança ou atualizações. Por conseguinte, recomendamos que mantenha VMs, pelo menos, duas em cada camada de aplicação para garantir a disponibilidade contínua. Azure não fornece SLA para uma única máquina de virtual. Para obter mais informações, consulte [orientações técnicas RDP Azure](../resiliency/resiliency-technical-guidance.md).|Azure gere falhas resultantes o software de hardware ou sistema operativo subjacente. Recomendamos que implementar várias instâncias de uma função web ou de trabalho para se certificar de elevada disponibilidade da sua aplicação. Para obter informações, consulte o artigo [serviços em nuvem, máquinas virtuais e Virtual contrato de nível de serviço de rede](http://www.microsoft.com/download/details.aspx?id=38427) e [recuperação de falhas e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>For o responsável da cópias de segurança seus próprios dados e de aplicações.<br/><br/>Para bases de dados que residem numa base de dados do SQL Server numa VM Azure é responsável para implementar uma solução de recuperação de disponibilidade e falhas alta para evitar qualquer tempo de inatividade. Para tecnologias HDAR suportadas, consulte o artigo elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure.<br/><br/>**Base de dados do SQL Server espelhando as**: utilizar serviços em nuvem Azure (funções web/trabalho). SQL Server VMs e um projecto de serviço de nuvem, podem ser na mesma rede Virtual Azure. Se VM do SQL Server não estiver na mesma rede Virtual, tem de criar um Alias de servidor SQL para encaminhar comunicação para a instância do SQL Server. Além disso, o nome de alias tem de corresponder o nome do SQL Server.|Disponibilidade elevada é herdada do Azure trabalhador funções, armazenamento de Blobs do Azure e base de dados do SQL Azure. Por exemplo, o armazenamento do Windows Azure mantém três réplicas de todos os BLOBs, tabela e dados de fila de espera. Em qualquer momento, a base de dados do SQL Azure mantém três réplicas de dados em execução — uma réplica principal e duas réplicas secundárias. Para mais informações, consulte o artigo [Armazenamento do Windows Azure](https://azure.microsoft.com/documentation/services/storage/) e [Base de dados do SQL Azure](../sql-database/sql-database-technical-overview.md).<br/><br/>Quando utilizar o SQL Server no Azure VM como origem de dados para aplicações Web do Azure, tenha em atenção que Azure Web Apps não suporta a rede Virtual Azure. Por outras palavras, todas as ligações a partir do Azure Web Apps para SQL Server VMs no Azure tem de percorrer públicos pontos finais de máquinas virtuais. Isto pode causar algumas limitações de elevada disponibilidade e cenários de recuperação de falhas. Por exemplo, a aplicação cliente no Azure Web Apps estabelecer ligação ao SQL Server VM com base de dados espelhando as seria não conseguir ligar para o novo servidor principal como base de dados espelhando as requer que defina o Azure rede Virtual entre VMs de anfitrião do SQL Server no Azure. Por conseguinte, utilizar a **Base de dados do SQL Server espelhando as** Azure Web Apps não é suportada neste momento.<br/><br/>**Grupos de disponibilidade do SQL Server AlwaysOn**: pode configurar o grupos de Disponibilidade AlwaysOn ao utilizar o Azure Web Apps com o SQL Server VMs no Azure. Mas tem de configurar AlwaysOn disponibilidade grupo escuta para encaminhar comunicação para a réplica principal através de pontos finais de balanceamento de carga públicos.|
|**Conectividade de publicação em local**|Pode utilizar a rede Virtual Azure para ligar a no local.|Pode utilizar a rede Virtual Azure para ligar a no local.|Azure rede Virtual é suportada. Para mais informações, consulte o artigo [Integração da rede Virtual aplicações Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Escalabilidade**|Escala de cima está disponível ao aumentar os tamanhos de máquina virtual ou ao adicionar mais discos. Para mais informações sobre os tamanhos de máquina virtual, consulte o artigo [Tamanhos de Máquina Virtual para Azure](virtual-machines-windows-sizes.md).<br/><br/>**Para servidor de bases de dados**: escala-out está disponível através de técnicas de criação de partições de base de dados e grupos de disponibilidade do SQL Server AlwaysOn.<br/><br/>Para grossas leitura das cargas de trabalho, pode utilizar [Grupos de Disponibilidade AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) no vários nós secundários, bem como replicação do SQL Server.<br/><br/>Para escrever grossa das cargas de trabalho, pode implementar horizontais dados partições por vários servidores físicos para fornecer a saída de escala de aplicação.<br/><br/>Além disso, pode implementar uma escala de saída ao utilizar o [SQL Server com o encaminhamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx). Com dados dependentes encaminhamento (DDR), tem de lhe implementar o partições mecanismo na aplicação de cliente, normalmente na camada de camada de empresas, para encaminhar os pedidos de base de dados para vários nós do SQL Server. A camada de empresas contém mapeamentos para como os dados são divididos e qual o nó contém os dados.<br/><br/>É possível dimensionar aplicações que são executadas máquinas virtuais. Para mais informações, consulte o artigo [como dimensionar uma aplicação](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Importante**: A funcionalidade de **AutoScale** no Azure permite-lhe automaticamente aumentar ou diminuir as máquinas virtuais que são utilizadas pela aplicação. Esta funcionalidade garante que a experiência de utilizador final não é afectada negativa durante períodos de pico e VMs são encerrar quando o pedido é reduzido. Recomenda-se que o utilizador não defina a opção AutoScale do seu serviço de nuvem se incluir VMs do SQL Server. O motivo é que a funcionalidade de AutoScale permite Azure para ativar uma máquina virtual quando a utilização da CPU nesse VM for superior a algumas limiar de e para desativar uma máquina virtual quando a utilização da CPU vai inferior-lo. A funcionalidade de AutoScale é útil para aplicações sem estado, como servidores da web, onde qualquer VM pode gerir a carga de trabalho sem as referências para qualquer estado anterior. No entanto, a funcionalidade de AutoScale não é útil para as aplicações com estado, como SQL Server, onde a apenas uma instância permite escrita à base de dados.|Escala de cima está disponível ao utilizar várias funções web e trabalhador. Para mais informações sobre os tamanhos de máquina virtual para web funções e funções de trabalho, consulte o artigo [Configurar tamanhos para serviços em nuvem](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Ao utilizar os **Serviços em nuvem**, pode definir várias funções para distribuir o processamento e também alcançar flexível dimensionamento da sua aplicação. Cada serviço em nuvem inclui um ou mais funções da web e/ou funções de trabalho, cada uma com as suas próprias ficheiros da aplicação e de configuração. Pode escala de cópia de um serviço na nuvem, aumentando o número de ocorrências de função (máquinas virtuais) implementado para uma função e escala descendente num serviço na nuvem, diminuindo o número de ocorrências de função. Para obter informações detalhadas, consulte o artigo [Modelos de execução do Azure](../cloud-services/cloud-services-choose-me.md).<br/><br/>Escala de saída está disponível através do suporte de elevada disponibilidade incorporados Azure através de [serviços em nuvem, máquinas virtuais, Virtual contrato de nível de serviço de rede](http://www.microsoft.com/download/details.aspx?id=38427) e Balanceador de carga.<br/><br/>Para uma aplicação de várias camada, recomendamos que liga a aplicação de funções web/trabalhador a VMs através da rede Virtual Azure do servidor de base de dados. Além disso, Azure fornece balanceamento de carga para VMs no serviço na nuvem mesmo, pedidos de utilizador a distribuição dos mesmos. Máquinas virtuais ligadas desta forma podem comunicar diretamente com um do outro através da rede local dentro de um centro de dados Azure.<br/><br/>Pode configurar o **AutoScale** no portal do Azure clássico, bem como os tempos de agenda. Para mais informações, consulte o artigo [como dimensionar uma aplicação](../cloud-services/cloud-services-how-to-scale.md).|**Dimensionar para cima e para baixo**: pode aumentar/diminuir o tamanho da instância (VM) reservada para o seu web site.<br/><br/>Escala de saída: pode adicionar mais reservadas instâncias (VMs) para o web site.<br/><br/>Pode configurar o **AutoScale** no portal do, bem como os tempos de agenda. Para mais informações, consulte o artigo [como escala Web Apps](../app-service-web/web-sites-scale.md).|

Para obter mais informações sobre como escolher entre estas métodos de programação, consulte o artigo [Azure Web Apps, serviços em nuvem e VMs: quando utilizar que](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre como executar o SQL Server em máquinas virtuais do Azure, consulte o artigo [Do SQL Server na descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
