<properties
    pageTitle="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para Azure utilizando a recuperação de Site com o portal do Azure | Microsoft Azure"
    description="Descreve como implementar o Azure recuperação de Site para orquestrar replicação, activação e recuperação do Hyper-V VMs no nuvens VMM para Azure através do portal Azure"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-azure-site-recovery-with-the-azure-portal--microsoft-azure"></a>Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para Azure utilizando a recuperação de Site do Azure com o portal do Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [Azure clássico](site-recovery-vmm-to-azure-classic.md)
- [Gestor de recursos do PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell clássico](site-recovery-deploy-with-powershell.md)

Bem-vindo ao Azure Site recuperação! Utilize este artigo se pretender criar uma réplica no local Hyper-V virtual máquinas gerido no nuvens sistema Center Máquina Virtual Gestor (VMM) para Azure utilizando a recuperação de Site do Azure no portal do Azure.

> [AZURE.NOTE]Azure tem dois diferentes [modelos de implementação](../resource-manager-deployment-model
> ) para criar e trabalhar com recursos: Gestor de recursos do Azure e clássica. Azure também tem dois portais – o portal clássico Azure que suporta o modelo clássico de implementação e portal do Azure com o suporte para ambos os modelos de implementação.


Azure recuperação de Site no portal do Azure fornece várias novas funcionalidades:

- No portal do Azure os serviços de cópia de segurança do Azure e recuperação de sites do Azure são combinados numa única Cofre de serviços de recuperação, para que possa configurar e gerir continuidade do negócio e recuperação de falhas (BCDR) a partir de uma única localização. Um dashboard unificado permite-lhe monitorizar e gerir operações nos seus sites no local e na nuvem pública Azure.
- Os utilizadores com subscrições do Azure aprovisionados com o programa de fornecedor de solução da nuvem (CSP) podem agora gerir operações de recuperação de Site no portal do Azure.
- Recuperação de site no portal do Azure pode criar uma réplica máquinas a contas de armazenamento do Gestor de recursos do Azure. Em falha na ligação, recuperação de Site cria VMs com base no Gestor de recursos no Azure.
- Recuperação de site continua a suportar a replicação para contas de armazenamento clássica. Em falha na ligação, recuperação de Site cria VMs utilizando o modelo clássico.


Depois de ler este artigo, registe quaisquer comentários na parte inferior dos mesmos Disqus. Faça técnicas perguntas no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral

As organizações devem uma estratégia BCDR que determina como dados, das cargas de trabalho e apps manter-se em execução e disponível durante o tempo de inatividade planeado e não em recuperar a normal condições de trabalho mais cedo possível. Sua estratégia de BCDR deve manter os dados de negócio seguros e recuperáveis e garantir que das cargas de trabalho permanecem continuamente disponíveis quando ocorre falhas.

Recuperação de sites é um serviço Azure que contribui para a sua estratégia de BCDR pela orquestrar replicação de servidores físicos no local e máquinas virtuais na nuvem (Azure), ou para um centro de dados secundário. Quando ocorrem falhas na sua localização principal, falhar ao longo para a localização secundária para manter as aplicações e das cargas de trabalho disponíveis. Falhar voltar a sua localização principal quando devolve a operações normais. Saiba mais em [o que é o Azure recuperação de Site?](site-recovery-overview.md)

Este artigo fornece todas as informações que necessita para criar uma réplica no local Hyper-V VMs no nuvens VMM para Azure. Inclui uma descrição geral arquitetura, informações e implementação os passos para configurar o Azure, servidores no local, definições de replicação e planeamento da capacidade de planeamento. Depois de ter configurado a infraestrutura de pode ativar a replicação em computadores que pretende proteger e verificar que esse activação pós-falha funciona.


## <a name="business-advantages"></a>Vantagens de empresas

- Recuperação de site fornece protecção externa para cargas de trabalho de negócio e aplicações em execução no VMs Hyper-V.
- O portal de serviços de recuperação fornece uma única localização para configurar, gerir e monitorizar replicação, activação pós-falha e recuperação.
- Pode facilmente executar activações pós-falha a partir da sua infraestrutura no local para Azure e reposição de recurso (restaurar) a partir do Azure para servidores de anfitriões Hyper-V no seu site no local.
- Pode configurar os planos de recuperação com vários computadores para que das cargas de trabalho em camadas aplicação falharem ao longo em conjunto.


## <a name="scenario-architecture"></a>Arquitetura de cenário


Estes são os componentes de cenário:

- **Servidor VMM**: um servidor VMM no local com um ou mais nuvens.
- **Cluster ou anfitrião Hyper-V**: servidores de anfitrião de Hyper-V ou clusters gerido no nuvens VMM.
- **Fornecedor de recuperação de Site do Azure e agente de recuperação de serviços**: durante a implementação instalar o fornecedor de recuperação de Site do Azure no servidor VMM e o agente de serviços de recuperação do Microsoft Azure em servidores de anfitriões Hyper-V. O fornecedor no servidor VMM comunica com recuperação do Site através de 443 HTTPS para criar uma réplica orchestration. O agente no servidor de anfitrião Hyper-V replica os dados ao armazenamento Azure sobre HTTPS 443 por predefinição.
- **Azure**: precisa de uma subscrição do Azure, uma conta de armazenamento Azure ao arquivo de replicadas dados e uma rede virtual Azure para que o Azure VMs estiver ligados a uma rede após activação pós-falha.

![E2A topologia](./media/site-recovery-vmm-to-azure/architecture.png)


## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Eis o que precisa no Azure implementar este cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta Azure**| É necessária uma conta do [Microsoft Azure](http://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**Armazenamento Azure** | É necessária uma conta de armazenamento Azure padrão para armazenar dados replicados. Pode utilizar uma conta de armazenamento LRS ou GRS. Recomendamos que GRS para que seja e são dados se ocorre uma falha de regional ou se a região principal não pode ser recuperada. [Saiba mais](../storage/storage-redundancy.md). A conta tem de ser na mesma região como o Cofre de serviços de recuperação.<br/><br/>Armazenamento de Premium não é suportado.<br/><br/> Dados replicados são armazenados no Azure armazenamento e Azure VMs são criados quando ocorre activação pós-falha. <br/><br/> [Leia sobre](../storage/storage-introduction.md) Armazenamento Azure.
**Rede Azure** | Precisa de uma rede virtual Azure que se ligam Azure VMs quando ocorre activação pós-falha. A rede tem de ser na mesma região como o Cofre de serviços de recuperação.

## <a name="on-premises-prerequisites"></a>Pré-requisitos de no local

Eis o que precisa no local

**Pré-requisito** | **Detalhes**
--- | ---
**VMM**| Um ou mais VMM os servidores a executar o sistema Centro 2012 R2. Cada servidor VMM deve ter uma ou mais nuvens configurados. Deverá conter uma nuvem:<br/><br/> Um ou mais grupos de anfitrião VMM.<br/><br/> Uma ou mais servidores de anfitrião de Hyper-V ou clusters de cada grupo de anfitrião.<br/><br/>[Saiba mais](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) sobre a configuração de nuvens VMM.
**Hyper-V** | Servidores de anfitrião Hyper-V, pelo menos, tem de executar o **Windows Server 2012 R2** com a função Hyper-V ou **Microsoft Hyper-V Server 2012 R2** e tem instalado as atualizações mais recentes.<br/><br/> Um servidor de Hyper-V deve conter um ou mais VMs.<br/><br/> Um servidor de anfitrião Hyper-V ou cluster que inclua VMs que pretende criar uma réplica têm de ser gerido numa nuvem VMM.<br/><br/>Os servidores de Hyper-V devem estar ligados à Internet, seja direta ou através de um proxy.<br/><br/>Os servidores de Hyper-V devem ter correções mencionadas no artigo [2961977](https://support.microsoft.com/kb/2961977) instalado.<br/><br/>Servidores de anfitrião Hyper-V tem acesso à internet para a replicação dados Azure.
**Fornecedor e agente** | Durante a implementação do Azure recuperação de sites que irá instalar o fornecedor de recuperação de Site do Azure no servidor VMM e o agente de serviços de recuperação em anfitriões Hyper-V. Fornecedor e agente necessitam ligar à Azure através da internet diretamente ou através de um proxy. Tenha em atenção que um proxy baseado em HTTPS não é suportado. O servidor de proxy no servidor VMM e anfitriões Hyper-V deverá permitir acesso ao: <br/><br/> *. hypervrecoverymanager.windowsazure.com <br/> <br/> *. accesscontrol.windows.net <br/><br/> *. backup.windowsazure.com <br/> <br/> *. blob.core.windows.net <br/><br/> *. store.core.windows.net<br/><br/>Se tiver regras de firewall baseada em endereço IP do servidor de VMM, certifique-se de que as regras permitem comunicação com o Azure. É necessário permitir que os [Intervalos de IP do Centro de dados de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e à porta HTTPS (443).<br/><br/>Permitir que os intervalos de endereços IP para o Azure região da sua subscrição e para EUA Ocidental.<br/><br/>Para além disso. o servidor de proxy no servidor VMM precisa de aceder ao https://www.msftncsi.com/ncsi.txt


## <a name="protected-machine-prerequisites"></a>Pré-requisitos de máquina protegida


**Pré-requisito** | **Detalhes**
--- | ---
**VMs protegidas** | Antes de falhar ao longo de uma VM, certifique-se de que o nome que é atribuído a VM Azure está em conformidade com a [Pré-requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Pode modificar o nome depois de ter ativado replicação para a VM. <br/><br/> Capacidade de disco individuais no máquinas protegidas não deve ser mais do que 1023 GB. Uma VM pode ter até 16 discos (assim 16 TB).<br/><br/> Partilhada convidado disco clusters não são suportados.<br/><br/> Unified Extensible Firmware Interface (UEFI) / Extensible Firmware Interface(EFI) arranque não é suportada.<br/><br/> Se a origem VM tiver NIC equipas é convertida para uma única NIC após activação pós-falha ao Azure.<br/><br/>Proteger VMs Linux com um endereço IP estático não é suportada.

## <a name="prepare-for-deployment"></a>Preparar para implementação

Para se preparar para implementação que terá de:

1. [Configurar uma rede Azure](#set-up-an-azure-network) na qual Azure VMs irão estar localizados após activação pós-falha.
2. [Configurar uma conta de armazenamento Azure](#set-up-an-azure-storage-account) para dados replicados.
4. [Preparar o servidor VMM](#prepare-the-vmm-server) para implementação de recuperação de sites.
5. [Preparar para mapeamento de rede](#prepare-for-network-mapping). Configure o redes, de modo a que pode configurar mapeamento de rede durante a implementação de recuperação de sites.

### <a name="set-up-an-azure-network"></a>Configurar uma rede Azure

Precisa de uma rede Azure para que o Azure VMs criadas após activação pós-falha ligar-se à mesma.

- A rede deve estar na mesma região como aquele em que implementar o Cofre de serviços de recuperação.
- Consoante o modelo de recursos que pretende utilizar para Ocorreu uma falha ao longo do Azure VMs, irá configurar a rede Azure no [modo de Gestor de recursos](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Recomendamos que configurar uma rede antes de começar. Se não, terá de fazê-lo durante a implementação de recuperação de sites.

> [AZURE.NOTE] [Migração das redes](../resource-group-move-resources.md) através de grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportado para as redes utilizadas para implementar o recuperação de sites.


### <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento Azure

- Terá de uma conta de armazenamento Azure padrão para reter dados replicados para Azure. A conta tem de ser na mesma região como o Cofre de serviços de recuperação.
- Consoante o modelo de recursos que pretende utilizar para Ocorreu uma falha ao longo do Azure VMs, configurar uma conta no [modo de Gestor de recursos](../storage/storage-create-storage-account.md) ou [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- Recomendamos que configure uma conta antes de começar. Se não, terá de fazê-lo durante a implementação de recuperação de sites.

> [AZURE.NOTE] [Migração de contas de armazenamento de](../resource-group-move-resources.md) grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportada para contas de armazenamento utilizadas para implementar o recuperação de sites.

### <a name="prepare-the-vmm-server"></a>Preparar o servidor VMM

- Certifique-se de que o servidor VMM está em conformidade com os [Pré-requisitos](#on-premises-prerequisites).
- Durante a implementação de recuperação de sites, pode especificar que todas as nuvens num servidor VMM devem ser disponíveis no portal do Azure. Se apenas quiser nuvens específicos que seja apresentado no portal do, pode ativar a essa definição na nuvem na consola do administrador VMM.


### <a name="prepare-for-network-mapping"></a>Preparar para mapeamento de rede

Tem de configurar o mapeamento de rede durante a implementação de recuperação de sites. Rede mapas de mapeamento entre VMM VM redes de origem e destino Azure redes para ativar o seguinte:

- Máquinas falharem ao longo na mesma rede podem ligar umas às outras, mesmo se estiver não a falha ao longo da mesma forma ou no plano de recuperação do mesmo.
- Se um gateway de rede está configurado no destino rede Azure, Azure máquinas virtuais pode ligar a máquinas virtuais de no local.
- Para configurar a rede mapeamento aqui é o que precisa para preparar:

    - Certifique-se de que VMs do servidor de origem Hyper-V anfitrião estão ligadas a uma rede VMM VM. Essa rede deve ser ligada a uma rede lógica que está associada a nuvem.
    - Uma rede Azure como descrito [acima](#set-up-an-azure-network)

- [Saiba mais](site-recovery-network-mapping.md) sobre como funciona o mapeamento de rede.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre de serviços de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gestão** > **Serviços de recuperação**. Em alternativa, pode clicar em **Procurar** > cofres de**Serviços de recuperação** > **Adicionar**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. Em **nome**, especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione uma delas.
4. [Criar um grupo de recursos](../resource-group-template-deploy-portal.md), ou selecione uma existente. Especifique uma região do Azure. Máquinas vai ser replicadas para esta região. Para verificar regiões suportadas Consulte geográficos disponibilidade no [Recuperação preços detalhes do Site Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se quiser aceder rapidamente o cofre a partir do Dashboard, clique em **Afixar ao dashboard** > **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

O novo cofre é apresentado no **Dashboard** > **todos os recursos**e, no principal pá **cofres de serviços de recuperação** .

## <a name="getting-started"></a>Introdução

Recuperação de site fornece uma introdução experiência que o ajuda a implementar o mais rapidamente possível. Introdução ao verifica os pré-requisitos e orienta-o através de recuperação de Site implementação os passos na ordem correta.

Começar a seleccionar o tipo de máquinas que pretende criar uma réplica e, em que pretende criar uma réplica para. Configurar servidores no local, contas do Azure armazenamento e redes. Criar políticas de replicação e planeamento da capacidade de executar. Depois de que configurou a sua infraestrutura de ativar a replicação para VMs. Pode executar activações pós-falha para máquinas específicas ou criar planos de recuperação falha através de vários computadores.

Começar a introdução ao selecionar como pretende implementar recuperação de sites. O fluxo de introdução ao altera ligeiramente consoante seus requisitos de replicação.



## <a name="step-1-choose-your-protection-goals"></a>Passo 1: Escolher os seus objetivos proteção

Selecione o que pretende criar uma réplica e onde pretende que sejam replicadas para.

1. Na pá **cofres de serviços de recuperação de** , selecione cofre e clique em **Definições**.
2. **Começar** a trabalhar, clique em **Site recuperação** > **passo 1: preparar infraestrutura** > **objectivo de proteção**.

    ![Selecione os seus objetivos](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. Em **objetivo de proteção** selecione **Para Azure**e, selecione **Sim, com Hyper-V**. Selecione **Sim** para confirmar que está a utilizar VMM para gerir anfitriões Hyper-V e o site de recuperação. Em seguida, clique em **OK**.

    ![Selecione os seus objetivos](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## <a name="step-2-set-up-the-source-environment"></a>Passo 2: Configurar o ambiente de origem

Instalar o fornecedor de recuperação de Site do Azure no servidor VMM e registar o servidor no cofre. Instale o agente de serviços de recuperação do Azure em anfitriões Hyper-V.

1. Clique em **passo 2: preparar infraestrutura** > **origem**.

    ![Configurar o origem](./media/site-recovery-vmm-to-azure/set-source1.png)

2. Em **preparar origem** clique em **+ VMM** para adicionar um servidor VMM.

    ![Configurar o origem](./media/site-recovery-vmm-to-azure/set-source2.png)

3. Na verificação pá **Adicionar servidor** que **o servidor de sistema Center VMM** aparece no **tipo de servidor** e que o servidor VMM cumpre os [requisitos de URL e pré-requisitos](#on-premises-prerequisites).
4. Transferir o ficheiro de instalação do fornecedor de recuperação do Azure sites.
5. Transfira a chave de registo. Precisa de isto quando executa a configuração. A tecla é válida por cinco dias após lhe gerá-lo.

    ![Configurar o origem](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Instale o fornecedor de recuperação de Site do Azure no servidor VMM.


### <a name="set-up-the-azure-site-recovery-provider"></a>Configurar o fornecedor de recuperação de Site do Azure

1.  Execute o fornecedor de ficheiro de configuração.
2. No **Microsoft Update** , pode optar ativamente por participar no existência de actualizações para que o fornecedor actualizações estão instaladas em conformidade com a política do Microsoft Update.
3. Numa **instalação**, aceite ou modificar a localização de instalação do fornecedor de predefinida e clique em **instalar**.

    ![Localização de instalação](./media/site-recovery-vmm-to-azure/provider2.png)

4. Quando termina de instalação clique em **Registe-se** para registar o servidor VMM no cofre.
5. Na página **Definições do cofre** , clique em **Procurar** para selecionar o ficheiro de chave do cofre. Especifique a subscrição de recuperação de sites do Azure e o nome do cofre.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure/provider10.PNG)

6. Na **Ligação à Internet**, especifique como o fornecedor executado no servidor do VMM irá ligar a recuperação do Site através da internet.

    - Se pretender que o fornecedor para se ligar diretamente selecione **Ligar diretamente a recuperação de sites do Azure sem um proxy**.
    - Se o proxy existente requer autenticação ou que pretende utilizar um seleccione proxy personalizado **ligar a recuperação de sites do Azure utilizando um servidor proxy**.
    - Se utilizar um proxy personalizado, especifique o endereço, porta e as credenciais.
    - Se estiver a utilizar um proxy deve tiver já permitido os URLs descritos na [Pré-requisitos](#on-premises-prerequisites).
    - Se utilizar um proxy personalizado uma conta de VMM RunAs (DRAProxyAccount) será criada automaticamente utilizando as credenciais do proxy especificado. Configure o servidor proxy para que esta conta pode autenticar com êxito. As definições da conta VMM RunAs podem ser modificadas na consola do VMM. Nas **Definições**, expanda **segurança** > **Executar como contas**e, em seguida, modificar a palavra-passe para DRAProxyAccount. Terá de reiniciar o serviço VMM para que esta definição entra em vigor.

    ![Internet](./media/site-recovery-vmm-to-azure/provider13.PNG)

7. Aceitar ou modificar a localização de um certificado SSL gerado automaticamente para encriptação de dados. Este certificado é utilizado se ativar encriptação de dados para uma nuvem protegida por Azure no portal do Azure recuperação de sites. Manter este certificado seguros. Quando executa uma activação pós-falha ao Azure terá-lo para desencriptar, se a encriptação de dados estiver ativada.


8. Em **nome de servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Na configuração do cluster, especifique o nome da função cluster VMM.
9. Ative a **sincronização nuvem metadados** se pretender sincronizar metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só tem de ocorrer uma vez por cada servidor. Se não pretender sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades na nuvem na consola do VMM. Clique em **Registe-se** para concluir o processo.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure/provider16.PNG)

10. Registo é iniciado. Depois de concluir o registo, o servidor é apresentado nas **Definições** > pá de**servidores** no cofre.


#### <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Instalação de linha de comandos para o fornecedor de recuperação de Site do Azure

O fornecedor de recuperação do Azure Site podem ser instalado da linha de comandos. Este método pode ser utilizado para instalar o fornecedor no servidor principais para o Windows Server 2012 R2.

1. Transferir a chave de ficheiro e registo de instalação do fornecedor para uma pasta. Por exemplo, C:\ASR.
2. A partir de uma linha de comandos elevada execute estes comandos para extrair o instalador do fornecedor:

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

            C:\ASR> setupdr.exe /i

4. Em seguida, execute estes comandos para registar o servidor no cofre:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Em que:

- **/Credentials**: parâmetro obrigatório que especifica onde está localizado o ficheiro de chave de registo.  
- **/FriendlyName**: parâmetro obrigatório para o nome do servidor de anfitrião Hyper-V que aparece no portal do Azure recuperação de sites.
- - **/EncryptionEnabled**: parâmetro opcional quando estiver a replicação Hyper-V VMs no VMM nuvens para Azure. Especifique se pretende encriptar máquinas virtuais no Azure (na encriptação resto). Certifique-se de que o nome do ficheiro tem uma extensão **. pfx** . Encriptação está desativada por predefinição.
- **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
- **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
- **/proxyUsername**: parâmetro opcional que especifica o nome de utilizador do proxy (se proxy requer autenticação).
- **/proxyPassword**: parâmetro opcional que especifica a palavra-passe para autenticar com um servidor proxy (se o proxy requer autenticação).


### <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Instalar o agente de serviços de recuperação do Azure em anfitriões Hyper-V

1. Depois de ter configurado o fornecedor, tem de transferir o ficheiro de instalação do agente de serviços de recuperação do Azure. Execute a configuração em cada servidor Hyper-V na nuvem VMM.

    ![Sites de Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. Na página **Verificação de pré-requisitos** , clique em **seguinte**. Qualquer pré-requisitos em falta serão instalados automaticamente.

    ![Agente de serviços de recuperação de pré-requisitos](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. Na página **Definições de instalação** , aceitar ou modificar a localização de instalação e a localização da cache. Pode configurar a cache numa unidade de que tem pelo menos 5 GB de armazenamento disponível, mas recomendamos uma unidade de cache com 600 GB ou mais de espaço livre. Em seguida, clique em **instalar**.
4. Após a instalação estiver concluída, clique em **Fechar** para concluir.

    ![Registe-se MARTE agente](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### <a name="command-line-installation-for-azure-site-recovery-services-agent"></a>Instalação de linha de comandos para agente de serviços de recuperação de Site do Azure

Pode instalar o agente de serviços de recuperação do Microsoft Azure da linha de comandos utilizando o seguinte comando:

     marsagentinstaller.exe /q /nu

#### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Configurar o acesso de proxy à internet para recuperação de sites de anfitriões Hyper-V

O agente de serviços de recuperação em execução em Hyper-V anfitriões tem acesso à internet para Azure para a replicação VM. Se está a aceder à internet através de um proxy, configure-o da seguinte forma:

1. Abra o snap-in MMC de cópia de segurança do Microsoft Azure no anfitrião do Hyper-V. Por predefinição, um atalho para a cópia de segurança do Microsoft Azure está disponível no ambiente de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. In snap-, clique em **Alterar propriedades**.
3. No separador **Configuração do Proxy** , especifique as informações do servidor proxy.

    ![Registe-se MARTE agente](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Certifique-se de que o agente pode atingir os URLs descritos na [Pré-requisitos](#on-premises-prerequisites).


## <a name="step-3-set-up-the-target-environment"></a>Passo 3: Configurar o ambiente de destino

Especificar a conta de armazenamento Azure para ser utilizado para replicação e a rede Azure ao qual vai ligar Azure VMs após activação pós-falha.

1.  Clique em **infraestrutura preparar** > **destino** e selecione a subscrição Azure que pretende utilizar.
2.  Especifique o modelo de implementação que pretende utilizar para VMs após activação pós-falha.
3.  Recuperação de sites verifica que tem um ou mais contas de armazenamento Azure compatível e redes.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.  Se ainda não criou uma conta de armazenamento e que pretende criar uma através do Gestor de recursos, clique em **+ conta de armazenamento** para fazer essa inline.  No pá a **criar a conta de armazenamento** Especifique um nome de conta, o tipo, a subscrição e a localização. A conta deverá ser na mesma localização, como o Cofre de serviços de recuperação.

    ![Armazenamento](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

    Tenha em atenção que:

    - Se pretender criar uma conta de armazenamento utilizando o modelo clássico, fazê-lo no portal do Azure. [Saiba mais](../storage/storage-create-storage-account-classic-portal.md)
    - Se estiver a utilizar uma conta de armazenamento premium para os dados replicados, terá de configurar uma conta de armazenamento adicional de padrão para armazenar registos de replicação capturar em curso alterações aos dados no local.

4.  Se ainda não criou uma rede Azure e que pretende criar uma utilizando o Gestor de recursos clique **+ rede** para fazer essa inline. No pá a **criar rede virtual** Especifique um nome de rede, intervalo de endereços, detalhes de sub-rede, subscrição e localização. A rede deve estar na mesma localização, como o Cofre de serviços de recuperação.

    ![Rede](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

    Se pretender criar uma rede utilizando o modelo clássico irá fazê-lo no portal do Azure. [Saiba mais](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### <a name="configure-network-mapping"></a>Configurar mapeamento de rede

- [Leitura](#prepare-for-network-mapping) faz uma descrição geral do que mapeamento de rede. [Leia este artigo](site-recovery-network-mapping.md) para obter uma explicação de forma mais aprofundada.
- Certifique-se de que máquinas virtuais no servidor VMM estão ligadas a uma rede VM e que criou, pelo menos, uma rede virtual Azure. Múltiplas redes VM podem ser mapeadas para uma única rede Azure.

Configure o mapeamento da seguinte forma:

1. Nas **Definições** > **Infraestrutura de recuperação de Site** > **mapeamentos de rede** > **Mapeamento de rede**, clique no ícone **+ mapeamento de rede** .

    ![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. No **mapeamento de rede adicionar** selecione o servidor de VMM de origem e **Azure** como o destino.
3. Verifique se a subscrição e o modelo de implementação após activação pós-falha.
4. Na **rede de origem**, selecione a rede VM origem no local que pretende mapear a partir da lista associada ao servidor VMM.
5. Na **rede de destino**, selecione a rede Azure na qual réplica Azure VMs será localizado quando estiver a criados. Em seguida, clique em **OK**.

    ![Mapeamento de rede](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Eis o que acontece quando começa a mapeamento de rede:

- VMs existentes na rede VM origem estão ligados à rede de destino quando começa a mapeamento. Novos VMs ligados à rede VM origem estão ligados à rede Azure mapeada quando ocorre a replicação.
- Se modificar um mapeamento de rede existente, máquinas virtuais de réplica serão ligadas utilizando as novas definições.
- Se a rede de destino tem várias sub-redes e uma desses sub-redes tem o mesmo nome como sub-rede no qual está localizada a máquina virtual da origem, em seguida, a máquina de virtual réplica será ligada nessa sub-rede de destino depois activação pós-falha.
- Se não existirem sem sub-rede destino com um nome correspondente, a máquina virtual será ligada para a primeira sub-rede na rede.



## <a name="step-4-set-up-replication-settings"></a>Passo 4: Configurar definições de replicação


1. Para criar uma nova política de replicação, clique em **infraestrutura preparar** > **Definições de replicação** > **+ criar e associar**.

    ![Rede](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. Em **criar e a política associar**, especifique um nome de política.
3. Na **Copiar frequência**, especifique a frequência com que pretende criar uma réplica dados delta após a replicação inicial (cada 30 segundos, 5 ou 15 minutos).
4. No **ponto de recuperação de retenção**, especifique horas quanto tempo a janela de retenção para cada ponto de recuperação. Podem ser recuperadas máquinas protegidas para qualquer ponto dentro de uma janela.
6. Na **aplicação consistentes frequência de instantâneo**, especifique frequência (1 a 12 horas) que contém instantâneos consistente para a aplicação de pontos de recuperação será criado. Hyper-V utiliza dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo utilizarão da máquina virtual toda e um instantâneo de aplicação consistentes tira um instantâneo de ponto em vez dos dados da aplicação dentro da máquina virtual. Aplicação consistentes instantâneos utilizam Volume sombra cópia serviço (VSS) para garantir que aplicações estão num estado consistente quando o instantâneo é disponibilizado. Tenha em atenção que se ativar a aplicação consistente instantâneos, irá afetar o desempenho das aplicações em execução em máquinas virtuais de origem. Certifique-se de que o valor que definiu for menor que o número de pontos de recuperação adicionais que configura.
3. Em **hora de início de replicação inicial**, indicam quando iniciar a replicação inicial. A replicação ocorre sobre a largura de banda da internet, pelo que poderá pretende agende a fora da sua ocupadas horas.
5. **Encriptar os dados armazenados no Azure**, especifique se pretende encriptar no dados resto no Azure armazenamento. Em seguida, clique em **OK**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Quando cria uma nova política tem automaticamente associados com a nuvem VMM. Clique em **OK**. Pode associar adicionais VMM nuvens (e VMs nas mesmas) esta política de replicação nas **Definições** > **replicação** > nome da política > **Associar nuvem VMM**.

    ![Política de replicação](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="step-5-capacity-planning"></a>Passo 5: Planear a capacidade

Agora que tem o basic infraestrutura de configurar o pode refletir acerca do planeamento de capacidades e descubra se precisar de recursos adicionais.

Recuperação de site fornece um planeador de capacidade para ajudá-lo alocar os recursos da direita para o seu ambiente de origem, os componentes de recuperação do site, redes e armazenamento. Pode executar o planeador de no modo rápido para estimativas com base no número médio de VMs, discos e armazenamento ou no modo detalhado na qual irá entrada ilustrações ao nível de carga de trabalho. Antes de começar terá:

- Reunir informações sobre o ambiente de replicação, incluindo VMs, discos por VMs e armazenamento por disco.
- Calcule a taxa de alteração (vasilha) diária, que terá de para dados replicados. Pode utilizar o [Planeador de capacidade para réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para o ajudar a efetuar o seguinte.

1.  Clique em **Transferir** para transferir a ferramenta de e, em seguida, executá-la. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.  Quando tiver terminado selecione **Sim** na **tem executar o planeador de capacidade**?

    ![Planeamento de capacidades](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### <a name="network-bandwidth-considerations"></a>Considerações de largura de banda de rede

Pode utilizar a ferramenta de Planeador de capacidade para calcular a largura de banda que precisa para replicação (replicação inicial e, em seguida, delta). Para controlar a quantidade de utilização de largura de banda para a replicação tem algumas opções:

- **Optimizar a largura de banda**: o tráfego de Hyper-V que replica para um site secundário analise um anfitrião Hyper-V específico. Optimizar a largura de banda no servidor do anfitrião.
- **Aperfeiçoar a largura de banda**: pode influenciar a largura de banda utilizada para a replicação utilizar algumas das chaves de registo.

#### <a name="throttle-bandwidth"></a>Optimizar a largura de banda

1. Abra o snap-in MMC de cópia de segurança do Microsoft Azure no servidor de anfitrião Hyper-V. Por predefinição, um atalho para a cópia de segurança do Microsoft Azure está disponível no ambiente de trabalho ou em C:\Program Files\Microsoft Azure recuperação serviços Agent\bin\wabadmin.
2. In snap-, clique em **Alterar propriedades**.
3. No separador **Throttling** selecione **Ativar a utilização da largura de banda de internet limitação para operações de cópia de segurança**e defina os limites do trabalho e que não sejam-trabalho horas. Intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

    ![Optimizar a largura de banda](./media/site-recovery-vmm-to-azure/throttle2.png)

Também pode utilizar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para configurar a limitação. Eis um exemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Conjunto OBMachineSetting NoThrottle** indica que não sem limitação é necessária.


#### <a name="influence-network-bandwidth"></a>Largura de banda de rede de influência

O valor de registo **UploadThreadsPerVM** controla o número de threads que são usados para transferência de dados (replicação inicial ou delta) de um disco. Um valor maior aumenta a largura de banda de rede utilizada para a replicação. O valor de registo **DownloadThreadsPerVM** Especifica o número de threads utilizado para transferência de dados durante a reposição de recurso.

1. No registo navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.

    - Modificar o valor **UploadThreadsPerVM** (ou se não existe a criar a chave) para controlo threads utilizados para a replicação de disco.
    - Modificar o valor **DownloadThreadsPerVM** (ou se não existe a criar a chave) para controlo threads utilizados para o tráfego de reposição de recurso a partir do Azure.
2. O valor predefinido é 4. Numa rede "overprovisioned", devem ser alteradas estas chaves de registo dos valores predefinidos. O valor máximo é 32. Monitorizar o tráfego para otimizar o valor.

## <a name="step-6-enable-replication"></a>Passo 6: Replicação de ativar

Active agora a replicação da seguinte forma:

1. Clique em **passo 2: criar uma réplica da aplicação** > **origem**. Depois de ter ativado replicação pela primeira vez, clique em **+ criar uma réplica** no cofre para activar a replicação para máquinas adicionais.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. No pá a **origem** > selecionar o servidor VMM e na nuvem na qual os anfitriões Hyper-V estão localizados. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. Em **destino** , selecione a subscrição, modelo de mensagem com falha implementação e a conta de armazenamento que estiver a utilizar o dados replicados.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Selecione a conta de armazenamento que pretende utilizar. Se pretender utilizar uma conta de armazenamento diferente daqueles tiver que pode [criar uma](#set-up-an-azure-storage-account). Para criar um armazenamento conta utilizando o modelo de Gestor de recursos, clique em **Criar novo**. Se pretender criar uma conta de armazenamento utilizando o modelo clássico, faça esse [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md). Em seguida, clique em **OK**.
5. Selecione a rede Azure e sub-rede ao qual o Azure VMs vai ligar ao que está a fiadas o após activação pós-falha. Selecione **configurar agora para máquinas selecionadas** para aplicar a definição de rede para todos os computadores que selecionar para protecção. Selecione **configurar mais tarde** para selecionar a rede Azure por máquina. Se pretende utilizar uma rede diferente das tem que pode [criar uma](#set-up-an-azure-network). Para criar uma rede utilizando o modelo de Gestor de recursos, clique em **Criar novo**. Se pretender criar uma rede utilizando o modelo clássico faça esse [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede se aplicável. Em seguida, clique em **OK**.
6. Em **máquinas virtuais do** > **Selecione máquinas virtuais** clique em e selecione cada máquina que pretende criar uma réplica. Apenas pode selecionar máquinas para os quais pode ser ativada replicação. Em seguida, clique em **OK**.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. Nas **Propriedades** > **configurar as propriedades**, selecione o sistema operativo para os VMs selecionados e o disco SO. Em seguida, clique em **OK**. Pode definir propriedades adicionais mais tarde.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/enable-replication6.png)


12. Nas **definições de replicação** > **Configurar definições de replicação**, selecione a política de replicação que pretende aplicar para os VMs protegidas. Em seguida, clique em **OK**. Pode modificar a política de replicação nas **Definições** > **políticas de replicação** > nome da política > **Editar definições**. Alterações que se apliquem são utilizadas para máquinas que já estão replicação e máquinas novas.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Pode controlar o progresso da tarefa **Ativar proteção** nas **Definições**de > **tarefas** > **tarefas recuperação de sites**. Após a tarefa de **Proteção finalizar** é executada a máquina está pronta para activação pós-falha.

### <a name="view-and-manage-vm-properties"></a>Ver e gerir propriedades VM

Recomendamos que verifique se as propriedades do computador de origem. Lembre-se de que o nome do Azure VM deve estar em conformidade com [os requisitos de máquina virtual Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Clique em **Definições** > **Protegida itens** > **Itens de replicadas** > e selecione o computador para ver os seus detalhes.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. Nas **Propriedades** pode ver informações de replicação e activação pós-falha para a VM.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. No **cluster e de rede** > **Calcular propriedades** pode especificar o tamanho do Azure VM nome e de destino. Modificar o nome para cumprir os [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) se precisar de. Também pode ver e modificar informações sobre a rede de destino, sub-rede e endereço IP que é atribuído a VM Azure. Tenha em atenção o seguinte procedimento:

    - Pode definir o endereço de IP de destino. Se não fornecer um endereço de falhado ao longo do computador irá utilizar DHCP. Se definir um endereço de que não está disponível na activação pós-falha, a activação pós-falha irá falhar. O mesmo endereço IP de destino pode ser utilizado para teste activação pós-falha se o endereço estiver disponível na rede de activação pós-falha de teste.
    - O número de adaptadores de rede é ditado pelo tamanho que especificar para o computador virtual de destino, da seguinte forma:

        - Se o número de adaptadores de rede no computador de origem for menor ou igual ao número de placas permitida para o tamanho de máquina de destino, em seguida, o destino tem o mesmo número de placas como a origem de.
        - Se o número de adaptadores para a máquina de virtual origem excede o número permitido para o tamanho de destino, em seguida, é utilizado o valor máximo de tamanho de destino.
        - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho de máquina de destino suporta quatro, o computador de destino terá duas placas. Se o computador de origem tem duas placas, mas o tamanho de suportados de destino suporta apenas um computador de destino ter apenas uma placa de.     
        - Se a VM tem vários adaptadores de rede irá todos ligar à mesma rede.

    ![Activar a replicação](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.  Em **discos** pode ver o sistema operativo e os discos de dados na VM que vai ser replicada.



## <a name="step-7-test-your-deployment"></a>Passo 7: Testar a sua implementação

Para testar a implementação pode executar um teste activação pós-falha para uma máquina virtual única ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### <a name="prepare-for-failover"></a>Preparar para activação pós-falha

- Para executar um teste activação pós-falha que recomendamos que cria uma nova rede Azure que tem isoladas da sua rede de produção Azure (é comportamento predefinido quando cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre como executar activações pós-falha de teste.
- Para obter o melhor desempenho quando não é possível ao longo do Azure, instale o agente do Azure no computador protegido. Torna mais rápido de arranque e ajuda a resolução de problemas. Instale o agente [Linux](https://github.com/Azure/WALinuxAgent) ou [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) .
- Para testar totalmente a sua implementação tem uma infraestrutura para o computador de replicadas funcionar como esperado. Se pretende testar Active Directory e DNS pode criar uma máquina virtual como controlador de domínio com o DNS e criar uma réplica isto Azure utilizando Azure a recuperação do Site. Leia mais em [Considerações de activação pós-falha de teste do Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
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

- Adicionar um ponto final de público para o protocolo RDP (porta 3389) e especifique as credenciais de início de sessão.
- Certifique-se de que não tem qualquer políticas de domínio que o impedem de ligar a uma máquina de virtual utilizando um endereço público.
- Tente ligar. Se não conseguir ligar, certifique-se de que a VM está em execução. Para obter mais sugestões de resolução de problemas Leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Se pretender aceder a um VM Azure Linux a ser executado depois activação pós-falha a utilizar um seguro Shell cliente (ssh), faça o seguinte:

**No computador antes de activação pós-falha no local**:

- Certifique-se de que o serviço de Shell seguro na VM Azure está definido para iniciar automaticamente no arranque do sistema.
- Certifique-se de que as regras de firewall permitem uma ligação de SSH à mesma.

**No the Azure VM após activação pós-falha**:

- As regras de grupo de segurança de rede no Falha ao longo do VM e a sub-rede Azure a que está ligado necessário permitir ligações de entrada para a porta SSH.
- Deverá ser criado um ponto final de público para permitir ligações de entrada na porta SSH (porta TCP 22 por predefinição).
- Se a VM é acedida através de uma ligação VPN (encaminhar Express ou site para site VPN) o cliente pode ser utilizado para ligar diretamente para a VM em SSH.


### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

Para executar o teste activação pós-falha fazer o seguinte:

1. Falha ao longo de uma única VM nas **Definições** > **Itens de replicadas**, clique na VM > **+ teste activação pós-falha**.
2. Falha ao longo de um plano de recuperação, nas **Definições** > de**Recuperação de planos**, com o botão direito do plano > **Teste activação pós-falha**. Para criar uma recuperação planear [siga estas instruções](site-recovery-create-recovery-plans.md).

3. Na **Activação pós-falha de teste** selecione a rede Azure à qual Azure VMs ligar depois de activação pós-falha ocorre.
4. Clique em **OK** para começar a activação pós-falha. Pode controlar o progresso ao clicar em VM para abrir as respetivas propriedades ou a tarefa de **Teste activação pós-falha** nas **Definições** > **tarefas recuperação de sites**.
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


## <a name="monitor-your-deployment"></a>Monitorizar a sua implementação

Eis como pode monitorizar as definições de configuração, estado e estado de funcionamento para a sua implementação de recuperação de sites:

1. Clique no nome do cofre para aceder ao dashboard de **Essentials** . Neste dashboard pode tarefas, o estado de replicação, planos de recuperação, estado de funcionamento do servidor e eventos de recuperação de sites.  Pode personalizar Essentials para mostrar os mosaicos e os esquemas que são mais úteis para si, incluindo o estado dos outros cofres recuperação de sites e de cópia de segurança.

    ![Tópicos essenciais](./media/site-recovery-vmm-to-azure/essentials.png)

2. No mosaico do **Estado de funcionamento** pode monitorizar os servidores de site (VMM ou a configuração de servidores) que ocorrerem o problema e os eventos criados por recuperação de sites no últimas 24 horas.
3. Pode gerir e monitorize a replicação nos **Itens de replicadas**, **Recuperação de planos**, e mosaicos de **Tarefas recuperação de Site** . Pode agregar para tarefas nas **Definições** -> **tarefas** -> **Tarefas recuperação de Site**.


## <a name="next-steps"></a>Próximos passos

Após a sua implementação está configurado e em execução, [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de activação pós-falha.
