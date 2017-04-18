<properties
    pageTitle="O que fazer: no caso de uma interrupção de serviço Azure impactos Azure máquinas virtuais | Microsoft Azure"
    description="Saiba o que fazer: no caso de uma interrupção de serviço Azure impactos máquinas virtuais Azure."
    services="virtual-machines"
    documentationCenter=""
    authors="kmouss"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines"
    ms.workload="virtual-machines"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-virtual-machines"></a>O que fazer: no caso de uma interrupção de serviço Azure impactos máquinas virtuais Azure

A Microsoft, podemos trabalhar definitivamente para se certificar de que os nossos serviços estão sempre disponíveis para si quando deles necessita. Forças para além dos nossos controlo, por vezes, ter um impacto-nos de formas que causam interrupções de serviço não planeado.

A Microsoft fornece um contrato de nível de serviço (SLA) para os seus serviços, como um compromisso para tempo útil e conectividade. SLA para serviços Azure individuais pode ser encontrado em [Acordos do nível de serviço de Azure](https://azure.microsoft.com/support/legal/sla/).

Azure já tem muitas funcionalidades de plataforma incorporados que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia [recuperação de falhas e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo abrange um cenário de recuperação de falhas verdadeiro, quando uma região toda tem uma falha de devido a falhas natural principais ou de interrupção de serviço executarão. Estas são as ocorrências raras, mas tem de preparar para a possibilidade de que existe uma falha de toda a região. Se a toda a região experiências uma interrupção de serviço, as cópias dos seus dados redundantes localmente seria temporariamente indisponíveis. Se ativou o geo replicação, três cópias adicionais das suas blobs de armazenamento do Windows Azure e as tabelas são armazenadas numa região diferente. Em caso de uma falha de regional concluída ou uma falhas em que a região principal não é recuperável, Azure reatribui todas as entradas DNS à região de replicadas geo.

>[AZURE.NOTE]Tenha em atenção que não tem qualquer controlo sobre este processo e só irá ocorrer para interrupções de serviço de toda a região. Por esta razão, tem também dependem outras estratégias de cópia de segurança específicos da aplicação para alcançar o nível mais alto de disponibilidade. Para mais informações, consulte a secção sobre [estratégias de dados para recuperação de falhas](../resiliency/resiliency-disaster-recovery-azure-applications.md#data-strategies-for-disaster-recovery).

Para ajudar a lidar com estes ocorrências raras, fornecemos-as seguintes orientações para Azure máquinas virtuais no caso de uma interrupção de serviço da região inteira onde a aplicação de máquina virtual Azure é implementada.

##<a name="option-1-wait-for-recovery"></a>Opção 1: Aguardar recuperação
Neste caso, não é necessária nenhuma ação da sua parte. Sabe que estamos a trabalhar diligentemente para restaurar a disponibilidade do serviço. Pode ver o estado atual do serviço no nosso [Dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Este é a melhor opção se não tiver configurado Azure recuperação de sites, máquina virtual cópia de segurança, armazenamento geo redundantes acesso de leitura ou armazenamento geo redundantes antes das interrupções. Se configurou armazenamento geo redundante ou armazenamento geo redundantes de acesso de leitura para a conta de armazenamento onde estão armazenadas os VM virtuais unidades de disco rígido (VHDs), pode procurar para recuperar a imagem base VHD e tente aprovisionar uma nova VM a partir dos mesmos. Não é uma opção preferida porque não existem sem garantias de sincronização dos dados, a menos que a cópia de segurança do Azure VM ou recuperação de sites do Azure são utilizadas. Por conseguinte, esta opção não se garante a trabalhar.

Para clientes que quiser acesso imediato a máquinas virtuais, duas opções seguintes estão disponíveis.  

>[AZURE.NOTE]Tenha em atenção que têm ambas as opções seguintes a possibilidade de perda de dados.     

##<a name="option-2-restore-a-vm-from-a-backup"></a>Opção 2: Restaurar uma VM a partir de uma cópia de segurança
Para clientes que tenham configurado uma cópia de segurança VM, pode restaurar a VM a partir do seu ponto de cópia de segurança e recuperação.

Para restaurar uma VM nova a partir do Azure cópia de segurança, consulte o artigo [Restaurar máquinas virtuais no Azure](../backup/backup-azure-restore-vms.md).

Para ajudar a planear a sua infraestrutura de cópia de segurança do Azure máquinas virtuais, consulte [Planear a sua infraestrutura de cópia de segurança VM no Azure](../backup/backup-azure-vms-introduction.md).

##<a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Opção 3: Iniciar um activação pós-falha ao utilizar a recuperação de sites do Azure
Se tiver configurado o Azure recuperação de Site para trabalhar com afetada máquinas virtuais Azure, pode restaurar o seu VMs a partir do seus réplicas. Estas réplicas podem residem no Azure ou no local. Neste caso, pode criar uma nova VM a partir do seu réplica já existente. Para restaurar o seu VMs a partir de uma réplica Azure recuperação de sites, consulte o artigo [Migrar IaaS Azure virtual máquinas entre regiões Azure com Azure recuperação de Site](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Apesar do sistema operativo de máquina virtual Azure e os dados discos vai ser replicados para um VHD secundário, se estiverem numa conta de armazenamento geo redundantes acesso de leitura ou de armazenamento geo redundante, cada VHD é replicada forma independente. Este nível de replicação não garante consistência ao longo de replicadas VHDs. Se a aplicação e/ou bases de dados que utilizam estes discos de dados tem dependências uns dos outros, não é garante que todos os VHDs são replicados como um instantâneo. Não é também possível garantir que a réplica VHD a partir do armazenamento geo redundante ou armazenamento geo redundantes acesso de leitura irá resultar num instantâneo consistente de aplicação para iniciar a VM.

##<a name="next-steps"></a>Próximos passos
Para saber mais sobre como implementar uma estratégia de elevada disponibilidade e recuperação de falhas, consulte o artigo [recuperação de falhas e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma na nuvem, consulte o artigo [orientações técnicas RDP Azure](../resiliency/resiliency-technical-guidance.md).

Para saber como criar uma cópia de segurança VMs, consulte o artigo [criar uma cópia de segurança máquinas virtuais Azure](../backup/backup-azure-vms.md).

Saiba como utilizar o Azure recuperação de sites para orquestrar e automatizar a proteção da sua físicas (e) Windows e Linux máquinas virtuais que executar no VMWare e Hyper-V VMs, consulte o artigo [Azure recuperação de sites](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Se as instruções são não limpar, ou se optar por Microsoft para efetuar operações em seu nome, contacte o [Suporte ao cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
