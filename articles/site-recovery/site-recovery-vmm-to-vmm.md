<properties
    pageTitle="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para um site VMM secundário através do portal Azure | Microsoft Azure"
    description="Descreve como implementar o Azure recuperação de Site para orquestrar replicação, activação e recuperação do Hyper-V VMs no nuvens VMM para um site VMM secundário através do portal Azure."
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
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-the-azure-portal"></a>Criar uma réplica Hyper-V máquinas virtuais nuvens VMM a um site VMM secundário utilizando o portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-vmm.md)
- [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestor de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Bem-vindo ao Azure Site recuperação! Utilize este artigo se pretender criar uma réplica no local Hyper-V virtual máquinas gerido no nuvens sistema Center Máquina Virtual Gestor (VMM) para um site secundário. Este artigo descreve como configurar a replicação utilizando a recuperação de Site do Azure no portal do Azure.

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gestor de recursos do Azure e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação.


Azure recuperação de Site no portal do Azure fornece várias novas funcionalidades:

- O Azure portal, a cópia de segurança do Azure e serviços de recuperação de Site do Azure são combinados num único cofre serviços de recuperação, para que possa configurar e gerir continuidade do negócio e recuperação de falhas (BCDR) a partir de uma única localização. Um dashboard unificado permite-lhe monitorizar e gerir operações nos seus sites no local e na nuvem pública Azure.
- Os utilizadores com subscrições do Azure aprovisionados com o programa de fornecedor de solução da nuvem (CSP) podem agora gerir operações de recuperação de Site no portal do Azure.


Depois de ler este artigo, registe quaisquer comentários na parte inferior dos mesmos Disqus. Faça técnicas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Descrição geral

As organizações devem uma estratégia BCDR que determina como dados, das cargas de trabalho e apps manter-se em execução e disponível durante o tempo de inatividade planeado e não em recuperar a normal condições de trabalho mais cedo possível. Sua estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis e garantir que das cargas de trabalho permanecem continuamente disponíveis quando ocorre falhas.

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR por orquestrar replicação de servidores físicos no local e máquinas virtuais na nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem falhas na sua localização principal, falhar ao longo para a localização secundária para manter as aplicações e das cargas de trabalho disponíveis. Falhar voltar a sua localização principal quando devolve a operações normais. Saiba mais em [o que é o Azure recuperação de Site?](site-recovery-overview.md)

Este artigo fornece todas as informações que necessita para criar uma réplica no local Hyper-V VMs no nuvens VMM para um site VMM secundário. Inclui uma descrição geral arquitetura, informações e implementação os passos para configurar servidores no local, definições de replicação e planeamento da capacidade de planeamento. Depois de ter configurado a infraestrutura de pode ativar a replicação em computadores que pretende proteger e verificar que esse activação pós-falha funciona.

## <a name="business-advantages"></a>Vantagens de empresas

- Recuperação de site fornece protecção para cargas de trabalho de negócio e aplicações em execução no Hyper-V VMs através da replicação-los para um servidor de Hyper-V secundário.
- O portal de serviços de recuperação fornece uma única localização para configurar, gerir e monitorizar replicação, activação pós-falha e recuperação.
- Pode executar activações pós-falha facilmente executar a partir da sua infraestrutura principal no local para o site secundário e reposição de recurso (restaurar) a partir do site secundário para a página principal.
- Pode configurar os planos de recuperação com vários computadores para que das cargas de trabalho em camadas aplicação falharem ao longo em conjunto.

## <a name="scenario-architecture"></a>Arquitetura de cenário

Estes são os componentes de cenário:

- **Site principal**: no site principal, existem um ou mais Hyper-V anfitrião os servidores a executar o VMs de origem que pretende criar uma réplica. Nestes servidores anfitrião principal encontram-se numa nuvem VMM privada.
- **Site secundário**: no site do secundário, existem um ou mais Hyper-V anfitrião servidores a executar o destino VMs à qual replica os VMs principais. Nestes servidores anfitrião encontram-se numa nuvem VMM privada. Nuvem pode ser no servidor principal (se tiver apenas um único servidor VMM) ou num servidor VMM secundário.
- **Fornecedor**: durante a implementação de recuperação de sites, instale o fornecedor de recuperação do Azure sites em servidores VMM e registar essas servidores num cofre serviços de recuperação. O fornecedor executado no servidor do VMM comunica com recuperação do Site através de 443 HTTPS para criar uma réplica orchestration. Replicação de dados ocorre entre servidores de anfitrião Hyper-V principais e secundários. Dados replicados permanecem dentro de sites no local e redes e não são enviados para o Azure. Leia mais sobre [Privacidade](#privacy-information-for-site-recovery).

![E2E topologia](./media/site-recovery-vmm-to-vmm/architecture.png)

### <a name="data-privacy-overview"></a>Descrição geral de privacidade de dados

A seguinte tabela resume como os dados são armazenados neste cenário:
****
Ação | **Detalhes** | **Dados recolhidos** | **Utilizar** | **Obrigatório**
--- | --- | --- | --- | ---
**Registo** | Registe-se de um servidor VMM num cofre serviços de recuperação. Se quiser regressar posteriormente ao anular o registo de um servidor, pode fazê-lo ao eliminar as informações do servidor a partir do portal Azure. | Depois de um servidor VMM está registado recuperação de sites recolhe, processos e transfere metadados sobre o servidor VMM e os nomes das nuvens VMM detectados pelo recuperação de sites. | Os dados são utilizados para identificar e comunicar com o servidor VMM adequado e configurar definições para nuvens VMM adequados. | Esta funcionalidade é necessária. Se não pretender enviar esta informação para recuperação de sites não deva utilizar o serviço de recuperação de sites.
**Activar a replicação** | O fornecedor de recuperação do Azure Site está instalado no servidor VMM e está a ligação para a comunicação com o serviço de recuperação de sites. O fornecedor é uma biblioteca de ligação dinâmica (DLL) alojada no processo de VMM. Após ter instalado o fornecedor, a funcionalidade de "Recuperação do Centro de dados" obtém ativada na consola do administrador de VMM. Podem ativar esta funcionalidade activar a protecção para uma VM a VMs novos e existentes. | Com esta propriedade definida, o fornecedor envia o nome e o ID da VM a recuperação de sites.  Replicação está ativada por Windows Server 2012 ou Windows Server 2012 R2 Hyper-V réplica. Os dados de máquina virtual obtém replicados a partir de um anfitrião do Hyper-V para outra (normalmente localizado num centro de dados diferentes "recuperação"). | Recuperação de site utiliza os metadados para preencher as informações de VM no portal do Azure. | Esta funcionalidade é uma parte essencial do serviço e não pode ser desativada. Se não quiser enviar esta informação, não ative proteção de recuperação de sites para VMs. Tenha em atenção que todos os dados enviados pelo fornecedor para recuperação de sites é enviado por HTTPS.
**Plano de recuperação** | Planos de recuperação ajudam a criar um plano do orchestration para o Centro de dados de recuperação. Pode definir a ordem pela qual deve ser iniciado no site da recuperação VMs ou grupo de máquinas virtuais. Também pode especificar quaisquer scripts automatizados qualquer manual ou executar ação a tomar, no momento da recuperação para cada VM. Normalmente, é acionada activação pós-falha ao nível de plano de recuperação de recuperação coordenado. | Recuperação de sites recolhe, processa e transmite metadados para o plano de recuperação, incluindo os metadados de máquina virtual e metadados da quaisquer scripts de automatização e notas de ação manual. | Os metadados são utilizado para construir o plano de recuperação no portal do Azure. | Esta funcionalidade é uma parte essencial do serviço e não pode ser desativada. Se não quiser enviar esta informação para recuperação de sites, não crie planos de recuperação.
**Mapeamento de rede** | Mapas informações de rede no Centro de dados principal do Centro de dados de recuperação. Quando são recuperados VMs no site de recuperação, mapeamento de rede ajuda-o a estabelecer a conectividade da rede. | Recuperação de sites recolhe, processa e transmite os metadados das redes lógicas para cada site (primário e Centro de dados). | Os metadados são utilizado para preencher as definições de rede para que possa mapear as informações da rede. | Esta funcionalidade é uma parte essencial do serviço e não pode ser desativada. Se não quiser enviar esta informação para recuperação de sites, não utilize o mapeamento de rede.
**Activação pós-falha (planeado/não planeado/teste)** | Falha activação pós-falha ao longo do VMs a partir do Centro de dados gerido pelo VMM uma para outra. A ação de activação pós-falha é acionada manualmente no portal do Azure. | O fornecedor no servidor VMM é notificado do evento activação pós-falha pelo recuperação de sites e executa uma ação de activação pós-falha no anfitrião do Hyper-V através de VMM interfaces. É real activação pós-falha de uma VM a partir de um anfitrião do Hyper-V para outra e processada ao Windows Server 2012 ou Windows Server 2012 R2 Hyper-V réplica. Depois de activação pós-falha estiver concluída, o fornecedor no servidor VMM no Centro de dados de recuperação envia informações de sucesso a recuperação de sites. | Recuperação de site utiliza as informações enviadas para preencher o estado das informações de ação activação pós-falha no portal do Azure. | Esta funcionalidade é uma parte essencial do serviço e não pode ser desativada. Se não quiser enviar esta informação para recuperação de sites, não utilize activação pós-falha.


## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Eis o que precisa no Azure implementar este cenário:

**Pré-requisitos** | **Detalhes**
--- | ---
**Azure**| É necessária uma conta do [Microsoft Azure](http://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.


## <a name="on-premises-prerequisites"></a>Pré-requisitos de no local

Eis o que precisa nos sites principais e secundários no local implementar este cenário:

**Pré-requisitos** | **Detalhes**
--- | ---
**VMM** | Recomendamos que implementar um servidor VMM no site principal e um servidor VMM no site secundário.<br/><br/> Também pode [criar uma réplica entre nuvens num único servidor VMM](site-recovery-single-vmm.md). Para fazer isto tem, pelo menos, duas nuvens configurados no servidor de VMM.<br/><br/> Os servidores de VMM devem estar em execução, pelo menos, sistema Centro 2012 SP1 com as atualizações mais recentes.<br/><br/> Cada servidor VMM tem de ter um ou mais nuvens configurados e todas as nuvens têm de ter o perfil da capacidade de Hyper-V definir. <br/><br/>Nuvens tem de conter um ou mais grupos de anfitrião VMM.<br/><br/>Saiba mais sobre a configuração de nuvens VMM na [configurar o ferro nuvem VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric), e [Tutorial: criar nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).<br/><br/> Os servidores de VMM tem acesso à internet.
**Hyper-V** | Os servidores de Hyper-V devem estar em execução, pelo menos, Windows Server 2012, com a função Hyper-V e tem instalado as atualizações mais recentes.<br/><br/> Um servidor de Hyper-V deve conter um ou mais VMs.<br/><br/>  Os servidores de anfitrião Hyper-V devem estar localizados em grupos de anfitrião em nuvens VMM principais e secundários.<br/><br/> Se estiver a executar o Hyper-V num cluster no Windows Server 2012 R2 deve instalar [Atualizar 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver a executar o Hyper-V num cluster no Windows Server 2012, tenha em atenção que corretor cluster não é criada automaticamente se tiver um cluster de baseados em endereços IP estático. Tem de configurar o corretor de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Fornecedor** | Durante a implementação de recuperação de sites, instale o fornecedor de recuperação de Site do Azure nos servidores VMM. O fornecedor de comunica com a recuperação do Site através da 443 HTTPS para orquestrar replicação. Ocorre replicação de dados entre os servidores de Hyper-V principais e secundários através da rede local ou de uma ligação VPN.<br/><br/> O fornecedor executado no servidor do VMM precisa de aceder a estes URLs: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.Windows.NET; *. backup.windowsazure.com; *. blob.Core.Windows.NET; *. store.core.windows.net.<br/><br/> Além disso, permitir a comunicação de firewall dos servidores VMM para os [intervalos IP do Centro de dados Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e permitir que o protocolo HTTPS (443).

## <a name="prepare-for-deployment"></a>Preparar para implementação

Para se preparar para implementação que terá de:

1. [Preparar o servidor VMM](#prepare-the-vmm-server) para implementação de recuperação de sites.
2. [Preparar para mapeamento de rede](#prepare-for-network-mapping). Configure o redes, de modo a que pode configurar mapeamento de rede.

### <a name="prepare-the-vmm-server"></a>Preparar o servidor VMM

Certifique-se de que o servidor VMM está em conformidade com a [Pré-requisitos](#on-premises-prerequisites) e pode aceder aos URLs listados.


### <a name="prepare-for-network-mapping"></a>Preparar para mapeamento de rede

Mapas de mapeamento de rede entre redes VMM VM nos servidores VMM principais e secundários para:

- Coloque optimizada réplica VMs em secundários anfitriões de Hyper-V após activação pós-falha.
- Ligar réplica VMs a redes VM adequados.
- Se não configurar réplica de mapeamento de rede que VMS não estar ligados a quaisquer rede após activação pós-falha.
- Se pretender configurar a rede durante a recuperação de Site de mapeamento implementação aqui é o que precisa:

    - Certifique-se de que VMs do servidor de origem Hyper-V anfitrião estão ligadas a uma rede VMM VM. Essa rede deve ser ligada a uma rede lógica que está associada a nuvem.
    - Certifique-se de que a nuvem secundária que utilizar para recuperação tem uma rede VM correspondente configurado. Que rede VM deve ser ligado a uma rede lógica que está associada a nuvem secundária.

- [Saiba mais](site-recovery-network-mapping.md) sobre como funciona o mapeamento de rede.

## <a name="prepare-for-deployment-with-a-single-vmm-server"></a>Preparar para implementação com um único servidor VMM

Se tiver apenas um único servidor VMM qual pode criar uma réplica VMs em anfitriões Hyper-V na nuvem VMM para [Azure](site-recovery-vmm-to-azure.md) ou para uma nuvem VMM secundária. Recomendamos que a primeira opção porque replicação entre nuvens não está totalmente integrada, mas se precisar de fazer isto aqui é o que precisa de fazer:

1. **Configurar o VMM numa VM Hyper-V**. Quando Vamos fazer sugerimos que colocar instância do SQL Server utilizada pelo VMM sobre a mesma VM. Isto poupa tempo como VM apenas uma tem de ser criado. Se pretender utilizar ocorre remota instância do SQL Server e uma falha, é necessário recuperar essa instância antes de pode recuperar VMM.
2. **Certifique-se o servidor VMM tem, pelo menos, duas nuvens configurados**. Uma nuvem irá conter as VMs que pretende criar uma réplica e outros nuvem irá servir como a localização secundária. Na nuvem que contém os VMs que pretende proteger deve cumprir os [Pré-requisitos](#on-premises-prerequisites).
3. Configure o Site recuperação conforme descrito neste artigo. Criar e registar o servidor VMM no cofre, definir uma política de replicação e activar a replicação. Deverá especificar que a replicação inicial ocorre o mais através da rede.
4. Ao configurar o mapeamento de rede mapear a rede VM para o cloud principal para a rede VM na nuvem secundária.
5. Na consola do Gestor de Hyper-V, ativar Hyper-V réplica no anfitrião do Hyper-V que contém a VM VMM e activar a replicação na VM. Certifique-se de que não adiciona máquina virtual VMM para nuvens que estejam protegidos por recuperação de Site, para se certificar de que as definições de réplica Hyper-V não são substituídas por recuperação de sites.
6. Se criar planos de recuperação para activação pós-falha utilize o mesmo servidor VMM de origem e destino.
7. Falha ao longo e recuperar da seguinte forma:

    - Manualmente falhe sobre a VM VMM para o site secundário utilizando o Gestor de Hyper-V com um activação planeada pós-falha.
    - Falhe sobre os VMs.
    - Depois da página principal VMM VM ter sido recuperada, registo no portal do Azure -> Serviços de recuperação do Cofre de palavras e execute uma não planeado activação pós-falha dos VMs a partir do site secundário para o site principal.
    - Depois da activação não planeado pós-falha estiver concluída, todos os recursos podem ser acedidos novamente a partir do site principal.


### <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gestão** > **Serviços de recuperação**. Em alternativa, pode clicar em **Procurar** > cofres de**Serviços de recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. Em **nome** , especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. [Criar um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione uma existente e, especifique uma região do Azure. Máquinas vai ser replicadas para esta região. Para verificar regiões suportadas Consulte geográficos disponibilidade no [Recuperação preços detalhes do Site Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se pretender aceder rapidamente o cofre a partir do Dashboard, clique em **Afixar ao dashboard** > **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

O novo cofre irão aparecer no **Dashboard** > **todos os recursos**e, no principal pá **cofres de serviços de recuperação** .




## <a name="getting-started"></a>Introdução

Recuperação de site fornece uma introdução experiência que o ajuda a implementar o mais rapidamente possível. Introdução ao verifica os pré-requisitos e orienta-o através de recuperação de Site implementação os passos na ordem correta.

Começar a seleccionar o tipo de máquinas que pretende criar uma réplica e, em que pretende criar uma réplica para. Configurar o servidores no local, criar políticas de replicação e planeamento da capacidade de executar. Depois de que configurou a sua infraestrutura de ativar a replicação para VMs. Pode executar activações pós-falha para máquinas específicas ou criar planos de recuperação falha através de vários computadores.

Começar a introdução ao selecionar como pretende implementar recuperação de sites. O fluxo de introdução ao altera ligeiramente consoante seus requisitos de replicação.

## <a name="step-1-choose-your-protection-goal"></a>Passo 1: Escolher o seu objetivo de proteção

Selecione o que pretende criar uma réplica e onde pretende que sejam replicadas para.

1. Na pá **Serviços de recuperação cofres** selecione cofre e clique em **Definições**.
2. Nas **Definições** > **Introdução** clique em **Site recuperação** > **passo 1: preparar infraestrutura** > **objectivo de proteção**.
3. Em **objetivo de proteção** selecione **ao site de recuperação**e, selecione **Sim, com Hyper-V**.
4. Selecione **Sim** para indicar que está a utilizar VMM para gerir os anfitriões Hyper-V e selecione **Sim** , se tiver um servidor VMM secundário. Se está a implementar replicação entre nuvens num único servidor VMM clique em **não**. Em seguida, clique em **OK**.

    ![Selecione os seus objetivos](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## <a name="step-2-set-up-the-source-environment"></a>Passo 2: Configurar o ambiente de origem

Instalar o fornecedor de recuperação do Azure sites em servidores VMM e registar servidores no cofre.

1. Clique em **passo 2: preparar infraestrutura** > **origem**.

    ![Configurar o origem](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. Em **preparar origem** clique em **+ VMM** para adicionar um servidor VMM.

    ![Configurar o origem](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. Na verificação pá **Adicionar servidor** que **o servidor de sistema Center VMM** aparece no **tipo de servidor** e que o servidor VMM cumpre os [requisitos de URL e pré-requisitos](#on-premises-prerequisites).
4. Transferir o ficheiro de instalação do fornecedor de recuperação do Azure sites.
5. Transfira a chave de registo. Precisa de isto quando executa a configuração. A chave é válida para 5 dias após lhe gerá-lo.

    ![Configurar o origem](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Instale o fornecedor de recuperação de Site do Azure no servidor VMM.

> [AZURE.NOTE] Não tem de instalar nada explicitamente em servidores de anfitriões Hyper-V.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurar o fornecedor de recuperação de Site do Azure

1. Execute o fornecedor de ficheiro de configuração no servidor cada VMM. Se VMM é implementado num cluster e estiver a instalar o fornecedor para a primeira vez instalá-la um nó activo e concluir a instalação para registar o servidor VMM no cofre. Em seguida, instale o fornecedor nos outros nós. Nós de cluster devem executar a mesma versão do fornecedor.
2. A configuração é executado algumas verificações prerequirement e os pedidos de permissão para parar o serviço VMM. O serviço de VMM vai ser reiniciado automaticamente quando termina de configuração. Se estiver a instalar num será solicitado para parar a função de Cluster de computação VMM.

2.  No **Microsoft Update** , pode optar ativamente por participar no existência de actualizações para que o fornecedor actualizações estão instaladas em conformidade com a política do Microsoft Update.
3. Numa **instalação** aceitar ou modificar a localização de instalação do fornecedor de predefinida e clique em **instalar**.

    ![Localização de instalação](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Após a instalação estiver concluída clique em **Registe-se** para registar o servidor no cofre.

    ![Localização de instalação](./media/site-recovery-vmm-to-vmm/provider-register.png)

9. Em **nome do cofre**, verifique se o nome do cofre na qual será registado no servidor. Clique em *seguinte*.

    ![Registo do servidor](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. Na **Ligação à Internet** especifique a forma como o fornecedor executado no servidor do VMM se liga à Internet. Selecione **ligar-se com as definições de proxy existente** para utilizar as predefinições de ligação à Internet configuradas no servidor.

    ![Definições da Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Se pretender utilizar um proxy personalizado que deve configurá-lo antes de instalar o fornecedor. Quando configurar as definições de proxy personalizado será executado um teste para verificar a ligação de proxy.
    - Se utilizar um proxy personalizado ou o proxy predefinido requer autenticação que necessitar de introduzir os detalhes de proxy, incluindo o endereço proxy e a porta.
    - Seguir urls deve ser acessível a partir do servidor de VMM e os anfitriões Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Permitir que os endereços IP descritos em [Intervalos de IP do Centro de dados de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e HTTPS protocolo (443). Teria intervalos IP lista branca da região Azure que planeia utilizar e que EUA Ocidental.
    - Se utilizar um proxy personalizado uma conta de VMM RunAs (DRAProxyAccount) será criada automaticamente utilizando as credenciais do proxy especificado. Configure o servidor proxy para que esta conta pode autenticar com êxito. As definições da conta VMM RunAs podem ser modificadas na consola do VMM. Para fazer isto, abra a área de trabalho de **Definições** , expanda **segurança**, clique em **Executar como contas**e, em seguida, modificar a palavra-passe para DRAProxyAccount. Terá de reiniciar o serviço VMM para que esta definição entra em vigor.


8. Na **Chave de registo**, selecione a chave que transferiu a partir do Azure recuperação de sites e copiado para o servidor VMM.


10.  A definição de encriptação é utilizada apenas quando estiver a replicação Hyper-V VMs no nuvens VMM para Azure. Se estiver a replicação para um site secundário não é utilizada.

11.  Em **nome de servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Numa configuração do cluster especifique o nome da função cluster VMM.
12.  Em **sincronizar na nuvem metadados** selecionar se pretende sincronizar metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só tem de ocorrer uma vez por cada servidor. Se não pretender sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades na nuvem na consola do VMM.

13.  Clique em **seguinte** para concluir o processo. Após o registo, metadados do servidor VMM são obtido por Azure recuperação de sites. O servidor é apresentado no separador **Servidores VMM** na página **Servers** no cofre.

    ![Servidor](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

11. Depois do servidor está disponível na consola de recuperação de Site, na **origem** > **preparar origem** selecione servidor VMM e, selecione na nuvem na qual o anfitrião do Hyper-V está localizado. Em seguida, clique em **OK**.

#### <a name="command-line-installation"></a>Instalação de linha de comandos

O fornecedor de recuperação do Azure Site podem ser instalado da linha de comandos. Este método pode ser utilizado para instalar o fornecedor no servidor principais para o Windows Server 2012 R2.

1. Transferir a chave de ficheiro e registo de instalação do fornecedor para uma pasta. Por exemplo C:\ASR.
2. Pare o serviço de Gestor de Máquina Virtual do Centro de sistema.
3. A partir de uma linha de comandos elevada execute estes comandos para extrair o instalador do fornecedor:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Execute este comando para instalar o fornecedor de:

        C:\ASR> setupdr.exe /i

5. Em seguida, execute estes comandos para registar o servidor no cofre:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Onde estão os parâmetros:

 - **/Credentials**: parâmetro obrigatório que especifica a localização na qual está localizado o ficheiro de chave de registo  
 - **/FriendlyName**: parâmetro obrigatório para o nome do servidor de anfitrião Hyper-V que aparece no portal do Azure recuperação de sites.
 - **/EncryptionEnabled**: parâmetro opcional que apenas utiliza quando de replicadas a partir do VMM para Azure.
 - **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername**: parâmetro opcional que especifica o nome de utilizador do Proxy (se proxy requer autenticação).
 - **/proxyPassword**: parâmetro opcional que especifica a palavra-passe para autenticar com o servidor de proxy (se proxy requer autenticação).  

## <a name="step-3-set-up-the-target-environment"></a>Passo 3: Configurar o ambiente de destino

Selecione o servidor de VMM de destino e na nuvem.

1. Clique em **infraestrutura preparar** > **destino** e selecione o servidor de VMM de destino que pretende utilizar.
2.  Serão apresentados nuvens no servidor que estão sincronizados com a recuperação de Site. Selecione a nuvem de destino.

    ![Destino](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## <a name="step-4-set-up-replication-settings"></a>Passo 4: Configurar definições de replicação

1. Para criar uma nova replicação política, clique em **infraestrutura preparar** > **Definições de replicação** > **+ criar e associar**.

    ![Rede](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. Na **política associar e criar** Especifique um nome de política. O tipo de origem e destino deve ser **Hyper-V**.
3. Na **versão de anfitrião Hyper-V** selecione qual o sistema operativo está em execução no anfitrião do.

    > [AZURE.NOTE] Nuvem VMM pode conter anfitriões a Hyper-V executar diferentes versões do Windows Server (suportadas), mas uma política de replicação é aplicados anfitriões a executar o sistema operativo mesmo. Se tiver anfitriões a executar mais do que a versão de um sistema operativo, em seguida, criar políticas de replicação separada.

4. Em **tipo de autenticação** e em **autenticação porta** especificar como o tráfego seja autenticado entre os servidores de anfitrião de Hyper-V principal e recuperação. Selecione o **certificado** , a menos que tenha um Kerberos ambiente de trabalho. Azure recuperação de Site irá configurar automaticamente certificados para autenticação HTTPS. Não precisa de fazer nada manualmente. Por predefinição, será aberta na Firewall do Windows nos servidores de anfitrião Hyper-V porta 8083 e 8084 (para os certificados). Se selecionar **Kerberos**, uma permissão Kerberos será utilizada para autenticação comum dos servidores de anfitrião. Note que esta definição só é relevante para os servidores de anfitrião Hyper-V em execução no Windows Server 2012 R2.
3. Na **Copiar frequência** especifique a frequência com que pretende criar uma réplica dados delta após a replicação inicial (cada 30 segundos, 5 ou 15 minutos).
4. No **ponto de recuperação de retenção**, especifique horas quanto tempo a janela de retenção para cada ponto de recuperação. Podem ser recuperadas máquinas protegidas para qualquer ponto dentro de uma janela.
6. Na **aplicação consistentes frequência de instantâneo** especifique frequência (1 a 12 horas) que contém instantâneos consistente para a aplicação de pontos de recuperação será criado. Hyper-V utiliza dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo utilizarão da máquina virtual toda e um instantâneo de aplicação consistentes tira um instantâneo de ponto em vez dos dados da aplicação dentro da máquina virtual. Aplicação consistentes instantâneos utilizam Volume sombra cópia serviço (VSS) para garantir que aplicações estão num estado consistente quando o instantâneo é disponibilizado. Tenha em atenção que se ativar a aplicação consistente instantâneos, irá afetar o desempenho das aplicações em execução em máquinas virtuais de origem. Certifique-se de que o valor que definiu for menor que o número de pontos de recuperação adicionais que configura.
7. Em **compressão de transferência de dados** , especifique se devem ser comprimidos dados replicados que são transferidos.
8. Selecione **Eliminar réplica VM** para especificar que o réplica virtual machine deve ser eliminado se desativar a proteção contra para a origem VM. Se ativar esta definição, ao desativar a proteção para a origem VM é removida da consola de recuperação de Site, definições de recuperação de sites para o VMM são removidas a partir da consola VMM e a réplica é eliminada.
3. Método de **replicação inicial** se estiver a replicação através da rede Especifique se pretende iniciar a replicação inicial ou agendá-la. Para guardar a largura de banda de rede poderá pretender agende a fora da sua ocupadas horas. Em seguida, clique em **OK**.

    ![Política de replicação](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Quando cria uma nova política tem automaticamente associados com a nuvem VMM. Na **política de replicação** clique em **OK**. Pode associar adicionais VMM nuvens (e VMs nas mesmas) esta política de replicação nas **Definições** > **replicação** > nome da política > **Associar nuvem VMM**.

    ![Política de replicação](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### <a name="prepare-for-offline-initial-replication"></a>Preparar para a replicação inicial offline

O que pode fazer replicação offline para a cópia inicial dos dados. Pode preparar isto da seguinte forma:

- No servidor de origem, irá especifique uma localização de caminho a partir do qual será executada a exportação de dados. Atribua controlo total para as permissões de NTFS e de partilha ao serviço de VMM no caminho de exportação. No servidor de destino, irá especifique uma localização de caminho a partir do qual irá ocorrer a importação de dados. Atribua as mesmas permissões neste caminho de importação.
- Se o caminho de importação ou exportação for partilhado, atribua membros do grupo de administrador, utilizador avançado, operador de impressão ou operador do servidor para a conta de serviço VMM no computador remoto no qual o partilhada está localizada.
- Se estiver a utilizar todas as contas executar como para adicionar anfitriões, caminhos de importação e exportação, atribua de leitura e permissões de escrita às contas de executar como no VMM.
- As partilhas de importação e exportação não devem estar localizadas em qualquer computador utilizado como um servidor de anfitrião Hyper-V, porque loopback configuração não é suportada pelo Hyper-V.
- No Active Directory, em cada Hyper-V servidor de anfitrião que contém máquinas virtuais que pretende proteger, ativar e configurar delegação restrita para confiar em computadores remotos no qual os caminhos de importação e exportação estão localizados, da seguinte forma:
    1. No controlador de domínio, abra a **utilizadores do Active Directory e computadores**.
    2. Na árvore de consola do clique **NomeDomínio** > **computadores**.
    3. Botão direito do rato no nome do servidor anfitrião Hyper-V > **Propriedades**.
    4. No separador **delegação** clique em **Confiar neste computador para delegação apenas a serviços especificados**.
    5. Clique em **utilizar qualquer protocolo de autenticação**.
    6. Clique em **Adicionar** > **utilizadores e computadores**.
    7. Escreva o nome do computador que aloja o caminho de exportação > **OK**. Na lista de serviços disponíveis, mantenha premida a tecla CTRL e clique em **cifs** > **OK**. Repita para o nome do computador que aloja o caminho de importação. Repita conforme necessário para os servidores de anfitrião Hyper-V adicionais.


### <a name="configure-network-mapping"></a>Configurar mapeamento de rede

Configure o mapeamento de rede entre redes de origem e destino.

- Faz a [leitura](#prepare-for-network-mapping) para uma descrição geral do que mapeamento de rede. Na adição [de leitura](site-recovery-network-mapping.md) para obter uma explicação de forma mais aprofundada.
- Certifique-se de que máquinas virtuais em servidores VMM estão ligadas a uma rede VM.

Configure o mapeamento da seguinte forma:

1. **Definições de** > **Infraestrutura de recuperação de Site** > **Mapeamento de rede** > **mapeamentos de rede** clique **+ mapeamento de rede**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. No separador de **mapeamento de rede de adicionar** , selecione a origem e VMM servidores de destino. As redes VM associadas com os servidores VMM são obtidas.
3. Na **rede de origem**, selecione a rede que pretende utilizar na lista de redes VM associados ao servidor VMM principal.
6. Na **rede de destino** , selecione a rede que pretende utilizar no servidor VMM secundário. Em seguida, clique em **OK**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Eis o que acontece quando começa a mapeamento de rede:

- Qualquer máquinas de virtuais réplica existente que correspondem à rede VM de origem serão ligadas à rede VM de destino.
- Novas máquinas virtuais que estão ligadas à rede VM de origem serão ligadas à rede de destino mapeado após a replicação.
- Se modificar um mapeamento de com uma nova rede existente, máquinas virtuais de réplica serão ligadas utilizando as novas definições.
- Se a rede de destino tem várias sub-redes e uma desses sub-redes tem o mesmo nome como sub-rede no qual está localizada a máquina virtual da origem, em seguida, a máquina de virtual réplica será ligada nessa sub-rede de destino depois activação pós-falha. Se não existirem sem sub-rede destino com um nome correspondente, a máquina virtual será ligada para a primeira sub-rede na rede.

### <a name="configure-storage-mapping"></a>Configurar mapeamento de armazenamento
Por predefinição quando replica uma máquina virtual num servidor origem Hyper-V anfitrião para um servidor de anfitrião de Hyper-V de destino, dados replicados são armazenados na localização predefinida que está indicada para o anfitrião de Hyper-V de destino no Gestor de Hyper-V. Para ter mais controlo sobre a localização onde armazenou dados replicados, pode configurar mapeamento de armazenamento<br/><br/> Para configurar o mapeamento de armazenamento, precisa de configurar classificações de armazenamento na origem e destino servidores VMM antes de começar a implementação. Atualmente mapeamento de armazenamento através do portal de Azure novo não é suportado. No entanto, pode ser ativado através do Powershell. [Saiba mais](site-recovery-vmm-to-vmm-powershell-resource-manager.md#step-6-configure-storage-mapping).

## <a name="step-5-capacity-planning"></a>Passo 5: Planear a capacidade

Agora que tem o basic infraestrutura de configurar o pode refletir acerca do planeamento de capacidades e descubra se precisar de recursos adicionais.

Recuperação de site fornece um planeador de capacidade baseadas no Excel para o ajudar a atribuir os recursos direita para o seu ambiente de origem, os componentes de recuperação do site, redes e armazenamento. Pode executar o planeador de no modo rápido para estimativas com base no número médio de VMs, discos e armazenamento ou no modo detalhado na qual irá entrada ilustrações ao nível de carga de trabalho. Antes de começar terá:

- Reunir informações sobre o ambiente de replicação, incluindo VMs, discos por VMs e armazenamento por disco.
- Calcule a taxa de alteração (vasilha) diária, que terá de para os dados de replicadas delta. Pode utilizar o [Planeador de capacidade para réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para o ajudar a efetuar o seguinte.

1.  Clique em **Transferir** para transferir a ferramenta de e, em seguida, executá-la. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando tiver terminado selecione **Sim** na **tem executar o planeador de capacidade**?

    ![Planeamento de capacidades](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="control-bandwidth"></a>Largura de banda de controlo

Depois de ter recolhidos informações de replicação de delta em tempo real com capacidade de Planeador de Hyper-V réplica, a ferramenta de Planeador de capacidade baseadas no Excel ajuda-o para calcular a largura de banda que precisa para replicação (inicial e delta). Para controlar a quantidade de largura de banda utilizada para a replicação pode configurar a política de NetQos utilizando a política de grupo ou o Windows PowerShell. Existem algumas maneiras pode fazê-lo:

**PowerShell** | **Detalhes**
--- | ---
**Novo - NetQosPolicy "QoS para sub-rede de destino"** | Optimizar tráfego a partir de um anfitrião Hyper-V com o Windows Server 2012 R2 para uma sub-rede secundária. Utilize se as principais e secundárias sub-redes são diferentes.
**Novo - NetQosPolicy "QoS à porta de destino"** | Optimizar tráfego a partir de um anfitrião Hyper-V a executar o Windows Server 2012 R2-uma porta de destino.
**Novo - NetQosPolicy "Borboleta tráfego de VMM"** | Optimizar o tráfego de vmms.exe. Isto irá optimizar o tráfego de Hyper-V e Live tráfego de migração. Pode fazer corresponder protocolos IP e portas para refinar.

Pode utilizar as definições de espessura de largura de banda ou limitar o tráfego por bits por secundário. Se estiver a utilizar um que tem de fazer isto em todos os nó de cluster de computação. Para obter mais informações, consulte:


- Blogue de Ferreira Maurer no [Limitação tráfego de réplica Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Informações adicionais sobre o [cmdlet NetQosPolicy novo](https://technet.microsoft.com/library/hh967468.aspx).


## <a name="step-6-enable-replication"></a>Passo 6: Replicação de ativar

Active agora a replicação da seguinte forma:

1. Clique em **passo 2: criar uma réplica da aplicação** > **origem**. Depois de ter ativado replicação pela primeira vez, clicar em **+ criar uma réplica** no cofre para activar a replicação para máquinas adicionais.

    ![Activar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. No pá a **origem** > selecione servidor VMM e na nuvem na qual os anfitriões Hyper-V que pretende criar uma réplica estão localizados. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. Na pá **destino** Verifique se o servidor VMM secundário e na nuvem.
4. Em **máquinas virtuais do** selecione VMs que pretende proteger a partir da lista.

    ![Ativar a proteção de máquina virtual](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Pode controlar o progresso da ação **Ativar proteção** em Definições > **tarefas** > **tarefas recuperação de sites**. Após a tarefa de **Proteção finalizar** é executada a máquina virtual está pronta para activação pós-falha.


>[AZURE.NOTE] Também pode ativar a proteção por para máquinas virtuais na consola do VMM. Clique em **Ativar proteção** na barra de ferramentas nas propriedades máquina virtual > separador **Azure recuperação de Site** .

Depois de ter ativado replicação pode ver as propriedades para a VM nas **Definições** > **Itens de replicadas** > vm nome. Pode ver informações sobre a política de replicação para a VM e o respetivo estado no dashboard de **Essentials** . Para obter mais detalhes, clique em **Propriedades** .

### <a name="onboard-existing-virtual-machines"></a>Incorporada máquinas virtuais existentes

Se tiver existentes máquinas virtuais VMM que são replicação com Hyper-V réplica pode bordo-las para protecção Azure Site recuperação da seguinte forma:

1. Certifique-se de que o servidor de Hyper-V que aloja a VM existente está localizado na nuvem principal e que o servidor de Hyper-V que aloja a máquina de virtual réplica está localizado na nuvem secundária.
2. Certifique-se de que uma política de replicação está configurada para o cloud VMM principal.
2. Active a replicação para a máquina virtual principal. Azure recuperação de sites e VMM irão garantir que o mesmo anfitrião réplica e máquina virtual é detetado e recuperação de sites do Azure serão reutilizar e restabelecer a replicação utilizando as definições especificadas.


## <a name="step-7-test-your-deployment"></a>Passo 7: Testar a sua implementação

Para testar a sua implementação pode executar um teste activação pós-falha para uma única máquina de virtual ou criar um plano de recuperação que contém uma ou mais máquinas virtuais.

### <a name="prepare-for-failover"></a>Preparar para activação pós-falha

- Para testar totalmente a sua implementação tem uma infraestrutura para o computador de replicadas funcionar como esperado. Se pretende testar Active Directory e DNS pode criar uma máquina virtual como controlador de domínio com o DNS e criar uma réplica isto Azure utilizando Azure a recuperação do Site. Leia mais em [Considerações de activação pós-falha de teste do Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- As instruções neste artigo descrevem como executar uma activação de teste pós-falha com nenhuma rede. Esta opção irá testar que a VM Falha ao longo, mas não testar definições de rede para a VM. [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre outras opções.
- Se pretender executar uma não planeado activação pós-falha em vez de um teste activação pós-falha tenha em atenção o seguinte procedimento:

    - Se possível deve encerrar máquinas primárias antes de executar um activação não planeado pós-falha. Isto garante que não tem de origem e de réplica máquinas a executar ao mesmo tempo.
    - Quando executa uma activação não planeado pós-falha deixa replicação de dados a partir do máquinas principais para que qualquer delta de dados não serem transferida depois de um activação não planeado pós-falha começa. Além disso se executar uma activação não planeado pós-falha num plano de recuperação será executado até estar concluído, mesmo se ocorre um erro.




### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

1. Falha ao longo de uma única VM nas **Definições** > **Itens de replicadas**, clique na VM > **+ teste activação pós-falha**.

    ![Teste activação pós-falha](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Falha ao longo de um plano de recuperação, nas **Definições** > de**Recuperação de planos**, com o botão direito do plano > **Teste activação pós-falha**. Para criar uma recuperação planear [siga estas instruções](site-recovery-create-recovery-plans.md).
2. No **Activação pós-falha de teste**, selecione **nenhum**. Com esta opção testá-lo a VM falha sobre como esperado, mas a VM replicada não estar ligada a uma rede.

    ![Teste activação pós-falha](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Clique em **OK** para começar a activação pós-falha. Pode controlar o progresso ao clicar em VM para abrir as respetivas propriedades ou a tarefa de **Teste activação pós-falha** nas **Definições** > **tarefas** > **tarefas recuperação de sites**.
4. Quando a tarefa de activação pós-falha atinge a fase de **testes concluída** , faça o seguinte:

    -  Ver a réplica VM na nuvem VMM secundária.
    -  Clique em **Concluir o teste** para concluir o teste activação pós-falha.
    -  Clique em **notas** para gravar e guardar quaisquer observações associadas a activação de teste pós-falha.

5. O teste virtual machine será criado no anfitrião do mesmo como o anfitrião no qual existe máquina réplica virtual. Esta é adicionada na nuvem mesmo na qual a máquina de virtual réplica está localizada.
6. Depois de verificar com êxito, que começam VMs clique **a activação de teste pós-falha está concluída**. Nesta fase são eliminados quaisquer elementos criados automaticamente pelo Site recuperação durante a mudança de teste.  

    > [AZURE.NOTE] Se um teste activação pós-falha persistir mais de duas semanas está concluída por força.



### <a name="update-dns-with-the-replica-vm-ip-address"></a>Actualizar o DNS com a réplica endereço VM IP

Depois de activação pós-falha a réplica VM poderá não ter o mesmo endereço IP como a principal máquina de virtual.

- Máquinas virtuais irá actualizar o servidor DNS que estiverem a utilizar após o início.
- Pode atualizar manualmente também DNS da seguinte forma:

#### <a name="retrieve-the-ip-address"></a>Obter o endereço IP

Execute este exemplo de script para obter o endereço IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="update-dns"></a>Actualizar o DNS

Execute este exemplo de script para actualizar o DNS, especificando o endereço IP que recuperou utilizando o script do exemplo anterior.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="next-steps"></a>Próximos passos

Após a sua implementação está configurado e em execução, [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de activações pós-falha.
