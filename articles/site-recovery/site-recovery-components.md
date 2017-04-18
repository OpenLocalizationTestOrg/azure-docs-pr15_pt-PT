<properties
    pageTitle="Como funciona a recuperação de Site? | Microsoft Azure"
    description="Este artigo fornece uma descrição geral de arquitectura de recuperação de sites"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="how-does-azure-site-recovery-work"></a>Como funciona o Azure recuperação de Site?

Leia este artigo para compreender a arquitetura subjacente do serviço de recuperação de sites do Azure e os componentes de que a tornam trabalhar. 

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Descrição geral

As organizações devem uma estratégia BCDR que determina como dados, das cargas de trabalho e apps manter-se em execução e disponível durante o tempo de inatividade planeado e não em recuperar a normal condições de trabalho mais cedo possível. Sua estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis e garantir que das cargas de trabalho permanecem continuamente disponíveis quando ocorre falhas. 

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR por orquestrar replicação de servidores físicos no local e máquinas virtuais na nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem falhas na sua localização principal, falhar ao longo para a localização secundária para manter as aplicações e das cargas de trabalho disponíveis. Falhar voltar a sua localização principal quando devolve a operações normais. Saiba mais em [o que é a recuperação de Site?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de site no portal do Azure

Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: o modelo de Gestor de recursos do Azure e o modelo de gestão de serviços clássica. Azure também tem dois portais – do [Azure portal clássica](https://manage.windowsazure.com/) compatível com o modelo clássico de implementação e o [Azure portal](https://portal.azure.com) com o suporte para ambos os modelos de implementação.

Recuperação de site está disponível no portal do clássico e o portal do Azure. No portal do Azure clássico pode suportar a recuperação de Site com o modelo de gestão de serviços clássica. No portal do Azure pode suportar o modelo clássico ou implementações do modelo de recurso. [Leia mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implementação com o portal do Azure.

As informações neste artigo aplica-se ao clássicas e Azure implementações portais. Diferenças estão anotadas onde aplicável.

## <a name="deployment-scenarios"></a>Cenários de implementação

Recuperação de sites pode ser implementada para orquestrar replicação dentro de um número de cenários:

- **Criar uma réplica VMware virtual máquinas**: pode criar uma réplica no local VMware virtual máquinas para Azure ou para um centro de dados secundário.
- - **Criar uma réplica máquinas físicas**: pode criar uma réplica máquinas físicas a executar o Windows ou Linux para Azure ou para um centro de dados secundário. O processo de replicadas máquinas físicas é praticamente igual o processo de replicadas VMware VMs
- **Criar uma réplica VMs Hyper-V (sem VMM)**: pode criar uma réplica VMs Hyper-V que não são geridos pelo VMM para Azure.
- **Criar uma réplica VMs Hyper-V gerido no sistema Center VMM nuvens**: pode criar uma réplica no local Hyper-V máquinas virtuais em servidores de anfitrião Hyper-V no nuvens VMM para Azure ou para um centro de dados secundário. Pode criar uma réplica utilizando o padrão Hyper-V réplica ou utilizando a replicação de SAN.
- **Migrar VMs**: pode utilizar a recuperação de Site para [Migrar Azure IaaS VMs](site-recovery-migrate-azure-to-azure.md) entre regiões ou para [Migrar AWS Windows instâncias](site-recovery-migrate-aws-to-azure.md) para Azure IaaS VMs. Migração atualmente só é suportada que significa que pode falhar sobre estes VMs mas não pode falhar novamente.

Recuperação de sites pode criar uma réplica da maioria das aplicações em execução nestes VMs e servidores físicos. Pode obter um total resumo das aplicações suportadas no [das cargas de trabalho que pode proteger recuperação de sites do Azure?](site-recovery-workload.md)


## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Repetições para Azure: VMware máquinas virtuais ou servidores Windows/Linux físicos

Existem algumas formas de criar uma réplica VMware VMs com a recuperação de Site.

- **Utilizando o portal Azure**-quando implementar recuperação de sites no portal do Azure pode falhar ao longo do VMs ao armazenamento de Gestor de serviço clássico ou para o Gestor de recursos. Replicação VMware VMs no portal do Azure mostra um número das vantagens, incluindo a capacidade de replicadas para classic ou armazenamento de Gestor de recursos no Azure. [Saiba mais](site-recovery-vmware-to-azure.md).
- **Utilizando o portal clássico**-pode implementar recuperação de sites no portal do clássico utilizando uma experiência melhorada. Isto deve ser utilizado para todas as novas implementações no portal do clássico. Nesta implementação lhe pode apenas falhar ao longo do VMs clássico armazenamento no Azure e não ao armazenamento de Gestor de recursos. [Saiba mais](site-recovery-vmware-to-azure-classic.md). Também existe uma [experiência de legado](site-recovery-vmware-to-azure-classic-legacy.md) para configurar o VMware replicação no portal do clássico. Isto não deveriam ser utilizado para implementações novas.  Se já tiver implementado utilizando a experiência de legado [Saiba mais sobre como migrar](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) para a implementação melhorada.



Os requisitos de arquitecturais para implementar recuperação de sites para criar uma réplica servidores VMware VMs/físico no portal do Azure ou portal clássico do Azure (avançado) são semelhantes, com algumas diferenças:

- Se implementar no portal do Azure, pode criar uma réplica ao armazenamento baseado em Gestor de recursos e utilizar redes de Gestor de recursos para ligar o Azure VMs após activação pós-falha.
- Quando implementar no portal do Azure ambas as LRS e armazenamento GRS é suportado. No portal do clássico GRS é necessário.
- O processo de implementação é mais fácil de utilizar no portal do Azure e simplificada.


Eis o que precisa:

- **Conta Azure**: terá de uma conta do Microsoft Azure.
- **Armazenamento Azure**: terá de uma conta de armazenamento Azure para armazenar dados replicados. Pode utilizar uma conta clássica ou com uma conta de armazenamento do Gestor de recursos. A conta pode ser LRS ou GRS quando implementar no portal do Azure. Dados replicados são armazenados no Azure armazenamento e Azure VMs são fiadas para cima quando ocorre activação pós-falha. 
- **Rede Azure**: terá de uma rede virtual Azure que Azure VMs irá estabelecer ligação quando estiver a criados no activação pós-falha. No portal do Azure podem ser redes criados no modelo de Gestor de serviço clássico ou no modelo de Gestor de recursos.
- **Servidor de configuração no local**: terá uma máquina de Windows Server 2012 R2 no local que executa o servidor de configuração e outros componentes de recuperação de sites. Se estiver a replicação VMware VMs deverá uma VM VMware altamente disponível. Se pretender criar uma réplica servidores físicos na máquina pode ser física. Estes componentes de recuperação de Site serão instalados no computador:
    - **Servidor de configuração**: coordenadas a comunicação entre o seu ambiente no local e o Azure e gere a replicação de dados e recuperação.
    - **Servidor do processo**: age como um gateway replicação. Recebe dados de replicação de máquinas origem protegida, optimiza-o com colocação em cache, compressão e encriptação e envia os dados ao armazenamento Azure. Também processa instalação de push do serviço de mobilidade para máquinas protegidas e executa a deteção automática de VMware VMs. À medida que aumenta a sua implementação pode adicionar os servidores de processo de dedicada separado adicionais para processar crescentes volumes de tráfego de replicação.
    - **Servidor de destino do modelo global**: processa dados de replicação durante a reposição de recurso a partir do Azure. 
- **VMware VMs ou servidores físicos para criar uma réplica**: cada máquina que pretende criar uma réplica para Azure terão o componente de serviço de mobilidade instalado. Este serviço escritas de dados no computador para capturar e encaminha-los para o servidor do processo. Este componente pode ser instalado manualmente, ou pode ser seguia e instalado automaticamente pelo servidor de processo quando ativa a replicação para uma máquina.
- **servidor de anfitriões/vCenter vSPhere**: terá de uma ou mais vSphere anfitrião servidores a executar o VMware VMs. Recomendamos que implementar um servidor de vCenter para gerir esses anfitriões.
- **Reposição de recurso**: Eis o que precisa:
    - **Não é suportada física-para-físico reposição de recurso**: Isto significa que se falhar ao longo de servidores físicos para Azure e, em seguida, pretende voltar a falhar, tem falhar voltar a uma VM VMware. Não pode falhar voltar a um servidor físico. Terá de um VM Azure para voltar a falhar e, se não tiver implementar o servidor de configuração como uma VM VMware terá de configurar um servidor separado destino principal como uma VM VMware. Isto é necessário porque o servidor de destino mestra interage e anexa ao armazenamento de VMware para restaurar os discos para uma VM VMware.
    - - **Servidor de processo temporário no Azure**: se pretende falhar novamente a partir do Azure após activação pós-falha terá de configurar um VM Azure configurado como um servidor de processo para gerir a replicação do Azure. Pode eliminar este VM após termina de reposição de recurso.
    - **Ligação VPN**: para reposição de recurso terá uma ligação VPN (ou Azure ExpressRoute) de configurar a partir do Azure rede para o site no local.
    - **Servidor de destino do modelo global em separado no local**: O servidor de destino principal no local alças de reposição de recurso. O servidor de destino mestra é instalado por predefinição no servidor de gestão, mas se está a falhar novamente grandes volumes de tráfego deve configurar um servidor de destino Mestra em separado no local para esse efeito.

**Arquitetura de geral**

![Avançada](./media/site-recovery-components/arch-enhanced.png)

**Componentes de implementação**

![Avançada](./media/site-recovery-components/arch-enhanced2.png)

**Reposição de recurso**

![Reposição de recurso melhorada](./media/site-recovery-components/enhanced-failback.png)


- [Saiba mais](site-recovery-vmware-to-azure.md#azure-prerequisites) sobre os requisitos para implementação portal Azure.
- [Saiba mais](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sobre os requisitos de implementação melhorada no portal do clássico.
- [Saiba mais](site-recovery-failback-azure-to-vmware.md) sobre a reposição de recurso no portal do Auzre.
- [Saiba mais](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) sobre a reposição de recurso no portal do clássico.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Repetições para Azure: VMs Hyper-V não são geridos pelo VMM

Pode criar uma réplica VMs Hyper-V que não são geridos pelo sistema Center VMM para Azure com a recuperação de Site da seguinte forma:

- **Utilizando o portal Azure**-quando implementar recuperação de sites no portal do Azure pode falhar ao longo do VMs ao armazenamento clássico ou para o Gestor de recursos. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).
- **Utilizando o portal clássico**-pode implementar recuperação de sites no portal do clássico. Nesta implementação lhe pode apenas falhar ao longo do VMs clássico armazenamento no Azure e não ao armazenamento de Gestor de recursos. [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md).

A arquitetura para ambas as implementações é semelhante, exceto que:

- Se implementar no portal do Azure, pode criar uma réplica ao armazenamento de Gestor de recursos e utilizar redes de Gestor de recursos para ligar o Azure VMs após activação pós-falha.
- O processo de implementação é mais fácil de utilizar no portal do Azure e simplificada.

Eis o que precisa:

- **Conta Azure**: terá de uma conta do Microsoft Azure.
- **Armazenamento Azure**: terá de uma conta de armazenamento Azure para armazenar dados replicados. No portal do Azure pode utilizar uma conta clássica ou com uma conta de armazenamento do Gestor de recursos. No portal do clássico pode utilizar uma conta clássica apenas. Dados replicados são armazenados no Azure armazenamento e Azure VMs são criados quando ocorre activação pós-falha.
- **Rede Azure**: terá de uma rede Azure que Azure VMs irá estabelecer ligação quando estiver a criadas após activação pós-falha. 
- **Anfitrião Hyper-v**: terá de uma ou mais servidor de anfitrião do Windows Server 2012 R2 Hyper-V. Durante a implementação de recuperação de sites que irá instalar o fornecedor de recuperação de Site do Azure e o agente de serviços de recuperação do Microsoft Azure no anfitrião do.
- **VMs Hyper-V**: terá de uma ou mais VMs no servidor de anfitrião Hyper-V. Fornecedor de recuperação de Site Azure e o agente de serviços de recuperação do Azure no anfitrião do Hyper-V durante a implementação de recuperação de sites. O fornecedor de coordenadas e orquestra replicação com o serviço de recuperação do Site através da internet. O agente de trata os dados de replicação de dados ao longo do HTTPS 443. Comunicações a partir do fornecedor e o agente são encriptados e seguro. Dados replicados no Azure armazenamento também estão encriptados.

**Arquitetura de geral**

![Site de Hyper-V para Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [Saiba mais](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sobre os requisitos para implementação portal Azure.
- [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sobre os requisitos para implementação portal clássica.



## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Repetições para Azure: VMs Hyper-V geridas pelo VMM

Pode duplicar Hyper-V VMs no nuvens VMM para Azure com a recuperação de Site da seguinte forma:

- **Utilizando o portal Azure**-quando implementar recuperação de sites no portal do Azure pode falhar ao longo do VMs ao armazenamento clássico ou para o Gestor de recursos. [Saiba mais](site-recovery-vmm-to-azure.md).
- **Utilizando o portal clássico**-pode implementar recuperação de sites no portal do clássico. Nesta implementação lhe pode apenas falhar ao longo do VMs clássico armazenamento no Azure e não ao armazenamento de Gestor de recursos. [Saiba mais](site-recovery-vmm-to-azure-classic.md).

A arquitetura para ambas as implementações é semelhante, exceto que:

- Se implementar no portal do Azure, pode criar uma réplica ao armazenamento baseado em Gestor de recursos e utilizar redes de Gestor de recursos para ligar o Azure VMs após activação pós-falha.
- O processo de implementação é mais fácil de utilizar no portal do Azure e simplificada.


Eis o que precisa:

- **Conta Azure**: terá de uma conta do Microsoft Azure.
- **Armazenamento Azure**: terá de uma conta de armazenamento Azure para armazenar dados replicados. No portal do Azure pode utilizar uma conta clássica ou com uma conta de armazenamento do Gestor de recursos. No portal do clássico pode utilizar uma conta clássica apenas. Dados replicados são armazenados no Azure armazenamento e Azure VMs são criados quando ocorre activação pós-falha.
- **Rede Azure**: terá de configurar o mapeamento de rede para que o Azure VMs estão ligadas a redes adequados quando estiver a criadas após activação pós-falha. 
- **Servidor VMM**: irá precisar de uma ou mais no local VMM servidores a executar no sistema de centro de 2012 R2 e configurar o com um ou mais nuvens privados. Se está a implementar o Azure portal terá de lógica e redes VM configurar para que possa configurar mapeamento de rede. No portal do clássico isto é opcional.  Uma rede VM deve ser ligada a uma rede lógica que está associada a nuvem.
- **Anfitrião Hyper-v**: terá de uma ou mais servidores de anfitrião de Windows Server 2012 R2 Hyper-V na nuvem VMM.
- **VMs Hyper-V**: terá de uma ou mais VMs no servidor de anfitrião Hyper-V.

**Arquitetura de geral**

![VMM para Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [Saiba mais](site-recovery-vmm-to-azure.md#azure-requirements) sobre os requisitos para implementação portal Azure.
- [Saiba mais](site-recovery-vmm-to-azure-classic.md#before-you-start) sobre os requisitos para implementação portal clássica.




## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Repetições para um site secundário: VMware máquinas virtuais ou servidores físicos 

Para criar uma réplica VMware VMs ou servidores físicos para um site secundário como transferência InMage Scout que vem incluído na subscrição de recuperação de sites do Azure. Podem ser transferido a partir do portal do Azure ou a partir do Azure portal clássico. 

Configurar os servidores de componentes em cada site (configuração, processo, destino principal) e instalar o agente de Unified em computadores que pretende criar uma réplica. Após a replicação inicial o agente em cada máquina envia alterações de replicação delta no servidor do processo. O servidor de processo optimiza os dados e transferências para o servidor alvo mestra no site secundário. O servidor de configuração gere o processo de replicação.

Eis o que precisa:

**Conta Azure**: implementar este cenário utilizando InMage Scout. Para obtê-lo terá uma subscrição do Azure. Depois de criar um cofre de recuperação de sites InMage Scout de transferir e instalar as atualizações mais recentes para configurar a implementação.
**Servidor de processo (site principal)**: configurar o componente de servidor do processo para processar colocação em cache, compressão e otimização de dados, no seu site principal. Este também processa instalação push do agente Unified para máquinas que pretende proteger. 
**VMware ESX/ESXi e vCenter server (site principal)**: Se estiver a proteger VMware VMs terá um hipervisor VMware EXS/ESXi e, opcionalmente, um servidor de vCenter VMware para gerir hypervisors.
- **Os servidores de VMs/física (site principal)**: VMware VMs ou Windows/Linux servidores físicos que pretende proteger irá precisar do Agent Unified instalado. Agente de Unified também é instalado em computadores serve como o servidor de destino principal. O agente age como um fornecedor de comunicação entre todos os componentes. 
- - **Servidor de configuração (site secundário)**: O servidor de configuração é o primeiro componente instalar e estiver instalado no site para gerir, configurar e monitorizar a sua implementação, quer utilizar o site de gestão ou consola do vContinuum secundário. Existe apenas um servidor de configuração de único numa implementação e tem de estar instalado num computador a executar o Windows Server 2012 R2.
- **servidor de vContinuum (site secundário)**: é instalado na mesma localização (site secundário) como o servidor de configuração. Fornece uma consola para gerir e monitorizar o seu ambiente protegido. Numa instalação predefinida o servidor de vContinuum é o primeiro servidor de destino principal e tem o agente Unified instalado.
- **Servidor de destino (site secundário) principal**: O servidor de destino mestra retém dados replicados. Receba dados a partir do servidor de processo, cria uma máquina de réplica do site secundário e mantém os pontos de retenção de dados. O número de servidores de destino mestra que precisa depende o número de máquinas que está a proteger. Se pretender que provocam a falha novamente para o site principal terá também existe um servidor de destino principal. 

**Arquitetura de geral**

![VMware para VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Repetições para um site secundário: VMs Hyper-V geridas pelo VMM


Pode criar uma réplica VMs Hyper-V que são geridos pelo sistema Center VMM da seguinte forma para um centro de dados secundário com a recuperação de Site:

- **Utilizando o portal Azure**-quando implementar recuperação de sites no portal do Azure. [Saiba mais](site-recovery-hyper-v-site-to-azure.md).
- **Utilizando o portal clássico**-pode implementar recuperação de sites no portal do clássico. [Saiba mais](site-recovery-hyper-v-site-to-azure-classic.md).

A arquitetura para ambas as implementações é semelhante, exceto que:

- Se implementar no portal do Azure, que tem de configurar mapeamento de rede. Isto é opcional no portal do clássico.
- O processo de implementação é mais fácil de utilizar no portal do Azure e simplificada.
- - Se implementar no Azure portal clássica [mapeamento de armazenamento](site-recovery-storage-mapping.md) está disponível.

Eis o que precisa:

- **Conta Azure**: terá de uma conta do Microsoft Azure.
- **Servidor VMM**: Recomendamos que um servidor VMM no site principal e uma no site do secundário, cada que contém pelo menos uma nuvem privada de VMM. O servidor deve estar em execução, pelo menos, sistema centrar 2012 SP1 com as atualizações mais recentes e ligado à internet. Nuvens deverão ter o perfil da capacidade de Hyper-V definir. Irá instalar o fornecedor de recuperação de Site do Azure no servidor VMM. O fornecedor de coordenadas e orquestra replicação com o serviço de recuperação do Site através da internet. Comunicações entre o fornecedor e Azure são encriptados e seguro.
- **Servidor de Hyper-V**: servidores de anfitrião de Hyper-V devem estar localizados no nuvens VMM principais e secundárias. Anfitrião do servidores devem estar em execução, pelo menos, Windows Server 2012, com as atualizações mais recentes instalado e ligado à internet. Dados são replicados entre os servidores de anfitrião Hyper-V principais e secundários sobre o LAN ou VPN utilizando a autenticação Kerberos ou certificado.  
- **Protegido máquinas**: O servidor de anfitrião de origem Hyper-V deve ter pelo menos uma VM que pretende proteger.

**Arquitetura de geral**

![No local para o local](./media/site-recovery-components/arch-onprem-onprem.png)


- [Saiba mais](site-recovery-vmm-to-vmm.md#azure-prerequisites) sobre os requisitos de implementação no portal do Azure.
- - [Saiba mais](site-recovery-vmm-to-vmm-classic.md#before-you-start) sobre os requisitos de implementação no portal do Azure clássico.




## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Repetições para um site secundário com replicação de SAN: VMs Hyper-V geridas pelo VMM

Pode criar uma réplica VMs Hyper-V gerido no nuvens VMM para um site secundário utilizando a replicação de SAN através do portal clássico Azure. Este cenário atualmente não é suportado no novo portal do Azure. 

Neste cenário durante a implementação de recuperação de sites que irá instalar o fornecedor de recuperação de Site do Azure nos servidores VMM. O fornecedor de coordenadas e orquestra replicação com o serviço de recuperação do Site através da internet. Dados são replicados entre as matrizes de armazenamento principais e secundários utilizando a replicação de SAN síncrona.

Eis o que precisa:

**Conta Azure**: terá de uma subscrição do Azure
- **Matriz de SAN**: uma [suportadas matriz SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) gerido pelo servidor de VMM principal. SAN partilha uma infraestrutura de rede com outra matriz de SAN no site secundário.
- **Servidor VMM**: Recomendamos que um servidor VMM no site principal e uma no site do secundário, cada que contém pelo menos uma nuvem privada de VMM. O servidor deve estar em execução, pelo menos, sistema centrar 2012 SP1 com as atualizações mais recentes e ligado à internet. Nuvens deverão ter o perfil da capacidade de Hyper-V definir.
- **Servidor de Hyper-V**: servidores de anfitrião de Hyper-V localizados na nuvens VMM principais e secundárias. Anfitrião do servidores devem estar em execução, pelo menos, Windows Server 2012, com as atualizações mais recentes instalado e ligado à internet.
- **Protegido máquinas**: O servidor de anfitrião de origem Hyper-V deve ter pelo menos uma VM que pretende proteger.

**Arquitetura de replicação de SAN**

![Replicação de SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Saiba mais](site-recovery-vmm-san.md#before-you-start) sobre os requisitos de implementação.
### <a name="on-premises"></a>No local



## <a name="hyper-v-protection-lifecycle"></a>Ciclo de vida de proteção de Hyper-V

Este fluxo de trabalho apresenta o processo para proteger, replicação e a falhar ao longo do máquinas virtuais de Hyper-V. 

1. **Activar a protecção**: configurar o Cofre de recuperação de sites, configure as definições de replicação para uma nuvem VMM ou site de Hyper-V e ativar proteção para VMs. Uma tarefa de chamada **Ativar protecção** é iniciada e pode ser monitorizada no separador **tarefas** . Verifica se a tarefa que o computador está em conformidade com a pré-requisitos e, em seguida, invoca o método de [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) que configura replicação para Azure com as definições que configurou. A tarefa de **Ativar proteção** invoca também o método de [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para iniciar uma replicação VM completa.
2. **Replicação inicial**: um instantâneo de máquina virtual é disponibilizado e os virtuais rígido discos replicada um a um até que está a todos os copiados para Azure ou ao centro de dados secundário. O tempo necessário para concluir esta depende o método de replicação inicial, largura de banda de rede e o tamanho da memória virtual. Se ocorrerem alterações de disco enquanto a replicação inicial está em curso o controlador de replicação de réplica Hyper-V controla essas alterações como os registos de replicação Hyper-V (.hrl) que estão localizados na mesma pasta como os discos. Cada disco tem um ficheiro associado .hrl que será enviado para armazenamento secundário. Tenha em atenção os instantâneo e ficheiros de registo consumam recursos de disco, enquanto a replicação inicial está em curso. Quando termina a replicação inicial o instantâneo VM é eliminado e as alterações de disco delta no registo de são sincronizadas e intercaladas.
3. **Proteção de Finalize**: Configura após a replicação inicial termina a tarefa de **proteção de Finalize** de rede e outras definições de POST-replicação de modo a que a máquina virtual está protegida. Se estiver a ser replicadas para Azure poderá ter de optimizar as definições de máquina virtual para que seja está preparado para activação pós-falha. Neste momento pode executar um teste activação pós-falha para verificar se que está tudo a funcionar como esperado.
4. **Replicação**: após a replicação inicial sincronização delta começa, de acordo com as definições de replicação. 
    - **Falha de replicação**: se replicação delta falha e uma replicação completa seria dispendiosa em termos de largura de banda ou hora, em seguida, a nova sincronização ocorre. Por exemplo, se os ficheiros de .hrl chegar à 50% do tamanho do disco, em seguida, a VM será marcado para a nova sincronização. Nova sincronização minimiza a quantidade de dados que foi enviados por computação somas de verificação das máquinas virtuais origem e destino e enviar apenas a variação de. Após termina de nova sincronização será retomada replicação delta. Por predefinição, a nova sincronização está agendada para ser executada automaticamente fora do horário de trabalho, mas pode voltar a sincronizar uma máquina virtual manualmente.
    - **Erro de replicação**: se ocorrer um erro de replicação existe uma repetição incorporada. Se é um erro não recuperável como um erro de autenticação ou autorização ou uma máquina de réplica for num estado inválido, em seguida, sem repetir será tentada. Se é um erro recuperável como um erro de rede ou o espaço de disco baixa/memória, em seguida, uma repetição ocorre com intervalos entre tentativas de crescentes (1, 2, 4, 8, 10 e, em seguida, a cada 30 minutos).
4. **Activações pós-falha planeada planeado**: pode executar activações pós-falha planeada ou não planeada, conforme necessário. Se executar uma activação pós-falha planeada, em seguida, origem VMs são encerrar para certificar-se sem perda de dados. Após a criação de réplica VMs elas estiver colocadas numa consolidação pendentes estado. Precisa de consolidá-los para concluir a activação pós-falha, a menos que está a replicação com SAN consolidar cujo caso é automática. Depois do site principal está a trabalhar pode ocorrer a reposição de recurso. Se tiver replicadas para Azure inverso replicação é automática. Caso contrário, pode iniciar replicação inversa manualmente.
 

![fluxo de trabalho](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Próximos passos

[Preparar para implementação](site-recovery-best-practices.md)
