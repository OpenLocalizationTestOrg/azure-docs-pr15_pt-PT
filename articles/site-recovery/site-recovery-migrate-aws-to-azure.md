<properties
    pageTitle="Migrar máquinas virtuais de Windows a partir do Amazon Web Services para o Azure com a recuperação de Site | Microsoft Azure"
    description="Este artigo descreve como migrar Windows máquinas virtuais nos serviços do Amazon Web (Voltada) para Azure utilizando Azure a recuperação do Site."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="08/22/2016"
    ms.author="raynew"/>

#  <a name="migrate-windows-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuais do Windows no Serviços de Web Amazon (AWS) para o Azure com a recuperação de Site do Azure

## <a name="overview"></a>Descrição geral

Bem-vindo ao Azure Site recuperação. Utilize este artigo para migrar instâncias do Windows a ser executada em AWS para Azure com a recuperação de Site. Antes de começar, tenha em atenção que:

- Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: Gestor de recursos do Azure e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação. Os passos básicos para migração são os mesmos, se estiver a configurar recuperação de Site no Gestor de recursos ou em clássico. No entanto, as instruções de IU e capturas de ecrã neste artigo são relevantes para o portal do Azure.
- **Atualmente pode apenas migrar a partir do AWS para o Azure. Pode falhar ao longo do VMs a partir do AWS para Azure, mas não pode falhá-los novamente novamente. Não existe nenhuma replicação em curso.**
- As instruções de migração neste artigo são baseadas nas instruções de replicadas uma máquina física para o Azure. Inclui ligações para os passos na [criar uma réplica VMs VMware ou servidores físicos para Azure](site-recovery-vmware-to-azure.md), que descreve como criar uma réplica um servidor físico no portal do Azure.
- Se estiver a configurar recuperação de sites no portal do clássico, siga as instruções detalhadas [neste](site-recovery-vmware-to-azure-classic.md)artigo. **Já não deve utilizar** as instruções neste [artigo legado](site-recovery-vmware-to-azure-classic-legacy.md).

Registar quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## <a name="prerequisites"></a>Pré-requisitos

Eis o que precisa para esta implementação

- **Servidor de configuração**: uma VM no local com o Windows Server 2012 R2 que age como o servidor de configuração. Instale os componentes de recuperação de sites (incluindo o servidor de processo e o servidor de destino principal) neste VM demasiado. Leia mais em [arquitetura de cenário](site-recovery-vmware-to-azure.md#scenario-architecture) e [Pré-requisitos de servidor de configuração](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Instâncias de CE da Comissão2 VM**: as instâncias a executar o Windows que pretende migrar.

## <a name="deployment-steps"></a>Passos de implementação

Esta secção descreve os passos de implementação no novo portal do Azure. Se precisar destes passos de implementação para recuperação de sites no portal do clássico, consulte [Este artigo](site-recovery-vmware-to-azure-classic.md).

1. [Criar um cofre](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implementar um servidor de configuração](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Depois de ter implementado o servidor de configuração, validar que pode comunicar com os VMs que pretende migrar.
4. [Configurar definições de replicação](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Criar uma política de replicação e atribuir ao servidor de configuração.
5. [Instalar o serviço de mobilidade](site-recovery-vmware-to-azure.md#step-6-replication-application). Necessita de cada VM que pretende proteger o serviço de mobilidade instalado. Este serviço envia dados para o servidor do processo. O serviço de mobilidade pode ser instalado manualmente ou seguia e instalado automaticamente pelo servidor de processo quando proteção para a VM está ativada. Regras de firewall em instâncias CE da Comissão2 que pretende migrar deverão estar configuradas para permitir a instalação de push deste serviço. O grupo de segurança para CE da Comissão2 instâncias deve ter as seguintes regras:

    ![regras da firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Activar a replicação](site-recovery-vmware-to-azure.md#enable-replication). Active a replicação para VMs que pretende migrar. Pode descobrir as instâncias de CE da Comissão2 utilizando os endereços IP privados, que pode obter a partir da consola de CE da Comissão2.
7. [Executar uma activação não planeado pós-falha](site-recovery-failover.md#run-an-unplanned-failover). Depois de replicação inicial estiver concluída, pode executar uma activação não planeado pós-falha de AWS para Azure para cada VM. Opcionalmente, pode criar um plano de recuperação e executar uma falha na ligação não planeada, migrar várias máquinas virtuais de AWS para Azure. [Saiba mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre outros cenários de replicação no [o que é o Azure recuperação de Site?](site-recovery-overview.md)
