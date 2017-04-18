<properties
    pageTitle="Criar uma réplica da máquinas virtuais de VMware e servidores físicos para Azure com Azure Site recuperação no portal do Azure | Microsoft Azure"
    description="Descreve como implementar o Azure recuperação de Site para orquestrar replicação, activação e recuperação de máquinas de virtuais VMware no local e Windows/Linux servidores físicos para Azure através do portal Azure"
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
    ms.date="08/12/2016"
    ms.author="raynew"/>

# <a name="replicate-vmware-virtual-machines-and-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Criar uma réplica máquinas virtuais de VMware e máquinas físicas para Azure com a recuperação de Site Azure através do portal Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmware-to-azure.md)
- [Azure clássico](site-recovery-vmware-to-azure-classic.md)
- [Azure clássico (Legado)](site-recovery-vmware-to-azure-classic-legacy.md)

Bem-vindo ao Azure Site recuperação! Utilize este artigo se pretender criar uma réplica no local VMware virtual máquinas ou servidores físicos Windows/Linux para Azure utilizando a recuperação de Site do Azure no portal do Azure.

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Azure recurso Gestor (processador) e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação.

Recuperação de site no portal do Azure fornece várias novas funcionalidades:

- Os serviços de cópia de segurança do Azure e recuperação de sites do Azure são combinados numa única Cofre de serviços de recuperação para que possa configurar e gerir continuidade do negócio e recuperação de falhas (BCDR) a partir de uma única localização. No dashboard de unificado pode monitorizar e gerir as operações nos seus sites no local e na nuvem pública Azure.
- Os utilizadores com subscrições do Azure aprovisionados com o programa de fornecedor de solução da nuvem (CSP) podem agora gerir operações de recuperação de Site no portal do Azure.
- Recuperação de site no portal do Azure pode criar uma réplica máquinas a contas de armazenamento de processador. Em falha na ligação, recuperação de Site cria VMs processador baseado no Azure.
- Recuperação de site continua a suportar a replicação para contas de armazenamento clássica. Em falha na ligação, recuperação de Site cria VMs utilizando o modelo clássico.

Depois de ler esta mensagem de artigo quaisquer comentários na parte inferior dos mesmos Disqus. Faça técnicas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral

As organizações devem uma estratégia BCDR que determina como dados, das cargas de trabalho e apps manter-se em execução e disponível durante o tempo de inatividade planeado e não em recuperar a normal condições de trabalho mais cedo possível. Sua estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis e garantir que das cargas de trabalho permanecem continuamente disponíveis quando ocorre falhas.

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR por orquestrar replicação de servidores físicos no local e máquinas virtuais na nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem falhas na sua localização principal, falhar ao longo para a localização secundária para manter as aplicações e das cargas de trabalho disponíveis. Falhar voltar a sua localização principal quando devolve a operações normais. Saiba mais em [o que é o Azure recuperação de Site?](site-recovery-overview.md)

Este artigo fornece todas as informações que necessita para criar uma réplica no local VMware VMs e Windows/Linux servidores físicos para Azure. Inclui uma descrição geral arquitetura, informações e implementação os passos para configurar o Azure, servidores no local, definições de replicação e planeamento da capacidade de planeamento. Depois de ter configurado a infraestrutura de pode ativar a replicação em computadores que pretende proteger e verificar que esse activação pós-falha funciona.

## <a name="business-advantages"></a>Vantagens de empresas

- Recuperação de site fornece protecção externa para cargas de trabalho de negócio e aplicações em execução no VMware VMs e servidores físicos.
- O portal de serviços de recuperação fornece uma única localização para configurar, gerir e monitorizar replicação, activação pós-falha e recuperação.
- Recuperação de sites possam detetar automaticamente VMs VMware adicionados à vSphere anfitriões.
- Pode facilmente executar activações pós-falha a partir da sua infraestrutura no local para Azure e reposição de recurso (restaurar) a partir do Azure para servidores VMware VM no seu site no local.
- Pode ativar multi VM e criar grupos de replicação para que cargas de trabalho de aplicação camadas através de vários máquinas replicação ao mesmo tempo. Todos os computadores num grupo de replicação têm pontos de recuperação de falha de sistema consistentes e aplicação consistente quando estes falham ao longo do. Para falha na ligação, pode reunir vários computadores na planos de recuperação para que das cargas de trabalho em camadas aplicação falharem ao longo em conjunto.

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

Estes são os componentes de cenário:

- **Servidor de configuração**: uma máquina de no local que coordenadas comunicação e gere os processos de replicação e recuperação de dados. Nesta máquina vai executar um ficheiro de configuração único para instalar o servidor de configuração e estes componentes adicionais:
    - **Servidor do processo**: age como um gateway replicação. Recebe dados de replicação de máquinas origem protegida, optimiza-o com colocação em cache, compressão e encriptação e envia-o ao armazenamento Azure. Também processa instalação de push do serviço de mobilidade para máquinas protegidas e executa a deteção automática de VMware VMs. O servidor de processo predefinido é instalado no servidor de configuração. Pode implementar os servidores de processo autónomo adicionais para dimensionar a sua implementação.
    - **Servidor de destino do modelo global**: processa dados de replicação durante a reposição de recurso a partir do Azure.

- **Serviço de mobilidade**: este componente é implementado num cada computador (VMware VM ou servidor físico) que pretende criar uma réplica para Azure. -Captura escritas de dados no computador e encaminha-los para o servidor do processo.
- **Azure**: não precisa de criar qualquer VMs Azure para processar replicação e activação pós-falha ao Azure.  Precisa de uma subscrição do Azure, uma conta de armazenamento Azure para armazenar dados replicados e uma rede virtual Azure para que o Azure VMs estiver ligados a uma rede após activação pós-falha. A conta de armazenamento e de rede tem de ser na mesma região como o Cofre de serviços de recuperação.
- **Reposição de recurso**: quando estiver pronto para falhar novamente a partir do Azure após uma activação pós-falha ao seu site no local, terá de criar uma VM Azure como um servidor de processo temporário. Pode eliminá-lo Depois de concluída a reposição de recurso. Para reposição de recurso, também terá uma ligação VPN (ou Azure ExpressRoute) entre o site no local e a rede Azure na qual o seu VMs Azure estão localizados. Se o tráfego de reposição de recurso é intenso também poderá ter de configurar um modelo global de dedicada servidor de destino máquina no local. Para o tráfego mais claro pode ser utilizado o servidor de destino mestra predefinido executado no servidor de configuração.


O gráfico que mostra como estes componentes interagem.

![arquitetura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/física para Azure**

## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Eis o que precisa no Azure para implementar este cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta Azure**| Terá de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**Armazenamento Azure** | Dados replicados são armazenados no Azure armazenamento e Azure VMs são criados quando ocorre activação pós-falha. <br/><br/>Para armazenar dados terá uma conta de armazenamento de padrão ou premium na mesma região como o Cofre de serviços de recuperação.<br/><br/>Pode utilizar uma conta de armazenamento LRS ou GRS. Recomendamos que GRS para que seja e são dados se ocorre uma falha de regional ou se a região principal não pode ser recuperada. [Saiba mais](../storage/storage-redundancy.md).<br/><br/> [Armazenamento de Premium](../storage/storage-premium-storage.md) é normalmente utilizado para máquinas virtuais que necessitam de uma forma consistente alto desempenho de IO e latência baixa para anfitrião IO intensivos das cargas de trabalho.<br/><br/> Se pretender utilizar uma conta de premium para armazenar dados replicados, também terá uma conta de armazenamento padrão para armazenar os registos de replicação que capturar em curso alterações aos dados no local.<br/><br/> Tenha em atenção que as contas de armazenamento criadas no portal do Azure não podem ser movidas através de grupos de recursos. Também a proteção contra para contas de armazenamento premium na Índia Central e do Sul Índia atualmente não é suportada.<br/><br/> [Leia sobre](../storage/storage-introduction.md) Armazenamento Azure.
**Rede Azure** | Terá de uma rede virtual Azure que Azure VMs irá estabelecer ligação quando ocorre activação pós-falha. A rede virtual Azure tem de ser na mesma região como o Cofre de serviços de recuperação.
**Reposição de recurso a partir do Azure** | Terá de um temporário servidor processo configurar como um VM Azure. Pode criar isto quando estiver pronto para voltar a falhar e eliminá-lo após falhas novamente estar concluída.<br/><br/> Falha novamente terá uma ligação VPN (ou Azure ExpressRoute) a partir do Azure rede para o site no local.

## <a name="configuration-server--scale-out-process-prerequisites"></a>Servidor de configuração de / dimensionar saída pré-requisitos de processo

Irá configurar uma máquina no local, como o servidor de configuração / escala saída server do processo.

**Pré-requisito** | **Detalhes**
--- | ---
**Servidor de configuração**| Tem uma máquina de virtual com o Windows Server 2012 R2 ou físico no local. Todos os componentes de recuperação de sites no local são instalados nesta máquina.<br/><br/>Para a replicação VMware VM, recomendamos que a implementar o servidor como uma VM VMware altamente disponível. Se estiver a replicação máquinas físicas máquina pode ser um servidor físico.<br/><br/> Reposição de recurso para o site no local a partir do Azure é sempre VMware VMs, independentemente de se falhou sobre VMs ou servidores físicos. Se não implementar o servidor de configuração como uma VM VMware terá de configurar um servidor de destino mestra separada como uma VM VMware para receber tráfego de reposição de recurso.<br/><br/>Se o servidor pertencer um VM VMware, o tipo de adaptador de rede deve ser VMXNET3. Se utilizar um tipo diferente da placa de rede terá de instalar uma [VMware atualizar](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) no servidor vSphere 5.5.<br/><br/>O servidor deve ter um endereço IP estático.<br/><br/>O servidor não deve ser um controlador de domínio.<br/><br/>O nome do anfitrião do servidor deve ser 15 caracteres ou menos.<br/><br/>O sistema operativo devem estar em inglês apenas.<br/><br/> Terá de instalar VMware vSphere PowerCLI 6.0. no servidor de configuração.<br/><br/>O servidor de configuração tem acesso à internet. Acesso de saída é necessário da seguinte forma:<br/><br/>Acesso temporário no HTTP 80 durante a configuração os componentes de recuperação de sites (para transferir MySQL)<br/><br/>Acesso de saída em curso no HTTPS 443 para gestão de replicação<br/><br/>Acesso de saída em curso no HTTPS 9443 para o tráfego de replicação (pode ser modificada esta porta)<br/><br/>O servidor também terá acesso aos seguintes URLs para que pode ligar-se para Azure: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net<br/><br/>Se tiver regras de firewall baseada em endereço IP do servidor, certifique-se de que as regras permitem comunicação com o Azure. Terá de permitir que os [Intervalos de IP do Centro de dados de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443).<br/><br/>Permitir que os intervalos de endereços IP para o Azure região da sua subscrição e para EUA Ocidental.<br/><br/>Permitir que este URL para a transferência do MySQL:.http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi


## <a name="vmware-vcentervsphere-host-prerequisites"></a>Pré-requisitos de sistema anfitrião de vCenter/vSphere VMware

**Pré-requisito** | **Detalhes**
--- | ---
**vSphere**| Precisa de um ou mais hypervisors de vSphere VMware.<br/><br/>Hypervisors deve ser executado vSphere versão 6.0, 5.5 ou 5.1 com as atualizações mais recentes.<br/><br/>Recomendamos que os vSphere anfitriões e os servidores de vCenter estão localizados na mesma rede que o servidor de processo (esta será rede em que o servidor de configuração está localizado, a menos que configurou um servidor de processo dedicado).
**vCenter** | Recomendamos que implementar um servidor de vCenter VMware para gerir os anfitriões vSphere. -Deve estar em execução vCenter versão 6.0 ou 5.5 com as atualizações mais recentes.<br/><br/>Note que a recuperação de sites não suporta vCenter nova e vSphere 6.0 funcionalidades tais como Cruz vCenter vMotion, volumes virtuais e armazenamento DRS. Suporte de recuperação do site está limitado a funcionalidades que foram também disponíveis na versão 5.5.


## <a name="protected-machine-prerequisites"></a>Pré-requisitos de máquina protegida

**Pré-requisito** | **Detalhes**
--- | ---
**No local (VMware VMs)** | VMware VMs que pretende proteger deve ter VMware ferramentas instalada e em execução.<br/><br/> Máquinas que pretende proteger devem estar em conformidade com [Azure pré-requisitos](site-recovery-best-practices.md#azure-virtual-machine-requirements) para criar Azure VMs.<br/><br/>Capacidade de disco individuais no máquinas protegidas não deve ser mais do que 1023 GB. Uma VM pode ter até 64 discos (assim até 64 TB). <br/><br/>Mínimo 2 GB de espaço disponível na unidade de instalação de instalação do componente.<br/><br/>Proteção de VMs com discos encriptados não é suportada.<br/><br/>Partilhada convidado disco clusters não são suportados.<br/><br/>**Porta 20004** deve ser aberto no firewall local a máquina virtual protegida, se pretende ativar **consistência da aplicação**.<br/><br/>Máquinas que tem o Firmware Interface (Extensible Unificado) / Extensible Firmware Interface(EFI) arranque não é suportada.<br/><br/>Nomes de máquina devem conter entre 1 e 63 carateres (letras, números e hífenes). O nome tem de iniciar com uma letra ou número e termina com uma letra ou número. Depois de ter ativado replicação para uma máquina pode modificar o nome do Azure.<br/><br/>Se a origem VM tiver NIC equipas é convertida para uma única NIC após activação pós-falha ao Azure.<br/><br/>Se protegida máquinas virtuais tiver um disco iSCSI, em seguida, recuperação de sites converte o disco de iSCSI VM protegido para um ficheiro VHD quando a VM Falha ao longo do Azure. Em seguida, se o destino iSCSI pode ser alcançado através da VM Azure será ligá-la e, essencialmente, consulte o artigo dois discos – o disco VHD na VM Azure e o disco de iSCSI de origem. Neste caso, terá desligar o destino iSCSI que é apresentado a VM Azure.
**Máquinas com o Windows (física ou VMware)** | Computador deve estar em execução um sistema operativo de 64 bits suportado: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com em menos SP1.<br/><br/> O sistema operativo devem ser instalado na unidade de C:\. O disco SO deve ser um disco básico do Windows e não como dinâmico. O disco de dados pode ser dinâmico.<br/><br/>Recuperação de sites suporta VMs com um disco RDM. Durante a reposição de recurso, recuperação de Site irá reutilizar o disco RDM se o disco VM e RDM origem original está disponível. Se estes não estão disponíveis, recuperação de Site irá criar um novo ficheiro VMDK para cada disco durante a reposição de recurso.
**Linux máquinas** (phyical ou VMware)|  Terá de um sistema operativo de 64 bits suportado: Red chapéu Enterprise Linux 6.7,7.1,7.2; Centos 6.5, 6.6,6.7,7.0,7.1,7.2; Oracle Enterprise Linux 6.4, 6.5 executar o kernel compatível vermelho chapéu ou inviolável Enterprise Kernel lançamento 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>ficheiros de /etc/hosts no máquinas protegidas devem conter entradas que mapeiam o nome de anfitrião local para endereços IP associados todos os adaptadores de rede.<br/><br/>Se pretender estabelecer ligação com uma máquina virtual Azure executar Linux após activação pós-falha a utilizar um seguro Shell cliente (ssh), certifique-se de que o serviço de Shell seguro no computador protegido está definido para iniciar automaticamente no arranque do sistema e que as regras de firewall permitem um ssh ligação à mesma.<br/><br/>O nome de anfitrião, pontos de montagem, nomes de dispositivo e caminhos de sistema Linux e nomes de ficheiro (ex/etc /; /usr) devem ser apenas em inglês.<br/><br/>Proteção só pode ser ativada para Linux máquinas com o armazenamento seguinte: (EXT3, ETX4, ReiserFS, XFS); de sistema de ficheiros I-dispositivo de software mapeamento de pontos (i)); Gestor de volume: (LVM2). Servidores físicos com HP CCISS controlador de armazenamento não são suportados. O sistema de ficheiros ReiserFS só é suportado em SUSE Linux Enterprise Server 11 SP3.<br/><br/>Recuperação de sites suporta VMs com um disco RDM.  Durante a reposição de recurso para Linux, recuperação de sites não reutilizar o disco RDM. Em vez disso, que cria um novo ficheiro VMDK para cada disco RDM correspondente.<br/><br/>Certifique-se de que o utilizador defina a definição de disk.enableUUID=true nos parâmetros de configuração da VM no VMware. Crie a entrada se não existe. Tem de conforme necessário para fornecer um UUID consistente à VMDK para que-montagens corretamente. Também é a adição desta definição assegura que as alterações de delta apenas são transferidas para o local durante a reposição de recurso e não uma replicação completa.
**Serviço de mobilidade** |  **Windows**: irá para transmitir automaticamente o serviço de mobilidade para VMs a executar o Windows, terá de fornecer uma conta de administrador (local administrador no computador Windows), para que o servidor de processo pode fazer uma instalação push.<br/><br/>**Linux**: para transmitir automaticamente o serviço de mobilidade para VMs executar Linux terá de criar uma conta que pode ser utilizada pelo servidor de processo para efetuar uma instalação push.<br/><br/> Por predefinição, todos os discos num computador são replicados. Para [excluir um disco de replicação](#exclude-disks-from-replication), o serviço de mobilidade tem de estar instalado manualmente no computador antes de ativar a replicação.<br/>

## <a name="prepare-for-deployment"></a>Preparar para implementação

Para se preparar para implementação que vai precisar de:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual Azure VMs serão localizados quando que está a fiadas o após activação pós-falha. Além disso, para a reposição de recurso terá de configurar uma ligação VPN (ou Azure ExpressRoute) da rede Azure ao seu site no local.
2. [Configurar uma conta de armazenamento Azure](#set-up-an-azure-storage-account) para dados replicados.
3. [Preparar uma conta](#prepare-an-account-for-automatic-discovery) no servidor vCenter ou vSphere aloja para que a recuperação de sites pode detetar automaticamente VMs VMware que são adicionados.
4. [Preparar o servidor de configuração](#prepare-the-configuration-server) para garantir que pode aceder aos URLs necessários e instalar vSphere PowerCLI 6.0.


### <a name="set-up-an-azure-network"></a>Configurar uma rede Azure

- A rede deve estar na mesma região Azure como que na qual irá implementar o Cofre de serviços de recuperação.
- Consoante o modelo de recursos que pretende utilizar para Ocorreu uma falha ao longo do Azure VMs, irá configurar a rede Azure no [modo de processador](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Para poder falhar novamente a partir do Azure ao seu site de VMware no local tem uma ligação VPN (ou uma ligação do Azure ExpressRoute) da rede Azure na qual o Azure VMs replicada estão localizados, à rede no local em que se encontra o servidor de configuração.
- [Saiba mais sobre](../vpn-gateway/vpn-gateway-site-to-site-create.md) a implementação suportada modelos para ligações de site para o site VPN e como [Configurar uma ligação](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- Em alternativa pode configurar o [Azure ExpressRoute](../expressroute/expressroute-introduction.md). [Saiba mais](../expressroute/expressroute-howto-vnet-portal-classic.md) sobre como configurar uma rede Azure com ExpressRoute.

> [AZURE.NOTE] [Migração das redes](../resource-group-move-resources.md) através de grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportado para as redes utilizadas para implementar o recuperação de sites.

### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento Azure

- Irá precisar de um padrão ou uma conta de armazenamento Azure premium para armazenarem dados replicados para Azure. A conta tem de ser na mesma região como o Cofre de serviços de recuperação. Consoante o modelo de recursos que pretende utilizar para Ocorreu uma falha ao longo do Azure VMs, irá configurar uma conta no [modo de processador](../storage/storage-create-storage-account.md) ou [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Se estiver a utilizar uma conta de premium para os dados replicados que necessárias para criar uma conta adicional padrão para armazenar registos de replicação capturar em curso alterações aos dados no local.  

> [AZURE.NOTE] [Migração de contas de armazenamento de](../resource-group-move-resources.md) grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportada para contas de armazenamento utilizadas para implementar o recuperação de sites.

### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para deteção automática

Servidor de processo de recuperação do Site possam detetar automaticamente VMware VMs vSphere anfitriões ou num servidor vCenter que faz a gestão dos anfitriões. Para efetuar automática deteção credenciais de recuperação de sites que pode aceder ao servidor de VMware. Esta não é relevante se estiver a replicação físicas máquinas apenas.

1. Para utilizar uma conta dedicada para deteção automática, crie uma função (por exemplo Azure_Site_Recovery) ao nível do vCenter com as [permissões necessárias](#vmware-account-permissions).
2. Criar um novo utilizador no servidor anfitrião ou vCenter vSphere e atribuir uma função ao utilizador. Mais tarde irá informar recuperação do Site saber sobre estas credenciais para que possa efectuar deteção automática.

    >[AZURE.NOTE] Uma conta de utilizador vCenter com uma função só de leitura pode executar activação pós-falha mas não é possível encerrar máquinas origem protegida. Se pretender encerrar essas máquinas terá a função [Azure_Site_Recovery](#vmware-account-permissions) . Se está apenas a migrar VMs a partir do VMware para Azure e não precisa de reposição de recurso a função só de leitura é suficiente.

### <a name="prepare-the-configuration-server"></a>Preparar o servidor de configuração

1.  Certifique-se de que está em conformidade com a [Pré-requisitos de](#configuration-server-prerequisites)máquina que está a utilizar para o servidor de configuração. Em particular, certifique-se de que o computador está ligado à internet com estas definições:

    - Permitir o acesso a estes URLs: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net
    - Permitir acesso ao [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) para transferir MySQL.
    - Permita a comunicação de firewall para Azure com os [intervalos IP do Centro de dados Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (443).

2.  Transfira e instale [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) no servidor de configuração. (Atualmente outras versões do PowerCLI não são suportados, incluindo lançamentos R da versão 6.0.)


## <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gestão** > **cópia de segurança e recuperação de sites (OMS)**. Em alternativa, pode clicar em **Procurar** > **Recuperação serviços cofre** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. Em **nome** , especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. [Criar um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione uma existente. Especifique uma região do Azure. Máquinas vai ser replicadas para esta região. Tenha em atenção que armazenamento Azure e redes utilizadas para recuperação de sites terão de ser na mesma região. Para verificar regiões suportadas Consulte geográficos disponibilidade no [Recuperação preços detalhes do Site Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se pretender aceder rapidamente o cofre a partir do Dashboard, clique em **Afixar ao dashboard** e, em seguida, clique em **Criar**.

    ![Novo cofre](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

O novo cofre irão aparecer no **Dashboard** > **todos os recursos**e, no principal pá **cofres de serviços de recuperação** .

## <a name="getting-started"></a>Introdução

Recuperação de site fornece uma experiência de introdução ao concebido para ajudar o utilizador e a executar o mais rapidamente possível. Verifica a pré-requisitos e orienta-o através dos passos que precisa para obter implementado de recuperação de sites.

Seleciona o tipo de máquinas que pretende criar uma réplica e, em que pretende criar uma réplica para. Configurar a infraestrutura, incluindo servidores no local, definições do Azure, políticas de replicação e planeamento da capacidade. Após a sua infraestrutura é num local ativar replicação para VMs e servidores físicos. Em seguida, pode executar activações pós-falha para máquinas específicas ou criar planos de recuperação falha através de vários computadores.

Começar a introdução ao selecionar como pretende implementar recuperação de sites. O fluxo de introdução ao altera ligeiramente consoante seus requisitos de replicação.


## <a name="step-1-choose-your-protection-goals"></a>Passo 1: Escolher os seus objetivos proteção

Selecione o que pretende criar uma réplica e onde pretende que sejam replicadas para.

1. Na pá **Serviços de recuperação cofres** selecione cofre e clique em **Definições**.
2. Nas **Definições** > **Introdução** clique em **Site recuperação** > **passo 1: preparar infraestrutura** > **objectivo de proteção**.

    ![Selecione os seus objetivos](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. Em **objetivo de proteção** selecione **Para Azure**e, selecione **Sim, com VMware vSphere hipervisor**. Em seguida, clique em **OK**.

    ![Selecione os seus objetivos](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Passo 2: Configurar o ambiente de origem

Configure o servidor de configuração e registá-lo no cofre serviços de recuperação. Se estiver a replicação VMware VMs especificar a conta VMware estiver a utilizar o âmbito da deteção automático.

1. Clique em **passo 1: preparar infraestrutura** > **origem**. Em **preparar origem** se não tiver um servidor de configuração clique em **+ servidor de configuração** para adicionar um.

    ![Configurar o origem](./media/site-recovery-vmware-to-azure/set-source1.png)

2. A verificação de pá **Adicionar servidor** nesse **Servidor de configuração** é apresentada no **servidor, escreva**.
3. Antes de configurar o servidor de configuração verifique [Pré-requisitos](#configuration-server-prerequisites). Em verificação específica que o computador têm acesso URLs necessários.
4.  Transferir o ficheiro de instalação de configuração de Unified de recuperação do Site.
5.  Transfira a chave de registo do cofre. Terá este quando executa a configuração de Unified. A chave é válida para 5 dias após lhe gerá-lo.

    ![Configurar o origem](./media/site-recovery-vmware-to-azure/set-source2.png)

6.  No computador que está a utilizar como o servidor de configuração, execute a configuração de Unified para instalar o servidor de configuração, o servidor de processo e o servidor de destino principal.


### <a name="run-site-recovery-unified-setup"></a>Recuperação de Site executar Unified configuração

1.  Execute o ficheiro de instalação de configuração de Unified.
2.  No **antes de começar** , selecione **instalar o servidor de configuração e o servidor de processo**.

    ![Antes de começar](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. Na **Licença de Software de terceiros** clique em **Aceitar posso** transferir e instalar MySQL.

    ![Terceiro = software de terceiros](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. No **registo** procure e selecione a chave de registo que transferiu do cofre.

    ![Registo](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. Nas **Definições do Internet** especificar como o fornecedor executado no servidor de configuração será liga ao Azure recuperação de Site através da internet.

    - Se pretender estabelecer ligação com o proxy de que está atualmente configurado no computador, selecione **ligar-se com as definições de proxy existente**.
    - Se pretender que o fornecedor para se ligar diretamente selecione **Ligar diretamente sem um proxy**.
    - Se o proxy existente requer autenticação ou que pretende utilizar um proxy personalizado para a ligação do fornecedor, selecione **ligar-se com as definições de proxy personalizado**.
        - Se utilizar um proxy personalizado, que terá de especificar o endereço, porta e as credenciais
        - Se estiver a utilizar um proxy deve tiver já permitido os URLs descritos na [Pré-requisitos](#configuration-server-prerequisites).

    ![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. **Verificar a pré-requisitos da** configuração é executado uma verificação para se certificar de que a instalação pode ser executados. Se for apresentado um aviso sobre a **sincronização de tempo Global verificar** Certifique-se de que a hora no relógio do sistema (definições de**data e hora** ) é o mesmo que o fuso horário.

    ![Pré-requisitos](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. Configuração do **MySQL** crie credenciais para iniciar sessão a instância de servidor do MySQL será instalada.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. Em selecionar **Ambiente detalhes** se vai criar uma réplica VMware VMs. Se estiver, em seguida, o programa de configuração verifica que PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. Em selecionar **Instalar localização** onde pretende instalar os binários e armazenar a cache. Pode selecionar uma unidade que tenha pelo menos 5 GB de armazenamento disponível mas recomendamos uma unidade de cache com, pelo menos, 600 GB de espaço livre.

    ![Localização de instalação](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. Em **Selecção de rede** , especifique a escuta (adaptador de rede e porta de SSL) no qual o servidor de configuração enviar e receber replicação dados. Pode modificar a predefinição da porta (9443). Para além desta porta, será utilizada por um servidor web que orquestra operações de replicação porta 443. 443 não deveriam ser utilizados para o tráfego de replicação a receber.


    ![Selecção de rede](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  Num **Resumo** reveja as informações e clique em **instalar**. Quando termina de instalação é gerado uma frase de acesso. Irá precisar quando ativa a replicação por isso, copiá-la e mantê-lo numa localização segura.

    ![Resumo](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Após termina de registo do servidor é apresentado nas **Definições** > pá de**servidores** no cofre.



#### <a name="run-setup-from-the-command-line"></a>Executar a configuração da linha de comandos

Pode configurar o servidor de configuração da linha de comandos:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parâmetros:

- / ServerMode: obrigatório. Especifica se a configuração e o processo de servidores devem ser instalados, ou apenas o servidor do processo. Valores de entrada: CS, PS.
- InstallLocation: obrigatório. A pasta na qual os componentes são instalados.
- / MySQLCredsFilePath. Obrigatório. O caminho do ficheiro em que o servidor do MySQL as credenciais é armazenadas. O ficheiro deve ser neste formato:
    - [MySQLCredentials]
    - MySQLRootPassword = "<Password>"
    - MySQLUserPassword = "<Password>"
- / VaultCredsFilePath. Obrigatório. A localização do ficheiro de credenciais Cofre
- / EnvType. Obrigatório. O tipo de instalação. Valores: VMware, NonVMware
- / PSIP e /CSIP. Obrigatório. O endereço IP do servidor de processo e do servidor de configuração.
- / PassphraseFilePath. Obrigatório. A localização do ficheiro frase de acesso.
- / BypassProxy. Opcional. Especifica que o servidor de configuração se liga aos Azure sem um proxy.
- / ProxySettingsFilePath. Opcional. Definições de proxy (a predefinição de proxy requer autenticação ou um proxy personalizado). O ficheiro deve ser neste formato:
    - [ProxySettings]
    - ProxyAuthentication = "Sim/não"
    - Proxy IP = "endereço IP >"
    - ProxyPort = "<Port>"
    - ProxyUserName = "<User Name>"
    - ProxyPassword = "<Password>"
- DataTransferSecurePort. Opcional. Número de porta a ser utilizado para os dados de replicação.
- SkipSpaceCheck. Opcional. Ignorar verificação de espaço de cache.
- AcceptThirdpartyEULA. Obrigatório. Sinalizador implica aceitação de terceiros EULA.
- ShowThirdpartyEULA. Obrigatório. Apresenta o EULA de terceiros. Se fornecidos como entrada todos os outros parâmetros são ignorados.

### <a name="add-the-vmware-account-used-for-automatic-discovery"></a>Adicionar a conta de VMware utilizada para deteção automática

 Quando preparado para implementação que deve ter [criado uma conta de VMware](#prepare-an-account-for-automatic-discovery) que recuperação de sites podem utilizar para deteção automática. Adicione esta conta da seguinte forma:

1. Abra **CSPSConfigtool.exe**. É disponível como um atalho no ambiente de trabalho e localizada na pasta \home\svsystems\bin [localização instalar].
2. Clique em **Gerir contas** > **Adicionar conta**.

    ![Adicionar conta](./media/site-recovery-vmware-to-azure/credentials1.png)

3. Em **Detalhes da conta** adicione a conta que será utilizada para deteção automática. Note que pode demorar 15 minutos ou mais para o nome da conta que seja apresentado no portal. Para atualizar imediatamente, clique em **Servidores de configuração** > nome do servidor > **Atualizar servidor**.

    ![Detalhes](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vsphere-hosts-and-vcenter-servers"></a>Ligar a anfitriões de vSphere e os servidores de vCenter

Se estiver a replicação VMware VMs ligar ao vSphere anfitriões e os servidores de vCenter.

1. Certifique-se de que o servidor de configuração tem acesso à rede para os servidores de vCenter e vSphere anfitriões.
2. Clique em **infraestrutura preparar** > **origem**. Na **origem de preparar** selecione o servidor de configuração e, clique em **+ vCenter** para adicionar um servidor de anfitrião ou vCenter vSphere.
3. Em **Adicionar vCenter** Especifique um nome amigável para o servidor de anfitrião ou vCenter vSphere e especifique o endereço IP ou o FQDN do servidor. Deixe a porta como 443 a menos que os servidores de VMware estiverem configurados para escutar pedidos de uma porta diferente. Em seguida, selecione a conta que será utilizada para ligar ao servidor VMware. Clique em **OK**.

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

    >[AZURE.NOTE] Se estiver a adicionar o servidor de vCenter ou vSphere anfitrião com uma conta que não tiver privilégios de administrador no servidor vCenter ou anfitrião, em seguida, certifique-se de que a conta tem estes privilégios ativados: Centro de dados, arquivo de dados, pasta, anfitrião, rede, recursos, Virtual de máquina, vSphere distribuído mudar. Além disso, o servidor de vCenter necessita o privilégio de vistas de armazenamento.

Recuperação de sites liga-se para os servidores de VMware utilizando as definições que especificou e detetar VMs.

## <a name="step-3-set-up-the-target-environment"></a>Passo 3: Configurar o ambiente de destino

Certifique-se de que tem uma conta de armazenamento de replicação e uma rede Azure ao qual vai ligar Azure VMs após activação pós-falha.

1.  Clique em **infraestrutura preparar** > **destino** e selecione a subscrição Azure que pretende utilizar.
2.  Especifique o modelo de implementação que pretende utilizar para VMs após activação pós-falha.
3.  Recuperação de sites verifica que tem um ou mais contas de armazenamento Azure compatível e redes.

    ![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)

4.  Se ainda não criou uma conta de armazenamento e pretender criar um processador de utilizar clique em **+ conta de armazenamento** para fazer essa inline.  No pá a **criar a conta de armazenamento** Especifique um nome de conta, o tipo, a subscrição e a localização. A conta deverá ser na mesma região como o Cofre de serviços de recuperação.

    ![Armazenamento](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

    Tenha em atenção que:

    - Se pretender criar uma conta de armazenamento utilizando o modelo clássico irá fazê-lo no portal do Azure. [Saiba mais](../storage/storage-create-storage-account-classic-portal.md)
    - Se estiver a utilizar uma conta de armazenamento premium para os dados replicados que terá de configurar uma conta de armazenamento adicional de padrão para loja replicação regista que todas as alterações em curso do captura dados no local.

    > [AZURE.NOTE] Atualmente, a proteção contra para contas de armazenamento premium na Índia Central e Índia Sul não é suportada.

4.  Selecione uma rede Azure. Se ainda não criou uma rede e que pretende fazer que utilizando processador clique **+ rede** para fazer essa inline. No pá a **criar rede virtual** Especifique um nome de rede, intervalo de endereços, detalhes de sub-rede, subscrição e localização. A rede deve estar na mesma localização, como o Cofre de serviços de recuperação.

    ![Rede](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

    Se pretender criar uma rede utilizando o modelo clássico irá fazê-lo no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## <a name="step-4-set-up-replication-settings"></a>Passo 4: Configurar definições de replicação

1. Para criar uma nova replicação política, clique em **infraestrutura preparar** > **Definições de replicação** > **+ criar e associar**.
2. Na **política associar e criar** Especifique um nome de política.
3. Em **limiar RPO**: Especifique o limite RPO. Alertas serão gerados quando replicação contínua excede este limite.
5. No **ponto de recuperação de retenção**, especifique horas quanto tempo a janela de retenção para cada ponto de recuperação. Podem ser recuperadas máquinas protegidas para qualquer ponto dentro de uma janela. A retenção de 24 horas é suportada para máquinas replicadas para armazenamento premium.
6. Na **aplicação consistentes frequência de instantâneo**, especifique com que frequência (em minutos) que contém instantâneos consistente para a aplicação de pontos de recuperação serão criados.
7. Quando cria uma política de replicação, por predefinição uma política de correspondência é automaticamente criada para reposição de recurso. Por exemplo, se a política de replicação for **Republic política** , em seguida, a política de reposição de recurso estarão **Republic-política-reposição de recurso**. Esta política não é utilizada até iniciar reposição de uma recurso.  
8. Clique em **OK** para criar a política.

    ![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Quando cria uma nova política tem automaticamente associados com o servidor de configuração. Clique em **OK**.

    ![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## <a name="step-5-capacity-planning"></a>Passo 5: Planear a capacidade

Agora que tem o basic infraestrutura de configurar o pode refletir acerca do planeamento de capacidades e descubra se precisar de recursos adicionais.

Recuperação de site fornece um planeador de capacidade para ajudá-lo alocar os recursos da direita para o seu ambiente de origem, os componentes de recuperação do site, redes e armazenamento. Pode executar o planeador de no modo rápido para estimativas com base no número médio de VMs, discos e armazenamento ou no modo detalhado na qual irá entrada ilustrações ao nível de carga de trabalho. Antes de começar terá:

- Reunir informações sobre o ambiente de replicação, incluindo VMs, discos por VMs e armazenamento por disco.
- Calcule a taxa de alteração (vasilha) diária, que terá de para dados replicados. Pode utilizar a [capacidade de vSphere planeamento aparelho](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para o ajudar a efetuar o seguinte.

1.  Clique em **Transferir** para transferir a ferramenta de e, em seguida, executá-la. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando tiver terminado selecione **Sim** **já concluiu planeamento da capacidade?**

    ![Planeamento de capacidades](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

A tabela abaixo para capturar um número de pontos para o ajudar com capacidade de planeamento para este cenário.


**Componente** | **Detalhes**
--- | --- | ---
**Replicação** | **Taxa de alteração de máxima diariamente**— uma máquina protegida só pode utilizar um servidor de processo e um servidor de um único processo pode processar diária alterar classificar até 2 TB. Assim 2 TB é que os dados diários máximos alterar a taxa que é suportada para uma máquina protegida.<br/><br/> **Débito máximo**— uma máquina de replicadas pode pertencer a uma conta de armazenamento no Azure. Uma conta de armazenamento padrão pode lidar com um máximo de 20.000 pedidos por segundo e, recomendamos que mantenha o número de IOPS através de uma máquina de origem a 20000. Por exemplo se tiver uma máquina de origem com 5 discos e cada disco gera 120 IOPS (tamanho de 8K) na origem de, em seguida, será dentro Azure por limite IOPS disco de 500. O número de contas de armazenamento necessárias = origem do total IOPs/20000.
**Servidor de configuração** | O servidor de configuração deverá conseguir processar a capacidade de taxa de alteração diária através de todas as cargas de trabalho em execução no máquinas protegidas e precisa de largura de banda suficiente para continuamente criar uma réplica dados ao armazenamento Azure.<br/><br/> Como prática recomendada Recomendamos que o servidor de configuração de estar localizados na mesma rede e segmento LAN como máquinas que pretende proteger. Pode estar localizado numa rede diferente mas máquinas que pretende proteger devem ter L3 visibilidade de rede à mesma.<br/><br/> Recomendações de tamanho para o servidor de configuração são resumidas na tabela abaixo.
**Servidor de processo** | O primeiro servidor de processo é instalado por predefinição no servidor de configuração. Pode implementar os servidores de processo adicionais para dimensionar o seu ambiente. Tenha em atenção que:<br/><br/> O servidor de processo recebe dados de replicação de máquinas protegidas e optimiza-lo com a colocação em cache, compressão e encriptação antes de enviar a Azure. O computador de servidor do processo deve ter recursos suficientes para efetuar estas tarefas.<br/><br/> O servidor de processo utiliza cache baseada no disco. Recomendamos que um disco de cache separada de 600 GB ou mais para processar alterações aos dados armazenadas trabalho congestionamento de rede ou falha do.

### <a name="size-recommendations-for-the-configuration-server"></a>Recomendações de tamanho para o servidor de configuração

**CPU** | **Memória** | **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz) | 16 GB | 300 GB | 500 GB ou menos | Criar uma réplica máquinas menos de 100.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB para 1 TB | Criar uma réplica entre 100 e 150 máquinas.
16 vCPUs (2 sockets * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB para 2 TB | Criar uma réplica entre máquinas de 150-200.
Implementar outro servidor de processo | | | > 2 TB | Implemente os servidores de processo adicionais se estiver a replicação máquinas mais de 200 ou se os dados diários alterar taxa excede 2 TB.

Em que:

- Cada máquina de origem está configurada com 3 discos de 100 GB.
- Armazenamento de avaliação comparativa de unidades de SA 8 de 10 K rotações/minuto é utilizada com RAID 10 para as medidas de disco cache.

### <a name="size-recommendations-for-the-process-server"></a>Recomendações para o tamanho para o servidor de processo

Se necessita de proteger máquinas mais de 200 ou alterar a velocidade de diária for maior do que 2 TB pode adicionar os servidores de processo adicionais para processar a carga de replicação. Para dimensionar saída pode:

- Aumente o número de servidores de configuração. Por exemplo pode proteger até 400 máquinas com dois servidores de configuração.
- Adicionar os servidores de processo adicionais e utilizá-los para processar o tráfego em vez de (ou, para além) o servidor de configuração.

Esta tabela descreve um cenário em que:

- Não está a planear utilizar o servidor de configuração como um servidor do processo.
- Configurou um servidor de processo adicionais.
- Configurar o máquinas virtuais protegidas para utilizar o servidor de processo adicionais.
- Cada máquina de origem protegida está configurada com três discos de 100 GB.

**Servidor de configuração** | **Servidor de processo adicionais**| **Tamanho da cache do disco** | **Taxa de alteração de dados** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memória | 4 vCPUs (2 sockets * 2 núcleos @ 2,5 GHz), 8 GB de memória | 300 GB | 250 GB ou menos | Criar uma réplica máquinas inferior ou igual a 85.
8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memória | 8 vCPUs (2 sockets * 4 núcleos @ 2,5 GHz), 12 GB de memória | 600 GB | 250 GB para 1 TB | Criar uma réplica entre 85 150 máquinas.
12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz), 18 GB de memória | 12 vCPUs (2 sockets * 6 núcleos @ 2,5 GHz) 24 GB de memória | 1 TB | 1 TB para 2 TB | Criar uma réplica entre 150 225 máquinas.


A forma na qual dimensionar os servidores irá dependem a sua preferência para uma escala de cores para cima ou dimensionar saída modelo.  Dimensionar ao implementar alguns configuração de alto nível e os servidores de processo ou dimensionar saída ao implementar mais servidores com menos recursos. Por exemplo: Se precisar de proteger 220 máquinas poderia efetue um dos seguintes procedimentos:

- Configure o servidor de configuração com 12vCPU, 18 GB de memória, um servidor de processo adicionais com 12vCPU, 24 GB de memória e configurar máquinas protegidas para utilizar apenas o servidor de processo adicionais.
- Em alternativa poderia configurar duas configuração servidores (2 x 8vCPU, 16 GB de RAM) e dois processos adicionais (1 x 8vCPU) e 4vCPU x1 para processar 135 + 85 máquinas (220) e configurar máquinas protegidas para utilizar apenas os servidores de processo adicionais.

[Siga estas instruções](#deploy-additional-process-servers) para configurar um servidor de processo adicionais.

### <a name="network-bandwidth-considerations"></a>Considerações de largura de banda de rede

Pode utilizar a ferramenta de Planeador de capacidade para calcular a largura de banda que precisa para replicação (replicação inicial e, em seguida, delta). Para controlar a quantidade de utilização de largura de banda para a replicação tem algumas opções:

- **Optimizar a largura de banda**: o tráfego de VMware que replica para Azure acede através de um servidor de processo específico. Optimizar a largura de banda nos computadores em execução como servidores do processo.
- **Influenciar a largura de banda**: pode influenciar a largura de banda utilizada para a replicação utilizar algumas das chaves de registo:
    - O valor de registo **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** Especifica o número de threads que são usados para transferência de dados (replicação inicial ou delta) de um disco. Um valor maior aumenta a largura de banda de rede utilizada para a replicação.
    - O **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** Especifica o número de threads utilizado para transferência de dados durante a reposição de recurso.

#### <a name="throttle-bandwidth"></a>Optimizar a largura de banda

1. Abra o snap-in MMC de cópia de segurança do Microsoft Azure no computador serve como o servidor do processo. Por predefinição, um atalho para a cópia de segurança do Microsoft Azure está disponível no ambiente de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. In snap-, clique em **Alterar propriedades**.

    ![Optimizar a largura de banda](./media/site-recovery-vmware-to-azure/throttle1.png)

3. No separador **Throttling** selecione **Ativar a utilização da largura de banda de internet limitação para operações de cópia de segurança**e defina os limites do trabalho e que não sejam-trabalho horas. Intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Optimizar a largura de banda](./media/site-recovery-vmware-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Eis um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Conjunto OBMachineSetting NoThrottle** indica que não sem limitação é necessária.


#### <a name="influence-network-bandwidth"></a>Largura de banda de rede de influência

1. No registo navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
    - Para influenciar o tráfego de largura de banda num disco replicação, modifique o valor de **UploadThreadsPerVM**ou criar a chave de se não existe.
    - Para influenciar a largura de banda para o tráfego de reposição de recurso a partir do Azure, modifique o valor **DownloadThreadsPerVM**.
2. O valor predefinido é 4. Numa rede "overprovisioned", devem ser alteradas estas chaves de registo dos valores predefinidos. O valor máximo é 32. Monitorizar o tráfego para otimizar o valor.

## <a name="step-6-replicate-applications"></a>Passo 6: Repetições aplicações

Certifique-se de que máquinas que pretende criar uma réplica da são preparadas para instalação do serviço de mobilidade e, em seguida, ativar replicação.

### <a name="install-the-mobility-service"></a>Instalar o serviço de mobilidade

É o primeiro passo na ativação protecção para máquinas virtuais e servidores físicos instalar o serviço de mobilidade. Pode fazê-lo de duas formas:

- **Push de servidor do processo**: Quando ativa a replicação num computador, emissão e instalar o componente de serviço de mobilidade a partir do servidor de processo. Repare que a instalação push não ocorre se máquinas já estiverem em execução uma versão de cima todate do componente.
- **Enterprise push**: instalar o componente utilizando o processo de push enterprise como WSUS ou Gestor de configuração do sistema centro ou [Automatização do Azure e configuração do Estado de desejado](./site-recovery-automate-mobility-service-install.md)automaticamente. Configure o servidor de configuração, antes de efetuar esta ação.
- **Instalação manual**: instalar o componente manualmente em cada máquina que pretende criar uma réplica. Configure o servidor de configuração, antes de efetuar esta ação.


#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparar para push automática no máquinas com o Windows

Eis como preparar máquinas com o Windows para que o serviço de mobilidade pode ser instalado automaticamente pelo servidor de processo.

1.  Crie uma conta que pode ser utilizada pelo servidor de processo para aceder ao computador. A conta deverá ter privilégios de administrador (local ou domínio) e só é utilizado para a instalação push.

    >[AZURE.NOTE] Se não estiver a utilizar uma conta de domínio terá de desativar o controlo de acesso remoto do utilizador no computador local. Para executar esta tarefa, no registo em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System adicione a entrada de DWORD LocalAccountTokenFilterPolicy com um valor de 1. Para adicionar a entrada de registo de um tipo de clip **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  A Firewall do Windows do computador que pretende proteger, selecione **Permitir que uma aplicação ou funcionalidade através da Firewall**. Ative a **partilha ficheiros e impressoras** e **Instrumentação de gestão do Windows**. Para máquinas pertencerem a um domínio pode configurar as definições da firewall com um GPO.

    ![Definições da firewall](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Adicione a conta que criou:

    - Abra **cspsconfigtool**. É disponível como um atalho no ambiente de trabalho e localizada na pasta \home\svsystems\bin [localização instalar].
    - No separador **Gerir contas** , clique em **Adicionar conta**.
    - Adicione a conta que criou. Depois de adicionar a conta terá de fornecer as credenciais quando ativa a replicação para uma máquina.


#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparar para push automática em servidores Linux

1.  Certifique-se de que o computador Linux que pretende proteger é suportado, tal como descrito na [Pré-requisitos de máquina protegida](#protected-machine-prerequisites). Certifique-se de que existe conectividade entre a máquina Linux e no servidor do processo de rede.

2.  Crie uma conta que pode ser utilizada pelo servidor de processo para aceder ao computador. A conta deve ser um utilizador de raiz do servidor de origem Linux e só é utilizado para a instalação push.

    - Abra **cspsconfigtool**. É disponível como um atalho no ambiente de trabalho e localizada na pasta \home\svsystems\bin [localização instalar].
    - No separador **Gerir contas** , clique em **Adicionar conta**.
    - Adicione a conta que criou. Depois de adicionar a conta terá de fornecer as credenciais quando ativa a replicação para uma máquina.

3.  Verifique se o ficheiro /etc/hosts do servidor de origem Linux contém as entradas que mapeiam o nome do anfitrião local para endereços IP associados todos os adaptadores de rede.
4.  Instalar o antes de openssh mais recente, servidor de antes openssh, openssl pacotes no computador que pretende criar uma réplica.
5.  Certifique-se de que SSH é activado e em execução no Porta 22.
6.  Ativar a autenticação subsystem e palavra-passe SFTP no ficheiro sshd_config da seguinte forma:

    - Inicie sessão como raiz.
    - No ficheiro de /etc/ssh/sshd_config de ficheiro, localize a linha que começa com **PasswordAuthentication**.
    - Remova os comentários da linha e altere o valor de **sem** como **Sim**.
    - Localize a linha que começa com **Subsystem** e remova os comentários da linha.

        ![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


### <a name="install-the-mobility-service-manually"></a>Instalar manualmente o serviço de mobilidade

Os programas de instalação estão disponíveis no servidor de configuração na **c:\Programas\Microsoft ficheiros (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

Sistema operativo de origem | Ficheiro de instalação do serviço de mobilidade
--- | ---
Windows Server (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_Windows_* release.exe
CentOS 6.4, 6.5, 6.6 (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_RHEL6-64_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_SLES11-SP3-64_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (apenas 64 bits) | Microsoft-ASR_UA_9. *.0.0_OL6-64_*release.tar.gz


#### <a name="install-mobility-service-on-a-windows-server"></a>Instalar o serviço de mobilidade num Windows Server


1. Transfira e execute o installer relevante.
2. No **antes de começar** , selecione **o serviço de mobilidade**.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility3.png)

3. Na **Configuração de detalhes do servidor** especifique o endereço IP do servidor de configuração e a frase de acesso que foi gerado quando executou a instalação do Unified. Pode obter a frase de acesso ao executar: ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – v** no servidor de configuração.

    ![Serviço de mobilidade](./media/site-recovery-vmware-to-azure/mobility6.png)

4. Na **Localização instalar** deixe a predefinição e clique em **seguinte** para começar a instalação.
5. Em **Curso instalação** monitorizar instalação e reiniciar o computador se lhe for solicitado. Depois de instalar o serviço, pode demorar cerca de 15 minutos para que o estado atualizar no portal.

#### <a name="install-mobility-service-on-a-windows-server-using-the-command-prompt"></a>Instalar o serviço de mobilidade num Windows server utilizando a linha de comandos

1. Copie o instalador para uma pasta local (apresentamos C:\Temp) no servidor que pretende proteger. Pode encontrar o instalador do servidor de configuração no **\home\svsystems\pushinstallsvc\repository [localização instalar]**. O pacote para sistemas operativos do Windows vai ter um nome semelhante ao Microsoft ASR_UA_9.3.0.0_Windows_GA_17thAug2016_release.exe
2. **Mudar o nome** deste ficheiro para MobilitySvcInstaller.exe
3. Execute o seguinte comando para extrair o programa de instalação MSI </br>

        C:\> cd C:\Temp
        C:\Temp> MobilitySvcInstaller.exe /q /xC:\Temp\Extracted
        C:\Temp> cd Extracted
        C:\Temp\Extracted> UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>

#####<a name="full-command-line-syntax"></a>Sintaxe de linha de comandos completa

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>

**Parâmetros**

- **/Role:** Obrigatório. Especifica se o serviço de mobilidade deve ser instalado. Agente de valores de entrada | MasterTarget
- **/InstallLocation:** Obrigatório. Especifica onde pretende instalar o serviço.
- **/PassphraseFilePath:** Obrigatório. A frase de acesso do servidor de configuração.
- **/LogFilePath:** Obrigatório. Localização onde devem ser criados os ficheiros de registo de instalação.



#### <a name="uninstall-mobility-service-manually"></a>Desinstalar manualmente o serviço de mobilidade

Serviço de mobilidade pode ser desinstalado utilizando o adicionar remover programa a partir do painel de controlo ou linha de comandos.

O comando para desinstalar o serviço de mobilidade utilizando linha de comandos é

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### <a name="install-mobility-service-on-a-linux-server-using-command-line"></a>Instalar o serviço de mobilidade num servidor Linux utilizando linha de comandos

1. Copie o arquivo de alcatrão adequado com base na tabela acima para a máquina Linux que pretende criar uma réplica.
2. Abra um programa de shell e extrair o arquivo de alcatrão compactados como um caminho local através da execução:`tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Crie um ficheiro de passphrase.txt no diretório local para o qual extraídas que o conteúdo do arquivo tar. Para fazer isto copiar a frase de acesso do C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase no servidor de configuração e guardá-lo no passphrase.txt executando *`echo <passphrase> >passphrase.txt`* na shell.
4. Instale o serviço de mobilidade, executando *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique o endereço IP interno do servidor de configuração e certifique-se de porta 443 está selecionada. Depois de instalar o serviço, pode demorar cerca de 15 minutos para que o estado atualizar no portal.

**Também pode instalar a partir da linha de comandos**:

1. Copie a frase de acesso a partir de ficheiros c:\Programas\Microsoft (x86) \InMage Systems\private\connection no servidor de configuração e guardá-lo como "passphrase.txt" no servidor de configuração. Em seguida, execute estes comandos. No nosso exemplo, o endereço IP do servidor de configuração é 104.40.75.37 e à porta HTTPS deve ser 443:

Para instalar num servidor de produção:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar o servidor de destino principal:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### <a name="enable-replication"></a>Activar a replicação

#### <a name="before-you-start"></a>Antes de começar

Se estiver a replicação VMware virtual máquinas tenha em atenção o seguinte procedimento:

- VMware VMs sejam detetados a cada 15 minutos e, poderá demorar 15 minutos ou mais demorada para que apareçam no portal do após deteção. Da mesma forma deteção pode demorar 15 minutos ou mais quando adiciona um novo vCenter servidor ou vSphere anfitrião.
- As alterações de ambiente na máquina virtual (tal como a instalação das ferramentas de VMware) também poderão demorar 15 minutos ou mais a ser atualizados no portal.
- Pode procurar a última vez descoberta VMware VMs no **Último contacto no** campo para o anfitrião de servidor/vSphere vCenter em pá os **Servidores de configuração** .
- Para adicionar máquinas para a replicação sem ter de esperar para a deteção agendada, realce o servidor de configuração (não clique) e clique no botão **Atualizar** .
- Quando ativar replicação, se o computador está preparado automaticamente o servidor de processo instala o serviço de mobilidade no mesmo.

#### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Quando ativa a replicação, por predefinição todos os discos num computador são replicados. Pode excluir discos da replicação. Por exemplo, não poderá querer criar uma réplica discos com dados temporários ou os dados que tem atualizados sempre uma máquina ou aplicação reinicia (por exemplo Pagefile ou tempdb do SQL Server). Se pretende excluir discos tenha em atenção que:

- Apenas pode excluir discos que já tem o serviço de mobilidade instalado. Terá de instalar [manualmente o serviço de mobilidade](#install-the-mobility-service-manually) porque o serviço de mobilidade só é instalado utilizando o mecanismo de push depois de replicação está ativada.
- Apenas os discos básicos podem excluídos da replicação. Não é possível excluir SO ou discos dinâmicos.
- Depois de replicação é activada não pode adicionar ou remover discos para a replicação. Se pretender adicionar ou excluir um disco terá de desativar a proteção contra para o computador e, em seguida, reactivá-lo.
- Se excluir um disco que tem necessário trabalhar com uma aplicação, após activação pós-falha ao Azure terá criá-lo manualmente no Azure para que a aplicação de replicadas pode ser executada. Em alternativa poderia integrar automatização Azure um plano de recuperação para criar o disco durante activação pós-falha do computador.
- Criar manualmente no Azure de discos vai ser Ocorreu uma falha novamente. Por exemplo se falhar superior três discos e criar duas diretamente no Azure, todos os cinco vai ser Ocorreu uma falha novamente. Não é possível excluir discos criados manualmente a partir de reposição de recurso.

**Agora activar a replicação da seguinte forma**:

1. Clique em **passo 2: criar uma réplica da aplicação** > **origem**. Depois de ter ativado replicação pela primeira vez irá clique em **+ criar uma réplica** no cofre para activar a replicação para máquinas adicionais.
2. No pá a **origem** > **origem** selecione o servidor de configuração.
3. Em **tipo de máquina** selecione **máquinas virtuais** ou **Máquinas física**.
4. No **vCenter/vSphere hipervisor** selecione servidor vCenter que gere o anfitrião do vSphere ou selecione o anfitrião. Esta definição não está relevante se estiver a replicação máquinas físicas.
5. Selecione o servidor do processo. Se não tiver criado quaisquer servers processo adicionais será o nome do servidor de configuração. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Em **destino** , selecione a subscrição de cofre e, no **modelo de implementação de mensagem com falha** selecione o modelo (gestão de classic ou de recurso) que pretende utilizar no Azure após activação pós-falha.
7. Selecione a conta de armazenamento Azure que irá utilizar para a replicação de dados. Tenha em atenção que:

    - Pode selecionar uma conta de armazenamento padrão ou premium. Se selecionar uma conta de premium terá de especificar uma conta de armazenamento adicional de padrão para registos de replicação em curso. Contas têm de estar na mesma região como o Cofre de serviços de recuperação.
    - Se pretender utilizar uma conta de armazenamento diferente daqueles tiver que pode [criar uma](#set-up-an-azure-storage-account). Para criar um armazenamento utilizando o modelo de processador de conta, clique em **Criar novo**. Se pretender criar uma conta de armazenamento utilizando o modelo clássico faça esse [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Selecione a rede Azure e sub-rede ao qual o Azure VMs vai ligar ao que está a fiadas o após activação pós-falha. A rede tem de ser na mesma região como o Cofre de serviços de recuperação. Selecione **configurar agora para máquinas selecionadas** para aplicar a definição de rede para todos os computadores que selecionar para protecção. Selecione **configurar mais tarde** para selecionar a rede Azure por máquina. Se não tiver uma rede terá de [criar uma](#set-up-an-azure-network). Para criar uma rede utilizando o modelo de processador, clique em **Criar novo**. Se pretender criar uma rede utilizando o modelo clássico faça esse [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede se aplicável. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. Em **máquinas virtuais do** > **Selecione máquinas virtuais** clique em e selecione cada máquina que pretende criar uma réplica. Apenas pode selecionar máquinas para os quais pode ser ativada replicação. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. Nas **Propriedades** > **configurar as propriedades**, selecione a conta que será utilizada pelo servidor de processo para automaticamente instala o serviço de mobilidade no computador. Por predefinição, todos os discos são replicados. Clique em **Todos os discos** e desmarque qualquer discos que não quiser criar uma réplica. Em seguida, clique em **OK**. Pode definir propriedades adicionais mais tarde.

    ![Activar a replicação](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. Nas **definições de replicação** > **Configurar definições de replicação** Certifique-se de que a política de replicação correto está selecionada. Pode modificar as definições de política de replicação no **Definições** > **políticas de replicação** > nome da política > **Editar definições**. Alterações serem aplicadas a uma política serão aplicadas a replicação e novas máquinas.

12. Ativar **múltiplos VM consistência** se pretende reunir máquinas para um grupo de replicação e especifique um nome para o grupo. Em seguida, clique em **OK**. Tenha em atenção que:

    - Máquinas na replicação agrupar replicação e partilharam pontos de recuperação de falha de sistema consistentes e aplicação consistente quando estes falham ao longo do.
    - Recomendamos que recolher VMs e servidores físicos em conjunto para que sejam simétricas seu das cargas de trabalho. Activar consistência de multi VM pode afetar o desempenho de carga de trabalho e só deve ser utilizada se máquinas estiver a executar a carga de trabalho mesmo e precisar de consistência.

    ![Activar a replicação](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Clique em **Ativar replicação**. Pode controlar o progresso da tarefa **Ativar proteção** nas **Definições**de > **tarefas** > **Tarefas recuperação de Site**. Após a tarefa de **Proteção finalizar** é executada a máquina está pronta para activação pós-falha.

> [AZURE.NOTE] Se o computador está preparado para instalação push que o componente de serviço de mobilidade será instalado quando proteção está ativada. Após o componente estiver instalado no computador que inicia uma tarefa de proteção e falha. Depois da falha, é necessário reiniciar manualmente cada máquina. Após o reinício a tarefa de proteção começa novamente e a replicação inicial ocorre.

### <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades VM

Recomendamos que verifique se as propriedades do computador de origem. Lembre-se de que o nome do Azure VM deve estar em conformidade com [os requisitos de máquina virtual Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Clique em **Definições** > **replicada itens** > e selecione o computador. O pá **Essentials** mostra informações sobre definições de máquinas e de estado.

2. Nas **Propriedades** pode ver informações de replicação e activação pós-falha para a VM.

    ![Activar a replicação](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. No **cluster e de rede** > **Calcular propriedades** pode especificar o tamanho do Azure VM nome e de destino. Modificar o nome para cumprir os requisitos de Azure se precisar de.
Também pode ver e adicione informações sobre a rede de destino, sub-rede e endereço de IP que será atribuído à VM Azure. Tenha em atenção o seguinte procedimento:

    - Pode definir o endereço de IP de destino. Se não fornecer um endereço de falhado ao longo do computador irá utilizar DHCP. Se definir um endereço de que não está disponível na activação pós-falha, não irá funcionar a activação pós-falha. O mesmo endereço IP de destino pode ser utilizado para teste activação pós-falha se o endereço estiver disponível na rede de activação pós-falha de teste.
    - O número de adaptadores de rede é ditado pelo tamanho que especificar para o computador virtual de destino, da seguinte forma:

        - Se o número de adaptadores de rede no computador de origem for menor ou igual ao número de placas permitida para o tamanho de máquina de destino, em seguida, o destino tem o mesmo número de placas como a origem de.
        - Se o número de adaptadores para a máquina de virtual origem excede o número permitido para o tamanho de destino, em seguida, o número máximo de tamanho de destino será utilizado.
        - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho de máquina de destino suporta quatro, o computador de destino terá duas placas. Se o computador de origem tem duas placas, mas o tamanho de suportados de destino suporta apenas um computador de destino ter apenas uma placa de.     
    - Se a VM tem vários adaptadores de rede irá todos ligar à mesma rede.

    ![Activar a replicação](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. Em **discos** pode ver o sistema operativo e os discos de dados na VM que vai ser replicada.


## <a name="step-7-test-the-deployment"></a>Passo 7: Testar a implementação

Para testar a implementação pode executar um teste activação pós-falha para uma máquina virtual única ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### <a name="prepare-for-failover"></a>Preparar para activação pós-falha

- Para executar um teste activação pós-falha que recomendamos que cria uma nova rede Azure que tem isoladas da sua rede de produção Azure (é comportamento predefinido quando cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre como executar activações pós-falha de teste.
- Para obter o melhor desempenho quando não é possível ao longo do Azure, instale o agente do Azure no computador protegido. Torna mais rápido de arranque e ajuda a resolução de problemas. Instale o agente [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para testar totalmente a sua implementação terá uma infraestrutura para o computador de replicadas funcionar como esperado. Se pretende testar Active Directory e DNS pode criar uma máquina virtual como controlador de domínio com o DNS e criar uma réplica isto Azure utilizando Azure a recuperação do Site. Leia mais em [Considerações de activação pós-falha de teste do Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Certifique-se de que o servidor de configuração está em execução. Caso contrário activação pós-falha irá falhar.
- Se tiver excluídos discos da replicação poderá ter de criar nesses discos manualmente no Azure após activação pós-falha para que a aplicação é executada conforme esperado.
- Se pretender executar uma não planeado activação pós-falha em vez de um teste activação pós-falha tenha em atenção o seguinte procedimento:

    - Se possível deve encerrar máquinas primárias antes de executar um activação não planeado pós-falha. Isto garante que não tem de origem e de réplica máquinas a executar ao mesmo tempo. Se estiver a replicação VMware VMs, em seguida, pode especificar que recuperação de sites farão um esforço de utilizações para encerrar as máquinas de origem. Consoante o estado do site principal poderá ou pode não funcionar. Se estiver a replicação servidores físicos recuperação de sites não oferecer esta opção.
    - Quando executa uma activação não planeado pós-falha deixa replicação de dados a partir do máquinas principais para que qualquer delta de dados não serem transferida depois de um activação não planeado pós-falha começa. Além disso se executar uma activação não planeado pós-falha num plano de recuperação será executado até estar concluído, mesmo se ocorre um erro.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar-se para ligar a Azure VMs depois de activação pós-falha

Se pretender ligar ao Azure VMs utilizar RDP após activação pós-falha, certifique-se de que efetue o seguinte procedimento:

**No computador antes de activação pós-falha no local**:

- Para o access através da internet ativar RDP, certifique-se de que as regras TCP e UDP são adicionadas para o **público**e certifique-se de que é permitido RDP a **Firewall do Windows** -> **permitidos aplicações e funcionalidades** para todos os perfis.
- Para o access através de uma ligação de site para o site ativar RDP no computador e, certifique-se de que é permitido RDP a **Firewall do Windows** -> **permitidos aplicações e funcionalidades** para redes **privadas** e de **domínio** .
- Instale o [Azure VM agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) no computador no local.
- [Instalar manualmente o serviço de mobilidade](#install-the-mobility-service-manually) no máquinas em vez de utilizar o servidor de processo para transmitir automaticamente o serviço. Isto acontece porque a instalação push só acontece depois do computador está ativado para a replicação.
- Certifique-se de que a política de SAN o sistema operativo está definida para OnlineAll. [Saiba mais]( https://support.microsoft.com/kb/3031135)
- Desative o serviço IPSec antes de executar o activação pós-falha.

**No the Azure VM após activação pós-falha**:

- Adicionar um ponto final de público para o protocolo RDP (porta 3389) e especifique as credenciais de início de sessão.
- Certifique-se de que não tem qualquer políticas de domínio que o impedem de ligar a uma máquina de virtual utilizando um endereço público.
- Tente ligar. Se não conseguir ligar Certifique-se de que a VM está em execução. Para obter mais sugestões de resolução de problemas Leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Se pretender aceder a um VM Azure Linux a ser executado depois activação pós-falha a utilizar um seguro Shell cliente (ssh), faça o seguinte:

**No computador antes de activação pós-falha no local**:

- Certifique-se de que o serviço de Shell seguro na VM Azure está definido para iniciar automaticamente no arranque do sistema.
- Certifique-se de que as regras de firewall permitem uma ligação de SSH à mesma.

**No the Azure VM após activação pós-falha**:

- As regras de grupo de segurança de rede no Falha ao longo do VM e a sub-rede Azure a que está ligado necessário permitir ligações de entrada para a porta SSH.
- Deverá ser criado um ponto final de público para permitir ligações de entrada na porta SSH (porta TCP 22 por predefinição).
- Se a VM é acedida através de uma ligação VPN (encaminhar Express ou site para site VPN) o cliente pode ser utilizado para ligar diretamente para a VM em SSH.

**No the Azure Windows/Linux VM após activação pós-falha**:

Se tiver um grupo de segurança de rede associados a Máquina Virtual ou sub-rede ao qual pertence a máquina para, certifique-se de que o grupo de segurança de rede tem uma regra de saída para permitir que HTTP/HTTPS. Também, certifique-se de que o DNS da rede para máquina virtual à qual é introdução não conseguiu superior está configurado corretamente. De que outra a activação pós-falha poderia limite de tempo com o erro-'Task PreFailoverWorkflow WaitForScriptExecutionTask excedeu'. Para compreender isto em detalhe, consulte a secção no recuperação na [monitorização e guia de resolução de problemas](site-recovery-monitoring-and-troubleshooting.md#recovery).

## <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

1. Falha ao longo de um único computador, nas **Definições** > **Itens de replicadas**, clique na VM > ícone **+ teste activação pós-falha** .

    ![Teste activação pós-falha](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Falha ao longo de um plano de recuperação, nas **Definições** > de**Recuperação de planos**, com o botão direito do plano > **Teste activação pós-falha**. Para criar uma recuperação planear [siga estas instruções](site-recovery-create-recovery-plans.md).

3. No **Teste activação pós-falha** seleccione a rede Azure ao qual irá estar ligado Azure VMs depois de activação pós-falha ocorre.
4. Clique em **OK** para começar a activação pós-falha. Pode controlar o progresso ao clicar em VM para abrir as respetivas propriedades ou a tarefa de **Teste activação pós-falha** em nome do cofre > **definições de** > **tarefas** > **tarefas recuperação de sites**.
5. Quando a activação pós-falha atinge o estado de **testes concluída** , faça o seguinte:

    1. Ver a máquina de virtual réplica no portal do Azure. Certifique-se de que a máquina virtual for iniciado com êxito.
    2. Se estiver a definir até máquinas virtuais de acesso da sua rede no local pode iniciar uma ligação de ambiente de trabalho remoto para a máquina virtual.
    3. Clique em **Testar concluída** para conclui-lo.

        ![Teste activação pós-falha](./media/site-recovery-vmware-to-azure/test-failover6.png)


    4. Clique em **notas** para gravar e guardar quaisquer observações associadas a activação de teste pós-falha.
    5. Clique em **a activação de teste pós-falha está concluída** para limpar automaticamente o ambiente de teste. Após isto é feito o teste activação pós-falha irá mostrar um Estado de **conclusão** .
    6.  Nesta fase são eliminados qualquer elementos ou VMs criados automaticamente pelo Site recuperação durante a mudança de teste. Quaisquer elementos adicionais que criou para teste activação pós-falha não são eliminados.

    > [AZURE.NOTE] Se um teste activação pós-falha persistir mais de duas semanas está concluída por força.


6. Após completa o activação pós-falha também deverá conseguir ver a réplica Azure máquina aparecem no portal do Azure > **máquinas virtuais**. Deve certificar-se de que a VM é o tamanho adequado, que tem ligado à rede adequada, e que está a funcionar.
7. Se lhe [preparado para ligações após activação pós-falha](#prepare-to-connect-to-azure-vms-after-failover) deverá conseguir ligar para a VM Azure.

## <a name="monitor-your-deployment"></a>Monitorizar a sua implementação

Eis como pode monitorizar as definições de configuração, estado e estado de funcionamento para a sua implementação de recuperação de sites:

1. Clique no nome do cofre para aceder ao dashboard de **Essentials** . Neste dashboard pode tarefas, o estado de replicação, planos de recuperação, estado de funcionamento do servidor e eventos de recuperação de sites.  Pode personalizar Essentials para mostrar os mosaicos e os esquemas que são mais úteis para si, incluindo o estado dos outros cofres recuperação de sites e de cópia de segurança.<br>
![Tópicos essenciais](./media/site-recovery-vmware-to-azure/essentials.png)

2. No mosaico do **Estado de funcionamento** pode monitorizar os servidores de site (VMM ou a configuração de servidores) que ocorrerem o problema e os eventos criados por recuperação de sites no últimas 24 horas.
3. Pode gerir e monitorize a replicação nos **Itens de replicadas**, **Recuperação de planos**, e mosaicos de **Tarefas recuperação de Site** . Pode desagregar tarefas nas **Definições** -> **tarefas** -> **Tarefas recuperação de Site**.


## <a name="deploy-additional-process-servers"></a>Implementar os servidores de processo adicionais

Se tiver para dimensionar fora da sua implementação para além das 200 máquinas de origem ou de uma taxa de vasilha diária total de mais de 2 TB, terá de servidores de processo adicionais para processar o volume de tráfego.

Verifique as [recomendações de tamanho para os servidores de processo](#size-recommendations-for-the-process-server) e, em seguida, siga estas instruções para configurar o servidor do processo. Depois de configurar o servidor irá migrar máquinas de origem para utilizá-la.

### <a name="install-an-additional-process-server"></a>Instalar um servidor de processo adicionais

1. Nas **Definições** > o servidor de configuração de clique em**servidores de recuperação de sites** > **servidor do processo**.

    ![Adicionar o servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. No **Tipo de servidor** clique em **processo server (no local)**.

    ![Adicionar o servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Transfira o ficheiro de configuração de Unified recuperação de Site e execute-o para instalar o servidor de processo e registá-lo no cofre.
4. Na **antes de começar** selecione **Adicionar os servidores de processo adicionais para dimensionar saída implementação**.
5. Concluir o Assistente da mesma forma que efetuou quando [Configurar](#step-2-set-up-the-source-environment) o servidor de configuração.

    ![Adicionar o servidor de processo](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. Na **Configuração de detalhes do servidor** especifique o endereço IP do servidor de configuração e a frase de acesso. Para obter a frase de acesso executar ** <SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe – n** no servidor de configuração.

    ![Adicionar o servidor de processo](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para utilizar o novo servidor de processo

1. Nas **Definições** > **os servidores de recuperação de sites** clique o servidor de configuração e, em seguida, expanda **os servidores de processo**.

    ![Actualizar o servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. O servidor de processo atualmente em utilização com o botão direito e clique em **Mudar**.

    ![Actualizar o servidor de processo](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. Em **Selecione servidor do processo de destino**, selecione o novo servidor de processo que pretende utilizar e, em seguida, selecione as máquinas virtuais que o servidor de processo novo processará. Clique no ícone de informações para obter informações sobre o servidor. Para ajudá-lo a tomar decisões de carregar, é apresentado o espaço de média tem necessárias para criar uma réplica cada máquina virtual selecionada para o novo servidor do processo. Clique na marca de verificação para iniciar a replicação para o novo servidor do processo.

## <a name="vmware-account-permissions"></a>Permissões da conta VMware

O servidor de processo possam detetar automaticamente VMs num servidor vCenter. Para executar a deteção automática terá para [definir uma função (Azure_Site_Recovery)](#prepare-an-account-for-automatic-discovery) permitir a recuperação de Site aceder ao servidor VMware. Tenha em atenção que, se apenas precisar de migrar VMware máquinas para Azure e não precisa de reposição de recurso a partir do Azure, pode definir uma função de só de leitura é suficiente. As permissões da função necessários estão resumidas na tabela seguinte.

**Função** | **Detalhes** | **Permissões**
--- | --- | ---
Função Azure_Site_Recovery | Deteção de VMware VM |Atribua estes privilégios para o servidor do Centro de v:<br/><br/>Arquivo de dados -> alocar espaço, procurar arquivo de dados, baixo ficheiro nível operações., ficheiro de remover, ficheiros de máquina virtual de actualização<br/><br/>Rede -> atribuir de rede<br/><br/>Recurso -> máquina de virtual atribuir ao agrupamento de recursos, migrar desligado máquina virtual, migrar powered numa máquina virtual<br/><br/>Tarefas -> Criar tarefa, a tarefa de actualização<br/><br/>Máquina virtual -> configuração<br/><br/>Máquina virtual -> interagir -> pergunta de resposta, dispositivo ligação., configurar CD multimédia, configurar disquetes multimédia, Power desativar Power no, instale o VMware ferramentas<br/><br/>Máquina virtual -> inventário -> criar, Register, Unregister<br/><br/>Máquina virtual -> aprovisionar -> transferência de máquina virtual permitir, carregar ficheiros de máquina virtual permitir<br/><br/>Máquina virtual -> instantâneos -> Remover instantâneos
função de utilizador vCenter | VMware VM deteção/activação pós-falha sem encerramento da origem VM | Atribua estes privilégios para o servidor do Centro de v:<br/><br/>Objeto de centro de dados –> propagar para objecto subordinado, função = só de leitura <br/><br/>O utilizador é atribuído ao nível do Centro de dados e, pelo que tem acesso a todos os objectos no Centro de dados.  Se quiser restringir o acesso, atribua a função **sem acesso** com o objeto **propagar para a criança** os objectos subordinados (vSphere anfitriões, datastores, VMs e redes).
função de utilizador vCenter | Activação e reposição de recurso | Atribua estes privilégios para o servidor do Centro de v:<br/><br/>Centro de dados objecto – propagar para objecto subordinado, função = Azure_Site_Recovery<br/><br/>O utilizador é atribuído ao nível do Centro de dados e, pelo que tem acesso a todos os objectos no Centro de dados.  Se quiser restringir o acesso, atribua uma função **sem acesso** com a **propagar para objecto subordinado** ao objecto subordinado (vSphere anfitriões, datastores, VMs e redes).  
## <a name="next-steps"></a>Próximos passos

- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de activação pós-falha.
- [Saber mais sobre a reposição de recurso](site-recovery-failback-azure-to-vmware.md) para trazer o seu Falha ao longo do máquinas em execução no Azure novamente para o seu ambiente no local.

## <a name="third-party-software-notices-and-information"></a>Avisos de terceiros software e informações

Não faça traduzir ou Localize

O software e firmware em execução no Microsoft produto ou serviço baseado ou incorpora material de projetos listados abaixo (constituem, "terceiros código").  A Microsoft está o autor não original do código de terceiros.  O aviso de copyright original e a licença, sob o qual a Microsoft recebido essas código de terceiros, são definidas quarta abaixo.

As informações na secção A são relacionadas com componentes de código de terceiros de projetos listados abaixo. Esses licenças e as informações são fornecidos apenas para fins informativos.  Este código de terceiros está a ser relicensed para si pela Microsoft em termos de licenciamento de software da Microsoft para o Microsoft produto ou serviço.  

As informações na secção B são relacionadas com componentes de código de terceiros que estão a ser disponibilizados para si pela Microsoft em termos de licenciamento originais.

O ficheiro concluído poderá encontrar no do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). A Microsoft reserva-se por sugestão, todos os direitos não expressamente concedidos mencionados, estoppel ou de outra forma.
