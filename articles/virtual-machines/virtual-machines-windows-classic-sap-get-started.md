<properties
   pageTitle="Utilizar o SAP em máquinas virtuais de Windows | Microsoft Azure"
   description="Limpar sobre como utilizar SAP no Windows máquinas virtuais (VMs) Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Utilizar o SAP máquinas virtuais Windows Azure

É um termo amplamente utilizado que está a ganhar mais importância dentro da indústria IT, de pequenas empresas até empresas de grandes e multinacionais de informática em nuvem. Microsoft Azure é a plataforma de serviços na nuvem da Microsoft que disponibiliza um grande espectro de novas possibilidades. Agora clientes que conseguem aprovisionar e anular aprovisionar aplicações como serviços na nuvem, para que não estão limitados a restrições técnicas ou orçamentação rapidamente. Em vez de investir tempo e o orçamento para infraestrutura hardware, empresas podem concentrar-na aplicação, processos de negócio e os benefícios para clientes e utilizadores.

Com máquinas virtuais do Microsoft Azure, a Microsoft oferece uma infraestrutura abrangente como uma plataforma de serviço (IaaS). As aplicações SAP NetWeaver com base são suportadas no Azure máquinas virtuais (IaaS). Os documentos técnicos abaixo descrevem como planear e implementar aplicações SAP NetWeaver com base em máquinas virtuais Windows Azure. Também pode implementar aplicações SAP NetWeaver com base em [máquinas virtuais de Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver no Azure - HA

Título: SAP NetWeaver no Azure - clusters SAP ASCS/SCS instâncias utilizando Cluster de activação pós-falha do Windows Server no Azure com SIOS DataKeeper

Resumo: ' Este documento descreve como utilizar SIOS DataKeeper para definir uma configuração SAP ASCS/SCS altamente disponíveis no Azure. SAP protege a sua único ponto de componentes de falha como SAP ASCS/SCS ou serviços de replicação de colocar com configurações de Cluster de activação pós-falha do Windows Server que requerem discos partilhados. Estes componentes SAP são essenciais para a funcionalidade de um sistema SAP. Por conseguinte, a funcionalidade de alta disponibilidade tem de colocar num local para se certificar de que essas componentes podem manter a uma falha de um servidor ou uma VM como concluído com configurações de Cluster do Windows para ambientes de Hyper-V e base. Partir Agosto de 2015 Azure sobre si próprio não pode fornecer discos partilhados, que seriam necessários para o Windows com base configurações altamente disponíveis necessárias para estes componentes SAP críticas. No entanto com a ajuda do produto DataKeeper por SIOS, podem ser criadas configurações de Cluster de activação pós-falha do Windows Server, conforme necessário para SAP ASCS/SCS a plataforma Azure IaaS. Este papel numa abordagem de passo a passo descreve como instalar uma configuração de Cluster de activação pós-falha do Windows Server com disco partilhado fornecido pela SIOS Datakeeper no Azure. Papel irá explicar detalhes no configurações no lado Azure, Windows e pela SAP, que tornam a configuração de elevada disponibilidade trabalhar de forma ideal. Papel completa a documentação de instalação do SAP e SAP notas que representam os recursos principais para implementações do software SAP e instalações em determinadas plataformas.

Atualização: Agosto de 2015

[Transferir este guia agora](http://go.microsoft.com/fwlink/?LinkId=613056)
