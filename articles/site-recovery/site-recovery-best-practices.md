<properties
    pageTitle="Preparar para a implementação de recuperação de sites | Microsoft Azure"
    description="Este artigo descreve como preparar-se implementar a replicação com Azure recuperação de sites."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="prepare-for-azure-site-recovery-deployment"></a>Preparar para a implementação de recuperação de sites do Azure

Leia este artigo para obter uma descrição de alto nível de requisitos de implementação para cada cenário de replicação suportado pelo serviço do Azure recuperação de sites. Depois de ler os requisitos gerais para cada cenário, pode ligar a detalhes de implementação específico para cada implementação.

Depois de ler esta mensagem de artigo quaisquer comentários ou perguntas na parte inferior do artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral

As organizações devem uma estratégia BCDR que determina como dados, das cargas de trabalho e apps manter-se em execução e disponível durante o tempo de inatividade planeado e não em recuperar a normal condições de trabalho mais cedo possível. Sua estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis e garantir que das cargas de trabalho permanecem continuamente disponíveis quando ocorre falhas. 

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR por orquestrar replicação de servidores físicos no local e máquinas virtuais na nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem falhas na sua localização principal, falhar ao longo para a localização secundária para manter as aplicações e das cargas de trabalho disponíveis. Falhar voltar a sua localização principal quando devolve ao modo normal. Saiba mais em [o que é a recuperação de Site?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Selecione o seu modelo de implementação

Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: o modelo de Gestor de recursos do Azure e o modelo de gestão de serviços clássica. Azure também tem dois portais – do [Azure portal clássica](https://manage.windowsazure.com/) compatível com o modelo clássico de implementação e o [Azure portal](https://ms.portal.azure.com/) com o suporte para ambos os modelos de implementação.

Recuperação de site está disponível no portal do clássico e o portal do Azure. No portal do Azure clássico pode suportar a recuperação de Site com o modelo de gestão de serviços clássica. No portal do Azure pode suportar o modelo clássico ou implementações do Gestor de recursos. [Leia mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implementação com o portal do Azure.

Quando estiver a escolher uma nota de modelo de implementação que:

- Recomendamos que utilize o [portal do Azure](https://portal.azure.com) e o modelo de Gestor de recursos sempre que possível.
- Recuperação de site fornece uma mais simples e intuitivo mais introdução experiência no portal do Azure.
- Utilizar o portal do Azure, pode criar uma réplica máquinas clássica e armazenamento de Gestor de recursos no Azure. Além disso, no portal do Azure pode utilizar LRS ou GRS contas de armazenamento.
- Portal do Azure combina os serviços de recuperação de sites e de cópia de segurança numa única Cofre de serviços de recuperação para que possa configurar e gerir os serviços BCDR a partir de uma única localização.
- Os utilizadores com subscrições do Azure aprovisionados com o programa de fornecedor de solução da nuvem (CSP) podem agora gerir operações de recuperação de Site no portal do Azure.
- Efectuar a replicação VMware VMs ou máquinas físicas para Azure no portal do Azure fornece várias novas funcionalidades, incluindo o suporte para o armazenamento do premium e a capacidade para excluir discos específicos da replicação.


## <a name="select-your-deployment-scenario"></a>Selecione o seu cenário de implementação

**Implementação** | **Detalhes** | **Portal do Azure** | **Portal clássico** | **PowerShell**
---|---|---|---|---
**VMware VMs para Azure** | Criar uma réplica VMware VMs ao armazenamento Azure | No Azure portal VMs pode não conseguir sobre classic ou armazenamento de Gestor de recursos<br/><br/> Implementação no [portal do Azure](site-recovery-vmware-to-azure.md) fornece uma experiência de implementação simplificada e um número de benefícios da funcionalidade. | No portal do Azure clássico pode implementar com o [modelo melhorado](site-recovery-vmware-to-azure-classic.md) e falhar ao longo ao armazenamento Azure clássico.<br/><br/> Também é um modelo de legado que não deveriam ser utilizado para implementações novas. |  PowerShell atualmente não é suportado.
**VMs Hyper-V para Azure** | Hyper-V VMs ao armazenamento Azure. Podem ser VMs em anfitriões Hyper-V geridos no nuvens System Center VMM ou sem VMM. | No Azure portal VMs pode não conseguir sobre classic ou armazenamento de Gestor de recursos.<br/><br/> Portal do Azure fornece uma experiência de implementação simplificada. [Saiba mais](site-recovery-vmm-to-azure.md) sobre a replicação Hyper-V VMs no nuvens VMM. [Saiba mais](site-recovery-hyper-v-site-to-azure.md) sobre a replicação VMs Hyper-V (sem VMM).| No portal do Azure clássico pode falhar VMs sobre ao armazenamento Azure clássico | Implementação do PowerShell é suportada.
**Servidores físicos para Azure** | Criar uma réplica servidores Windows/Linux físicos ao armazenamento Azure | No Azure servidores portais podem não conseguir sobre classic ou armazenamento de Gestor de recursos.<br/><br/> Implementação no [portal do Azure](site-recovery-vmware-to-azure.md) fornece uma experiência de implementação simplificada e um número de benefícios da funcionalidade. | No portal do Azure clássico pode implementar com o [modelo melhorado](site-recovery-vmware-to-azure-classic.md) e falhar ao longo ao armazenamento Azure clássico.<br/><br/> Também é um modelo de legado que não deveriam ser utilizado para implementações novas. | Implementação do PowerShell atualmente não é suportada.
**Servidores de VMware VMs/física para sites secundário* | Criar uma réplica VMware VMs ou servidores Windows/Linux físicos para um site secundário. [Saiba mais e transferir](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) o Guia do utilizador InMage Scout. | Não está disponível no portal do Azure | No portal do clássico irá transferir InMage Scout do cofre recuperação de sites. | Implementação do PowerShell atualmente não é suportada
**VMs Hyper-V para um site secundário** | Criar uma réplica Hyper-V VMs no nuvens VMM uma nuvem secundário | No [portal do Azure](site-recovery-vmm-to-vmm.md) criar uma réplica Hyper-V VMs no nuvens VMM para um site secundário utilizando apenas o Hyper-V réplica. Castelo atualmente não é suportado. | No portal do Azure clássico pode de replicadas Hyper-V VMs no nuvens VMM para um site secundário utilizando [Hyper-V réplica](site-recovery-vmm-to-vmm-classic.md) ou [replicação de SAN](site-recovery-vmm-san.md) | Implementação do PowerShell é suportada



## <a name="check-what-you-need-for-deployment"></a>Verifique o que precisa para implementação

### <a name="replicate-to-azure"></a>Criar uma réplica para Azure

**Requisito de registo** | **Detalhes** 
---|---
**Conta Azure** | Terá de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**Armazenamento Azure** | Dados replicados são armazenados no Azure armazenamento e Azure VMs são criados quando ocorre activação pós-falha. Para criar uma réplica para Azure, terá uma [conta de armazenamento Azure](../storage/storage-introduction.md).<br/><br/>Se está a implementar o Site recuperação no portal do clássico terá uma ou mais [contas de armazenamento GRS padrão](..storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Se está a implementar no portal do Azure pode utilizar o armazenamento GRS ou LRS.<br/><br/> Se estiver a replicação VMware VMs ou servidores físicos no armazenamento Azure portal premium é suportada. Tenha em atenção que se estiver a utilizar uma conta de armazenamento premium também terá uma conta de armazenamento padrão para armazenar os registos de replicação que capturar em curso alterações aos dados no local. [Armazenamento de Premium](../storage/storage-premium-storage.md) é normalmente utilizado para máquinas virtuais que necessitam de uma forma consistente alto desempenho de IO e latência baixa para anfitrião IO intensivos das cargas de trabalho.<br/><br/> Se pretender utilizar uma conta de premium para armazenar dados replicados, também terá uma conta de armazenamento padrão para armazenar os registos de replicação que capturar em curso alterações aos dados no local.
**Rede Azure** | Para criar uma réplica para Azure, terá de uma rede Azure que Azure VMs irá estabelecer ligação quando estiver a criadas após activação pós-falha.<br/><br/> Se está a implementar no portal do clássico que irá utilizar uma rede clássica. Se está a implementar no portal do Azure, pode utilizar uma classic ou rede de Gestor de recursos.<br/><br/> A rede tem de ser na mesma região como cofre.
**Mapeamento de rede (VMM para Azure)** | Se estiver a replicação de VMM Azure, [mapeamento de rede](site-recovery-network-mapping.md) assegura que Azure VMs estão ligadas a redes corretas depois activação pós-falha.<br/><br/> Para configurar o mapeamento de rede terá de configurar as redes VM no portal do VMM.
**No local** | **VMware VMs**: terá uma máquina no local a executar componentes de recuperação de sites, VMware vSphere anfitriões/vCenter server e VMs que pretende criar uma réplica. [Leia mais](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Servidores físicos**: Se estiver a replicação servidores físicos terá uma máquinas no local que executem o componentes de recuperação de sites e servidores físicos que pretende criar uma réplica. [Leia mais](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Se pretender que provocam a [falha novamente](site-recovery-failback-azure-to-vmware.md) após activação pós-falha ao Azure terá uma infraestrutura VMware para o fazer.<br/><br/> **VMs Hyper-V**: Se pretender criar uma réplica Hyper-V VMs no nuvens VMM terá de um servidor VMM e estão localizados os anfitriões Hyper-V na quais VMs que pretende proteger. [Leia mais](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Se pretender criar uma réplica Hyper-V VMs sem VMM terá anfitriões Hyper-V onde estão localizadas VMs. [Leia mais](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites).
**Máquinas protegidas** | Máquinas protegidas que irão criar uma réplica Azure devem cumprir os [Pré-requisitos Azure](#azure-virtual-machine-requirements) descrito abaixo.


### <a name="replicate-to-a-secondary-site"></a>Criar uma réplica para um site secundário

**Requisito de registo** | **Detalhes** 
---|---
**Conta Azure** | Terá de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**No local** | **VMware VMs**: no site principal terá de um servidor do processo de colocação em cache, comprimir e encriptação de dados de replicação antes de o enviar para o site secundário. No site secundário que irá instalar um servidor de configuração para gerir e monitorizar a implementação e um servidor de destino principal. Recomendamos também um servidor de vContinuum para facilitar a gestão. Além disso, precisa de um ou mais anfitriões de vSphere VMware e, opcionalmente, um servidor de vCenter. [Saiba mais](site-recovery-vmware-to-vmware.md)<br/><br/> **Hyper-V VMs no nuvens VMM**: terá de configurar os servidores de VMM e anfitriões Hyper-V que contém VMs que pretende criar uma réplica. [Leia mais](site-recovery-vmm-to-vmm.md#on-premises-prerequisites).
**Mapeamento de rede (VMM para VMM)** | Se estiver a replicação de VMM VMM, o [mapeamento de rede](site-recovery-network-mapping.md) assegura que réplica VMs estão ligadas a redes adequados após activação pós-falha e optimizada são colocados em anfitriões Hyper-V. Para configurar o mapeamento de rede terá de configurar redes VM nos seus servidores de VMM.
**Mapeamento de armazenamento** | Se estiver a replicação de VMM para VMM, opcionalmente, pode configurar o [mapeamento de armazenamento](site-recovery-storage-mapping.md) para especificar o destino de armazenamento para VMs replicadas. Mapeamento de armazenamento pode ser configurado para a replicação réplica Hyper-V e SAN.<br/><br/> Mapeamento de armazenamento atualmente não é suportado no portal do Azure.


## <a name="verify-url-access"></a>Verifique o acesso do URL

Certifique-se destes URLs acessíveis a partir de servidores.

**URL** | **VMM para VMM** | **VMM para Azure** | **Hyper-V para Azure** | **VMware para Azure**
---|---|---|---|---
*. accesscontrol.windows.net | Permitir | Permitir | Permitir | Permitir
*. backup.windowsazure.com | Não é necessário | Permitir | Permitir | Permitir
*. hypervrecoverymanager.windowsazure.com | Permitir | Permitir | Permitir | Permitir
*. store.core.windows.net | Permitir | Permitir | Permitir | Permitir
*. blob.core.windows.net | Não é necessário | Permitir | Permitir | Permitir
https://www.msftncsi.com/ncsi.txt | Permitir | Permitir | Permitir | Permitir
https://Dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | Não é necessário | Não é necessário | Não é necessário | Permitir

## <a name="azure-virtual-machine-requirements"></a>Requisitos de máquina virtual Azure

Pode implementar recuperação de Site para criar uma réplica máquinas virtuais e físicos servidores a executar qualquer sistema operativo suportado pelo Azure. Isto inclui a maioria das versões do Windows e Linux. Terá para se certificar de que está em conformidade com máquinas virtuais que pretende proteger com os requisitos de Azure no local.


**Funcionalidade** | **Requisitos de** | **Detalhes**
---|---|---
Anfitrião Hyper-V | Deve estar a executar o Windows Server 2012 R2 | Pré-requisitos verificação falhará se sistema operativo não suportado
VMware hipervisor  | Sistema operativo suportado | [Verifique os requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operativo convidado | Hyper-V para replicação Azure: todos os sistemas operativos são [suportados pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)suporta a recuperação de sites. <br/><br/> Para VMware e a replicação de servidor físico: verificar os [Pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) Windows e Linux | Pré-requisitos verificação falhará se não suportadas.
Arquitetura de sistema operativo convidado | 64 bits | Pré-requisitos verificação falhará se não suportadas
Tamanho de disco do sistema operativo |  1023 GB | Pré-requisitos verificação falhará se não suportadas
Contagem de disco do sistema operativo | 1 | Pré-requisitos verificação falhará se não suportadas.
Contagem de disco de dados | 16 ou menos (valor máximo é uma função do tamanho da máquina virtual a ser criado. 16 = XL) | Pré-requisitos verificação falhará se não suportadas
Tamanho VHD do disco de dados | Até 1023 GB | Pré-requisitos verificação falhará se não suportadas
Adaptadores de rede | Várias placas são suportadas |
Endereço IP estático | Suportada | Se a máquina virtual principal estiver a utilizar um endereço IP estático pode especificar o endereço IP estático a máquina virtual que será criado no Azure. Tenha em atenção que o endereço IP estático para uma máquina de virtual linux em execução no Hyper-v não é suportado.
disco iSCSI | Não é suportado | Pré-requisitos verificação falhará se não suportadas
VHD partilhada | Não é suportado | Pré-requisitos verificação falhará se não suportadas
FC disco | Não é suportado | Pré-requisitos verificação falhará se não suportadas
Formato de disco rígido| VHD <br/><br/> VHDX | Apesar de VHDX atualmente não é suportada no Azure, recuperação de sites automaticamente converte VHDX VHD quando não é possível ao longo do Azure. Quando falham para o local as máquinas virtuais continuar a utilizar o formato VHDX.
BitLocker | Não é suportado | Tem de ser desactivado BitLocker antes de proteger uma máquina virtual.
Nome da máquina virtual| Entre 1 e 63 carateres. Restringido a letras, números e hífenes. Deverá inicie e termine com uma letra ou um número | Atualizar o valor nas propriedades de máquina virtual no recuperação de sites
Tipo de máquina virtual | <p>Geração de 1</p> <p>Geração de 2 - Windows</p> |  Geração 2 máquina de virtual com o tipo de disco SO de disco básico que inclui 1 ou 2 volumes de dados com o formato de disco como VHDX que é inferior a 300GB é suportada. Máquinas virtuais de 2. ª geração de Linux não são suportadas. [Leia mais informações](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## <a name="optimizing-your-deployment"></a>Otimizar a sua implementação

Utilize as seguintes sugestões para ajudar a otimizar e dimensionar a sua implementação.

- **Tamanho de volume do sistema operativo**: Quando replica uma máquina virtual para Azure volume do sistema operativo tem de ser menor que 1 TB. Se tiver mais volumes superior a esta pode manualmente movê-las para um disco diferente antes de começar a implementação.
- **Tamanho do disco dados**: Se estiver a ser replicadas para Azure pode ter até 32 discos de dados numa máquina virtual, cada uma com um máximo de 1 TB. Pode criar uma réplica eficazmente e falhar ao longo de uma máquina de virtual ~ 32 TB.
- **Limites de plano de recuperação**: recuperação de sites pode dimensionar a milhares de máquinas virtuais. Planos de recuperação destinam-se como um modelo para as aplicações que deverá falhar ao longo em conjunto, para que recomendamos limitar o número de máquinas num plano de recuperação a 50.
- **Limites de serviço Azure**: Azure em cada subscrição inclui um conjunto de limites predefinidos no núcleos, cloud services, etc. Recomendamos que executa um teste activação pós-falha para validar a disponibilidade de recursos na sua subscrição. Pode modificar estes limites através do suporte Azure.
- **Planeamento da capacidade**: saber mais sobre [Planeamento da capacidade](site-recovery-capacity-planner.md) de recuperação de sites.
- **Largura de banda de replicação**: Se estiver breve largura de banda de replicação tenha em atenção que:
    - **ExpressRoute**: recuperação de sites funciona com o Azure ExpressRoute e WAN optimizers como Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
    - **O tráfego de replicação**: utilizações de recuperação de sites executa uma inteligente utilizando apenas os blocos de dados e não o VHD todo a replicação inicial. Apenas as alterações são replicadas durante a replicação em curso.
    - **O tráfego de rede**: pode controlar o tráfego de rede utilizado para a replicação ao configurar [QoS do Windows](https://technet.microsoft.com/library/hh967468.aspx) com uma política de com base em endereço IP de destino e uma porta.  Além disso, se estiver a replicação a recuperação de Site do Azure utilizando o agente de cópia de segurança do Azure pode configurar a limitação para esse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
- **RTO**: medir o objectivo de tempo de recuperação (RTO) que pode esperar com a recuperação de Site sugerimos executa um teste activação pós-falha e ver as tarefas recuperação de sites para analisar quanto sincronizá-la leva para concluir as operações. Se está a falhar ao longo do Azure, para o melhor RTO Recomendamos para automatizar a integração com o Azure planos de automatização e recuperação todas as ações manuais.
- **RPO**: recuperação de sites suporta um objectivo de ponto de recuperação perto síncrono (RPO) quando criar uma réplica Azure. Este comando assume a largura de banda suficiente entre o seu centro de dados e o Azure.


##<a name="service-urls"></a>URLs do serviço
Certifique-se que destes URLs são acessíveis a partir do servidor


**URLs** | **VMM para VMM** | **VMM para Azure** | **Site de Hyper-V para Azure** | **VMware para Azure**
---|---|---|---|---
 \*. accesscontrol.windows.net | É necessário acesso  | É necessário acesso  | É necessário acesso  | É necessário acesso
 \*. backup.windowsazure.com |  | É necessário acesso  | É necessário acesso  | É necessário acesso
 \*. hypervrecoverymanager.windowsazure.com | É necessário acesso  | É necessário acesso  | É necessário acesso  | É necessário acesso
 \*. store.core.windows.net | É necessário acesso  | É necessário acesso  | É necessário acesso  | É necessário acesso
 \*. blob.core.windows.net |  | É necessário acesso  | É necessário acesso  | É necessário acesso
 https://www.msftncsi.com/ncsi.txt | É necessário acesso  | É necessário acesso  | É necessário acesso  | É necessário acesso
 https://Dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi | | | | É necessário acesso


## <a name="next-steps"></a>Próximos passos

Depois de aprendizagem e comparar os requisitos de implementação geral pode ler os pré-requisitos detalhados de início e implementar cada cenário.

- [Criar uma réplica máquinas virtuais de VMware para Azure](site-recovery-vmware-to-azure-classic.md)
- [Criar uma réplica servidores físicos para Azure](site-recovery-vmware-to-azure-classic.md)
- [Criar uma réplica Hyper-V server no nuvens VMM para Azure](site-recovery-vmm-to-azure.md)
- [Criar uma réplica máquinas virtuais de Hyper-V (sem VMM) para Azure](site-recovery-hyper-v-site-to-azure.md)
- [Criar uma réplica VMs Hyper-V para um site secundário](site-recovery-vmm-to-vmm.md)
- [Criar uma réplica da VMs Hyper-V para um site secundário com SAN](site-recovery-vmm-san.md)
- [Criar uma réplica Hyper-V VMs com um único servidor VMM](site-recovery-single-vmm.md)
