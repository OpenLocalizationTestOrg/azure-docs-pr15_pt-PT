<properties
    pageTitle="Criar uma réplica máquinas virtuais de Hyper-V (sem VMM) para utilizar o Azure recuperação de Site com o portal do Azure do Azure | Microsoft Azure"
    description="Descreve como implementar o Azure recuperação de Site para orquestrar replicação, activação e recuperação no local Hyper-V VMs que não são geridos pelo VMM para Azure através do portal Azure"
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
    ms.date="09/19/2016"
    ms.author="raynew"/>


# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Criar uma réplica máquinas virtuais de Hyper-V (sem VMM) para utilizar o Azure recuperação de Site com o portal do Azure do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [Azure clássico](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell - Gestor de recursos](site-recovery-deploy-with-powershell-resource-manager.md)



Bem-vindo ao Azure Site recuperação! Utilize este artigo se pretender criar uma réplica no local Hyper-V virtual máquinas que **não estão** gerido no nuvens sistema Center máquinas virtuais Gestor (VMM) para Azure. Este artigo descreve como configurar a replicação utilizando a recuperação de Site do Azure no portal do Azure.

> [AZURE.NOTE] Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model.md) para criar e trabalhar com recursos: Gestor de recursos do Azure e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação.

> Recuperação de Site Azure suporta recuperação e migração das máquinas virtuais de Hyper-V para Azure. Os passos neste artigo aplicam-se idêntico ao configurar a replicação Azure para recuperação de falhas ou para migrar VMs para Azure

Azure recuperação de Site no portal do Azure fornece várias novas funcionalidades:

- O Azure portal, cópia de segurança do Azure e serviços de recuperação de Site do Azure são combinados num único cofre serviços de recuperação para que possa configurar e gerir continuidade do negócio e recuperação de falhas (BCDR) a partir de uma única localização. Um dashboard unificado permite monitorizar e gerir as operações nos seus sites no local e na nuvem pública Azure.
- Os utilizadores com subscrições do Azure aprovisionados com o programa de fornecedor de solução da nuvem (CSP) podem agora gerir operações de recuperação de Site no portal do Azure.
- Recuperação de site no portal do Azure pode criar uma réplica máquinas a contas de armazenamento do Gestor de recursos. Em falha na ligação, recuperação de Site cria VMs com base no Gestor de recursos no Azure.
- Recuperação de site continua a suportar a replicação activação pós-falha de VMs utilizando o modelo clássico de implementação e contas de armazenamento clássico de.


Depois de leitura este artigo publicar o seu comentário na parte inferior na secção de comentários Disqus. Faça técnicas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Descrição geral


As organizações devem uma estratégia BCDR que determina como dados, das cargas de trabalho e apps manter-se em execução e disponível durante o tempo de inatividade planeado e não em recuperar a normal condições de trabalho mais cedo possível. Sua estratégia de BCDR irá manter dados de negócio seguros e recuperáveis e certifique-se de que das cargas de trabalho estão continuamente disponíveis quando ocorre falhas.

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR por orquestrar replicação de servidores físicos no local e máquinas virtuais na nuvem (Azure) ou para um centro de dados secundário. Quando ocorrem falhas na sua localização principal, pode falhar ao longo para a localização secundária para manter as aplicações e das cargas de trabalho disponíveis. Pode falhar voltar a sua localização principal quando devolve ao modo normal. Saiba mais em [o que é o Azure recuperação de Site?](site-recovery-overview.md)

Este artigo fornece todas as informações que necessita criar uma réplica no local Hyper-V virtual máquinas que **não estão** gerido no nuvens sistema Center máquinas virtuais Gestor (VMM) para Azure. Inclui uma descrição geral arquitetura, informações e implementação os passos para configurar servidores no local, Azure, uma política de replicação e planeamento da capacidade de planeamento. Depois de ter configurado a infraestrutura de pode activar a replicação em computadores que pretende proteger e realizar um ensaio activação pós-falha para validar a configuração da compilação. Pode também a migrar o seu VMs para Azure através da primeira execução de um activação planeada pós-falha e, em seguida, concluir a migração.

## <a name="business-advantages"></a>Vantagens de empresas

- Fornece externo (Azure) activação pós-falha para das cargas de trabalho do business aplicações e em execução em máquinas virtuais de Hyper-V.
- Fornece uma única consola de serviços de recuperação para a configuração simple e gestão de processos de replicação, activação pós-falha e recuperação.
- Permite-lhe facilmente executar activações pós-falha a partir do seu infraestrutura no local para Azure e falhas-anterior (restaurar) a partir do Azure para o site no local.
- Pode configurar os planos de recuperação com vários computadores, para que das cargas de trabalho em camadas aplicação falharem ao longo em conjunto.

## <a name="scenario-architecture"></a>Arquitetura de cenário

Estes são os componentes de cenário:

- **Cluster ou anfitrião Hyper-V**: servidores de anfitrião no local Hyper-V ou clusters. Os anfitriões Hyper-V executar VMs que pretende proteger são recolhidos nos sites do Hyper-V lógicos durante a implementação de recuperação de sites.
- **Fornecedor de recuperação de Site do Azure e agente de recuperação de serviços**: durante a implementação instale o fornecedor de recuperação de Site do Azure e o agente de serviços de recuperação do Microsoft Azure em servidores de anfitriões Hyper-V. O fornecedor comunica com recuperação de sites do Azure através de 443 HTTPS para criar uma réplica orchestration. O agente no servidor de anfitrião Hyper-V replica os dados ao armazenamento Azure sobre HTTPS 443 por predefinição.
- **Azure**: precisa de uma subscrição do Azure, uma conta de armazenamento Azure ao arquivo de replicadas dados e uma rede virtual Azure para que o Azure VMs estiver ligados a uma rede após activação pós-falha.

![Arquitetura de site Hyper-V](./media/site-recovery-hyper-v-site-to-azure/architecture.png)



## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Eis o que precisa no Azure para implementar este cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta Azure**| Terá de uma conta [Do Microsoft Azure](http://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**Armazenamento Azure** | Terá de uma conta de armazenamento padrão. Pode utilizar uma conta de armazenamento LRS ou GRS. Recomendamos que GRS para que seja e são dados se ocorre uma falha de regional ou se a região principal não pode ser recuperada. [Saiba mais](../storage/storage-redundancy.md). A conta tem de ser na mesma região como o Cofre de serviços de recuperação.<br/><br/> Armazenamento de Premium não é suportado.<br/><br/> Dados replicados são armazenados no Azure armazenamento e Azure VMs são criados quando ocorre activação pós-falha.<br/><br/> [Leia sobre](../storage/storage-introduction.md) Armazenamento Azure.
**Rede Azure** | Terá de uma rede virtual Azure que Azure VMs irá estabelecer ligação quando ocorre activação pós-falha. A rede virtual Azure tem de ser na mesma região como o Cofre de serviços de recuperação.

## <a name="on-premises-prerequisites"></a>Pré-requisitos de no local

Eis o que vai precisar no local.

**Pré-requisito** | **Detalhes**
--- | ---
**Hyper-V** | Um ou mais no local servidores a executar o **Windows Server 2012 R2** com mais recentes atualizações e a função Hyper-V ativado ou **Microsoft Hyper-V Server 2012 R2**.<br/><br/>O servidor de Hyper-V deve conter um ou mais máquinas virtuais.<br/><br/>Os servidores de Hyper-V devem estar ligados à Internet, seja direta ou através de um proxy.<br/><br/>Os servidores de Hyper-V devem ter correções mencionadas na [KB2961977](https://support.microsoft.com/en-us/kb/2961977 "KB2961977") instalado.
**Fornecedor e agente** | Durante a implementação do Azure recuperação de sites que irá instalar o fornecedor de recuperação de Site do Azure. A instalação do fornecedor também instalará o agente de serviços de recuperação do Azure em cada servidor Hyper-V executar máquinas virtuais que pretende proteger. Todos os servidores de Hyper-V num cofre recuperação de sites devem ter as mesmas versões do fornecedor e agente.<br/><br/>O fornecedor terá de ligar a recuperação de sites do Azure através da Internet. Tráfego pode ser enviado diretamente ou através de um proxy. Note que HTTPS baseados no proxy não é suportada. O servidor proxy deverá permitir acesso ao: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blog.core.windows.net <br/><br/> *Store.Core.Windows.NET <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>Se tiver regras de firewall baseada em endereço IP do servidor, certifique-se de que as regras permitem comunicação com o Azure. Terá de permitir que os [Intervalos de IP do Centro de dados de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e à porta HTTPS (443).<br/><br/>Permitir que os intervalos de endereços IP para o Azure região da sua subscrição e para EUA Ocidental.

## <a name="protected-machine-prerequisites"></a>Pré-requisitos de máquina protegida


**Pré-requisito** | **Detalhes**
--- | ---
**VMs protegidas** | Antes de falhar ao longo de uma VM terá para se certificar de que o nome que será atribuído à VM Azure está em conformidade com a [Pré-requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Pode modificar o nome depois de ter ativado replicação para a VM.<br/><br/> Capacidade de disco individuais no máquinas protegidas não deve ser mais do que 1023 GB. Uma VM pode ter até 16 discos (assim 16 TB).<br/><br/> Partilhada convidado disco clusters não são suportados.<br/><br/> Se a origem VM tiver NIC equipas é convertida para uma única NIC após activação pós-falha ao Azure.<br/><br/>Proteger VMs Linux com um endereço IP estático não é suportada.

## <a name="prepare-for-deployment"></a>Preparar para implementação

Para se preparar para implementação que vai precisar de:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual Azure VMs será localizado quando estiver a criadas após activação pós-falha.
2. [Configurar uma conta de armazenamento Azure](#set-up-an-azure-storage-account) para dados replicados.
3. [Preparar o Hyper-V anfitriões](#prepare-the-hyper-v-hosts) para se certificar de que estes possam aceder URLs necessários.

### <a name="set-up-an-azure-network"></a>Configurar uma rede Azure

Configure uma rede Azure. Terá este para que o Azure VMs criadas após activação pós-falha estiver ligado a uma rede.

- A rede deve estar na mesma região como aquele em que irá implementar o Cofre de serviços de recuperação.
- Consoante o modelo de recursos que pretende utilizar para Ocorreu uma falha ao longo do Azure VMs, irá configurar a rede Azure no [modo de Gestor de recursos](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Recomendamos que configurar uma rede antes de começar. Caso não pretenda vai precisar de fazê-lo durante a implementação de recuperação de sites.

> [AZURE.NOTE] [Migração das redes](../resource-group-move-resources.md) através de grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportado para as redes utilizadas para implementar o recuperação de sites.

### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento Azure

- Terá de uma conta de armazenamento Azure padrão para reter dados replicados para Azure.
- Consoante o modelo de recursos que pretende utilizar para Ocorreu uma falha ao longo do Azure VMs, irá configurar uma conta no [modo de Gestor de recursos](../storage/storage-create-storage-account.md) ou [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Recomendamos que o utilizador defina configurar uma conta de armazenamento antes de começar. Caso não pretenda vai precisar de fazê-lo durante a implementação de recuperação de sites. As contas precisam de ser na mesma região como o Cofre de serviços de recuperação.

> [AZURE.NOTE] [Migração de contas de armazenamento de](../resource-group-move-resources.md) grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportada para contas de armazenamento utilizadas para implementar o recuperação de sites.

### <a name="prepare-the-hyper-v-hosts"></a>Preparar os anfitriões Hyper-V

- Certifique-se de que os anfitriões Hyper-V está em conformidade com os [Pré-requisitos](#on-premises-prerequisites).

### <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gestão** > **cópia de segurança e recuperação de sites (OMS)**. Em alternativa, pode clicar em **Procurar** > cofres de**Serviços de recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. Em **nome** , especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. [Criar um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione uma existente e, especifique uma região do Azure. Máquinas vai ser replicadas para esta região. Para verificar regiões suportadas Consulte geográficos disponibilidade no [Recuperação preços detalhes do Site Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se pretender aceder rapidamente o cofre a partir do Dashboard clique em **Afixar ao dashboard** e, em seguida, clique em **Criar cofre**.

    ![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

O novo cofre irão aparecer no **Dashboard** > **todos os recursos**e, no principal pá **cofres de serviços de recuperação** .

## <a name="getting-started"></a>Introdução

Recuperação de site fornece uma introdução experiência que o ajuda a implementar o mais rapidamente possível. Introdução ao verifica os pré-requisitos e orienta-o através de recuperação de Site implementação os passos na ordem correta.

Começar a seleccionar o tipo de máquinas que pretende criar uma réplica e, em que pretende criar uma réplica para. Configurar servidores no local, contas do Azure armazenamento e redes. Criar políticas de replicação e planeamento da capacidade de executar. Depois de que configurou a sua infraestrutura de ativar a replicação para VMs. Pode executar activações pós-falha para máquinas específicas ou criar planos de recuperação falha através de vários computadores.

Começar a introdução ao selecionar como pretende implementar recuperação de sites. O fluxo de introdução ao altera ligeiramente consoante seus requisitos de replicação.



## <a name="step-1-choose-your-protection-goals"></a>Passo 1: Escolher os seus objetivos proteção

Selecione o que pretende criar uma réplica e onde pretende que sejam replicadas para.

1. Na pá **Serviços de recuperação cofres** selecione cofre e clique em **Definições**.
2. Nas **Definições** > **Introdução** clique em **Site recuperação** > **passo 1: preparar infraestrutura** > **objectivo de proteção**.

    ![Selecione os seus objetivos](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. Em **objetivo de proteção** selecione **Para Azure**e, selecione **Sim, com Hyper-V**. Selecione **não** para confirmar que não estiver a utilizar VMM. Em seguida, clique em **OK**.

    ![Selecione os seus objetivos](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## <a name="step-2-set-up-the-source-environment"></a>Passo 2: Configurar o ambiente de origem

Configurar o site Hyper-V, instale o fornecedor de recuperação de Site do Azure e o agente de serviços de recuperação do Azure em anfitriões Hyper-V e registar os anfitriões no cofre.


1. Clique em **passo 2: preparar infraestrutura** > **origem**. Para adicionar um novo site de Hyper-V como um contentor para a sua anfitriões Hyper-V ou clusters, clique em **+ Hyper-V Site**.

    ![Configurar o origem](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. No **site de criar Hyper-V** pá Especifique um nome para o site. Em seguida, clique em **OK**. Selecione o site que acabou de criar.

    ![Configurar o origem](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Clique em **+ Hyper-V Server** para adicionar um servidor para o site.
4. Em **Adicionar servidor** > **tipo de servidor** verificar que é apresentado **Hyper-V server** . Certifique-se de que o servidor de Hyper-V que pretende adicionar está em conformidade com a [Pré-requisitos](#on-premises-prerequisites) e é possível aceder aos URLs especificados.
4. Transferir o ficheiro de instalação do fornecedor de recuperação do Azure sites. Vai executar para instalar o fornecedor e o agente de serviços de recuperação em cada anfitrião Hyper-V este ficheiro.
5. Transfira a chave de registo. Terá este quando executa a configuração. A chave é válida para 5 dias após lhe gerá-lo.

    ![Configurar o origem](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Execute o fornecedor de ficheiro de configuração em cada anfitrião que adicionou ao site Hyper-V. Se estiver a instalar num cluster Hyper-V, execute a configuração em cada nó cluster. Instalar e registar cada nós de Cluster de Hyper-V assegura que máquinas virtuais permanecem protegidas, mesmo se estes migram em nós.

### <a name="install-the-provider-and-agent"></a>Instale o fornecedor e agente

1. Execute o fornecedor de ficheiro de configuração.
2. No **Microsoft Update** , pode optar ativamente por participar no existência de actualizações para que o fornecedor actualizações estão instaladas em conformidade com a política do Microsoft Update.
3. Numa **instalação** aceitar ou modificar a localização de instalação do fornecedor de predefinida e clique em **instalar**.
4. Na página **Definições do cofre** , clique em **Procurar** para selecionar o ficheiro de chave do cofre que transferiu. Especifique a subscrição de recuperação de sites do Azure, o nome do cofre e o site de Hyper-V ao qual pertence o servidor de Hyper-V.

    ![Registo do servidor](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. nas **Definições de Proxy** especificar como o fornecedor que será instalado no servidor irá liga ao Azure recuperação de Site através da internet.

- Se pretender que o fornecedor para se ligar diretamente selecione **Ligar diretamente sem um proxy**.
- Se pretender estabelecer ligação com o proxy de que está atualmente configurado no servidor, selecione **ligar-se com as definições de proxy existente**.
- Se o proxy existente requer autenticação ou que pretende utilizar um proxy personalizado para o fornecedor ligação, selecione **ligar-se com as definições de proxy personalizado**.
- Se utilizar um proxy personalizado, que terá de especificar o endereço, porta e as credenciais
- Se estiver a utilizar uma marca de proxy tem a certeza os URLs descritos na [Pré-requisitos](#on-premises-prerequisites) são permitidos pela mesma.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6 após a instalação concluir clique em **Registe-se** para registar o servidor no cofre.

![Localização de instalação](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7 após a conclusão do registo de metadados de Hyper-V servidor é obtido por recuperação de sites do Azure e o servidor é apresentado nas **Definições** > **Infraestrutura de recuperação de Site** > pá**Anfitriões Hyper-V** .


### <a name="command-line-installation"></a>Instalação de linha de comandos

O fornecedor de recuperação de sites do Azure e agente também podem ser instalados utilizando a linha de comandos seguinte. Este método pode ser utilizado para instalar o fornecedor num servidor principais para o Windows Server 2012 R2.

1. Transferir a chave de ficheiro e registo de instalação do fornecedor para uma pasta. Por exemplo C:\ASR.
2. A partir de uma linha de comandos elevada execute estes comandos para extrair o instalador do fornecedor:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

            C:\ASR> setupdr.exe /i

4. Em seguida, executar estes comandos para registar o servidor no cofre: CD C:\Program Files\Microsoft Azure Site recuperação Provider\
            Fornecedor de recuperação do C:\Program Files\Microsoft Azure Site\> DRConfigurator.exe /r /Friendlyname <friendly name of the server> /credenciais<path of the credentials file>

<br/>
Em que:

- **/Credentials** : parâmetro obrigatório que especifica a localização na qual está localizado o ficheiro de chave de registo  
- **/FriendlyName** : parâmetro obrigatório para o nome do servidor de anfitrião Hyper-V que aparece no portal do Azure recuperação de sites.
- **/proxyAddress** : parâmetro opcional que especifica o endereço do servidor proxy.
- **/proxyport** : parâmetro opcional que especifica a porta do servidor proxy.
- **/proxyUsername** : parâmetro opcional que especifica o nome de utilizador do Proxy (se proxy requer autenticação).
- **/proxyPassword** : parâmetro opcional que especifica a palavra-passe para autenticar com o servidor de proxy (se proxy requer autenticação).


## <a name="step-3-set-up-the-target-environment"></a>Passo 3: Configurar o ambiente de destino

Especificar a conta de armazenamento Azure para ser utilizado para replicação e a rede Azure ao qual vai ligar Azure VMs após activação pós-falha.

1.  Clique em **infraestrutura preparar** > **destino** e selecione a subscrição Azure que pretende utilizar.
2.  Especifique o modelo de implementação que pretende utilizar para VMs após activação pós-falha.
3.  Recuperação de sites verifica que tem um ou mais contas de armazenamento Azure compatível e redes.

    ![Armazenamento](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.  Se ainda não criou uma conta de armazenamento e que pretende criar uma utilizando o Gestor do recurso clique em **+ armazenamento** conta fazer essa inline. No pá a **criar a conta de armazenamento** Especifique um nome de conta, o tipo, a subscrição e a localização. A conta deverá ser na mesma localização, como o Cofre de serviços de recuperação.

    ![Armazenamento](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

    Se pretender criar uma conta de armazenamento utilizando o modelo clássico faça esse [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md).

5.  Se ainda não criou uma rede Azure e que pretende criar uma utilizando o Gestor de recursos clique **+ rede** para fazer essa inline. No pá a **criar rede virtual** Especifique um nome de rede, intervalo de endereços, detalhes de sub-rede, subscrição e localização. A rede deve estar na mesma localização, como o Cofre de serviços de recuperação.

    ![Rede](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

    Se pretender criar uma rede utilizando o modelo clássico faça esse [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).


## <a name="step-4-set-up-replication-settings"></a>Passo 4: Configurar definições de replicação

1. Para criar uma nova replicação política, clique em **infraestrutura preparar** > **Definições de replicação** > **+ criar e associar**.

    ![Rede](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. Na **política associar e criar** Especifique um nome de política.
3. Na **Copiar frequência** especifique a frequência com que pretende criar uma réplica dados delta após a replicação inicial (cada 30 segundos, 5 ou 15 minutos).
4. No **ponto de recuperação de retenção**, especifique horas quanto tempo a janela de retenção para cada ponto de recuperação. Podem ser recuperadas máquinas protegidas para qualquer ponto dentro de uma janela.
6. Na **aplicação consistentes frequência de instantâneo** especifique frequência (1 a 12 horas) que contém instantâneos consistente para a aplicação de pontos de recuperação será criado. Hyper-V utiliza dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo utilizarão da máquina virtual toda e um instantâneo de aplicação consistentes tira um instantâneo de ponto em vez dos dados da aplicação dentro da máquina virtual. Aplicação consistentes instantâneos utilizam Volume sombra cópia serviço (VSS) para garantir que aplicações estão num estado consistente quando o instantâneo é disponibilizado. Tenha em atenção que se ativar a aplicação consistente instantâneos, irá afetar o desempenho das aplicações em execução em máquinas virtuais de origem. Certifique-se de que o valor que definiu for menor que o número de pontos de recuperação adicionais que configura.
3. Na **hora de início de replicação inicial** Especifique quando iniciar a replicação inicial. A replicação ocorre sobre a largura de banda da internet, pelo que poderá pretende agende a fora da sua ocupadas horas. Em seguida, clique em **OK**.

    ![Política de replicação](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Quando cria uma nova política tem automaticamente associados com o site Hyper-V. Clique em **OK**. Pode associar um site de Hyper-V (e VMs nela) várias políticas de replicação nas **Definições** > **replicação** > nome da política > **Site de Hyper-V associar**.

## <a name="step-5-capacity-planning"></a>Passo 5: Planear a capacidade

Agora que tem o basic infraestrutura de configurar o pode refletir acerca do planeamento de capacidades e descubra se precisar de recursos adicionais.

Recuperação de site fornece um planeador de capacidade para ajudá-lo alocar os recursos da direita para o seu ambiente de origem, os componentes de recuperação do site, redes e armazenamento. Pode executar o planeador de no modo rápido para estimativas com base no número médio de VMs, discos e armazenamento ou no modo detalhado na qual irá entrada ilustrações ao nível de carga de trabalho. Antes de começar terá:

- Reunir informações sobre o ambiente de replicação, incluindo VMs, discos por VMs e armazenamento por disco.
- Calcule a taxa de alteração (vasilha) diária, que terá de para dados replicados. Pode utilizar o [Planeador de capacidade para réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para o ajudar a efetuar o seguinte.

1.  Clique em **Transferir** para transferir a ferramenta de e, em seguida, executá-la. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando tiver terminado selecione **Sim** na **tem executar o planeador de capacidade**?

    ![Planeamento de capacidades](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerações de largura de banda de rede

Pode utilizar a ferramenta de Planeador de capacidade para calcular a largura de banda que precisa para replicação (replicação inicial e, em seguida, delta). Para controlar a quantidade de utilização de largura de banda para a replicação tem algumas opções:

- **Optimizar a largura de banda**: o tráfego de Hyper-V que replica para Azure analise um anfitrião Hyper-V específico. Optimizar a largura de banda no servidor do anfitrião.
- **Aperfeiçoar a largura de banda**: pode influenciar a largura de banda utilizada para a replicação utilizar algumas das chaves de registo.

#### <a name="throttle-bandwidth"></a>Optimizar a largura de banda

1. Abra o snap-in MMC de cópia de segurança do Microsoft Azure no servidor de anfitrião Hyper-V. Por predefinição, um atalho para a cópia de segurança do Microsoft Azure está disponível no ambiente de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. In snap-, clique em **Alterar propriedades**.
3. No separador **Throttling** selecione **Ativar a utilização da largura de banda de internet limitação para operações de cópia de segurança**e defina os limites do trabalho e que não sejam-trabalho horas. Intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Optimizar a largura de banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

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

## <a name="step-6-enable-replication"></a>Passo 6: Replicação de ativar

Active agora a replicação da seguinte forma:

1. Clique em **passo 2: criar uma réplica da aplicação** > **origem**. Depois de ter ativado replicação pela primeira vez irá clique em **+ criar uma réplica** no cofre para activar a replicação para máquinas adicionais.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. No pá a **origem** > selecione o site Hyper-V. Em seguida, clique em **OK**.
3. Em **destino** , selecione a subscrição do cofre e o modelo de activação pós-falha que pretende utilizar no Azure (gestão de classic ou de recurso) após activação pós-falha.
4. Selecione a conta de armazenamento que pretende utilizar. Se pretender utilizar uma conta de armazenamento diferente daqueles tiver que pode [criar uma](#set-up-an-azure-storage-account). Para criar um armazenamento conta utilizando o modelo de Gestor de recursos, clique em **Criar novo**. Se pretender criar uma conta de armazenamento utilizando o modelo clássico faça esse [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md). Em seguida, clique em **OK**.
5.  Selecione a rede Azure e sub-rede ao qual o Azure VMs vai ligar ao que está a fiadas o após activação pós-falha. Selecione **configurar agora para máquinas selecionadas** para aplicar a definição de rede para todos os computadores que selecionar para protecção. Selecione **configurar mais tarde** para selecionar a rede Azure por máquina. Se pretende utilizar uma rede diferente das tem que pode [criar uma](#set-up-an-azure-network). Para criar uma rede utilizando o modelo de Gestor de recursos, clique em **Criar novo**. Se pretender criar uma rede utilizando o modelo clássico faça esse [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede se aplicável. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. Em **máquinas virtuais do** > **Selecione máquinas virtuais** clique em e selecione cada máquina que pretende criar uma réplica. Apenas pode selecionar máquinas para os quais pode ser ativada replicação. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. Nas **Propriedades** > **configurar as propriedades**, selecione o sistema operativo para os VMs selecionados e o disco SO. Certifique-se de que o nome do Azure VM (nome de destino) está em conformidade com [os requisitos de máquina virtual Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) e modificá-lo se precisar de. Em seguida, clique em **OK**. Pode definir propriedades adicionais mais tarde.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. Nas **definições de replicação** > **Configurar definições de replicação**, selecione a política de replicação que pretende aplicar para os VMs protegidas. Em seguida, clique em **OK**. Pode modificar a política de replicação nas **Definições** > **políticas de replicação** > nome da política > **Editar definições**. Alterações que se apliquem serão utilizadas para máquinas que já estão replicação e máquinas novas.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Pode controlar o progresso da tarefa **Ativar proteção** nas **Definições**de > **tarefas** > **tarefas recuperação de sites**. Após a tarefa de **Proteção finalizar** é executada a máquina está pronta para activação pós-falha.

### <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades VM

Recomendamos que verifique se as propriedades do computador de origem.

1. Clique em **Definições** > **Protegida itens** > **Itens de replicadas** > e selecione o computador.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. Nas **Propriedades** pode ver informações de replicação e activação pós-falha para a VM.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. No **cluster e de rede** > **Calcular propriedades** pode especificar o tamanho do Azure VM nome e de destino. Modificar o nome para cumprir os requisitos de Azure se precisar de. Também pode ver e modificar informações sobre a rede de destino, sub-rede e endereço de IP que será atribuído à VM Azure. Tenha em atenção o seguinte procedimento:

    - Pode definir o endereço de IP de destino. Se não fornecer um endereço de falhado ao longo do computador irá utilizar DHCP. Se definir um endereço de que não está disponível na activação pós-falha, a activação pós-falha irá falhar. O mesmo endereço IP de destino pode ser utilizado para teste activação pós-falha se o endereço estiver disponível na rede de activação pós-falha de teste.
    - O número de adaptadores de rede é ditado pelo tamanho que especificar para o computador virtual de destino, da seguinte forma:

        - Se o número de adaptadores de rede no computador de origem for menor ou igual ao número de placas permitida para o tamanho de máquina de destino, em seguida, o destino tem o mesmo número de placas como a origem de.
        - Se o número de adaptadores para a máquina de virtual origem excede o número permitido para o tamanho de destino, em seguida, o número máximo de tamanho de destino será utilizado.
        - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho de máquina de destino suporta quatro, o computador de destino terá duas placas. Se o computador de origem tem duas placas, mas o tamanho de suportados de destino suporta apenas um computador de destino ter apenas uma placa de.     
        - Se a VM tem vários adaptadores de rede irá todos ligar à mesma rede.

    ![Activar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.  Em **discos** pode ver o sistema operativo e os discos de dados na VM que vai ser replicada.


## <a name="step-7-test-the-deployment"></a>Passo 7: Testar a implementação

Para testar a implementação pode executar um teste activação pós-falha para uma máquina virtual única ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### <a name="prepare-for-test-failover"></a>Preparar para activação pós-falha de teste

- Para executar um teste activação pós-falha que recomendamos que cria uma nova rede Azure que tem isoladas da sua rede de produção Azure (é comportamento predefinido quando cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre como executar activações pós-falha de teste.
- Para obter o melhor desempenho quando não é possível ao longo do Azure, instale o agente do Azure no computador protegido. Torna mais rápido de arranque e ajuda a resolução de problemas. Instale o agente [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para testar totalmente a sua implementação terá uma infraestrutura para o computador de replicadas funcionar como esperado. Se pretende testar Active Directory e DNS pode criar uma máquina virtual como controlador de domínio com o DNS e criar uma réplica isto Azure utilizando Azure a recuperação do Site. Leia mais em [Considerações de activação pós-falha de teste do Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Se pretender executar uma não planeado activação pós-falha em vez de um teste activação pós-falha tenha em atenção o seguinte procedimento:

    - Se possível deve encerrar máquinas primárias antes de executar um activação não planeado pós-falha. Isto garante que não tem de origem e de réplica máquinas a executar ao mesmo tempo.
    - Quando executa uma activação não planeado pós-falha deixa replicação de dados a partir do máquinas principais para que qualquer delta de dados não serem transferida depois de um activação não planeado pós-falha começa. Além disso se executar uma activação não planeado pós-falha num plano de recuperação será executado até estar concluído, mesmo se ocorre um erro.

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar-se para ligar a Azure VMs depois de activação pós-falha

Se pretender ligar ao Azure VMs utilizar RDP após activação pós-falha, certifique-se de que efetue o seguinte procedimento:

**No computador antes de activação pós-falha no local**:

- Para o access através da internet ativar RDP, certifique-se de que as regras TCP e UDP são adicionadas para o **público**e certifique-se de que é permitido RDP a **Firewall do Windows** -> **permitidos aplicações e funcionalidades** para todos os perfis.
- Para o access através de uma ligação de site para o site ativar RDP no computador e, certifique-se de que é permitido RDP a **Firewall do Windows** -> **permitidos aplicações e funcionalidades** para redes **privadas** e de **domínio** .
- Instale o [Azure VM agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) no computador no local.
- Certifique-se de que a política de SAN o sistema operativo está definida para OnlineAll. [Saiba mais]( https://support.microsoft.com/kb/3031135)
- Desative o serviço IPSec antes de executar o activação pós-falha.

**No the Azure VM após activação pós-falha**:

- Adicione um endereço IP público para o NIC associado a VM Azure para permitir que RDP.
- Certifique-se de que não tem qualquer políticas de domínio que o impedem de ligar a uma máquina de virtual utilizando um endereço público.
- Tente ligar. Se não conseguir ligar Certifique-se de que a VM está em execução. Para obter mais sugestões de resolução de problemas Leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Se pretender aceder a um VM Azure Linux a ser executado depois activação pós-falha a utilizar um seguro Shell cliente (ssh), faça o seguinte:

**No computador antes de activação pós-falha no local**:

- Certifique-se de que o serviço de Shell seguro na VM Azure está definido para iniciar automaticamente no arranque do sistema.
- Certifique-se de que as regras de firewall permitem uma ligação de SSH à mesma.

**No the Azure VM após activação pós-falha**:

- As regras de grupo de segurança de rede no Falha ao longo do VM e a sub-rede Azure a que está ligado necessário permitir ligações de entrada para a porta SSH.
- Deverá ser criado um ponto final de público para permitir ligações de entrada na porta SSH (porta TCP 22 por predefinição).
- Se a VM é acedida através de uma ligação VPN (encaminhar Express ou site para o site VPN) o cliente pode ser utilizado para ligar diretamente para a VM em SSH.

### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

Para executar o teste activação pós-falha fazer o seguinte:

1. Falha ao longo de uma única VM nas **Definições** > **Itens de replicadas**, clique na VM > **+ teste activação pós-falha**.

    ![Teste activação pós-falha](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. Falha ao longo de um plano de recuperação, nas **Definições** > de**Recuperação de planos**, com o botão direito do plano > **Teste activação pós-falha**. Para criar uma recuperação planear [siga estas instruções](site-recovery-create-recovery-plans.md).

3. No **Teste activação pós-falha** seleccione a rede Azure ao qual irá estar ligado Azure VMs depois de activação pós-falha ocorre.

    ![Teste activação pós-falha](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

4. Clique em **OK** para começar a activação pós-falha. Pode controlar o progresso ao clicar em VM para abrir o respetivos properies ou a tarefa de **Teste activação pós-falha** nas **Definições** > **tarefas recuperação de sites**.
5. Quando a activação pós-falha atinge a fase de **testes concluída** , faça o seguinte:
    1. Ver a máquina de virtual réplica no portal do Azure. Certifique-se de que a máquina virtual for iniciado com êxito.
    2. Se estiver a definir até máquinas virtuais de acesso da sua rede no local pode iniciar uma ligação de ambiente de trabalho remoto para a máquina virtual.
    3. Clique em **Concluir o teste** para conclui-lo.
    4. Clique em **notas** para gravar e guardar quaisquer observações associadas a activação de teste pós-falha.
    5. Clique na **activação de teste pós-falha está concluída**. Limpe o ambiente de teste para desligar e eliminar o teste virtual machine automaticamente.
    6. Nesta fase são eliminados qualquer elementos ou VMs criados automaticamente pelo Site recuperação durante a mudança de teste. Quaisquer elementos adicionais que criou para teste activação pós-falha não são eliminados.

    > [AZURE.NOTE] Se um teste activação pós-falha persistir mais de duas semanas está concluída por força.

6. Após completa o activação pós-falha também deverá conseguir ver a réplica Azure máquina aparecem no portal do Azure > **máquinas virtuais**. Deve certificar-se de que a VM é o tamanho adequado, que tem ligado à rede adequada, e que está a funcionar.
7. Se lhe [preparado para ligações após activação pós-falha](#prepare-to-connect-to-Azure-VMs-after-failover) deverá conseguir ligar para a VM Azure.


## <a name="failover"></a>Activação pós-falha

Depois de concluída para sua máquinas replicação inicial, pode invocar activações pós-falha conforme necessário. Recuperação de sites suporta vários tipos de activações pós-falha - activação pós-falha de teste, planeada activação pós-falha e não planeado activação pós-falha.
[Saiba mais](site-recovery-failover.md) sobre diferentes tipos de activações pós-falha e descrições detalhadas dos quando e como efetuar cada um deles.

> [AZURE.NOTE] Se estiver a intenção de migrar máquinas virtuais para o Azure, recomendamos que utilize uma [operação de activação pós-falha planeado](site-recovery-failover.md#run-a-planned-failover-primary-to-secondary) para migrar as máquinas virtuais para Azure. Assim que a aplicação migrada for validada no Azure utilizando activação pós-falha de teste, utilize os passos mencionados em [Migração completa](#Complete-migration-of-your-virtual-machines-to-Azure) para concluir a migração das suas máquinas virtuais. Não necessita de efetuar uma consolidação ou eliminar. Migração completa conclui a migração, remove a proteção para a máquina virtual e deixa de faturação do Azure recuperação de Site para o computador.


###<a name="run-an-unplanned-failover"></a>Executar uma activação não planeado pós-falha

Isto deve ser escolhido quando um site principal torna-se inacessível devido a um incidente inesperado, tal como um ataque de vírus ou falha do power. Este procedimento descreve como executar uma 'não planeado activação pós-falha' para um plano de recuperação. Em alternativa pode executar o activação pós-falha para uma única máquina de virtual no separador máquinas virtuais. Antes de começar, certifique-se de todas as máquinas virtuais que pretende falhar ao longo do tiver concluído a replicação inicial.

1. Selecione **recuperação de plano > recoveryplan_name**.
2. No pá de plano recuperação, clique em **Não planeado activação pós-falha**.
3. Na página **activação pós-falha não planeado** , selecione as localizações de origem e destino.
4. Selecione **Encerrar máquinas virtuais e sincronizar os dados mais recentes** para especificar que deverá tentar recuperação de sites encerrar as máquinas virtuais protegidas e sincronizar os dados para que a versão mais recente dos dados serão Ocorreu uma falha ao longo do.
5. Após a falha na ligação, as máquinas virtuais estão uma consolidação pendentes estado.  Clique em **consolidar** para consolidar a activação pós-falha.

[Saiba mais](site-recovery-failover.md#run-an-unplanned-failover)

## <a name="complete-migration-of-your-virtual-machines-to-azure"></a>Migração completa da sua máquinas virtuais para Azure


>[AZURE.NOTE] Os passos seguintes aplicam-se apenas se estiver a migrar máquinas virtuais do Azure

1. Desempenhar activação pós-falha planeada mencionadas [aqui](site-recovery-failover.md)
2. No **Definições > itens de replicadas**, o botão direito do rato a máquina virtual e selecione **Migração completa**

    ![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

2. Clique em **OK** para concluir a migração. Pode controlar o progresso ao clicar na VM para abrir as respetivas propriedades ou utilizando a tarefa de migração completa na **Definições > tarefas recuperação de sites**.


## <a name="monitor-your-deployment"></a>Monitorizar a sua implementação

Eis como pode monitorizar as definições de configuração, estado e estado de funcionamento para a sua implementação de recuperação de sites:

1. Clique no nome do cofre para aceder ao dashboard de **Essentials** . Neste dashboard pode tarefas, o estado de replicação, planos de recuperação, estado de funcionamento do servidor e eventos de recuperação de sites.  Pode personalizar Essentials para mostrar os mosaicos e os esquemas que são mais úteis para si, incluindo o estado dos outros cofres recuperação de sites e de cópia de segurança.

    ![Tópicos essenciais](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. No mosaico do **Estado de funcionamento** pode monitorizar os servidores de site que ocorrerem o problema e os eventos criados por recuperação de sites no últimas 24 horas.
3. Pode gerir e monitorize a replicação nos **Itens de replicadas**, **Recuperação de planos**, e mosaicos de **Tarefas recuperação de Site** . Pode agregar para tarefas nas **Definições** -> **tarefas** -> **Tarefas recuperação de Site**.
