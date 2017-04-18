<properties
    pageTitle="O que fazer em caso de um Azure service interrupção impactos Azure serviços em nuvem | Microsoft Azure"
    description="Saiba o que fazer em caso de uma interrupção de serviço Azure impactos serviços em nuvem Azure."
    services="cloud-services"
    documentationCenter=""
    authors="kmouss"
    manager="drewm"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="cloud-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="kmouss;aglick"/>

#<a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>O que fazer em caso de um Azure service interrupção impactos Azure serviços em nuvem

A Microsoft, podemos trabalhar definitivamente para se certificar de que os nossos serviços estão sempre disponíveis para si quando deles necessita. Forças para além dos nossos controlo, por vezes, ter um impacto-nos de formas que causam interrupções de serviço não planeado.

A Microsoft fornece um contrato de nível de serviço (SLA) para os seus serviços, como um compromisso para tempo útil e conectividade. SLA para serviços Azure individuais pode ser encontrado em [Acordos do nível de serviço de Azure](https://azure.microsoft.com/support/legal/sla/).

Azure já tem muitas funcionalidades de plataforma incorporados que suportam aplicações altamente disponíveis. Para mais informações sobre estes serviços, leia [recuperação de falhas e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Este artigo abrange um cenário de recuperação de falhas verdadeiro, quando uma região toda tem uma falha de devido a falhas natural principais ou de interrupção de serviço executarão. Estas são as ocorrências raras, mas tem de preparar para a possibilidade de que existe uma falha de toda a região. Se a toda a região experiências uma interrupção de serviço, as cópias dos seus dados redundantes localmente seria temporariamente indisponíveis. Se ativou o geo replicação, três cópias adicionais das suas blobs de armazenamento do Windows Azure e as tabelas são armazenadas numa região diferente. Em caso de uma falha de regional concluída ou uma falhas em que a região principal não é recuperável, Azure reatribui todas as entradas DNS à região de replicadas geo.

>[AZURE.NOTE]Tenha em atenção que não tem qualquer controlo sobre este processo e só irá ocorrer para interrupções de serviço de toda a centro de dados. Por esta razão, tem também dependem outras estratégias de cópia de segurança específicos da aplicação para alcançar o nível mais alto de disponibilidade. Para mais informações, consulte a secção sobre [estratégias de dados para recuperação de falhas](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md#DSDR). Se gostaria possam afetar a sua própria activação pós-falha, poderá considerar a utilização de [acesso de leitura armazenamento geo redundantes (GRS RT)](../storage/storage-redundancy.md#read-access-geo-redundant-storage), que cria uma cópia só de leitura dos dados noutra região.

Para ajudar a lidar com estes ocorrências raras, fornecemos-as seguintes orientações para Azure máquinas virtuais (VMs) no caso de uma interrupção de serviço da região inteira onde a sua aplicação de Azure VM é implementada.

##<a name="option-1-wait-for-recovery"></a>Opção 1: Aguardar recuperação
Neste caso, não é necessária nenhuma ação da sua parte. Sabe se equipas Azure estão a funcionar diligentemente para restaurar a disponibilidade do serviço. Pode ver o estado atual do serviço no nosso [Dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Este é a melhor opção se um cliente não configurou a recuperação do Azure Site ou se tiver uma implementação secundária numa região diferente.

Para clientes que quiser acesso imediato aos seus serviços em nuvem implementado, estão disponíveis as seguintes opções.

>[AZURE.NOTE]Tenha em atenção que estas opções têm a possibilidade de perda de dados.     

##<a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>Opção 2: Implementar novamente a configuração do serviço na nuvem para uma nova região

Se tiver o seu código original, basta apenas pode implementar a aplicação, configuração associados e recursos associados a um novo serviço de nuvem numa região nova.  

Para obter mais detalhes sobre como criar e implementar uma aplicação de serviço de nuvem, consulte o artigo [como criar e implementar um serviço na nuvem](./cloud-services-how-to-create-deploy-portal.md).

Dependendo das suas fontes de dados de aplicação, poderá ter verificar os procedimentos de recuperação para a sua origem de dados de aplicação.
  * Para origens de dados de armazenamento do Windows Azure, consulte o artigo [replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para consultar as opções que estão disponíveis com base no modelo de replicação de escolheu para a sua aplicação.
  * Para origens de base de dados SQL, leia o artigo [Descrição geral: na nuvem recuperação de falhas de bases de dados e continuidade empresas com base de dados SQL](../sql-database/sql-database-business-continuity.md) para verificar as opções que estão disponíveis com base no modelo de replicação que selecionou para a sua aplicação.

##<a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>Opção 3: Utilizar uma cópia de segurança implementação através do Gestor de tráfego Azure
Esta opção assume que já criou a sua solução aplicação com recuperação de falhas regionais deve ter em conta. Pode utilizar esta opção se já tiver uma implementação da aplicação dos serviços na nuvem secundário que está a ser executado numa região diferente e ligado através de um canal de Gestor de tráfego. Neste caso, verifique o estado de funcionamento da implementação secundário. Se for Saudável, pode redireccionar tráfego para o mesmo através do Gestor de tráfego Azure. Com esta estratégia, pode tirar partido do método de encaminhamento de tráfego e configurações de ordem activação pós-falha no Azure tráfego Manager. Para mais informações, consulte o artigo [como configurar as definições do Gestor de tráfego](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Equilibrar serviços em nuvem Azure entre as regiões com o Gestor de tráfego Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##<a name="next-steps"></a>Próximos passos

Para saber mais sobre como implementar uma estratégia de elevada disponibilidade e recuperação de falhas, consulte o artigo [recuperação de falhas e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Desenvolver uma compreensão técnica detalhada das capacidades de uma plataforma na nuvem, consulte [orientações técnico de RDP Azure](../resiliency/resiliency-technical-guidance.md).

Se as instruções são não limpar ou se optar por Microsoft para efetuar operações em seu nome, contacte o [Suporte ao cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
