<properties
    pageTitle="Migrar máquinas virtuais de Azure IaaS a partir de uma região Azure para outro com recuperação de sites | Microsoft Azure"
    description="Utilize a recuperação de Site do Azure migrar máquinas virtuais de Azure IaaS a partir de uma região Azure para outro."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="raynew"/>

#  <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais de Azure IaaS entre regiões Azure com a recuperação de Site do Azure

## <a name="overview"></a>Descrição geral

Bem-vindo ao Azure Site recuperação! Utilize este artigo caso que pretende migrar Azure VMs entre regiões Azure. Antes de começar, tenha em atenção que:

- Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: Gestor de recursos do Azure e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação. Os passos básicos para migração são os mesmos, se estiver a configurar recuperação de Site no Gestor de recursos ou em clássico. No entanto, as instruções de IU e capturas de ecrã neste artigo são relevantes para o portal do Azure.
- **Atualmente pode apenas migrar a partir de uma região para outra. Pode falhar ao longo do VMs a partir de uma região Azure para outro, mas não pode falhá-los novamente novamente.**
- As instruções de migração neste artigo são baseadas nas instruções de replicadas uma máquina física para o Azure. Inclui ligações para os passos na [criar uma réplica VMs VMware ou servidores físicos para Azure](site-recovery-vmware-to-azure.md), que descreve como criar uma réplica um servidor físico no portal do Azure.
- Se estiver a configurar recuperação de sites no portal do clássico, siga as instruções detalhadas [neste](site-recovery-vmware-to-azure-classic.md)artigo. **Já não deve utilizar** as instruções neste [artigo legado](site-recovery-vmware-to-azure-classic-legacy.md).

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa para esta implementação:

- **Servidor de configuração**: uma VM no local com o Windows Server 2012 R2 que age como o servidor de configuração. Instale os componentes de recuperação de sites (incluindo o servidor de processo e o servidor de destino principal) neste VM demasiado. Leia mais em [arquitetura de cenário](site-recovery-vmware-to-azure.md#scenario-architecture) e [Pré-requisitos de servidor de configuração](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Máquinas virtuais de IaaS**: VMs a que pretende migrar. Migrar estes VMs por treating-los como máquinas físicas.

## <a name="deployment-steps"></a>Passos de implementação

Esta secção descreve os passos de implementação no novo portal do Azure. Se precisar destes passos de implementação para recuperação de sites no portal do clássico, consulte [Este artigo](site-recovery-vmware-to-azure-classic.md).

1. [Criar um cofre](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implementar um servidor de configuração](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Depois de já implementou o servidor de configuração, verifique que pode comunicar com os VMs que pretende migrar.
4. [Configurar definições de replicação](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Criar uma política de replicação e atribuir ao servidor de configuração.
5. [Instalar o serviço de mobilidade](site-recovery-vmware-to-azure.md#step-6-replication-application). Necessita de cada VM que pretende proteger o serviço de mobilidade instalado. Este serviço envia dados para o servidor do processo. O serviço de mobilidade pode ser instalado manualmente ou seguia e instalado automaticamente pelo servidor de processo quando proteção para a VM está ativada. Regras de firewall nas VMs que pretende migrar deverão estar configuradas para permitir a instalação de push deste serviço.
6. [Activar a replicação](site-recovery-vmware-to-azure.md#enable-replication). Active a replicação para VMs que pretende migrar. Pode descobrir as máquinas virtuais IaaS que pretende migrar para o Azure utilizando o endereço IP privado das máquinas virtuais. Localize este endereço no dashboard de máquina virtual no Azure. Quando ativa a replicação, que defina o tipo de computador para os VMs como máquinas físicas.
7. [Executar uma activação não planeado pós-falha](site-recovery-failover.md#run-an-unplanned-failover). Depois de replicação inicial estiver concluída, pode executar um activação não planeado pós-falha a partir de uma região Azure para outra. Opcionalmente, pode criar um plano de recuperação e executar uma falha na ligação não planeada, para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre outros cenários de replicação no [o que é o Azure recuperação de Site?](site-recovery-overview.md)
