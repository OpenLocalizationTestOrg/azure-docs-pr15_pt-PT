<properties
    pageTitle="Descrição geral do SQL Server em máquinas virtuais Azure | Microsoft Azure"
    description="Saiba mais sobre como executar completos edições do SQL Server em máquinas virtuais do Azure. Obtenha ligações diretas para todas as imagens do SQL Server VM e conteúdo relacionado."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Descrição geral do SQL Server em máquinas virtuais Azure

Este tópico descreve as suas opções para executar o SQL Server em máquinas virtuais Azure (VMs), juntamente com [ligações para imagens portais](#option-1-create-a-sql-vm-with-per-minute-licensing) e uma descrição geral das [tarefas comuns](#manage-your-sql-vm).

>[AZURE.NOTE] Se já está familiarizado com o SQL Server e pretende ver como implementar uma VM do SQL Server, consulte o artigo [aprovisionar uma máquina de virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Descrição geral
Se for um administrador de base de dados ou um programador, Azure VMs fornece uma maneira de mover no local do SQL Server das cargas de trabalho e aplicações para a nuvem. O vídeo seguinte fornece uma descrição geral técnica do SQL Server Azure VMs.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

O vídeo abrange as seguintes áreas:

|Tempo|Área|
|---|---|
| 00:21 | O que são Azure VMs? |
| 01:45 | Segurança |
| 02:50 | Conectividade |
| 03:30 | Armazenamento fiabilidade e desempenho |
| 05:20 | Tamanhos VM |
| 05:54 | Disponibilidade de alta e SLA |
| 07:30 | Suporte de configuração |
| 08:00 | Monitorização |
| 08:32 | Demonstração: Criar uma VM do SQL Server 2016 |

>[AZURE.NOTE] O vídeo foca-se no SQL Server 2016, mas Azure fornece imagens VM para várias versões do SQL Server, incluindo 2008, 2012, 2014 e 2016. 

## <a name="scenarios"></a>Cenários
Existem várias razões pelas quais poderá optar por aloja os seus dados no Azure. Se a sua aplicação está a movê para Azure, melhora o desempenho também mover os dados. Mas existem outros benefícios. Tem automaticamente acesso a várias centros de dados para uma recuperação de presença e falhas global. Os dados também são altamente seguras e resistentes.

SQL Server em execução no Azure VMs é uma das opções para armazenar os seus dados relacionais no Azure. É boa escolha para vários cenários. Por exemplo, poderá configurar a VM Azure de forma semelhante possível para uma máquina de SQL Server no local. Ou, poderá pretender executar aplicações e serviços adicionais no mesmo servidor de base de dados. Existem dois recursos principais que podem ajudá-lo pensar ainda mais cenários e considerações de:

 - [SQL Server em máquinas virtuais Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) fornece uma descrição geral dos cenários para utilizar o SQL Server Azure VMs melhor. 
 - [Escolha uma opção do SQL Server nuvem: base de dados SQL Azure (PaaS) ou do SQL Server no Azure VMs (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) fornece uma comparação entre a base de dados SQL e o SQL Server em execução numa VM detalhada.

## <a name="create-a-new-sql-vm"></a>Criar uma nova VM de SQL
As secções seguintes fornecem diretas ligações ao portal do Azure para as imagens de Galeria de máquina virtual do SQL Server. Dependendo da imagem que selecionar, pode quer pagamento para SQL Server licenciamento custos numa base por minuto ou pode trazer com os seu próprios licença (BYOL).

Localize orientações passo a passo para que este processo no tutorial, [aprovisionar uma máquina de virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Além disso, reveja as [práticas recomendadas do desempenho para SQL Server VMs](virtual-machines-windows-sql-performance.md), que explica como selecionar o tamanho de máquina adequado e outras funcionalidades disponíveis durante o aprovisionamento.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opção 1: Criar uma VM SQL com o licenciamento por minuto
A tabela seguinte fornece uma matriz de imagens do SQL Server disponíveis na Galeria de máquina virtual. Clique em qualquer ligação para começar a criar uma nova VM de SQL com a sua versão especificada, edição e sistema operativo.

|Versão|Sistema operativo|Edition|
|---|---|---|
|**2016 DE SQL**|Windows Server 2012 R2|[Empresa](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [padrão](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev Center](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Empresa](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [padrão](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL DE 2014**|Windows Server 2012 R2|[Empresa](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [padrão](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Empresa](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [padrão](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Empresa](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [padrão](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Empresa](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [padrão](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Empresa](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [padrão](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Opção 2: Criar uma VM SQL com uma licença existente
Também pode trazer com os seu próprios licença (BYOL). Neste cenário, apenas pagar para a VM sem qualquer taxas adicionais para o licenciamento do SQL Server. Para utilizar o seu próprio licença, utilize a matriz de versões do SQL Server, edições e sistemas operativos abaixo. No portal do, estes nomes de imagem são com o prefixo **{BYOL}**.

|Versão|Sistema operativo|Edition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL padrão](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL padrão](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL padrão](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Para utilizar imagens BYOL VM, tem de ter e Enterprise Agreement com [Licenças mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Também precisa de uma licença válida para a versão de edição do SQL Server que pretende utilizar. Tem de [fornecer as informações de BYOL necessárias para a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) no prazo de **10** dias a VM de aprovisionamento.

## <a name="manage-your-sql-vm"></a>Gerir o seu VM de SQL
Depois de aprovisionar a VM do SQL Server, existem várias tarefas de gestão opcional. Em muitos aspectos, configurar e gerir o SQL Server exatamente como irá gerir uma instância do SQL Server no local. No entanto, algumas tarefas são específicas Azure. As secções seguintes realçar algumas destas áreas com ligações para mais informações.

### <a name="connect-to-the-vm"></a>Ligar para a VM
Um destes passos gestão mais básicos é ligar à sua VM de servidor SQL através de ferramentas, tal como o SQL Server Management Studio (SSMS). Para obter instruções sobre como ligar à sua nova VM de servidor de SQL, consulte o artigo [ligar a uma SQL Server Máquina Virtual no Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar os seus dados
Se tiver uma base de dados existente, que irá que pretende deslocar-se que a recentemente aprovisionada VM de SQL. Para obter uma lista de opções de migração e orientações, consulte o artigo [migrar uma base de dados para o SQL Server numa VM Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurar a disponibilidade de alta
Se necessitar de elevada disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve vários Azure VMs numa rede virtual. Portal do Azure tem um modelo que configura esta configuração para si. Para mais informações, consulte o artigo [configurar um grupo de Disponibilidade AlwaysOn em máquinas virtuais do Gestor de recursos do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Se pretender configurar manualmente o seu grupo de disponibilidade e escuta associada, consulte o artigo [Configurar grupos de Disponibilidade AlwaysOn na Azure VM](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações de elevada disponibilidade, consulte o artigo [elevada disponibilidade e recuperação de falhas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Criar cópia de segurança dos seus dados
Azure VMs podem tirar partido de [Cópia de segurança automatizado](virtual-machines-windows-sql-automated-backup.md), que cria regularmente cópias de segurança da base de dados ao armazenamento blob. Pode utilizar esta técnica também manualmente. Para mais informações, consulte o artigo [Utilizar armazenamento do Windows Azure para cópia de segurança do SQL Server e restaurar](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obter uma descrição geral de todas as opções de cópia de segurança e restaurar, consulte o artigo [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatizar atualizações
Azure VMs podem utilizar [Correcção automática](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para instalar o windows importantes e atualiza automaticamente os do SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Programa de melhoramento da experiência do cliente (PMEC)
O programa de melhoramento da experiência do cliente (PMEC) está ativada por predefinição. Isto envia periodicamente relatórios para a Microsoft para ajudar a melhorar o SQL Server. Não existe nenhuma tarefa gestão necessária com PMEC, a menos que pretenda desativá-lo Depois de aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se para a VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário de **erros do SQL Server e relatórios de utilização** . Siga as instruções para desativar o relatório. 

Para mais informações, consulte a secção PMEC do tópico [Aceitar os termos de licenciamento](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Próximos passos
[Explorar o caminho de formação](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais Azure.

Mais pergunta? Em primeiro lugar, consulte o artigo o [SQL Server em máquinas virtuais de Azure perguntas mais frequentes](virtual-machines-windows-sql-server-iaas-faq.md). Mas também a adicionar a sua perguntas ou comentários para a parte inferior de qualquer tópicos SQL VM para interagir com Microsoft e da Comunidade.
