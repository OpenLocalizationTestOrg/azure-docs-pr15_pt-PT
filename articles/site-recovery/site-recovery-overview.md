<properties
    pageTitle="O que é a recuperação de Site? | Microsoft Azure"
    description="Fornece uma descrição geral do serviço de recuperação de sites do Azure e resume cenários de implementação."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>O que é a recuperação de Site?

Bem-vindo à recuperação de Azure Site! Este artigo fornece uma descrição geral rápida do serviço de recuperação de sites e como contribui para o seu negócio.

Organização tem um continuidade do negócio e estratégia de recuperação (BCDR) de falhas manter os dados, das cargas de trabalho e apps seguros e disponível durante o tempo de inatividade planeado e não e, recupera a normal condições de trabalho mais cedo possível. Recuperação de sites é um serviço Azure que contribui para esta estratégia.

Recuperação de sites orquestra replicação de cargas de trabalho em execução servidores físicos no local e máquinas virtuais. Pode replica servidores e VMs a partir de um centro de dados principal para a nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem falhas no site principal, falhar ao longo para o site secundário para manter as aplicações e das cargas de trabalho acessíveis e disponíveis. Falhar voltar a sua localização principal quando devolve ao modo normal.

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de site no portal do Azure

Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos. O modelo de Gestor de recursos do Azure e o modelo de gestão de serviços clássica. Azure também tem dois portais – do [Azure portal clássica](https://manage.windowsazure.com/) compatível com o modelo clássico de implementação e o [Azure portal](https://portal.azure.com) com o suporte para a clássica e modelos de Gestor de recursos.

- Recuperação de site está disponível no portal do clássico e o portal do Azure.
- No portal do Azure clássico, que pode suporta recuperação de Site com o modelo de gestão de serviços clássica.
- No portal do Azure, pode suportar o modelo clássico ou implementações do Gestor de recursos. 

As informações neste artigo aplica-se ao clássicas e Azure implementações portais. Diferenças estão anotadas onde aplicável.


## <a name="why-deploy-site-recovery"></a>Por que motivo implementar recuperação de Site?

Eis o que pode fazer recuperação de Site para a sua empresa:

- **Simplificar BCDR**— pode processar replicação, activação e recuperação de vários cargas de trabalho num único local no portal do Azure. Recuperação de sites orquestra replicação e activação pós-falha mas não interceptar os dados da aplicação ou tiver qualquer informação acerca do mesmo.
- **Fornecer flexível replicação**— utilizando a recuperação de Site, pode criar uma réplica das cargas de trabalho em execução no suportados Hyper-V VMs, VMware VMs e servidores físicos Windows/Linux.
- **Executar a replicação fácil testes**— recuperação de Site fornece activações pós-falha de teste para suportar Brocas de recuperação de falhas, sem afetar os ambientes de produção.
- **Falha ao longo e recuperar**— pode executar activações pós-falha planeada para falhas esperadas com uma perda de dados de zero ou activações pós-falha não planeada com perda de dados mínima (consoante a frequência de replicação) para catástrofes inesperados. Depois de falha na ligação, pode reposição de recurso aos seus sites principais. Recuperação de site fornece os planos de recuperação que podem incluir scripts e livros de automatização Azure, para que pode personalizar activação e recuperação de aplicações com várias camadas.
- **Eliminar um centro de dados secundário**— pode criar uma réplica das cargas de trabalho para Azure, em vez de um site secundário. Isto elimina o custo e complexidade manutenção de um centro de dados secundário. Dados replicados são armazenados no armazenamento do Windows Azure, com todos os resistência que fornece. VMs são criados com os dados de replicadas quando ocorre activação pós-falha.
- **Integrar com tecnologias BCDR existentes**— recuperação de sites integra-se com outras funcionalidades BCDR. Por exemplo, pode utilizar a recuperação de sites para proteger o SQL Server back-end das cargas de trabalho da empresa, incluindo suporte nativo para SQL Server AlwaysOn, para gerir o activação pós-falha de grupos de disponibilidade.

## <a name="what-can-i-replicate"></a>O que pode posso criar uma réplica?

Eis um resumo dos quais pode criar uma réplica utilizando a recuperação do Site.

![No local para o local](./media/site-recovery-overview/asr-overview-graphic.png)

**CRIAR UMA RÉPLICA** | **CRIAR UMA RÉPLICA PARA** 
---|---
Das cargas de trabalho em execução no local VMware VMs | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md)
Das cargas de trabalho em execução no local Hyper-V VMs gerido no nuvens VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Site secundário](site-recovery-vmm-to-vmm.md) 
Carga de trabalho em execução no local Hyper-V VMs gerido no nuvens VMM, com o armazenamento de SAN|  [Site secundário](site-recovery-vmm-san.md)
Das cargas de trabalho em execução no VMs de Hyper-V no local, sem VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Das cargas de trabalho em execução no Windows/Linux servidores de físicos no local | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>O que das cargas de trabalho posso proteger?

Recuperação de sites permite BCDR deverá ter em consideração aplicação, para que das cargas de trabalho e apps continuam a ser executado de uma forma consistente quando ocorrem falhas. Fornece a recuperação de sites:

- **Aplicação consistentes instantâneos**— máquinas criar uma réplica da utilização de instantâneos aplicação consistentes, para as aplicações única ou várias camadas. Para além de capturar dados do disco, captura instantâneos aplicação consistentes capturar todos os dados na memória e todas as operações no processo.
- **Replicação perto síncrono**— recuperação de Site fornece a frequência de replicação é tão baixa quanto 30 segundos para Hyper-V e replicação contínua para VMware.
- **Planos de recuperação flexível**— pode criar e personalizar os planos de recuperação com scripts externos e ações manuais. Integração com o Azure automatização runbooks permitem-lhe recuperar a pilha de uma aplicação completo com um único clique.
- **Integração com o SQL Server AlwaysOn**— pode gerir a activação pós-falha de grupos de disponibilidade nos planos de recuperação de recuperação de sites.
- **Biblioteca de automatização**— uma biblioteca de automatização do Azure rich fornece pronto para produção, específicos da aplicação scripts que podem ser transferidos e integrados com o recuperação de sites.
- **Gestão de rede simples**— a gestão de rede avançadas no Site de recuperação e Azure simplifica requisitos da aplicação rede, incluindo a reserva de endereços IP, configurar balanceadores de carga e integrar o Gestor de tráfego Azure para switchovers eficaz à rede.


## <a name="next-steps"></a>Próximos passos

- Leia mais em [das cargas de trabalho que pode proteger recuperação de Site?](site-recovery-workload.md)
- Saiba mais sobre arquitetura de recuperação de sites no [como a recuperação de sites é que funciona?](site-recovery-components.md)
 
