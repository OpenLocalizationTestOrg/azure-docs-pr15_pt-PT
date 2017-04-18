<properties
    pageTitle="Recuperação de Site Azure: Perguntas mais frequentes | Microsoft Azure"
    description="Este artigo aborda perguntas populares sobre a recuperação de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Recuperação de Site Azure: Perguntas mais frequentes (FAQ)

Este artigo inclui perguntas mais frequentes sobre a recuperação de Site Azure. Se tiver questões depois de ler este artigo, publique-os no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz a recuperação de Site?

Recuperação de sites contribui para o seu negócio continuidade e falhas (BCDR) estratégia de recuperação, ao orquestrar e automatizar replicação de máquinas virtuais de no local e servidores físicos para Azure ou para um centro de dados secundário. [Saiba mais](site-recovery-overview.md).


### <a name="what-can-site-recovery-protect"></a>O que pode proteger recuperação de Site?

- **Máquinas virtuais de Hyper-V**: recuperação de sites pode proteger quaisquer carga de trabalho em execução numa VM Hyper-V.
- **Servidores físicos**: recuperação de sites pode proteger físicos servidores a executar o Windows ou Linux.
- **Máquinas virtuais de VMware**: recuperação de sites pode proteger quaisquer carga de trabalho em execução numa VM VMware.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Recuperação de sites suporta o modelo de Gestor de recursos do Azure?

Para além de recuperação de Site no portal do Azure clássico, recuperação de Site está disponível no portal do Azure com o suporte para o Gestor de recursos. Para a maioria dos cenários de implementação recuperação de Site no Azure portal fornece uma experiência de implementação simplificada e pode criar uma réplica VMs e servidores físicos para armazenamento clássico ou o armazenamento do Gestor de recursos. Seguem-se a implementações suportadas:

- [Criar uma réplica da VMware VMs ou servidores físicos para Azure no portal do Azure](site-recovery-vmware-to-azure.md)
- [Criar uma réplica da Hyper-V VMs no nuvens VMM para Azure no portal do Azure](site-recovery-vmm-to-azure.md)
- [Criar uma réplica da VMs Hyper-V (sem VMM) para Azure no portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [Criar uma réplica Hyper-V VMs no nuvens VMM para um site secundário no portal do Azure](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que preciso em Hyper-V para orquestrar replicação com a recuperação de Site?

Servidor Hyper-V de anfitrião de o que precisa depende do cenário de implementação. Veja os pré-requisitos Hyper-V no:

- [Replicadas VMs Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicadas VMs Hyper-V (com VMM) para o Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicação VMs Hyper-V para um centro de dados secundário](site-recovery-vmm-to-vmm.md#before-you-start)

- Se estiver a replicação para um centro de dados secundário, leia sobre os [sistemas operativos de convidado de suportados para VMs Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Se estiver a replicação para Azure, recuperação de sites suporta todos os convidados sistemas operativos que são [suportadas pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger VMs quando Hyper-V está em execução num sistema operativo de cliente?

Não, VMs têm de estar localizados num servidor anfitrião Hyper-V que está a ser executado num computador de servidor do Windows suportado. Se de que necessita de proteger um computador cliente poderia replicadas-la como uma máquina física para [Azure](site-recovery-vmware-to-azure.md) ou um [Centro de dados secundário](site-recovery-vmware-to-vmware.md).


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>O que das cargas de trabalho pode proteger com a recuperação de Site

Pode utilizar recuperação de sites para proteger a maioria das cargas de trabalho em execução num servidor física ou VM suportado. Recuperação de site fornece suporte para a replicação deverá ter em consideração aplicação, para que possam ser recuperadas aplicações a um Estado inteligente. Integra-se com aplicações Microsoft como o SharePoint, Exchange, Dynamics, SQL Server e do Active Directory e trabalha com fornecedores à esquerda, incluindo Oracle, SAP, IBM e chapéu vermelho. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Anfitriões Hyper-V precisam de estar no nuvens VMM?

Se pretender criar uma réplica para um centro de dados secundário, em seguida, tem de estar ligado Hyper-V VMs Hyper-V aloja servidores localizados na nuvem VMM. Se a que pretende criar uma réplica para Azure, pode de replicadas VMs em servidores de anfitriões Hyper-V com ou sem nuvens VMM. [Leia mais](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Posso implementar recuperação de Site com o VMM se apenas tiver um servidor VMM?

Sim. Quer pode criar uma réplica VMs em servidores Hyper-V na nuvem VMM para Azure ou pode criar uma réplica entre nuvens VMM no mesmo servidor. Para no local para a replicação no local, recomendamos que tem um servidor VMM nos sites principais e secundários.  [Saiba mais](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>O que servidores físicos posso proteger?

Pode criar uma réplica físicos servidores a executar o Windows e Linux para Azure ou para um site secundário. [Saiba mais sobre](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) requisitos do sistema operativo.  Os mesmos requisitos aplicam-se de que está a replicação de servidores físicos para Azure ou para um site secundário.

Tenha em atenção que servidores físicos serão executada como VMs no Azure se vai do seu servidor no local para baixo. Reposição de recurso para um servidor físico no local atualmente não é suportada, mas pode falhar novamente para uma máquina de virtual em execução no Hyper-V ou VMware.


### <a name="what-vmware-vms-can-i-protect"></a>O que VMs VMware posso proteger?

Para proteger VMware VMs terá um hipervisor vSphere e máquinas virtuais executar VMware ferramentas. Recomendamos também que tem um servidor de vCenter VMware para gerir o hypervisors. [Saiba mais](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sobre os requisitos de exatos para a replicação de servidores VMware e VMs para Azure ou para um site secundário.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Pode gerir recuperação de falhas para minha filiais com a recuperação de Site?

Sim. Ao utilizar a recuperação de Site para orquestrar replicação e activação pós-falha na sua filiais, obterá orchestration unificado e numa vista de todos os seus ramo office cargas de trabalho numa localização central. Pode executar activações pós-falha e administrar a recuperação de falhas de todos os ramos a partir do seu sede, sem visitar ramos facilmente.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Dados de replicação são enviados para o serviço de recuperação de Site?

Não, a recuperação de sites não intercetar dados replicados e não tem as informações sobre o que está a ser executado no seu máquinas virtuais ou servidores físicos.
Dados de replicação são trocados entre anfitriões de Hyper-V no local, VMware hypervisors, ou servidores físicos e Azure armazenamento ou no seu site secundário. Recuperação de sites não tem capacidades de interceptar esses dados. Apenas os metadados necessário para orquestrar replicação e activação pós-falha são enviado para o serviço de recuperação de sites.

Recuperação de sites é ISO 27001:2013, 27018, HIPAA, DPA certificação de processo de avaliações SOC2 e FedRAMP JAB.


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por razões de conformidade, mesmo nosso metadados no local tem de permanecer dentro da mesma região geográfica. Pode recuperação de sites Ajude-nos?

Sim. Quando cria um cofre recuperação do Site numa região, podemos Certifique-se de que todos os metadados que precisamos de ativar e orquestrar replicação e activação pós-falha permanece nessa região da geográficos limite.

### <a name="does-site-recovery-encrypt-replication"></a>Recuperação de sites encriptar replicação?

Para máquinas virtuais e servidores físicos, replicação entre sites de no local encriptação-em-trânsito é suportada. Para máquinas virtuais e servidores físicos replicadas para o Azure, são suportadas encriptação em trânsito e encriptação at rest (no Azure).


## <a name="replication"></a>Replicação


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Existem qualquer pré-requisitos para a replicação máquinas virtuais para Azure?

Máquinas virtuais que pretende criar uma réplica para Azure devem cumprir os [requisitos de Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicação Hyper-V geração 2 virtual máquinas para Azure?

Sim. Recuperação de sites converte de geração 2. ª geração 1 durante activação pós-falha. A reposição de recurso a máquina é convertida novamente geração 2. [Leia mais](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Se posso criar uma réplica Azure como posso pague para Azure VMs?

Durante a replicação normal, dados são replicados para geo redundantes armazenamento Azure e não precisa de pagamento de taxas de máquina virtual qualquer Azure IaaS, fornecer uma vantagem significativa. Quando executa uma activação pós-falha ao Azure, recuperação de Site cria automaticamente máquinas virtuais de Azure IaaS e depois que serão cobrados para os recursos de cluster consumir no Azure.


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>Existe um SDK, que pode utilizar para automatizar o fluxo de trabalho de recuperação automática?

Sim. Pode automatizar fluxos de trabalho de recuperação de sites com a API Rest, PowerShell ou o SDK do Azure. Cenários atualmente suportados para implementar recuperação do Site através do PowerShell:

- [Criar uma réplica Hyper-V VMs no VMMs nuvens Azure PowerShell clássico](site-recovery-deploy-with-powershell.md)
- [Criar uma réplica Hyper-V VMs no VMMs nuvens para o Gestor de recursos do PowerShell Azure](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Criar uma réplica Hyper-V VMs sem VMM Azure PowerShell clássico](site-recovery-hyper-v-site-to-azure-classic.md)
- [Criar uma réplica Hyper-V VMs sem VMM ao Gestor de recursos do PowerShell Azure](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Se posso criar uma réplica para Azure que tipo de conta de armazenamento é preciso?

- **Portal clássica Azure**: se está a implementar o Site recuperação no portal do Azure clássico, terá de uma [conta de armazenamento geo redundantes padrão](../storage/storage-redundancy.md#geo-redundant-storage). Armazenamento de Premium atualmente não é suportado. A conta tem de ser na mesma região como cofre recuperação de sites.

- **Azure portal**: se está a implementar o Site recuperação no portal do Azure, terá de uma conta de armazenamento LRS ou GRS. Recomendamos que GRS para que seja e são dados se ocorre uma falha de regional ou se a região principal não pode ser recuperada. A conta tem de ser na mesma região como o Cofre de serviços de recuperação. Armazenamento de Premium só é suportado se estiver a replicação VMware VMs ou servidores físicos.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicação dados?

- **Hyper-v:** Hyper-V VMs podem ser replicadas a cada 30 segundos, 5 minutos ou 15 minutos. Se tiver configurado replicação de SAN replicação é síncrona.
- **VMware e servidores físicos:** Frequência de replicação não está relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Pode expandir replicação a partir do site de recuperação existente para outro site superior?

Expandido ou interligada replicação não é suportada. Pedir esta funcionalidade no [Fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Pode fazer uma replicação offline a primeira vez que criar uma réplica Azure?

Isto não é suportado. Pedir esta funcionalidade no [Fórum de comentários](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="can-i-exclude-specific-disks-from-replication"></a>Pode excluir discos específicos da replicação?

Isto é suportado quando estiver a [replicação VMware VMs e servidores físicos](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) para Azure, utilizando o portal Azure.


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Pode posso criar uma réplica máquinas virtuais com discos dinâmicos?

Discos dinâmicos são suportados quando replicação máquinas virtuais de Hyper-V. Também são suportados quando de replicadas VMware VMs e máquinas físicas para o Azure. O disco do sistema operativo tem de ser um disco básico.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Pode adicionar um novo computador a um grupo de replicação existente?

Adicionar novas máquinas a grupos de replicação existentes é suportada. Para fazê-lo, selecione o grupo de replicação (a partir do pá 'Replicada itens') e o menu de contexto de clique direito do rato/selecionar no grupo de replicação e selecione a opção adequada.

![Adicionar ao grupo replicação](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Pode optimizar largura de banda atribuída para o tráfego de replicação Hyper-V?

Sim. Pode ler mais sobre a limitação de largura de banda nos artigos implementação:

- [Capacidade de planeamento de replicadas VMware VMs e servidores físicos](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Capacidade de planeamento de replicadas Hyper-V VMs no nuvens VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Capacidade de planeamento de replicadas Hyper-V VMs sem VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Activação pós-falha


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Se estiver a falhar ao longo do Azure, como posso aceder ao Azure máquinas virtuais após activação pós-falha?

Pode aceder a VMs Azure através de uma ligação à Internet segura, através de um site para o site VPN ou ao longo do Azure ExpressRoute. Terá de preparar um número de coisas para poder ligar. Leia mais em:

- [Ligar a Azure VMs depois de activação pós-falha de VMware VMs ou servidores físicos](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Ligar a Azure VMs depois de activação pós-falha de Hyper-V VMs no nuvens VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Ligar a Azure VMs depois de activação pós-falha de Hyper-V VMs sem VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se a Azure como Azure Certifique-se de falhar sobre os meus dados estão e são?

Azure destina-se para uma rápida recuperação. Recuperação de sites já está preparada para activação pós-falha a um secundário Azure Centro de dados, em conformidade com a SLA Azure caso seja necessário. Se tal acontecer, podemos Certifique-se os metadados e cofres permanecem no interior da região geográfica mesmo que tenha escolhido para o cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se de que estou a replicação entre dois centros de dados o que acontece se o meu Centro de dados principal experiências uma falha de inesperados?

Pode acionar uma não planeado activação pós-falha a partir do site secundário. Recuperação de sites não necessita de conectividade a partir do site principal para efetuar a activação pós-falha.

### <a name="is-failover-automatic"></a>É activação pós-falha automático?

Não activação pós-falha está automática. Iniciar o activações pós-falha com um único clique no portal do ou pode utilizar o [PowerShell de recuperação de Site](https://msdn.microsoft.com/library/dn850420.aspx) para acionar uma activação pós-falha. A falhar novamente é uma acção simple no portal do recuperação de sites.

Para automatizar poderia utilize Orchestrator no local ou Gestor de operações para detetar uma falha de máquina virtual e, em seguida, acionar o activação pós-falha utilizando o SDK.

- [Leia mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.
- [Leia mais](site-recovery-failover.md) sobre activação pós-falha.
- [Leia mais](site-recovery-failback-azure-to-vmware.md) sobre uma falha da cópia de segurança VMware VMs e servidores físicos


## <a name="service-providers"></a>Fornecedores de serviços


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um fornecedor de serviços. Recuperação de sites é que funciona para os modelos de infraestrutura dedicada e partilhada?

Sim, recuperação de sites suporta ambos os modelos de infraestrutura dedicada e partilhadas.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para um fornecedor de serviços, é a identidade do meu inquilino partilhada com o serviço de recuperação de Site?

Não. Identidade do inquilino permanece anónima. Os inquilinos não precisam de aceder ao portal de recuperação de sites. Apenas o administrador do serviço fornecedor interage com o portal.


### <a name="will-tenant-application-data-ever-go-to-azure"></a>Dados da aplicação inquilino nunca subscreve no Azure?

Quando a replicação entre sites de propriedade do fornecedor de serviço, os dados da aplicação nunca desaparece Azure. Os dados são encriptados em trânsito e replicada diretamente entre os sites do fornecedor de serviço.

Se estiver a replicação para Azure, dados da aplicação são enviados para o Azure armazenamento, mas não para o serviço de recuperação de sites. Dados são encriptado em trânsito e permanecem encriptados no Azure.


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Meu inquilinos receberão a fatura de quaisquer serviços Azure?

Não. Relação de faturação do Azure é diretamente com o fornecedor de serviços. Fornecedores de serviços são responsáveis por gerar faturas específicas para os respetivos inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se estiver a replicação Azure, é necessita executar máquinas virtuais no Azure sempre?

Não, dados são replicados para uma conta de armazenamento Azure na sua subscrição. Ao efetuar uma activação de teste pós-falha (DR desagregação) ou uma activação real pós-falha, recuperação de Site cria automaticamente máquinas virtuais na sua subscrição.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantir o nível de inquilino isolamento quando posso criar uma réplica Azure?

Sim.

### <a name="what-platforms-do-you-currently-support"></a>O que plataformas atualmente suporta?

Suportamos o pacote do Azure, sistema de plataforma na nuvem, e System Center baseados implementações (2012 e superiores). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do pacote do Azure e recuperação de sites.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Suporta única Azure pacote e a única implementações de servidor VMM?

Sim, pode criar uma réplica máquinas virtuais de Hyper-V para Azure ou criar uma réplica entre sites de fornecedor de serviço.  Tenha em atenção que se replica entre sites de fornecedor de serviço, livro de execuções Azure integração não está disponível.


## <a name="next-steps"></a>Próximos passos

- Ler um [Descrição geral de recuperação de Site](site-recovery-overview.md)
- Saiba mais sobre [arquitetura de recuperação de sites](site-recovery-components.md)  
