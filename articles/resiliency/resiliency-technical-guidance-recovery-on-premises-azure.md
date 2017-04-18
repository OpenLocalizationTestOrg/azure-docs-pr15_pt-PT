<properties
   pageTitle="Orientação técnica: recuperação nos locais para Azure | Microsoft Azure"
   description="Artigo sobre compreensão e recuperação de estruturar sistemas de infraestrutura no local para Azure"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Orientação técnica do Azure RDP: recuperação nos locais para Azure

Azure fornece um conjunto abrangente de serviços para ativar a extensão de um centro de dados no local para Azure para fins de recuperação de disponibilidade e falhas alta:

* __Funcionamento em rede__: com uma rede privada virtual, em segurança expande a sua rede no local para a nuvem.
* __Calcular__: clientes utilizando o Hyper-V no local podem "levantar e shift" existentes máquinas virtuais (VMs) para Azure.
* __Armazenamento__: StorSimple expande o seu sistema de ficheiros ao armazenamento do Azure. O serviço de cópia de segurança do Azure fornece cópia de segurança para ficheiros e bases de dados SQL ao armazenamento do Azure.
* __Replicação de bases de dados__: com SQL Server 2014 (ou posterior) disponibilidade grupos, pode implementar alta recuperação de disponibilidade e falhas para os seus dados no local.

##<a name="networking"></a>Funcionamento em rede

Pode utilizar a rede Virtual Azure para criar uma secção logicamente isolada no Azure e ligá-lo em segurança para o Centro de dados no local ou uma máquina de cliente único utilizando uma ligação de IPsec. Com a rede Virtual, pode tirar partido infraestrutura do dimensionáveis, a pedido no Azure enquanto fornecer conectividade a dados e as aplicações no local, incluindo os sistemas de em execução no Windows Server, mainframes e UNIX. Para obter mais informações, consulte a [documentação de rede do Azure](../virtual-network/virtual-networks-overview.md) .

##<a name="compute"></a>Cluster

Se estiver a utilizar o Hyper-V no local, pode "levantar e shift" existentes máquinas virtuais para Azure e executar o Windows Server 2012 (ou posterior), sem fazer alterações à VM ou converter VM formatos de fornecedores de serviços. Para mais informações, consulte o artigo [sobre discos e VHDs para máquinas virtuais Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a name="azure-site-recovery"></a>Recuperação de Azure Site

Se pretender recuperação de falhas como um serviço (DRaaS), o Azure fornece [Azure recuperação de sites](https://azure.microsoft.com/services/site-recovery/). Recuperação de Site Azure oferece proteção contra abrangente para os servidores de VMware, Hyper-V e físicas. Com o Azure recuperação de Site, pode utilizar outro servidor no local ou Azure como o seu site de recuperação. Para mais informações sobre Azure recuperação de sites, consulte a [documentação do Azure recuperação de sites](https://azure.microsoft.com/documentation/services/site-recovery/).

##<a name="storage"></a>Armazenamento

Existem várias opções para utilizar o Azure como um site de cópia de segurança para dados no local.

###<a name="storsimple"></a>StorSimple

StorSimple em segurança e transparente integra-se armazenamento em nuvem para as aplicações no local. Também oferece um única aparelho que proporciona local em camadas de alto desempenho e armazenamento em nuvem, arquivar direto, proteção de dados baseadas na nuvem e recuperação de falhas. Para mais informações, consulte a [página de produto StorSimple](https://azure.microsoft.com/services/storsimple/).

###<a name="azure-backup"></a>Cópia de segurança do Azure

Cópia de segurança Azure permite nuvem cópias de segurança ao utilizar as ferramentas familiares de cópia de segurança no Windows Server 2012 (ou posterior), o Windows Server 2012 Essentials (ou posterior) e o Gestor de proteção de dados do sistema Centro 2012 (ou posterior). Estas ferramentas fornecem um fluxo de trabalho para gestão de cópia de segurança que seja independente da localização de armazenamento das cópias de segurança, se um disco local ou o armazenamento do Windows Azure. Depois de dados são cópia de segurança na nuvem, os utilizadores autorizados facilmente podem recuperar cópias de segurança para qualquer servidor.

Com cópias de segurança elementares, apenas alterações a ficheiros são transferidas para a nuvem. Isto ajuda a utilizar o espaço de armazenamento eficientemente, reduzir o consumo de largura de banda e suportar a recuperação de em qualquer altura de várias versões dos dados. Também pode optar por utilizar funcionalidades adicionais, como políticas de retenção de dados, compressão de dados e limitação de transferência de dados. Utilizar o Azure como localização da cópia de segurança tem a vantagem óbvia as cópias de segurança são automaticamente "noutro local". Isto elimina os requisitos adicionais para proteger o suporte de cópia de segurança no local.

Para obter mais informações, consulte o artigo [o que é o Azure cópia de segurança?](../backup/backup-introduction-to-azure-backup.md) e [Configurar o Azure cópia de segurança para os dados DPM](https://technet.microsoft.com/library/jj728752.aspx).

##<a name="database"></a>Base de dados

Pode ter uma solução de recuperação de falhas para as bases de dados do SQL Server num ambiente híbrido-IT ao utilizar grupos de Disponibilidade AlwaysOn, espelhando as bases de dados, envio do registo e cópia de segurança e restaurar com o armazenamento de Blobs do Azure. Utilizam todas estas soluções do SQL Server em máquinas virtuais do Azure de executar.

Grupos de Disponibilidade AlwaysOn podem ser utilizados num ambiente híbrido-IT onde réplicas de base de dados existem ambas no local e na nuvem. Isto é mostrado no seguinte diagrama.

![Grupos de disponibilidade do SQL Server AlwaysOn numa arquitetura de nuvem híbrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

Também podem abranger a base de dados espelhando as servidores no local e na nuvem numa configuração baseada em certificados. O diagrama seguinte ilustra este conceito.

![SQL Server espelhando as bases de dados numa arquitetura de nuvem híbrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

O envio de registo pode ser utilizado para sincronizar uma base de dados no local com uma base de dados do SQL Server uma máquina virtual Azure.

![Registo do SQL Server envio numa arquitetura de nuvem híbrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

Por fim, pode agregar uma base de dados no local diretamente para o armazenamento de Blobs do Azure.

![Cópia de segurança do SQL Server ao armazenamento de Blobs do Azure numa arquitetura de nuvem híbrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Para mais informações, consulte o artigo [Alta recuperação de disponibilidade e falhas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md) e de [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Listas de verificação para recuperação no local no Microsoft Azure

###<a name="networking"></a>Funcionamento em rede

  1. Reveja a secção funcionamento em rede deste documento.
  2. Utilize a rede Virtual para ligar em segurança no local à nuvem.

###<a name="compute"></a>Cluster

  1. Reveja a secção cluster deste documento.
  2. Reposicionar VMs entre Hyper-V e Azure.

###<a name="storage"></a>Armazenamento

  1. Reveja a secção de armazenamento deste documento.
  2. Tire partido dos serviços de StorSimple para utilizar o armazenamento em nuvem.
  3. Utilize o serviço de cópia de segurança do Azure.

###<a name="database"></a>Base de dados

  1. Reveja a secção de base de dados deste documento.
  2. Considere utilizar o SQL Server Azure VMs como a cópia de segurança.
  3. Configure o grupos de Disponibilidade AlwaysOn.
  4. Configure base de dados com base no certificado espelhando as.
  5. Utilize o envio de registo.
  6. Criar cópias de segurança bases de dados no local ao armazenamento de Blobs do Azure.

##<a name="next-steps"></a>Próximos passos

Este artigo faz parte de uma série com o foco nas [orientações técnico RDP Azure](./resiliency-technical-guidance.md). O seguinte artigo nesta série é a [recuperação de danos nos dados ou acidentais](./resiliency-technical-guidance-recovery-data-corruption.md).