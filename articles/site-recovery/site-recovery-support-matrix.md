<properties
    pageTitle="Matriz de suporte de recuperação de sites Azure | Microsoft Azure"
    description="Resume os sistemas operativos suportados e os componentes de recuperação de Site do Azure"
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
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="azure-site-recovery-support-matrix"></a>Matriz de suporte do Azure recuperação de sites

Este artigo resume os sistemas operativos suportados e componentes para implementações do Azure recuperação de sites. Uma lista de componentes suportados e pré-requisitos está disponível para cada cenário de implementação em cada o artigo correspondente na implementação e este documento resume-los.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Sistemas operativos suportados por servidores Virtualização


**Origem** | **Destino** | **Anfitrião SO**
---|---|---|--- 
**Anfitriões Hyper-V (sem VMM)** | Azure | Windows Server 2012 R2 com as atualizações mais recentes
**Anfitriões Hyper-V (com VMM)** | Azure | Windows Server 2012 R2 com as atualizações mais recentes
**Anfitriões Hyper-V (com VMM)** | Site VMM secundário | Pelo menos o Windows Server 2012 com as atualizações mais recentes
**VMware anfitriões/vCenter** | Azure | vCenter 5.5 ou 6.0 (suporte para 5,5 funcionalidades apenas) <br/><br/> vSphere 6.0, 5.5 ou 5.1 com as atualizações mais recentes
**VMware anfitriões/vCenter** | Site de VMware secundário | vCenter 5.5 ou 6.0 (suporte para 5,5 funcionalidades apenas) <br/><br/> vSphere 6.0, 5.5 ou 5.1 com as atualizações mais recentes

## <a name="supported-requirements-for-replicated-machines"></a>Requisitos de suportados para máquinas replicadas

**Origem** | **O que é replicado** | **Destino** | **Anfitrião SO**
---|---|---|--- 
**VMs Hyper-V** | Qualquer carga de trabalho | Azure | Qualquer SO convidado [suportados pelo Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> VMs tem de cumprir [requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs Hyper-V (com VMM)** | Qualquer carga de trabalho | Azure | Qualquer SO convidado [suportados pelo Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> VMs tem de cumprir [requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMs Hyper-V (com VMM)** | Qualquer carga de trabalho | Site VMM secundário | Qualquer SO convidado [suportados pelo Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**VMware VMs** | Qualquer carga de trabalho em execução no Windows VM | Azure | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 na SP1, pelo menos,<br/><br/> VMs tem de cumprir [requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware VMs** | Qualquer carga de trabalho em execução no Linux VM | Azure | Chapéu vermelho Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento obrigatório: (EXT3, ETX4, ReiserFS, XFS); de sistema de ficheiros I-dispositivo de software mapeamento de pontos (i)); Gestor de volume: (LVM2). Servidores físicos com HP CCISS controlador de armazenamento não são suportados. O sistema de ficheiros ReiserFS só é suportado em SUSE Linux Enterprise Server 11 SP3.<br/><br/> VMs tem de cumprir [requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VMware VMs** | Qualquer carga de trabalho em execução no Windows VM | Site de VMware secundário | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 na SP1, pelo menos,
**VMware VMs** | Qualquer carga de trabalho em execução no Linux VM | Site de VMware secundário | Chapéu vermelho Enterprise Linux 6,7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6,7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento obrigatório: (EXT3, ETX4, ReiserFS, XFS); de sistema de ficheiros I-dispositivo de software mapeamento de pontos (i)); Gestor de volume: (LVM2). Servidores físicos com HP CCISS controlador de armazenamento não são suportados. O sistema de ficheiros ReiserFS só é suportado em SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Qualquer carga de trabalho em execução no Windows | Azure | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 com na SP1, pelo menos,
**Servidores físicos** | Qualquer carga de trabalho em execução no Linux | Azure | Chapéu vermelho Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento obrigatório: (EXT3, ETX4, ReiserFS, XFS); de sistema de ficheiros I-dispositivo de software mapeamento de pontos (i)); Gestor de volume: (LVM2). Servidores físicos com HP CCISS controlador de armazenamento não são suportados. O sistema de ficheiros ReiserFS só é suportado em SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Qualquer carga de trabalho em execução no Windows | Site secundário | 64-bit Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 com na SP1, pelo menos,
**Servidores físicos** | Qualquer carga de trabalho em execução no Linux | Site secundário | Chapéu vermelho Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Armazenamento obrigatório: (EXT3, ETX4, ReiserFS, XFS); de sistema de ficheiros I-dispositivo de software mapeamento de pontos (i)); Gestor de volume: (LVM2). Servidores físicos com HP CCISS controlador de armazenamento não são suportados. O sistema de ficheiros ReiserFS só é suportado em SUSE Linux Enterprise Server 11 SP3.


## <a name="provider-versions"></a>Versões de fornecedor

**Nome** | **Descrição** | **Versão mais recente** | **Suporte** | **Detalhes**
---|---|---|---| ---
**Fornecedor de recuperação de Azure Site** | Coordenadas comunicações entre servidores no local e o site do Azure/secundário <br/><br/> Se não existir VMM servidor instalado no local VMM servidores, ou Hyper-V | 5.1.1700 (disponível a partir do portal) | [Correções e funcionalidades mais recentes](https://support.microsoft.com/kb/3155002)
**Recuperação de Azure Site Unified configuração (VMware para Azure)** | Coordenadas as comunicações entre servidores de VMware no local e do Azure <br/><br/> Instalados nos servidores de VMware no local | 9.3.4246.1 (disponível a partir do portal) | [Correções e funcionalidades mais recentes](https://support.microsoft.com/kb/3155002)
**Serviço de mobilidade** | Replicação de coordenadas entre servidores de servidores/física VMware no local e o site do Azure/secundário | DISP (disponível a partir do portal) | Instalado no cada VMware VM ou servidor físico ao qual pretende criar uma réplica. **Agente do Microsoft Azure recuperação serviços (MARTE)** | Replicação de coordenadas entre VMs Hyper-V e Azure<br/><br/> Instalados nos servidores de Hyper-V no local (com ou sem um servidor de VMM) | 2.0.8689.0 (disponível a partir do portal) | Este agente é utilizado pela recuperação de Site do Azure e Azure cópia de segurança). [Mais] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Próximos passos

[Preparar para implementação](site-recovery-best-practices.md)

