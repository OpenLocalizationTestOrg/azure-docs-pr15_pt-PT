<properties
    pageTitle="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para Azure | Microsoft Azure"
    description="Este artigo descreve como criar uma réplica máquinas virtuais de Hyper-V em anfitriões Hyper-V localizados na nuvens System Center VMM para Azure."
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
    ms.topic="hero-article"
    ms.date="05/06/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-azure.md)
- [PowerShell - Gestor de recursos](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Portal clássico](site-recovery-vmm-to-azure-classic.md)
- [PowerShell - clássico](site-recovery-deploy-with-powershell.md)



O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md).

## <a name="overview"></a>Descrição geral

Este artigo descreve como implementar recuperação de Site para criar uma réplica máquinas virtuais de Hyper-V em servidores de anfitriões Hyper-V que estão localizados no nuvens privadas de VMM para Azure.

O artigo inclui pré-requisitos para o cenário e mostra-lhe como configurar o Cofre recuperação de sites, obter o fornecedor de recuperação de Site Azure instalados no servidor VMM origem, registar o servidor no cofre, adicionar uma conta de armazenamento Azure, instalar o agente de serviços de recuperação do Azure em servidores de anfitriões Hyper-V, configurar definições de proteção para nuvens VMM que serão aplicados a todas as máquinas virtuais protegidas e, em seguida, ativar proteção para esses máquinas virtuais. Terminar a configuração testando a activação pós-falha para se certificar de que tudo está a funcionar como esperado.

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arquitetura

![Arquitetura](./media/site-recovery-vmm-to-azure-classic/topology.png)

- O fornecedor de recuperação do Azure Site estiver instalado no VMM durante a implementação de recuperação de Site e o servidor VMM está registado no cofre recuperação de sites. O fornecedor comunica com recuperação de Site para processar orchestration replicação.
- O agente de serviços de recuperação do Azure está instalado em servidores de anfitriões Hyper-V durante a implementação de recuperação de sites. Trata-replicação de dados ao armazenamento Azure.


## <a name="azure-prerequisites"></a>Pré-requisitos Azure

Eis o que vai precisar no Azure.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta Azure**| Terá de uma conta [Do Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**Armazenamento Azure** | Terá de uma conta de armazenamento Azure para armazenar dados replicados. Dados replicados são armazenados no Azure armazenamento e Azure VMs são fiadas para cima quando ocorre activação pós-falha. <br/><br/>É necessária uma [conta de armazenamento geo redundantes padrão](../storage/storage-redundancy.md#geo-redundant-storage). A conta tem na mesma região como o serviço de recuperação de sites e de estar associadas a mesma subscrição. Tenha em atenção que a replicação para contas de armazenamento premium atualmente não é suportada e não deveriam ser utilizados.<br/><br/>[Leia sobre](../storage/storage-introduction.md) Armazenamento Azure.
**Rede Azure** | Terá de uma rede virtual Azure que Azure VMs irá estabelecer ligação quando ocorre activação pós-falha. A rede virtual Azure tem de ser na mesma região como cofre recuperação de sites.

## <a name="on-premises-prerequisites"></a>Pré-requisitos de no local

Eis o que vai precisar no local.

**Pré-requisito** | **Detalhes**
--- | ---
**VMM** | Terá de pelo menos uma VMM servidor implementado como um servidor físico ou virtual autónomo ou como um cluster virtual. <br/><br/>O servidor VMM deve estar em execução sistema Centro 2012 R2 com atualizações acumulativas mais recentes.<br/><br/>Terá de pelo menos uma nuvem configurada no servidor de VMM.<br/><br/>Nuvem de origem que pretende proteger tem de conter um ou mais grupos de anfitrião VMM.<br/><br/>Saiba mais sobre a configuração de nuvens VMM no [Tutorial: criar nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) no blogue de Keith Mayer.
**Hyper-V** | Irá precisar de uma ou mais servidores de anfitrião de Hyper-V ou clusters na nuvem VMM. O servidor de anfitrião deve ter e VMs um ou mais. <br/><br/>O servidor de Hyper-V deve estar em execução pelo menos o **Windows Server 2012 R2** com a função Hyper-V ou **Microsoft Hyper-V Server 2012 R2** e tem instalado as atualizações mais recentes.<br/><br/>Qualquer Hyper-V server que contém VMs que pretende proteger têm de estar localizado na nuvem VMM.<br/><br/>Se estiver a executar Hyper-V numa nota cluster esse corretor cluster não é automaticamente criada se tiver um cluster de baseados em endereços IP estático. Terá de configurar o corretor de cluster manualmente. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) na entrada de blogue do Aidan Finn.
**Máquinas protegidas** | VMs que pretende proteger devem cumprir os [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## <a name="network-mapping-prerequisites"></a>Pré-requisitos de mapeamento de rede
Quando proteger máquinas virtuais nos mapas de mapeamento de rede Azure entre redes VM no servidor VMM de origem e destino Azure redes para ativar o seguinte:

- Todos os computadores que activação pós-falha na mesma rede pode ligar umas às outras, independentemente que plano de recuperação estiverem na.
- Se um gateway de rede está configurada no destino rede Azure, máquinas virtuais pode ligar a outras máquinas virtuais no local.
- Se não configurar rede mapeamento apenas máquinas virtuais que falhar ao longo no plano de recuperação mesmo poderão ligar umas às outras depois activação pós-falha ao Azure.

Se pretende implementar o mapeamento de rede terá o seguinte procedimento:

- As máquinas virtuais que pretende proteger do servidor de origem VMM deve estar ligadas a uma rede VM. Essa rede deve ser ligada a uma rede lógica que está associada a nuvem.
- Uma rede Azure à qual pode ligar replicadas máquinas virtuais após activação pós-falha. Irá Selecione esta rede no momento da activação pós-falha. A rede deve estar na mesma região como a sua subscrição de recuperação de sites do Azure.

Prepare para o mapeamento de rede, da seguinte forma:

1. [Leia sobre](site-recovery-network-mapping.md) requisitos de mapeamento de rede.
2. Prepare redes VM VMM:

    - [Configurar o redes lógicas](https://technet.microsoft.com/library/jj721568.aspx).
    - [Configurar o redes VM](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>Passo 1: Criar um cofre de recuperação de sites

1. Inicie sessão no [Portal de gestão](https://portal.azure.com) do servidor VMM que deseja registar.
2. Clique em **Serviços de dados** > **Serviços de recuperação** > **Cofre de recuperação do Site**.
3. Clique em **Criar novo** > **criação rápida**.
4. Em **nome**, introduza um nome amigável para identificar o cofre.
5. Na **região**, selecione a região geográfica para o cofre. Para verificar regiões suportadas, consulte geográficos disponibilidade no [Azure Site recuperação preços detalhes](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Clique em **Criar cofre**.

    ![Novo cofre](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Verifique a barra de estado para confirmar que o Cofre foi criado com êxito. O Cofre irá estar listado como **ativa** na página principal de serviços de recuperação.

## <a name="step-2-generate-a-vault-registration-key"></a>Passo 2: Gerar uma chave de registo do Cofre

Gere uma chave de registo no cofre. Depois de transferir o fornecedor de recuperação de Site do Azure e instalá-lo no servidor VMM, irá utilizar esta chave para registar o servidor VMM no cofre.

1. Na página de **Serviços de recuperação** , clique no cofre para abrir a página de início rápido. Guia de introdução do também pode ser aberto em qualquer altura utilizando o ícone.

    ![Guia de introdução do ícone](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)

2. Na lista pendente, selecione **entre um site VMM no local e do Microsoft Azure**.
3. Em **Preparar servidores VMM**, clique em ficheiro de **Gerar chave de registo** . O ficheiro de chave é gerado automaticamente e é válido para 5 dias após é gerado. Se não está a aceder ao portal do Azure do servidor VMM que terá de copiar este ficheiro no servidor.

    ![Chave de registo](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Passo 3: Instalar o fornecedor de recuperação de Azure Site

1. **Guia de** > **VMM preparar servidores**, clique em **Transferir Microsoft Azure recuperação fornecedor de sites para a instalação em servidores VMM** para obter a versão mais recente do ficheiro de instalação do fornecedor.
2. Execute este ficheiro no servidor VMM de origem.

    >[AZURE.NOTE] Se VMM é implementado num cluster e estiver a instalar o fornecedor para a primeira vez instalá-la um nó activo e concluir a instalação para registar o servidor VMM no cofre. Em seguida, instale o fornecedor nos outros nós. Tenha em atenção que, se estiver a atualizar o fornecedor terá de atualizar em todos os nós porque estes devem todos estar em execução a mesma versão do fornecedor.

3. O instalador faz uma verificação de prerequirements e os pedidos de permissão para parar o serviço VMM para começar a configuração do fornecedor. O serviço de VMM vai ser reiniciado automaticamente quando termina de configuração. Se estiver a instalar num será solicitado para parar a função de Cluster de computação VMM.

4. No **Microsoft Update** pode optar pelas atualizações. Com esta definição ativada fornecedor actualizações serão instaladas de acordo com a política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-to-azure-classic/updates.png)


5.  A localização de instalação para o fornecedor de está definida para ** <SystemDrive>\Programas\Microsoft sistema Centro 2012 R2\Virtual máquina Manager\bin**. Clique em **instalar**.

    ![InstallLocation](./media/site-recovery-vmm-to-azure-classic/install-location.png)

6. Depois do fornecedor é instalado clique em **Registe-se** para registar o servidor no cofre.

    ![InstallComplete](./media/site-recovery-vmm-to-azure-classic/install-complete.png)

9. Em **nome do cofre**, verifique se o nome do cofre na qual será registado no servidor. Clique em *seguinte*.

    ![Registo do servidor](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)

7. Na **Ligação à Internet** especifique a forma como o fornecedor executado no servidor do VMM se liga à Internet. Selecione **ligar-se com as definições de proxy existente** para utilizar as predefinições de ligação à Internet configuradas no servidor.

    ![Definições da Internet](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

    - Se pretender utilizar um proxy personalizado que deve configurá-lo antes de instalar o fornecedor. Quando configurar as definições de proxy personalizado será executado um teste para verificar a ligação de proxy.
    - Se utilizar um proxy personalizado ou o proxy predefinido requer autenticação que irá necessitar de introduzir os detalhes de proxy, incluindo o endereço proxy e a porta.
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

11.  Em **nome de servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Na configuração de cluster especifique o nome da função cluster VMM.
12.  Em **sincronizar na nuvem metadados** selecionar se pretende sincronizar metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só tem de ocorrer uma vez por cada servidor. Se não pretender sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades na nuvem na consola do VMM.

13.  Clique em **seguinte** para concluir o processo. Após o registo, metadados do servidor VMM são obtido por Azure recuperação de sites. O servidor é apresentado no separador **Servidores VMM** na página **Servers** no cofre.

    ![LastPage](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

Após o registo, metadados do servidor VMM são obtido por Azure recuperação de sites. O servidor é apresentado no separador **Servidores VMM** na página **Servers** no cofre.

### <a name="command-line-installation"></a>Instalação de linha de comandos

Também pode ser instalado o fornecedor de recuperação de Site do Azure utilizando a linha de comandos seguinte. Este método pode ser utilizado para instalar o fornecedor num servidor principais para o Windows Server 2012 R2.

1. Transferir a chave de ficheiro e registo de instalação do fornecedor para uma pasta. Por exemplo: C:\ASR.
2. Parar o serviço de Gestor de Máquina Virtual do Centro de sistema
3. A partir de uma linha de comandos elevada extrai o instalador do fornecedor com estes comandos:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale o fornecedor da seguinte forma:

        C:\ASR> setupdr.exe /i

5. Registe o fornecedor da seguinte forma:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Onde parâmetros são da seguinte forma:

 - **/Credentials** : parâmetro obrigatório que especifica a localização na qual está localizado o ficheiro de chave de registo  
 - **/FriendlyName** : parâmetro obrigatório para o nome do servidor de anfitrião Hyper-V que aparece no portal do Azure recuperação de sites.
 - **/EncryptionEnabled** : parâmetro opcional para especificar se pretende encriptação máquinas virtuais no Azure (na encriptação resto). O nome de ficheiro deve ter uma extensão **. pfx** .
 - **/proxyAddress** : parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyport** : parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername** : parâmetro opcional que especifica o nome de utilizador de proxy.
 - **/proxyPassword** : parâmetro opcional que especifica a palavra-passe de proxy.  


## <a name="step-4-create-an-azure-storage-account"></a>Passo 4: Criar uma conta de armazenamento Azure

1. Se não tiver uma conta de armazenamento Azure clique em **Adicionar uma conta de armazenamento do Azure** para criar uma conta.
2. Crie uma conta com geo-replicação ativado. Tem na mesma região como o serviço de recuperação de sites do Azure e ser associadas a mesma subscrição.

    ![Conta de armazenamento](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [AZURE.NOTE] [Migração de contas de armazenamento de](../resource-group-move-resources.md) grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportada para contas de armazenamento utilizadas para implementar o recuperação de sites.

## <a name="step-5-install-the-azure-recovery-services-agent"></a>Passo 5: Instalar o agente de serviços de recuperação Azure

Instale o agente de serviços de recuperação do Azure num servidor de anfitrião cada Hyper-V na nuvem VMM.

1. Clique em **Guia** > **Transferir Azure Site serviços agente de recuperação e instale em anfitriões** para obter a versão mais recente do ficheiro de instalação agente.

    ![Instalar o agente de recuperação de serviços](./media/site-recovery-vmm-to-azure-classic/install-agent.png)

2. Execute o ficheiro de instalação num servidor de anfitrião cada Hyper-V.
3. Na página de **Pré-requisitos de verificar** , clique em **seguinte**. Qualquer pré-requisitos em falta serão instalados automaticamente.

    ![Agente de serviços de recuperação de pré-requisitos](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)

4. Na página **Definições de instalação** , especificar onde pretende instalar o agente e selecione a localização da cache na qual serão instalado metadados cópia de segurança. Em seguida, clique em **instalar**.
5. Após a instalação concluir clique em **Fechar** para concluir o assistente.

    ![Registe-se MARTE agente](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Instalação de linha de comandos

Também pode instalar o agente de serviços de recuperação do Microsoft Azure da linha de comandos, utilizar este comando:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>Passo 6: Configurar nuvem definições de proteção

Depois do servidor VMM está registado, pode configurar definições de proteção de nuvem. Ativar a opção **sincronizar dados do nuvem com o Cofre** quando instalou o fornecedor de modo que todas as nuvens no servidor VMM apareça no separador <b>Itens protegida</b> no cofre.

![Nuvem publicada](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. Na página de início rápido, clique em **Configurar proteção para nuvens VMM**.
2. No separador **Itens protegido** , clique na nuvem que pretende configurar e aceda ao separador **configuração** .
3. Em **destino** , selecione **Azure**.
4. Na **Conta de armazenamento** , selecione a conta de armazenamento Azure que utiliza para a replicação.
5. Defina a **encriptar dados armazenados** para **desativar**. Esta definição especifica que devem ser encriptados dados replicada entre o site no local e o Azure.
6. Em **Copiar frequência** deixe a predefinição. Este valor Especifica a frequência de dados devem ser sincronizados entre localizações de origem e destino.
7. Em **pontos de recuperação de reter para**, deixe a predefinição. Com um valor predefinido de zero, apenas o ponto de recuperação mais recente para uma máquina virtual primária é armazenado num servidor réplica anfitrião.
8. **Frequência de instantâneos aplicação consistentes**, deixe a predefinição. Este valor Especifica com que frequência deve criar instantâneos. Instantâneos utilizam o serviço de cópia de sombra de Volume (VSS) para se certificar de que aplicações estão num estado consistente quando o instantâneo é disponibilizado.  Se definir um valor, certifique-se de é menor que o número de pontos de recuperação adicionais que configura.
9. Em **hora de início de replicação**, especifique quando replicação inicial de dados para Azure deverá começar. O fuso horário do servidor de anfitrião Hyper-V será utilizado. Recomendamos que agende a replicação inicial horas de expediente.

    ![Definições de replicação na nuvem](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

Depois de guardar as definições de uma tarefa será criada e pode ser monitorizada no separador **tarefas** . Todos os servidores de anfitrião de Hyper-V na nuvem VMM origem serão configurados para a replicação.

Depois de guardar, definições de nuvem podem ser modificadas no separador **Configurar** . Para modificar a localização de destino ou a conta de armazenamento de destino terá de remover a configuração na nuvem e, em seguida, reconfigurar na nuvem. Tenha em atenção que se alterar a conta de armazenamento a alteração só é aplicada para máquinas virtuais que estejam ativadas para protecção depois de terem sido modificada a conta de armazenamento. Máquinas virtuais existentes não são migradas para a nova conta de armazenamento.

## <a name="step-7-configure-network-mapping"></a>Passo 7: Configurar mapeamento de rede
Antes de começar mapeamento de rede Certifique-se de que máquinas virtuais do servidor de origem VMM estão ligadas a uma rede VM. Além disso, crie um ou mais redes virtuais Azure. Tenha em atenção que múltiplas redes VM podem ser mapeadas para uma única rede Azure.

1. Na página de início rápido, clique em **mapear redes**.
2. No separador **redes** , numa **localização de origem**, selecione o servidor de VMM de origem. Na **localização de destino** selecione Azure.
3. Em redes de **origem de** uma lista de redes VM associados ao servidor VMM são apresentados. Em redes de **destino** , são apresentadas as redes Azure associadas à subscrição.
4. Selecione a rede VM de origem e clique em **mapa**.
5. Na página **selecionar uma rede de destino** , selecione o destino Azure rede que pretende utilizar.
6. Clique na marca de verificação para concluir o processo de mapeamento.

    ![Definições de replicação na nuvem](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

Depois de guardar as definições de uma tarefa é iniciada controlar o progresso de mapeamento e podem ser monitorizada no separador tarefas. Qualquer máquinas de virtuais réplica existente que correspondem à rede VM de origem serão ligadas ao destino Azure redes. Novas máquinas virtuais que estão ligadas à rede VM de origem serão ligadas à rede Azure mapeadas após a replicação. Se modificar um mapeamento de com uma nova rede existente, máquinas virtuais de réplica serão ligadas utilizando as novas definições.

Tenha em atenção que, se a rede de destino tem várias sub-redes e uma desses sub-redes tem o mesmo nome como sub-rede no qual se encontra a máquina virtual da origem, em seguida, a máquina de virtual réplica será ligada nessa sub-rede de destino depois activação pós-falha. Se não existirem sem sub-rede destino com um nome correspondente, a máquina virtual será ligada para a primeira sub-rede na rede.

> [AZURE.NOTE] [Migração das redes](../resource-group-move-resources.md) através de grupos de recursos dentro da mesma subscrição ou entre subscrições não é suportado para as redes utilizadas para implementar o recuperação de sites.

## <a name="step-8-enable-protection-for-virtual-machines"></a>Passo 8: Ativar proteção para máquinas virtuais

Depois de servidores, nuvens e redes estão configurados corretamente, pode ativar a proteção por para máquinas virtuais na nuvem. Tenha em atenção o seguinte procedimento:

- Máquinas virtuais tem de cumprir [requisitos Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Para ativar a proteção do sistema operativo e o sistema operativo propriedades do disco tem de estar definidas para a máquina virtual. Quando cria uma máquina virtual no VMM através de um modelo de máquina virtual pode definir a propriedade. Também pode definir estas propriedades para máquinas virtuais existentes nos separadores **gerais** e de **Configuração do Hardware** das propriedades máquina virtual. Se não definir estas propriedades no VMM poderá configurá-los no portal do Azure recuperação de sites.

    ![Criar máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![Modificar as propriedades de máquina virtual](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)


1. Para ativar a proteção, no separador **máquinas virtuais** na nuvem na qual a máquina virtual está localizada, clique em **Ativar proteção** > **Adicionar máquinas virtuais**.
2. Na lista de máquinas virtuais na nuvem, selecione aquele que pretende proteger.

    ![Ativar a proteção de máquina virtual](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    Controlar o progresso da ação **Ativar proteção** no separador **tarefas** , incluindo a replicação inicial. Após a tarefa de **Proteção finalizar** é executada a máquina virtual está pronta para activação pós-falha. Depois de proteção está ativada e máquinas virtuais são replicadas, poderá vê-los no Azure.


    ![Tarefa de proteção de máquina virtual](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

3. Verifique se as propriedades de máquina virtual e modificar conforme necessário.

    ![Verifique se máquinas virtuais](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)


4. No separador **Configurar** as propriedades de máquina virtual seguintes propriedades de rede podem ser modificadas.





- **Número de adaptadores de rede na máquina virtual destino** - o número de adaptadores de rede é ditado pelo tamanho especificado para o destino virtual machine. Verifique se [especificações de tamanho de máquina virtual](../virtual-machines/virtual-machines-linux-sizes.md#size-tables) para o número de placas suportadas pelo tamanho de máquina virtual. Quando modificar o tamanho para uma máquina virtual e guardar as definições, irá alterar o número de adaptador de rede ao abrir **Configurar** página da próxima vez. O número de adaptadores de rede do máquinas virtuais de destino é o número mínimo de adaptadores de rede na máquina de virtual de origem e o número máximo de adaptadores de rede suportados pelo tamanho da máquina virtual escolhido, da seguinte forma:

    - Se o número de adaptadores de rede no computador de origem for menor ou igual ao número de placas permitida para o tamanho de máquina de destino, em seguida, o destino tem o mesmo número de placas como a origem de.
    - Se o número de adaptadores para a máquina de virtual origem excede o número permitido para o tamanho de destino, em seguida, o número máximo de tamanho de destino será utilizado.
    - Por exemplo, se uma máquina de origem tem dois adaptadores de rede e o tamanho de máquina de destino suporta quatro, a máquina de destino tem duas placas. Se o computador de origem tem duas placas, mas o tamanho de suportados de destino suporta apenas um computador de destino ter apenas uma placa de.    

- **Rede da máquina virtual destino** - a rede para o qual a máquina virtual liga-se ao é determinada pela mapeamento de rede da rede de máquina de virtual de origem. Se a máquina virtual de origem tem mais do que uma placa de rede e redes de origem são mapeados para redes diferentes no destino, em seguida, terá escolher entre uma das redes de destino.
- **Sub-rede de cada adaptador de rede** - para cada adaptador de rede pode selecionar a sub-rede à qual a falha ao longo de máquina virtual seria ligar a.
- **Endereço IP de destino** - se a placa de rede de máquina de virtual origem estiver configurada para utilizar um endereço IP estático, em seguida, pode fornecer o endereço IP para o destino virtual machine. Utilizar esta funcionalidade Reter o endereço IP de uma máquina de virtual origem após uma activação pós-falha. Não se for fornecido nenhum endereço IP qualquer endereço IP disponível é dado à placa de rede no momento da activação pós-falha. Se o endereço de IP de destino for especificado mas já está a ser utilizado por outra máquina virtual em execução no Azure activação pós-falha irá falhar.  

    ![Modificar propriedades da rede](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

>[AZURE.NOTE] Máquinas virtuais de Linux com endereço IP estático não são suportadas.

## <a name="test-the-deployment"></a>Testar a implementação

Para testar a sua implementação pode executar um teste activação pós-falha para uma única máquina virtual, ou criar um plano de recuperação que consiste várias máquinas virtuais e executa um teste activação pós-falha para o plano.  

Teste activação pós-falha simula seu mecanismo activação e recuperação numa rede isolada. Tenha em atenção que:

- Se pretende ligar a máquina virtual no Azure utilizando o ambiente de trabalho remoto após a activação pós-falha, ative ligação de ambiente de trabalho remoto na máquina virtual antes de executar o teste activação pós-falha.
- Depois de activação pós-falha que irá utilizar um endereço IP público para ligar à máquina virtual no Azure utilizando o ambiente de trabalho remoto. Se quiser fazer isto, certifique-se de que não tem qualquer políticas de domínio que o impedem de ligar a uma máquina de virtual utilizando um endereço público.

>[AZURE.NOTE] Para obter o melhor desempenho quando fizer uma activação pós-falha ao Azure, certifique-se de que instalou o agente do Azure na máquina protegida. Isto ajuda-o no arranque mais rápido e também ajuda no diagnóstico em caso de problemas. Agente de Linux pode ser encontrado [aqui](https://github.com/Azure/WALinuxAgent) - e Windows agente pode ser encontrada [aqui](http://go.microsoft.com/fwlink/?LinkID=394789)

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No separador **Recuperação de planos** , adicione um novo plano. Especifique um nome, **VMM** no **tipo de origem**, e o servidor de origem VMM na **origem**, o destino será Azure.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)

2. Na página **Selecionar máquinas virtuais** , selecione máquinas virtuais para adicionar o plano de recuperação. Estes máquinas virtuais são adicionadas ao grupo predefinido plano recuperação — grupo 1. Um máximo de 100 máquinas virtuais num plano de recuperação única foram testadas.

- Se pretender verificar as propriedades de máquina virtual antes de os adicionar ao plano, clique na máquina virtual na página Propriedades da nuvem no qual do localizada. Também pode configurar as propriedades de máquina virtual na consola do VMM.
- Todas as máquinas virtuais que são apresentadas foram ativadas para protecção. A lista inclui ambas as máquinas virtuais que estejam ativadas para protecção e a replicação inicial foi concluída e aqueles que executam o protection com inicial replicação pendentes. Apenas máquinas virtuais com inicial replicação foi concluída pode falhar sobre como parte de um plano de recuperação.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

Depois de um plano de recuperação ter sido criado-aparece no separador **Planos de recuperação** . Também pode adicionar [Automatização Azure runbooks](site-recovery-runbook-automation.md) para o plano de recuperação para automatizar acções durante activação pós-falha.

### <a name="run-a-test-failover"></a>Executar um teste activação pós-falha

Existem duas formas de executar um teste activação pós-falha ao Azure.

- **Testar activação pós-falha sem uma rede Azure**— este tipo de teste activação pós-falha verifica que a máquina virtual surgir corretamente no Azure. A máquina virtual não estar ligada à rede qualquer Azure após activação pós-falha.
- **Testar activação pós-falha com uma rede Azure**— este tipo de activação pós-falha verificações que o ambiente de replicação inteira vem até como esperado e que falhou sobre as máquinas virtuais será ligado ao destino especificado Azure rede. Para processamento de sub-rede, para o teste activação pós-falha sub-rede da máquina virtual teste irá estar familiarizado com base na sub-rede da máquina réplica virtual. Este é diferente para a replicação normal quando sub-rede de uma máquina de virtual réplica baseia-se na sub-rede da máquina virtual da origem.

Se pretender executar um teste activação pós-falha para uma máquina de virtual ativada para a proteção contra para Azure sem especificar uma rede de destino Azure não terá de se preparar nada. Para executar um teste activação pós-falha com um alvo de rede Azure terá de criar uma nova rede Azure que tem isoladas da sua rede de produção Azure (comportamento predefinido quando cria uma nova rede no Azure). Veja como [executar uma activação de teste pós-falha](site-recovery-failover.md#run-a-test-failover) para obter mais detalhes.


Também terá de configurar a infraestrutura de replicadas máquina virtual para funcionar como esperado. Por exemplo, uma máquina de virtual com o controlador de domínio e DNS podem ser replicada para Azure utilizando a recuperação de Site do Azure e podem ser criada à rede de teste utilizando testar activação pós-falha. Observe a secção [Testar activação pós-falha considerações para o active directory](site-recovery-active-directory.md#considerations-for-test-failover) para obter mais detalhes.

Para executar uma teste activação pós-falha o seguinte:

1. No separador **Recuperação de plano** , selecione o plano e clique em **Testar activação pós-falha**.
2. Na página **Activação pós-confirmar teste falha** selecione **nenhum** ou uma rede Azure específica.  Tenha em atenção que, se selecionar nenhuma o teste activação pós-falha vai verificar que a máquina virtual corretamente replicadas para Azure mas não verifica a configuração de rede de replicação.

    ![Nenhuma rede](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)

3. Se a encriptação de dados estiver ativada para a nuvem, na **Chave de encriptação** , selecione o certificado que foi emitido durante a instalação do fornecedor no servidor VMM, quando a opção para ativar a encriptação de dados para uma nuvem ser ativado.
4. No separador **tarefas** pode controlar o progresso de activação pós-falha. Também deverá conseguir ver a réplica de teste de máquina virtual no portal do Azure. Se estiver a definir até máquinas virtuais de acesso da sua rede no local pode iniciar uma ligação de ambiente de trabalho remoto para a máquina virtual.
5. Quando a activação pós-falha atinge a fase de **testes concluída** , clique em **Testar concluído** para concluir o teste activação pós-falha. Pode agregar para baixo para o separador de **tarefa** para controlar o progresso de activação pós-falha e o estado e para realizar ações que são necessários.
6. Depois de activação pós-falha poderá ver a réplica de teste de máquina virtual no portal do Azure. Se estiver a definir até máquinas virtuais de acesso da sua rede no local pode iniciar uma ligação de ambiente de trabalho remoto para a máquina virtual. Faça o seguinte:

    1. Certifique-se de que as máquinas virtuais inicie com êxito.
    2. Se pretende ligar a máquina virtual no Azure utilizando o ambiente de trabalho remoto após a activação pós-falha, ative ligação de ambiente de trabalho remoto na máquina virtual antes de executar o teste activação pós-falha. Também terá de adicionar um ponto final RDP na máquina virtual. Pode tirar partido uma [Azure automatização Runbooks](site-recovery-runbook-automation.md) para o fazer.
    3. Depois de activação pós-falha se utilizar um endereço IP público para ligar à máquina virtual no Azure utilizando o ambiente de trabalho remoto, certifique-se não tem qualquer políticas de domínio que o impedem de ligar a uma máquina de virtual utilizando um endereço público.

7.  Depois da testar estiver concluída, faça o seguinte:
    - Clique na **activação de teste pós-falha está concluída**. Limpe o ambiente de teste para desligar e eliminar as máquinas virtuais do teste automaticamente.
    - Clique em **notas** para gravar e guardar quaisquer observações associadas a activação de teste pós-falha.

>

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [como configurar os planos de recuperação](site-recovery-create-recovery-plans.md) e [activação pós-falha](site-recovery-failover.md).
