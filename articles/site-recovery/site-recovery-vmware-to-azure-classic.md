<properties
    pageTitle="Criar uma réplica máquinas virtuais de VMware e servidores físicos para Azure com Azure Site recuperação | Microsoft Azure"
    description="Este artigo descreve como implementar o Azure recuperação de Site para orquestrar replicação, activação e recuperação de máquinas de virtuais VMware no local e Windows/Linux servidores físicos para Azure."
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
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Criar uma réplica máquinas virtuais de VMware e servidores físicos para Azure com a recuperação de Site do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Portal clássico](site-recovery-vmware-to-azure-classic.md)
- [Portal clássico (Legado)](site-recovery-vmware-to-azure-classic-legacy.md)


O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md).

## <a name="overview"></a>Descrição geral

Este artigo descreve como:

- **Criar uma réplica VMware virtual máquinas para Azure**— implementar recuperação de Site para coordenar replicação, activação pós-falha e recuperação de máquinas de virtuais VMware no local ao armazenamento Azure.
- **Criar uma réplica servidores físicos para Azure**— implementar recuperação do Azure Site para coordenar replicação, activação pós-falha e recuperação de servidores no local físicas Windows e Linux para Azure.

>[AZURE.NOTE] Este artigo descreve como criar uma réplica para Azure. Se pretender criar uma réplica VMware VMs ou servidores físicos Windows/Linux para um centro de dados secundário, siga as instruções [neste](site-recovery-vmware-to-vmware.md)artigo.

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Implementação melhorada

Este artigo inclui contém as instruções para uma implementação melhorada no portal do Azure clássica. Recomendamos que utilize esta versão para todas as implementações novas. Se já implementou utilizando uma versão anterior legada Recomendamos que migrar para a nova versão. Leia [mais](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) sobre a migração.

A implementação melhorada é uma atualização principal. Eis um resumo dos melhoramentos efetuámos:

- **Sem infraestrutura VMs no Azure**: replica os dados diretamente para uma conta de armazenamento Azure. Além de replicação e activação pós-falha não é necessário para configurar o qualquer infraestrutura VMs (servidor de configuração, servidor de destino principal), podemos conforme necessário na implementação legada.  
- **Instalação unificado**: fornece uma instalação única configuração simple e escalabilidade para componentes no local.
- **Implementação segura**: todo o tráfego está encriptado e comunicações de gestão de replicação são enviadas através de HTTPS 443.
- **Pontos de recuperação**: pontos de suporte para falha de sistema e recuperação aplicação consistente para Windows e Linux ambientes e suporta ambos único VM e multi VM configurações consistentes.
- **Testar activação pós-falha**: suporte para o teste não desorganização failover para Azure, sem que afetam produção ou interromper a replicação.
- **Não planeado activação pós-falha**: suporte para não planeado activação pós-falha ao Azure uma opção avançada para encerrar automaticamente a VMs antes de activação pós-falha.
- **Reposição de recurso**: integrada reposição de recurso que replica apenas as alterações de delta novamente para o site no local.
- **vSphere 6.0**: suporte para implementações VMware Vsphere 6.0 limitado.


## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Como é que o Site recuperação ajuda a proteger máquinas virtuais e servidores físicos?


- Os administradores de VMware podem configurar a protecção externa para Azure das cargas de trabalho de negócio e aplicações em execução em máquinas virtuais de VMware. Gestores de Server podem criar uma réplica física servidores no local Windows e Linux para Azure.
- Consola de recuperação de Site do Azure fornece uma única localização para a configuração simple e gestão de replicação, activação pós-falha e processos de recuperação.
- Se replica máquinas virtuais VMware que são geridas por um servidor de vCenter, recuperação de sites pode descobrir essas VMs automaticamente. Se forem máquinas num sistema anfitrião ESXi recuperação de sites detetar VMs no anfitrião do.
- Execute activações pós-falha fácil a partir do seu infraestrutura no local para Azure e reposição de recurso (restaurar) a partir do Azure para servidores VMware VM do site no local.
- Configure os planos de recuperação agrupar das cargas de trabalho de aplicação que são camadas através de múltiplos computadores. Pode falhar sobre os planos e recuperação de Site fornece consistência multi VM para que máquinas a executar as mesmo das cargas de trabalho podem ser recuperadas em conjunto para um ponto de dados consistentes.


## <a name="supported-operating-systems"></a>Sistemas operativos suportados

### <a name="windows64-bit-only"></a>Windows (apenas 64 bits)
- Windows Server 2008 R2 SP1 +
- Windows Server 2012
- Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (apenas 64 bits)
- Chapéu vermelho Enterprise Linux 6,7, 7.1, 7.2
- CentOS 6.5, 6.6, 6,7, 7.0, 7.1, 7.2
- Oracle Enterprise Linux 6.4, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Arquitetura de cenário

Componentes de cenário:

- **Um servidor de gestão de no local**: O servidor de gestão executa componentes de recuperação de sites:
    - **Servidor de configuração**: coordenadas comunicação e gere os processos de replicação e recuperação de dados.
    - **Servidor do processo**: age como um gateway replicação. Receba dados a partir máquinas origem protegida, optimiza-o com colocação em cache, compressão e encriptação e envia dados de replicação ao armazenamento Azure. Também processa instalação push do serviço de mobilidade para máquinas protegidas e executa a deteção automática de VMware VMs.
    - **Servidor de destino do modelo global**: processa dados de replicação durante a reposição de recurso a partir do Azure.
    Também pode implementar um servidor de gestão que age como um servidor de processo apenas, para poder dimensionar a sua implementação.
- **Serviço de mobilidade**: este componente é implementado em cada máquina (VMware VM ou servidor físico) que pretende criar uma réplica para Azure. -Captura escritas de dados no computador e encaminha-los para o servidor do processo.
- **Azure**: não precisa de criar qualquer VMs Azure para processar replicação e activação pós-falha. O serviço de recuperação de sites processa gestão de dados e dados replica diretamente ao armazenamento Azure. Replicada Azure VMs são automaticamente fiadas o apenas quando ocorre activação pós-falha ao Azure. No entanto, se pretender que provocam a falha novamente a partir do Azure para o site no local terá de configurar um VM Azure para funcionar como um servidor do processo.


O gráfico que mostra como estes componentes interagem.

![arquitetura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/física para Azure** (criada pelo Henry Robalino)


## <a name="capacity-planning"></a>Planeamento de capacidades

Quando está a planear capacidade, aqui o que precisa para em consideração:

- **O ambiente de origem**— planeamento da capacidade ou os requisitos de máquina VMware infraestrutura e origem.
- **Servidor de gestão de**— planeamento para os servidores de gestão de no local que executar componentes de recuperação de sites.
- **Largura de banda de rede de origem para destino**-planeamento de largura de banda de rede necessária para a replicação entre origem e Azure

### <a name="source-environment-considerations"></a>Considerações de ambiente de origem

- **Taxa de alteração de máxima diariamente**— uma máquina protegida só pode utilizar um servidor de processo e um servidor de um único processo pode tratar 2 TB de alteração de dados por dia. Assim 2 TB é que os dados diários máximos alterar a taxa que é suportada para uma máquina protegida.
- **Débito máximo**— uma máquina de replicadas pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20.000 pedidos por segundo e, recomendamos que mantenha o número de IOPS através de uma máquina de origem a 20000. Por exemplo se tiver uma máquina de origem com 5 discos e cada disco gera 120 IOPS (tamanho de 8K) na origem de, em seguida, será dentro Azure por limite IOPS disco de 500. O número de contas de armazenamento necessárias = origem do total IOPs/20000.


### <a name="management-server-considerations"></a>Considerações de servidor de gestão

O servidor de gestão é executado componentes de recuperação de sites que processam otimização de dados, replicação e gestão. Deverá conseguir processar a capacidade de taxa de alteração diária através de todas as cargas de trabalho em execução no máquinas protegidas e tem largura de banda suficiente para continuamente criar uma réplica dados ao armazenamento Azure. Especificamente:

- O servidor de processo recebe dados de replicação de máquinas protegidas e optimiza-lo com a colocação em cache, compressão e encriptação antes de enviar a Azure. Servidor de gestão de deve ter recursos suficientes para efetuar estas tarefas.
- O servidor de processo utiliza cache baseada no disco. Recomendamos que um disco de cache separada de 600 GB ou mais para processar alterações aos dados armazenadas trabalho congestionamento de rede ou falha do. Durante a implementação pode configurar a cache em qualquer unidade que tenha pelo menos 5 GB de armazenamento disponível mas 600 GB é a recomendação mínima.
- Como prática recomendada Recomendamos que o servidor de gestão de estar localizados na mesma rede e segmento LAN como máquinas que pretende proteger. Pode estar localizado numa rede diferente mas máquinas que pretende proteger devem ter L3 visibilidade de rede à mesma.

Recomendações de tamanho para o servidor de gestão estão resumidas na tabela seguinte.

**Servidor de gestão de CPU** | **Memória** | **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Implemente um servidor de gestão com estas definições para criar uma réplica máquinas menos de 100.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB para 1 TB | Implemente um servidor de gestão com estas definições para criar uma réplica entre 100 e 150 máquinas.
16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB para 2 TB | Implemente um servidor de gestão com estas definições para criar uma réplica entre máquinas de 150-200.
Implementar outro servidor de processo | | | > 2 TB | Implemente os servidores de processo adicionais se estiver a replicação máquinas mais de 200 ou se os dados diários alterar taxa excede 2 TB.

Em que:

- Cada máquina de origem está configurada com 3 discos de 100 GB.
- Armazenamento de avaliação comparativa de unidades de SA 8 de 10 K rotações/minuto é utilizada com RAID 10 para as medidas de disco cache.

### <a name="network-bandwidth-from-source-to-target"></a>Largura de banda rede de origem para destino
Certifique-se de que calcula a largura de banda seria necessária para a replicação inicial e a replicação de delta utilizando a [ferramenta de Planeador de capacidade](site-recovery-capacity-planner.md)

#### <a name="throttling-bandwidth-used-for-replication"></a>Limitação de largura de banda utilizada para a replicação

Vai tráfego VMware replicado para Azure através de um servidor de processo específico. Pode optimizar a largura de banda que está disponível para a replicação de recuperação de sites nesse servidor da seguinte forma:

1. Abrir o MMC de cópia de segurança do Microsoft Azure snap-in no servidor de gestão principal ou num servidor a executar o adicionais gestão aprovisionada servidores do processo. Por predefinição um atalho para a cópia de segurança do Microsoft Azure é criada no ambiente de trabalho ou pode encontrar-o na: C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. In snap-, clique em **Alterar propriedades**.

    ![Optimizar a largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. No separador **Throttling** especifique a largura de banda que pode ser utilizada para replicação de recuperação de Site e o agendamento aplicável.

    ![Optimizar a largura de banda](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Opcionalmente, também pode configurar limitação através do PowerShell. Eis um exemplo:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Maximizar a utilização de largura de banda
Para aumentar a largura de banda utilizada para a replicação por Azure recuperação de sites que precisa para alterar uma chave de registo.

A seguinte chave controla o número de threads por replicação de disco que são utilizadas quando replicação

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Numa rede "overprovisioned", esta chave de registo tem de ser alteradas a partir de valores de predefinido. Suportamos um máximo de 32.  


[Saiba mais](site-recovery-capacity-planner.md) sobre o planeamento de capacidade detalhada.

### <a name="additional-process-servers"></a>Servidores de processo adicionais

Se necessita de proteger mais de 200 máquinas ou alterar a velocidade de diária for maior do que 2 TB pode adicionar servidores adicionais para processar a carga. Para dimensionar saída pode:

- Aumente o número de servidores de gestão. Por exemplo pode proteger até 400 máquinas com dois servidores de gestão.
- Adicionar os servidores de processo adicionais e utilizá-los para processar o tráfego em vez de (ou, para além) o servidor de gestão.

Esta tabela descreve um cenário em que:

- Configurar o servidor de gestão original para utilizá-la como apenas um servidor de configuração.
- Configurar um servidor de processo adicionais.
- Configurar máquinas virtuais protegidas para utilizar o servidor de processo adicionais.
- Cada máquina de origem protegida está configurada com três discos de 100 GB.

**Servidor de gestão de original**<br/><br/>(servidor de configuração) | **Servidor de processo adicionais**| **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memória | 4 vCPUs (2 sockets * 2 núcleos @ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Pode criar uma réplica máquinas inferior ou igual a 85.
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memória | 8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 12 GB de memória | 600 GB | 250 GB para 1 TB | Pode criar uma réplica entre 85 150 máquinas.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz), 18 GB de memória | 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) 24 GB de memória | 1 TB | 1 TB para 2 TB | Pode criar uma réplica entre 150 225 máquinas.


A forma na qual dimensionar os servidores irá dependem a sua preferência para uma escala de cores para cima ou dimensionar saída modelo.  Dimensionar ao implementar alguns gestão de alto nível e os servidores de processo ou dimensionar saída ao implementar mais servidores com menos recursos. Por exemplo: Se precisar de proteger 220 máquinas poderia efetue um dos seguintes procedimentos:

- Configurar o servidor de gestão original com 12vCPU, 18 GB de memória, um servidor de processo adicionais com 12vCPU, 24 GB de memória e configurar máquinas protegidas para utilizar apenas o servidor de processo adicionais.
- Ou em alternativa que poderia configurar duas gestão servidores (2 x 8vCPU, 16 GB de RAM) e dois processos adicionais (1 x 8vCPU) e 4vCPU x1 para processar 135 + 85 máquinas (220) e configurar máquinas protegidas para utilizar apenas os servidores de processo adicionais.


[Siga estas instruções](#deploy-additional-process-servers) para configurar um servidor de processo adicionais.

## <a name="before-you-start-deployment"></a>Antes de começar a implementação

As tabelas resumem os pré-requisitos para implementar este cenário.


### <a name="azure-prerequisites"></a>Pré-requisitos Azure

**Pré-requisito** | **Detalhes**
--- | ---
**Conta Azure**| Terá de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**Armazenamento Azure** | Terá de uma conta de armazenamento Azure para armazenar dados replicados. Dados replicados são armazenados no Azure armazenamento e Azure VMs são fiadas para cima quando ocorre activação pós-falha. <br/><br/>É necessária uma [conta de armazenamento geo redundantes padrão](../storage/storage-redundancy.md#geo-redundant-storage). A conta deve ser na mesma região como o serviço de recuperação de sites e associado à subscrição mesmo. Tenha em atenção que a replicação para contas de armazenamento premium atualmente não é suportada e não deveriam ser utilizados.<br/><br/>Não suportamos a mudança de contas de armazenamento criado utilizando o [novo portal Azure](../storage/storage-create-storage-account.md) através de grupos de recursos. [Leia sobre](../storage/storage-introduction.md) Armazenamento Azure.<br/><br/>
**Rede Azure** | Terá de uma rede virtual Azure que Azure VMs irá estabelecer ligação quando ocorre activação pós-falha. A rede virtual Azure tem de ser na mesma região como cofre recuperação de sites.<br/><br/>Nota que provocam a falha após a activação pós-falha ao Azure terá uma VPN ligação (ou Azure ExpressRoute) configure o Azure rede para o site no local.


### <a name="on-premises-prerequisites"></a>Pré-requisitos de no local

**Pré-requisito** | **Detalhes**
--- | ---
**Servidor de gestão** | Precisa de um servidor do Windows 2012 R2 no local em execução num servidor física ou máquina virtual. Todos os componentes de recuperação de sites no local são instalados este servidor de gestão<br/><br/> Recomendamos que implementar o servidor como uma VM VMware altamente disponível. Reposição de recurso para o site no local a partir do Azure é sempre ser para VMware VMs, independentemente de se que ocorreu uma falha ao longo do VMs ou servidores físicos. Se não a configurar o servidor de gestão de como uma VM VMware terá de configurar um servidor de destino mestra separada como uma VM VMware para receber tráfego de reposição de recurso.<br/><br/>O servidor não deve ser um controlador de domínio.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O nome do anfitrião do servidor deve ser 15 caracteres ou menos.<br/><br/>A região do sistema operativo deve estar em inglês apenas.<br/><br/>Servidor de gestão de requer acesso à internet.<br/><br/>Precisa de aceder saída do servidor da seguinte forma: acesso temporário no HTTP 80 durante a configuração os componentes de recuperação de sites (para transferir MySQL); Acesso de saída em curso no HTTPS 443 para gestão de replicação Acesso de saída em curso no HTTPS 9443 para o tráfego de replicação (pode ser modificada esta porta)<br/><br/> Certifique-se que destes URLs são acessíveis a partir do servidor de gestão de: <br/>- \*. hypervrecoverymanager.windowsazure.com<br/>- \*. accesscontrol.windows.net<br/>- \*. backup.windowsazure.com<br/>- \*. blob.core.windows.net<br/>- \*. store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [https://Dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi]( https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Se tiver regras de firewall baseada em endereço IP do servidor, certifique-se de que as regras permitem comunicação com o Azure. Terá de permitir que os [Intervalos de IP do Centro de dados de Azure](https://www.microsoft.com/download/details.aspx?id=41653) e à porta HTTPS (443). Terá também lista branca intervalos de endereços IP para o Azure região da sua subscrição e para EUA Ocidental. É o URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") para transferir MySQL.
**VMware vCenter/ESXi anfitrião**: | Precisa de um ou mais vMware vSphere ESX/ESXi hypervisors gerir máquinas virtuais VMware, executar ESX/ESXi versão 6.0, 5.5 ou 5.1 com as atualizações mais recentes.<br/><br/> Recomendamos que implementar um servidor de vCenter VMware para gerir os anfitriões ESXi. -Deve estar em execução vCenter versão 6.0 ou 5.5 com as atualizações mais recentes.<br/><br/>Note que a recuperação de sites não suporta vCenter nova e vSphere 6.0 funcionalidades tais como Cruz vCenter vMotion, volumes virtuais e armazenamento DRS. Suporte de recuperação do site está limitado a funcionalidades que foram também disponíveis na versão 5.5.
**Protegido máquinas**: | **AZURE**<br/><br/>Máquinas que pretende proteger devem estar em conformidade com [Azure pré-requisitos](site-recovery-best-practices.md#azure-virtual-machine-requirements) para criar Azure VMs.<br><br/>Se pretender estabelecer ligação com o Azure VMs após activação pós-falha terá de ativar as ligações de ambiente de trabalho remoto na firewall local.<br/><br/>Capacidade de disco individuais no máquinas protegidas não deve ser mais do que 1023 GB. Uma VM pode ter até 64 discos (assim até 64 TB). Se tiver discos maiores do que 1 TB considere utilizando a replicação de bases de dados como SQL Server sempre no ou atento de dados Oracle.<br/><br/>Mínimo 2 GB de espaço disponível na unidade de instalação de instalação do componente.<br/><br/>Partilhada convidado disco clusters não são suportados. Se tiver uma implementação agrupada, considere utilizar replicação de bases de dados como SQL Server sempre no ou atento de dados Oracle.<br/><br/>Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) arranque não é suportada.<br/><br/>Nomes de máquina devem conter entre 1 e 63 carateres (letras, números e hífenes). O nome tem de iniciar com uma letra ou número e termina com uma letra ou número. Depois de uma máquina estiver protegida pode modificar o nome do Azure.<br/><br/>**VMware VMs**<br/><br>Terá de instalar VMware vSphere PowerCLI 6.0. no servidor de gestão de (servidor de configuração).<br/><br/>VMware VMs que pretende proteger deve ter VMware ferramentas instalada e em execução.<br/><br/>Se a origem VM tiver NIC equipas é convertida para uma única NIC após activação pós-falha ao Azure.<br/><br/>Se VMs protegidas tem um disco iSCSI, em seguida, recuperação de sites converte o disco de iSCSI VM protegido para um ficheiro VHD quando a VM Falha ao longo do Azure. Se o destino iSCSI pode ser alcançado através da VM Azure, em seguida, será ligar ao destino iSCSI e, essencialmente, consulte o artigo dois discos – o disco VHD na VM Azure e o disco de iSCSI de origem. Neste caso, terá desligar o destino iSCSI que é apresentado o Falha ao longo do Azure VM.<br/><br/>[Saiba mais](#vmware-permissions-for-vcenter-access) sobre as permissões de utilizador VMware que são necessários a recuperação de sites.<br/><br/> **WINDOWS SERVER máquinas (no VMware VM ou servidor físico)**<br/><br/>O servidor de um sistema operativo de 64 bits suportado deve estar em execução: Windows Server 2008 R2 na, Windows Server 2012 ou Windows Server 2012 R2 SP1, pelo menos.<br/><br/>O sistema operativo deve ser instalado na unidade C:\ e o disco SO deve ser um disco básico do Windows (o SO não deve ser instalado num disco dinâmico Windows).<br/><br/>Para o Windows Server 2008 R2 máquinas terá de ter .NET Framework 3.5.1 instalado.<br/><br/>Terá de fornecer uma conta de administrador (tem de ser um administrador no computador Windows local) para a instalação push o serviço de mobilidade nos servidores do Windows. Se a conta fornecida é uma conta de no domínio que não terá de desativar o controlo de acesso remoto do utilizador no computador local. [Saiba mais](#install-the-mobility-service-with-push-installation).<br/><br/>Recuperação de sites suporta VMs com disco RDM.  Durante a reposição de recurso recuperação do Site irá reutilizar o disco RDM se o disco VM e RDM origem original está disponível. Se estes não estão disponíveis, recuperação de Site irá criar um novo ficheiro VMDK para cada disco durante a reposição de recurso.<br/><br/>**LINUX MÁQUINAS**<br/><br/>Terá de um sistema operativo de 64 bits suportados: vermelho chapéu Enterprise Linux 6.7; Centos 6.5, 6.6,6.7; Oracle Enterprise Linux 6.4, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>ficheiros de /etc/hosts no máquinas protegidas devem conter entradas que mapeiam o nome de anfitrião local para endereços IP associados todos os adaptadores de rede. <br/><br/>Se pretender estabelecer ligação com uma máquina virtual Azure executar Linux após activação pós-falha a utilizar um seguro Shell cliente (ssh), certifique-se de que o serviço de Shell seguro no computador protegido está definido para iniciar automaticamente no arranque do sistema e que as regras de firewall permitem um ssh ligação à mesma.<br/><br/>Proteção só pode ser ativada para Linux máquinas com o armazenamento seguinte: (EXT3, ETX4, ReiserFS, XFS); de sistema de ficheiros I-dispositivo de software mapeamento de pontos (i)); Gestor de volume: (LVM2). Servidores físicos com HP CCISS controlador de armazenamento não são suportados. O sistema de ficheiros ReiserFS só é suportado em SUSE Linux Enterprise Server 11 SP3.<br/><br/>Recuperação de sites suporta VMs com disco RDM.  Durante a reposição de recurso para Linux, recuperação de sites não reutilizar o disco RDM. Em vez disso, que cria um novo ficheiro VMDK para cada disco RDM correspondente.

Apenas para Linux VM - Certifique-se de que o utilizador defina a definição de disk.enableUUID=true no parâmetros de configuração da VM no VMware. Se esta linha não existir, adicioná-la. Isto é necessário para fornecer um UUID consistente à VMDK para que-montagens corretamente. Tenha também em atenção que sem esta definição, reposição de recurso causará uma transferência completa, mesmo se a VM está disponível no prem. Adicionar esta definição irá garantir que apenas as alterações de delta são transferidas durante a reposição de recurso.

## <a name="step-1-create-a-vault"></a>Passo 1: Criar um cofre

1. Inicie sessão no [Portal de gestão](https://manage.windowsazure.com/).
2. Expandir a **Serviços de dados** > **Serviços de recuperação** e clique em **Site recuperação cofre**.
3. Clique em **Criar novo** > **criação rápida**.
4. Em **nome**, introduza um nome amigável para identificar o cofre.
5. Na **região**, selecione a região geográfica para o cofre. Para verificar regiões suportadas Consulte geográficos disponibilidade no [Recuperação preços detalhes do Site Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Clique em **Criar cofre**.
    ![Novo cofre](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Verifique a barra de estado para confirmar que o Cofre foi criado com êxito. O Cofre irá estar listado como **ativa** na página principal de **Serviços de recuperação** .

## <a name="step-2-set-up-an-azure-network"></a>Passo 2: Configurar uma rede Azure

Configure uma rede Azure para que o Azure VMs será ligado a uma rede após activação pós-falha e para que a reposição de recurso para o site no local pode funcionar como esperado.

1. No portal do Azure > **criar rede virtual** especifique o nome de rede. IP endereço intervalo e de sub-rede nome.
2. Que precisa para adicionar VPN/ExpressRoute à rede se precisar de fazer a reposição de recurso. VPN/ExpressRoute podem ser adicionado à rede mesmo após activação pós-falha.

[Leia mais](../virtual-network/virtual-networks-overview.md) sobre as redes Azure.

> [AZURE.NOTE] [Migração das redes](../resource-group-move-resources.md) através de grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportado para as redes utilizadas para implementar o recuperação de sites.

## <a name="step-3-install-the-vmware-components"></a>Passo 3: Instale os componentes de VMware

Se pretender criar uma réplica VMware virtual máquinas instalar os seguintes componentes de VMware no servidor de gestão de:

1. [Transferir](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e instalar VMware vSphere PowerCLI 6.0.
2. Reinicie o servidor.


## <a name="step-4-download-a-vault-registration-key"></a>Passo 4: Transferir uma chave de registo do Cofre

1. Partir da gestão de servidor de abrir a consola de recuperação de Site no Azure. Na página de **Serviços de recuperação** clique no cofre para abrir a página de início rápido. Guia de introdução do também pode ser aberto em qualquer altura utilizando o ícone.

    ![Guia de introdução do ícone](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Na página de **Início rápido** , clique em **Recursos de destino preparar** > **Transferir uma chave de registo**. O ficheiro de registo é gerado automaticamente. É válido para 5 dias após é gerado.


## <a name="step-5-install-the-management-server"></a>Passo 5: Instalar o servidor de gestão
> [AZURE.TIP] Certifique-se que destes URLs são acessíveis a partir do servidor de gestão de:
>
- *. hypervrecoverymanager.windowsazure.com
- *. accesscontrol.windows.net
- *. backup.windowsazure.com
- *. blob.core.windows.net
- *. store.core.windows.net
- https://Dev.MySQL.com/Get/Archives/MySQL-5.5/MySQL-5.5.37-Win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Na página de **Início rápido** transferir o ficheiro de instalação unificado no servidor.

2. Execute o ficheiro de instalação para iniciar a configuração no Assistente de configuração de Unified de recuperação do Site.

3.  No **antes de começar** , selecione **instalar o servidor de configuração e o servidor de processo**.

    ![Antes de começar](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. Na **Licença de Software de terceiros** clique em **Aceitar posso** transferir e instalar MySQL. 

    ![Terceiro = software de terceiros](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. No **registo** procure e selecione a chave de registo que transferiu do cofre.

    ![Registo](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. Nas **Definições do Internet** especificar como o fornecedor executado no servidor de configuração será liga ao Azure recuperação de Site através da internet.

    - Se pretender estabelecer ligação com o proxy de que está atualmente configurado no computador, selecione **ligar-se com as definições de proxy existente**.
    - Se pretender que o fornecedor para se ligar diretamente selecione **Ligar diretamente sem um proxy**.
    - Se o proxy existente requer autenticação ou que pretende utilizar um proxy personalizado para a ligação do fornecedor, selecione **ligar-se com as definições de proxy personalizado**.
        - Se utilizar um proxy personalizado, que terá de especificar o endereço, porta e as credenciais
        - Se estiver a utilizar um proxy deve tiver já permitido os URLs seguintes:
            - *. hypervrecoverymanager.windowsazure.com;    
            - *. accesscontrol.windows.net; 
            - *. backup.windowsazure.com; 
            - *. blob.core.windows.net; 
            - *. store.core.windows.net
            

    ![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. **Verificar a pré-requisitos da** configuração é executado uma verificação para se certificar de que a instalação pode ser executados. 

    
    ![Pré-requisitos](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Se for apresentado um aviso sobre a **sincronização de tempo Global verificar** Certifique-se de que a hora no relógio do sistema (definições de**data e hora** ) é o mesmo que o fuso horário.

    ![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. Configuração do **MySQL** crie credenciais para iniciar sessão a instância de servidor do MySQL será instalada.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. Em selecionar **Ambiente detalhes** se vai criar uma réplica VMware VMs. Se estiver, em seguida, o programa de configuração verifica que PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. Em selecionar **Instalar localização** onde pretende instalar os binários e armazenar a cache. Pode selecionar uma unidade que tenha pelo menos 5 GB de armazenamento disponível mas recomendamos uma unidade de cache com, pelo menos, 600 GB de espaço livre.

    ![Localização de instalação](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. Em **Selecção de rede** , especifique a escuta (adaptador de rede e porta de SSL) no qual o servidor de configuração enviar e receber replicação dados. Pode modificar a predefinição da porta (9443). Para além desta porta, será utilizada por um servidor web que orquestra operações de replicação porta 443. 443 não deveriam ser utilizados para o tráfego de replicação a receber.


    ![Selecção de rede](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  Num **Resumo** reveja as informações e clique em **instalar**. Quando termina de instalação é gerado uma frase de acesso. Irá precisar quando ativa a replicação por isso, copiá-la e mantê-lo numa localização segura.

    ![Resumo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  Em **Resumo** , reveja as informações.

    ![Resumo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING]Proxy do Microsoft Azure Service do agente de recuperação tem de estar a configuração.
>Assim que a instalação estiver concluída, inicie uma aplicação com o nome "Microsoft Azure recuperação de serviços da Shell de" a partir do menu Iniciar do Windows. Na janela do comando disponibilizado execute o seguinte conjunto de comandos para configurar as definições do servidor proxy.
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine



### <a name="run-setup-from-the-command-line"></a>Executar a configuração da linha de comandos

Também pode executar o assistente unificado da linha de comandos, da seguinte forma:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Em que:

- / ServerMode: obrigatório. Especifica se a instalação deve instalar os servidores de configuração e processo ou apenas o servidor de processo (utilizado para instalar os servidores de processo adicional). Valores de entrada: CS, PS.
- Unidade_de_instalação: obrigatório. Especifica a pasta onde os componentes são instalados.
- / MySQLCredFilePath. Obrigatório. Especifica o caminho para um ficheiro onde o servidor do MySQL as credenciais está história. Obter o modelo para criar o ficheiro.
- / VaultCredFilePath. Obrigatório. Localização do ficheiro de credenciais Cofre
- / EnvType. Obrigatório. Tipo de instalação. Valores: VMware, NonVMware
- / PSIP e /CSIP. Obrigatório. Endereço IP do servidor de processo e do servidor de configuração.
- / PassphraseFilePath. Obrigatório. Localização do ficheiro frase de acesso.
- / ByPassProxy. Opcional. Especifica que o servidor de gestão liga-se ao Azure sem um proxy.
- / ProxySettingsFilePath. Opcional. Especifica definições para um proxy personalizado em (proxy predefinido no servidor que requer autenticação), ou proxy personalizado




## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Passo 6: Configurar credenciais para o servidor de vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

O servidor de processo possam detetar automaticamente VMs VMware que são geridos por um servidor de vCenter. Para deteção automática recuperação de sites necessita de uma conta e as credenciais que podem aceder ao servidor vCenter. Esta não é relevante se estiver a replicação apenas para servidores físicos.

Faça o seguinte da seguinte forma:

1. No vCenter server crie uma função (**Azure_Site_Recovery**) ao nível do vCenter com as [permissões necessárias](#vmware-permissions-for-vcenter-access).
2. Atribua a função **Azure_Site_Recovery** a um utilizador vCenter.

    >[AZURE.NOTE] Uma conta de utilizador vCenter que tem a função só de leitura pode executar activação pós-falha sem encerrar máquinas origem protegida. Se pretender encerrar essas máquinas terá a função Azure_Site_Recovery. Tenha em atenção que se está apenas a migrar VMs a partir do VMware para Azure e não precisa de reposição de recurso, em seguida, a função só de leitura é suficiente.

3. Para adicionar a conta, abra **cspsconfigtool**. É disponível como um atalho no ambiente de trabalho e localizada na pasta \home\svsystems\bin [localização instalar].
2. no separador **Gerir contas** , clique em **Adicionar conta**.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Em **Detalhes da conta** adicione credenciais que podem ser utilizadas para aceder ao servidor vCenter. Tenha em atenção que poderá demorar mais de 15 minutos para que o nome da conta que seja apresentado no portal. Para atualizar imediatamente, clique em Atualizar no separador **Servidores de configuração** .

    ![Detalhes](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Passo 7: Adicionar vCenter servidores e ESXi anfitriões

Se estiver a replicação VMware VMs terá de adicionar um servidor de vCenter (ou anfitrião ESXi).

1. Nos **servidores** > **Servidores de configuração** de tabulação, selecione o servidor de configuração > **Adicionar vCenter servidor**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Adicione o servidor de vCenter ou ESXi detalhes de anfitrião, o nome da conta que especificou para aceder ao servidor vCenter no passo anterior e o servidor de processo que será utilizado para descobrir VMs VMware que são geridas pelo servidor vCenter. Tenha em atenção que o servidor de vCenter ou ESXi anfitrião deve estar localizado na mesma rede que o servidor onde está instalado o servidor do processo.

    >[AZURE.NOTE] Se estiver a adicionar o servidor de vCenter ou ESXi anfitrião com uma conta que não tiver privilégios de administrador no servidor vCenter ou anfitrião, certifique-se a vCenter ou ESXi contas têm estes privilégios ativados: Centro de dados, arquivo de dados, pasta, Jost, rede, recursos, Virtual de máquina, vSphere distribuído mudar. Além disso, o servidor de vCenter necessita o privilégio de vistas de armazenamento.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Após a conclusão da deteção o servidor de vCenter será listado no separador **Servidores de configuração** .

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## <a name="step-8-create-a-protection-group"></a>Passo 8: Criar um grupo proteção

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Os grupos de proteção são agrupamentos lógicos de máquinas virtuais ou físicos servidores que pretende proteger utilizando as mesmas definições de proteção. Aplicar definições de proteção a um grupo de proteção e essas definições serão aplicadas a todos os computadores máquinas virtuais/física que adicionar ao grupo.

1. Abrir **Itens protegida** > **Proteção de grupo** e clique em para adicionar um grupo de proteção.

    ![Criar grupo proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Na página **Especificar as definições de proteção de grupo** Especifique um nome para o grupo e na **partir de** , selecione o servidor de configuração no qual pretende criar o grupo. **Destino** é Azure.

    ![Definições de grupo proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Na página **Especificar definições de replicação** de configurar as definições de replicação que serão utilizadas para todos os computadores no grupo.

    ![Replicação de grupo proteção](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

    - **Multi VM consistência**: Se ativar este cria pontos de recuperação consistente para a aplicação partilhada através de máquinas no grupo proteção. Esta definição está mais relevante quando todas as máquinas no grupo proteção estiver a executar a carga de trabalho mesmo. Todos os computadores serão recuperados ao mesmo ponto de dados. Esta está disponível se estiver a replicação VMware VMs ou servidores físicos Windows/Linux.
    - **Limiar RPO**: define o RPO. Alertas serão gerados quando a replicação de proteção de dados contínuos excede o valor de limiar RPO configurado.
    - **Recuperação aponte retenção**: Especifica a janela de retenção. Podem ser recuperadas máquinas protegidas para qualquer ponto dentro nesta janela.
    - **Frequência de instantâneo aplicação consistente**: Especifica frequência pontos de recuperação que contém a aplicação consistente instantâneos serão criados.

Quando clica na marca de verificação será criado um grupo de proteção com o nome que especificou. In addition um segundo grupo proteção é criado com o nome < proteção de grupo-nome-reposição de recurso). Este grupo de proteção é utilizado se falhar novamente para o site no local após activação pós-falha ao Azure. Pode monitorizar os grupos de proteção de medida que está a ser criadas na página **Protegida itens** .

## <a name="step-9-install-the-mobility-service"></a>Passo 9: Instalar o serviço de mobilidade

É o primeiro passo na ativação protecção para máquinas virtuais e servidores físicos instalar o serviço de mobilidade. Pode fazê-lo de duas maneiras:

- Notificações push e instalar o serviço em cada máquina do servidor processo automaticamente. Repare que quando adicionar máquinas a um grupo de proteção e já estiverem a executar uma versão adequada da instalação mobilidade serviço push não ocorre.
- Instale automaticamente o serviço utilizando o seu método de push enterprise como WSUS ou Gestor de configuração do Centro de sistema. Certifique-se de que configurou o servidor de gestão antes de efetuar esta ação.
- Instale manualmente em cada máquina que pretende proteger. ormar certificar-se configurou o servidor de gestão antes de efetuar esta ação.


### <a name="install-the-mobility-service-with-push-installation"></a>Instalar o serviço de mobilidade com a instalação push

Quando adiciona máquinas a um grupo de proteção o serviço de mobilidade é automaticamente seguia e instalado em cada máquina pelo servidor de processo.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparar para push automática no máquinas com o Windows

Eis como preparar máquinas com o Windows para que o serviço de mobilidade pode ser instalado automaticamente pelo servidor de processo.

1.  Crie uma conta que pode ser utilizada pelo servidor de processo para aceder ao computador. A conta deverá ter privilégios de administrador (local ou domínio). Tenha em atenção que estas credenciais só são utilizadas para instalação de push do serviço de mobilidade.

    >[AZURE.NOTE] Se não estiver a utilizar uma conta de domínio terá de desativar o controlo de acesso remoto do utilizador no computador local. Para fazer isto, no registo em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System adicionar a entrada DWORD LocalAccountTokenFilterPolicy com um valor de 1 em. Para adicionar o registo entrada a partir de um clip abra comando ou através do PowerShell introduza **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  A Firewall do Windows do computador que pretende proteger, selecione **Permitir uma aplicação ou funcionalidade através da Firewall** e ativar a **partilha ficheiros e impressoras** e **Instrumentação de gestão do Windows**. Para máquinas pertencerem a um domínio pode configurar a política de firewall com um GPO.

    ![Definições da firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Adicione a conta que criou:

    - Abra **cspsconfigtool**. É disponível como um atalho no ambiente de trabalho e localizada na pasta \home\svsystems\bin [localização instalar].
    - No separador **Gerir contas** , clique em **Adicionar conta**.
    - Adicione a conta que criou. Depois de adicionar a conta terá de fornecer as credenciais quando adiciona uma máquina a um grupo de proteção.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparar para push automática em servidores Linux

1.  Certifique-se de que o computador Linux que pretende proteger é suportado, tal como descrito na [Pré-requisitos de no local](#on-premises-prerequisites). Certifique-se de que existe conectividade de rede entre o computador que pretende proteger e o servidor de gestão que executa o servidor do processo.

2.  Crie uma conta que pode ser utilizada pelo servidor de processo para aceder ao computador. A conta deverá ser um utilizador de raiz do servidor de origem Linux. Tenha em atenção que estas credenciais só são utilizadas para instalação de push do serviço de mobilidade.

    - Abra **cspsconfigtool**. É disponível como um atalho no ambiente de trabalho e localizada na pasta \home\svsystems\bin [localização instalar].
    - No separador **Gerir contas** , clique em **Adicionar conta**.
    - Adicione a conta que criou. Depois de adicionar a conta terá de fornecer as credenciais quando adiciona uma máquina a um grupo de proteção.

3.  Verifique se o ficheiro /etc/hosts do servidor de origem Linux contém as entradas que mapeiam o nome do anfitrião local para endereços IP associados todos os adaptadores de rede.
4.  Instalar o antes de openssh mais recente, servidor de antes openssh, openssl pacotes no computador que pretende proteger.
5.  Certifique-se de que SSH é activado e em execução no Porta 22.
6.  Ativar a autenticação subsystem e palavra-passe SFTP no ficheiro sshd_config da seguinte forma:

    - Inicie sessão como raiz.
    - No ficheiro de /etc/ssh/sshd_config de ficheiro, localize a linha que começa com PasswordAuthentication.
    - Remova os comentários da linha e altere o valor de **sem** como **Sim**.
    - Localize a linha que começa com **Subsystem** e remova os comentários da linha.

        ![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Instalar manualmente o serviço de mobilidade

Os programas de instalação estão disponíveis no c:\Programas\Microsoft ficheiros (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Sistema operativo de origem | Ficheiro de instalação do serviço de mobilidade
--- | ---
Windows Server (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-manually-on-a-windows-server"></a>Instalar manualmente num Windows server


1. Transfira e execute o installer relevante.
2. No **antes de começar **, selecione **o serviço de mobilidade**.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Na **Configuração de detalhes do servidor** especifique o endereço IP do servidor de gestão de e a frase de acesso que foi gerada quando instalou o os componentes de servidor de gestão. Pode obter a frase de acesso ao executar: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** no servidor de gestão de.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Na **Localização instalar** deixar a localização predefinida e clique em **seguinte** para começar a instalação.
5. Em **Curso instalação** monitorizar instalação e reiniciar o computador se lhe for solicitado.

Também pode instalar a partir da linha de comandos:

UnifiedAgent.exe [/ função < agente/MasterTarget >] [/ InstallLocation <Installation Directory>] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>] [/ LogFilePath <Log File Path>]

Em que:

- / Função: obrigatório. Especifica se o serviço de mobilidade deve ser instalado.
- / InstallLocation: obrigatório. Especifica onde pretende instalar o serviço.
- / PassphraseFilePath: obrigatório. Especifica a frase de acesso do servidor de configuração.
- / LogFilePath: obrigatório. Especifica a localização de ficheiros de configuração de registo

#### <a name="uninstall-mobility-service-manually"></a>Desinstalar manualmente o serviço de mobilidade

Serviço de mobilidade pode ser desinstalado utilizando o adicionar remover programa a partir do painel de controlo ou linha de comandos.

O comando para desinstalar o serviço de mobilidade utilizando linha de comandos é

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="modify-the-ip-address-of-the-management-server"></a>Modificar o endereço IP do servidor de gestão de

Depois de executar o assistente pode modificar o endereço IP do servidor de gestão da seguinte forma:

1. Abra o hostconfig.exe ficheiro (localizada no ambiente de trabalho).
2. No separador **Global** pode alterar o endereço IP do servidor de gestão de.

    >[AZURE.NOTE] Só deverá alterar o endereço IP do servidor de gestão de. O número da porta para comunicações de servidor de gestão tem de ser 443 e utilizar HTTPS deve ser ativado para a esquerda. Não deve ser modificada a frase de acesso.

    ![Endereço IP do servidor de gestão](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-manually-on-a-linux-server"></a>Instale manualmente num servidor Linux:

1. Copie o arquivo de alcatrão adequado com base na tabela acima para a máquina Linux que pretende proteger.
2. Abra um programa de shell e extrair o arquivo de alcatrão compactados como um caminho local através da execução:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um ficheiro de passphrase.txt no diretório local para o qual extraídas que o conteúdo do arquivo tar. Para fazer isto copiar a frase de acesso do C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de gestão de e guardá-lo no passphrase.txt executando *`echo <passphrase> >passphrase.txt`* na shell.
4. Instalar o serviço de mobilidade introduzindo *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP interno do servidor de gestão de e certifique-se de porta 443 está selecionada.

**Também pode instalar a partir da linha de comandos**:

1. Copie a frase de acesso a partir de ficheiros c:\Programas\Microsoft (x86) \InMage Systems\private\connection no servidor de gestão e guardá-lo como "passphrase.txt" no servidor de gestão de. Em seguida, execute estes comandos. No nosso exemplo, o endereço IP do servidor de gestão é 104.40.75.37 e à porta HTTPS deve ser 443:

Para instalar num servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar o servidor de destino principal:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Passo 10: Ativar proteção para uma máquina

Para ativar proteção Adicionar máquinas virtuais e servidores físicos a um grupo de proteção. Antes de começar, tenha em atenção o seguinte se estiver a proteger máquinas virtuais de VMware:

- VMware VMs sejam detetados a cada 15 minutos e poderá demorar mais de 15 minutos para que apareçam no portal do recuperação de Site após deteção.
- As alterações de ambiente na máquina virtual (tal como a instalação das ferramentas de VMware) também poderão demorar mais de 15 minutos para ser atualizados no recuperação de sites.
- Pode procurar a última vez descoberta VMware VMs no **Último contacto no** campo para o anfitrião de servidor/ESXi vCenter no separador **Servidores de configuração** .
- Se tiver um grupo de proteção já tiver criado e adicione um anfitrião de servidor ou ESXi vCenter após que, pode demorar mais de 15 minutos para o portal do Azure recuperação de sites para atualizar e para máquinas virtuais a ser listado na caixa de diálogo **Adicionar máquinas a um grupo de proteção** .
- Se gostaria de proceder imediatamente com a adição de máquinas ao grupo de proteção sem ter de esperar para a deteção agendada, realce o servidor de configuração (não clique) e clique no botão **Atualizar** .

Além disso note que:

- Recomendamos que Arquitectar seus grupos de proteção para que sejam simétricas seu das cargas de trabalho. Por exemplo, adicione máquinas a executar uma aplicação específica do mesmo grupo.
- Quando adiciona máquinas a um grupo de proteção, o servidor de processo emite automaticamente e instala o serviço de mobilidade, se já não está instalado. Tenha em atenção que terá de ter o mecanismo push prepare conforme descrito no passo anterior.


Adicione máquinas a um grupo de proteção:

1. Clique em **protegida itens** > **Grupo proteção** > **máquinas** > Adicionar máquinas. \As prática recomendada
2. Em **máquinas virtuais do selecione** se estiver a proteger máquinas virtuais de VMware, selecione um servidor de vCenter que está a gerir o seu máquinas virtuais (ou anfitrião do EXSi no qual está a executar) e, em seguida, selecione as máquinas.

    ![Activar a protecção](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Em **máquinas virtuais do selecione** se estiver a proteger servidores físicos, no assistente **Adicionar física máquinas** fornece o endereço IP e o nome amigável. Em seguida, selecione a família do sistema operativo.

    ![Activar a protecção](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. Em **Especificar recursos de destino** selecione a conta de armazenamento que está a utilizar para a replicação e selecione se as definições de devem ser utilizadas para todas as cargas de trabalho. Tenha em atenção que contas de armazenamento premium atualmente não são suportadas.

    >[AZURE.NOTE] 1. Não suportamos a mudança de contas de armazenamento criado utilizando o [novo portal Azure](../storage/storage-create-storage-account.md) através de grupos de recursos.                           2.[a migração de contas de armazenamento](../resource-group-move-resources.md) através de grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportada para contas de armazenamento utilizadas para implementar o recuperação de sites.

    ![Activar a protecção](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. Na **Especificar contas** , selecione a conta que [configurado](#install-the-mobility-service-with-push-installation) para utilizar para a instalação do serviço de mobilidade automática.

    ![Activar a protecção](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Clique na marca de verificação para terminar a adição máquinas para o grupo de proteção e para iniciar a replicação inicial para cada máquina.

    >[AZURE.NOTE] Se a instalação push tiver sido preparada o serviço de mobilidade é instalada automaticamente em computadores que não têm como são adicionados ao grupo de proteção. Depois do serviço é instalação de uma tarefa de proteção inicia e falha. Após a falha terá de reiniciar manualmente cada computador que tenha tido o serviço de mobilidade instalado. Após o reinício a tarefa de proteção começa novamente e a replicação inicial ocorre.

Pode monitorizar estado na página **tarefas** .

![Activar a protecção](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Além disso, estado de proteção pode ser monitorizado na **Protegida itens** > <protection group name> > **máquinas virtuais**. Depois de replicação inicial estar concluída e os dados são sincronizados, estado de máquina muda para** protegida**.

![Activar a protecção](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## <a name="step-11-set-protected-machine-properties"></a>Passo 11: Definir propriedades de máquina protegido por

1. Depois de um computador tem um estado **protegido** pode configurar as respetivas propriedades activação pós-falha. Os detalhes do grupo de proteção selecione computador e abra o separador de **Configurar** .
2. Recuperação de sites automaticamente sugere propriedades para a VM Azure e Deteta que definições de rede no local.

    ![Definir as propriedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Pode modificar estas definições:

    -  **Nome do Azure VM**: Este é o nome que será concedido à máquina no Azure após activação pós-falha. O nome deve cumprir os requisitos Azure.

    -  **Tamanho da memória virtual do Azure**: O número de adaptadores de rede é ditado pelo tamanho especificado para o destino virtual machine. [Leia mais](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) sobre tamanhos e placas. Tenha em atenção que:

        - Quando modificar o tamanho para uma máquina virtual e guardar as definições, irá alterar o número de adaptador de rede quando abre no separador **Configurar** próxima vez. O número de adaptadores de rede de máquinas virtuais de destino é mínimo, o número de adaptadores de rede na máquina de virtual de origem e o número máximo de adaptadores de rede suportados pelo tamanho da máquina virtual escolhido.
            - Se o número de adaptadores de rede no computador de origem for menor ou igual ao número de placas permitida para o tamanho de máquina de destino, em seguida, o destino tem o mesmo número de placas como a origem de.
            - Se o número de adaptadores para a máquina de virtual origem excede o número permitido para o tamanho de destino, em seguida, o número máximo de tamanho de destino será utilizado.
            - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho de máquina de destino suporta quatro, o computador de destino terá duas placas. Se o computador de origem tem duas placas, mas o tamanho de suportados de destino suporta apenas um computador de destino ter apenas uma placa de.
        - Se tiver a máquina virtual vários adaptadores de rede todas as placas devem ligado à rede Azure mesmo.
    - **Rede Azure**: tem de especificar uma rede Azure que Azure VMs será ligado ao após activação pós-falha. Se não especificar um VMs o Azure não estar ligado a uma rede. Além disso, terá de especificar uma rede Azure se pretender reposição de recurso a partir do Azure para o site no local. Reposição de recurso requer uma ligação VPN entre uma rede Azure e uma rede no local.
    - **Endereço de Azure IP/sub-rede**: para cada adaptador de rede selecione sub-rede à qual a VM Azure deve ligar. Tenha em atenção que:
        - Se o adaptador de rede do computador origem estiver configurado para utilizar um endereço IP estático, em seguida, pode especificar um endereço IP estático para a VM Azure. Se não fornecer um endereço IP estático qualquer endereço IP disponível será atribuído. Se o endereço de IP de destino for especificado mas já está a ser utilizado por outro VM no Azure activação pós-falha irá falhar. Se o adaptador de rede do computador origem estiver configurado para utilizar o DHCP, em seguida, terá de DHCP como a definição para Azure.

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Passo 12: Criar um plano de recuperação e executar uma activação pós-falha

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Pode executar um activação pós-falha para uma máquina única ou falhar ao longo do várias máquinas virtuais que executar a mesma tarefa ou executar a carga de trabalho mesmo. Falha ao longo de vários computadores ao mesmo tempo adicioná-los para um plano de recuperação.

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Na página **Plano de recuperação** clique em **Adicionar plano de recuperação** e adicione um plano de recuperação. Especificar os detalhes para o plano e selecione **Azure** como de destino.

    ![Configurar o plano de recuperação](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Na **máquina de Virtual selecione** selecione um grupo de proteção e, em seguida, selecione máquinas no grupo para adicionar o plano de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Pode personalizar o plano para criar grupos e sequência a ordem na qual são falhou máquinas no plano de recuperação sobre. Também pode adicionar scripts e solicita ações manuais. Scripts podem ser criados manualmente ou através ao [Azure automatização Runbooks](site-recovery-runbook-automation.md). [Saiba mais](site-recovery-create-recovery-plans.md) sobre como personalizar os planos de recuperação.

## <a name="run-a-failover"></a>Executar uma activação pós-falha

Antes de executar uma nota activação pós-falha que:


- Certifique-se de que está a ser executado o servidor de gestão e disponível - caso contrário activação pós-falha irá falhar.
- Se executar uma nota não planeado activação pós-falha que:

    - Se possível deve encerrar máquinas primárias antes de executar um activação não planeado pós-falha. Isto garante que não tem de origem e de réplica máquinas a executar ao mesmo tempo. Se estiver a replicação VMware VMs, em seguida, quando executa uma activação não planeado pós-falha pode especificar que recuperação de sites farão melhor esforço para encerrar as máquinas de origem. Consoante o estado do site principal poderá ou pode não funcionar. Se estiver a replicação servidores físicos recuperação de sites não oferecer esta opção.
    - Quando executar uma activação não planeado pós-falha deixa de replicação de dados a partir do máquinas principais para que qualquer delta de dados não serem transferida depois de um activação não planeado pós-falha começa.

- Se pretender ligar à máquina virtual réplica no Azure depois activação pós-falha, ative ligação de ambiente de trabalho remoto no computador de origem antes de executar o activação pós-falha e permitir a ligação RDP através da firewall. Também terá de permitir RDP no ponto final público da máquina virtual Azure após activação pós-falha. Siga estas [melhores práticas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) para garantir que RDP funciona após uma activação pós-falha.

>[AZURE.NOTE] Para obter o melhor desempenho quando fizer uma activação pós-falha ao Azure, certifique-se de que instalou o agente do Azure na máquina protegida. Isto ajuda-o no arranque mais rápido e também ajuda no diagnóstico em caso de problemas. Agente de Linux pode ser encontrado [aqui](https://github.com/Azure/WALinuxAgent) - e Windows agente pode ser encontrada [aqui](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

Executar um teste activação pós-falha para simular os processos de activação e recuperação numa rede de isolados não afeta o ambiente de produção e a replicação regular continua como normal. Inicia o teste activação pós-falha na origem e pode executá-la de duas formas:

- **Não especificar uma rede Azure**: se executa um teste activação pós-falha sem uma rede o teste simplesmente verificará que máquinas virtuais iniciar e são apresentados corretamente no Azure. Máquinas virtuais não estar ligadas a uma rede Azure após activação pós-falha.
- **Especificar uma rede Azure**: este tipo de activação pós-falha verifica o ambiente de replicação inteira é fornecido até conforme esperado e que máquinas virtuais Azure estão ligadas à rede especificada.


1. Na página **Plano de recuperação** selecione o plano e clique em **Testar activação pós-falha**.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. Em **Confirmar testar activação pós-falha** selecione **nenhum** para indicar que não pretende utilizar uma rede Azure para o teste activação pós-falha ou, selecione a rede para o qual o teste VMs será ligado após activação pós-falha. Clique na marca de verificação para iniciar a activação pós-falha.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Monitorizar o progresso de activação pós-falha no separador **tarefas** .

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Após completa o activação pós-falha também deverá conseguir ver a réplica Azure máquina aparecem no Azure portal > **máquinas virtuais**. Se pretender iniciar uma ligação de RDP para a VM Azure terá de abrir porta 3389 o ponto final VM.

5. Quando tiver terminado, quando activação pós-falha atinge concluir testes fase, clique em testar concluído para concluir. Nas notas gravar e guardar quaisquer observações associadas a activação de teste pós-falha.

6. Clique em **a activação de teste pós-falha está concluída** para limpar automaticamente o ambiente de teste. Após isto é feito o teste activação pós-falha irá mostrar um Estado de **conclusão** . Qualquer elementos ou VMs criados automaticamente durante a mudança de teste são eliminados. Tenha em atenção que, se um teste activação pós-falha persistir mais de duas semanas-foi concluída por força.



### <a name="run-an-unplanned-failover"></a>Executar uma activação não planeado pós-falha

Não planeada activação é iniciada a partir do Azure e pode ser executada, mesmo se o site principal não está disponível.


1. Na página **Plano de recuperação** selecione o plano e clique em **activação pós-falha** > **Activação pós-falha não planeado**.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Se estiver a replicação máquinas virtuais de VMware pode selecionar para experimentar e encerrar VMs no local. Esta é a melhor esforço e activação pós-falha irão continuar se o esforço com êxito ou não. Se não êxito detalhes desse erro irão aparecer no separador **tarefas **> **Não planeado activação pós-falha tarefas**.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

    >[AZURE.NOTE] Esta opção não está disponível se estiver a replicação servidores físicos. Terá de experimentar e encerrar aqueles manualmente se possível.

3. Na **Confirmar activação pós-falha** Verifique a direção de activação pós-falha (para Azure) e selecione o ponto de recuperação que pretende utilizar para o activação pós-falha. Caso a ativado multi VM quando configurou propriedades de replicação pode recuperar para o ponto de recuperação de aplicação ou falha de sistema consistentes mais recente. Também pode selecionar a **recuperação personalizada aponte** para recuperar um ponto anterior no tempo. Clique na marca de verificação para iniciar a activação pós-falha.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Aguarde que a tarefa de activação pós-falha não planeado concluir. Pode monitorizar o progresso de activação pós-falha no separador **tarefas** . Tenha em atenção que mesmo se ocorrem erros durante a activação pós-falha não planeado o plano de recuperação é executada até estar concluída. Também deverá conseguir ver a réplica Azure máquina aparecem máquinas virtuais no portal do Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Ligar a replicadas Azure máquinas virtuais após activação pós-falha

Para poder ligar ao replicadas máquinas virtuais no Azure depois de activação pós-falha aqui é o que precisa:

1. Uma ligação de ambiente de trabalho remoto deve ser activada no computador principal.
2. A Firewall do Windows no computador principal para permitir que RDP.
3. Depois de activação pós-falha terá de adicionar RDP para o público ponto final para Azure máquina virtual.

[Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) informações sobre esta configuração.


## <a name="deploy-additional-process-servers"></a>Implementar os servidores de processo adicionais

Se tiver de dimensionar fora da implementação para além das 200 máquinas de origem ou a taxa de vasilha diária total excede 2 TB, terá de servidores de processo adicionais para processar o volume de tráfego. Para configurar um servidor de processo adicionais Verifique os requisitos de nos [servidores do processo adicionais](#additional-process-servers) e, em seguida, siga as instruções para configurar o servidor do processo. Depois de configurar o servidor pode configurar máquinas de origem para utilizá-la.

### <a name="set-up-an-additional-process-server"></a>Configurar um servidor de processo adicionais

Configurar um servidor de processo adicionais da seguinte forma:

- Execute o assistente unificado para configurar um servidor de gestão como apenas um servidor do processo.
- Se pretender gerir a replicação de dados a utilizar apenas o servidor de processo novo terá de migrar o seu máquinas protegidas para o fazer.

### <a name="install-the-process-server"></a>Instalar o servidor de processo

1. Na página de início rápido transferir o ficheiro de instalação unificado para a instalação de componentes de recuperação de sites. Execute a configuração.
2. Na **antes de começar** selecione **Adicionar os servidores de processo adicionais para dimensionar saída implementação**.

    ![Adicionar o servidor de processo](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Concluir o Assistente da mesma forma que efetuou quando [Configurar](#step-5-install-the-management-server) o primeiro servidor de gestão.

4. Na **Configuração de detalhes do servidor** especifique o endereço IP do servidor de gestão de original na qual instalou o servidor de configuração e a frase de acesso. No servidor de gestão de original executar ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** para obter a frase de acesso.

    ![Adicionar o servidor de processo](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para utilizar o novo servidor de processo

1. Abrir os **Servidores de configuração** > **servidor** > nome do servidor de gestão de original > **Detalhes do servidor**.

    ![Actualizar o servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Na lista de **Servidores de processo** clique em **Mudar de servidor de processo** junto ao servidor que pretende modificar.

    ![Actualizar o servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. Em **Servidor de processo de alteração** > **Servidor do processo de destino** , selecione servidor de gestão de novo e, em seguida, selecione as máquinas virtuais que o servidor de processo novo processará. Clique no ícone de informações para obter informações sobre o servidor. O espaço de média tem necessárias para criar uma réplica cada máquina virtual selecionada para o novo servidor de processo é apresentado para ajudá-lo a tomar decisões de carregar. Clique na marca de verificação para iniciar a replicação para o novo servidor do processo.

    ![Actualizar o servidor de processo](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## <a name="vmware-permissions-for-vcenter-access"></a>VMware permissões de acesso de vCenter

O servidor de processo possam detetar automaticamente VMs num servidor vCenter. Para executar a deteção automática terá de definir uma função (Azure_Site_Recovery) ao nível do vCenter para permitir a recuperação de Site aceder ao servidor vCenter. Tenha em atenção que, se apenas precisar de migrar VMware máquinas para Azure e não precisa de reposição de recurso a partir do Azure, pode definir uma função de só de leitura é suficiente. Configurar as permissões conforme descrito no [passo 6: configurar credenciais para o servidor de vCenter](#step-6-set-up-credentials-for-the-vcenter-server) as permissões da função estão resumidas na tabela seguinte.

**Função** | **Detalhes** | **Permissões**
--- | --- | ---
Função Azure_Site_Recovery | Deteção de VMware VM |Atribua estes privilégios para o servidor do Centro de v:<br/><br/>Arquivo de dados -> alocar espaço, procurar arquivo de dados, baixo ficheiro nível operações., ficheiro de remover, ficheiros de máquina virtual de actualização<br/><br/>Rede -> atribuir de rede<br/><br/>Recurso -> máquina de virtual atribuir ao agrupamento de recursos, migrar desligado máquina virtual, migrar powered numa máquina virtual<br/><br/>Tarefas -> Criar tarefa, a tarefa de actualização<br/><br/>Máquina virtual -> configuração<br/><br/>Máquina virtual -> interagir -> pergunta de resposta, dispositivo ligação., configurar CD multimédia, configurar disquetes multimédia, Power desativar Power no, instale o VMware ferramentas<br/><br/>Máquina virtual -> inventário -> criar, Register, Unregister<br/><br/>Máquina virtual -> aprovisionar -> transferência de máquina virtual permitir, carregar ficheiros de máquina virtual permitir<br/><br/>Máquina virtual -> instantâneos -> Remover instantâneos
função de utilizador vCenter | VMware VM deteção/activação pós-falha sem encerramento da origem VM | Atribua estes privilégios para o servidor do Centro de v:<br/><br/>Objeto de centro de dados –> propagar para objecto subordinado, função = só de leitura <br/><br/>O utilizador é atribuído ao nível do Centro de dados e, pelo que tem acesso a todos os objectos no Centro de dados.  Se quiser restringir o acesso, atribua a função **sem acesso** com o objeto **propagar para a criança** os objectos subordinados (ESX anfitriões, datastores, VMs e redes).
função de utilizador vCenter | Activação e reposição de recurso | Atribua estes privilégios para o servidor do Centro de v:<br/><br/>Centro de dados objecto – propagar para objecto subordinado, função = Azure_Site_Recovery<br/><br/>O utilizador é atribuído ao nível do Centro de dados e, pelo que tem acesso a todos os objectos no Centro de dados.  Se quiser restringir o acesso, atribua uma função **sem acesso **com a **propagar para objecto subordinado** ao objecto subordinado (ESX anfitriões, datastores, VMs e redes).  



## <a name="third-party-software-notices-and-information"></a>Avisos de terceiros software e informações

Não faça traduzir ou Localize

O software e firmware em execução no Microsoft produto ou serviço baseado ou incorpora material de projetos listados abaixo (constituem, "terceiros código").  A Microsoft está o autor não original do código de terceiros.  O aviso de copyright original e a licença, sob o qual a Microsoft recebido essas código de terceiros, são definidas quarta abaixo.

As informações na secção A são relacionadas com componentes de código de terceiros de projetos listados abaixo. Esses licenças e as informações são fornecidos apenas para fins informativos.  Este código de terceiros está a ser relicensed para si pela Microsoft em termos de licenciamento de software da Microsoft para o Microsoft produto ou serviço.  

As informações na secção B são relacionadas com componentes de código de terceiros que estão a ser disponibilizados para si pela Microsoft em termos de licenciamento originais.

O ficheiro concluído poderá encontrar no do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). A Microsoft reserva-se por sugestão, todos os direitos não expressamente concedidos mencionados, estoppel ou de outra forma.

## <a name="next-steps"></a>Próximos passos

[Saber mais sobre a reposição de recurso](site-recovery-failback-azure-to-vmware-classic.md) para trazer o seu Falha ao longo do máquinas em execução no Azure novamente para o seu ambiente no local.
