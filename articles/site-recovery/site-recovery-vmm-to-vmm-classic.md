<properties
    pageTitle="Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para um site VMM secundário | Microsoft Azure"
    description="Este artigo descreve como criar uma réplica Hyper-V VMs no nuvens VMM a um site VMM secundário com a recuperação de Site do Azure."
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

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Criar uma réplica Hyper-V máquinas virtuais nuvens VMM para um site VMM secundário

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-vmm-to-vmm.md)
- [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestor de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md)

## <a name="overview"></a>Descrição geral

Este artigo descreve como criar uma réplica máquinas virtuais de Hyper-V em servidores de anfitriões de Hyper-V são geridos na nuvens VMM secundário VMM site com Azure recuperação de sites.

O artigo inclui pré-requisitos, mostra-lhe como configurar cofre recuperação de sites, instale o fornecedor de recuperação de Site do Azure origem e VMM servidores de destino, registar os servidores no cofre, configurar definições de proteção para nuvens VMM e, em seguida, ativar proteção para VMs Hyper-V. Terminar a configuração testando a activação pós-falha para se certificar de que tudo está a funcionar como esperado.

Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arquitetura

A imagem abaixo mostra os canais de comunicação diferentes e portas utilizadas pelo Azure recuperação de sites para orchestration e a replicação

![E2E topologia](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>Antes de começar

Certifique-se de que tem estes pré-requisitos num local:

**Pré-requisitos** | **Detalhes**
--- | ---
**Azure**| É necessária uma conta do [Microsoft Azure](https://azure.microsoft.com/) . Pode começar com uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre preços de recuperação de sites.
**VMM** | Precisa de pelo menos um servidor VMM.<br/><br/>O servidor VMM deve estar em execução, pelo menos, sistema Centro 2012 SP1 com atualizações acumulativas mais recentes.<br/><br/>Se pretender configurar proteção com um único servidor VMM, terá de nuvens, pelo menos, duas configurados no servidor de.<br/><br/>Se pretender implementar proteção com dois servidores VMM, cada servidor tem de ter pelo menos uma nuvem configurado no servidor de VMM principal que pretende proteger e uma nuvem configurado no servidor de VMM secundário que pretende utilizar para a proteção e recuperação<br/><br/>Todas as nuvens VMM tem de ter o perfil da capacidade de Hyper-V definir.<br/><br/>Nuvem de origem que pretende proteger tem de conter um ou mais grupos de anfitrião VMM.<br/><br/>Saiba mais sobre a configuração de nuvens VMM no [Tutorial: criar nuvens privadas com System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) no blogue de Keith Mayer.
**Hyper-V** | Precisa de um ou mais servidores de anfitrião de Hyper-V dos grupos de anfitrião VMM principais e secundários e um ou mais máquinas virtuais num servidor de anfitrião cada Hyper-V.<br/><br/>Os servidores de Hyper-V anfitrião e de destino tem de ter, pelo menos, Windows Server 2012, com a função Hyper-V e tem instalado as atualizações mais recentes.<br/><br/>Qualquer Hyper-V server que contém VMs que pretende proteger têm de estar localizado na nuvem VMM.<br/><br/>Se estiver a executar o Hyper-V num cluster, tenha em atenção que corretor cluster não é criada automaticamente se tiver um cluster de baseados em endereços IP estático. Tem de configurar o corretor de cluster manualmente. [Saiba mais](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) na entrada de blogue do Aidan Finn.
**Mapeamento de rede** | Pode configurar mapeamento de rede para se certificar de que máquinas virtuais replicadas optimizada são colocadas em servidores de anfitriões Hyper-V secundários após activação pós-falha e que pode ligar a redes VM adequados. Se não configurar mapeamento de rede, réplica VMs não estar ligada a quaisquer rede após activação pós-falha.<br/><br/>Para configurar o mapeamento de rede durante a implementação, certifique-se de que as máquinas virtuais do servidor de origem Hyper-V anfitrião estão ligadas a uma rede VMM VM. Essa rede deve ser ligada a uma rede lógica que está associada a nuvem. < br /<br/>Nuvem de destino no servidor VMM secundário que utilizar para a recuperação deve ter uma rede VM correspondente configurada e, por sua vez deverá ser ligado a uma rede lógica correspondente que está associada a nuvem de destino.<br/><br/>[Saiba mais](site-recovery-network-mapping.md) sobre mapeamento de rede.
**Mapeamento de armazenamento** | Por predefinição quando replica uma máquina virtual num servidor origem Hyper-V anfitrião para um servidor de anfitrião de Hyper-V de destino, dados replicados são armazenados na localização predefinida que está indicada para o anfitrião de Hyper-V de destino no Gestor de Hyper-V. Para ter mais controlo sobre a localização onde armazenou dados replicados, pode configurar mapeamento de armazenamento<br/><br/> Para configurar o mapeamento de armazenamento, precisa de configurar classificações de armazenamento na origem e destino servidores VMM antes de começar a implementação. [Saiba mais](site-recovery-storage-mapping.md).


## <a name="step-1-create-a-site-recovery-vault"></a>Passo 1: Criar um cofre de recuperação de sites

1. Inicie sessão no [Portal de gestão](https://portal.azure.com) do servidor VMM que deseja registar.

2. Expandir a **Serviços de dados** > **Serviços de recuperação** e clique em **Site recuperação cofre**.

3. Clique em **Criar novo** > **criação rápida**.

4. Em **nome**, introduza um nome amigável para identificar o cofre.

5. Na **região** , selecione a região geográfica para o cofre. Para verificar regiões suportadas, consulte geográficos disponibilidade no [Azure Site recuperação preços detalhes](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Clique em **Criar cofre**.

    ![Criar Cofre](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

Dar entrada a barra de estado que foi criado o cofre. O Cofre irá estar listado como **ativa** na página principal de serviços de recuperação.

## <a name="step-2-generate-a-vault-registration-key"></a>Passo 2: Gerar uma chave de registo do Cofre

Gere uma chave de registo no cofre. Depois de transferir o fornecedor de recuperação de Site do Azure e instalá-lo no servidor VMM, irá utilizar esta chave para registar o servidor VMM no cofre.

1. Na página de **Serviços de recuperação** , clique no cofre para abrir a página de início rápido. Guia de introdução do também pode ser aberto em qualquer altura utilizando o ícone.

    ![Guia de introdução do ícone](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. Na lista pendente, selecione **entre dois locais VMM no local**.
3. Em **Preparar servidores VMM**, clique em **ficheiro de chave de registo de gerar**. O ficheiro de chave é gerado automaticamente e é válido para 5 dias após é gerado. Se estiver a não a aceder ao portal do Azure do servidor VMM tem de copiar este ficheiro no servidor.

    ![Chave de registo](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Passo 3: Instalar o fornecedor de recuperação de Azure Site

4. Na página **Guia** , em **servidores VMM preparar**, clique em **Transferir Microsoft Azure recuperação fornecedor de sites para a instalação em servidores VMM** para obter a versão mais recente do ficheiro de instalação do fornecedor.

2. Execute este ficheiro no servidor VMM de origem.

    >[AZURE.NOTE] Se VMM é implementado num cluster e estiver a instalar o fornecedor para a primeira vez instalá-la um nó activo e concluir a instalação para registar o servidor VMM no cofre. Em seguida, instale o fornecedor nos outros nós. Tenha em atenção que se estiver a atualizar o fornecedor tem de atualizar em todos os nós porque estes devem todos estar em execução a mesma versão do fornecedor.

3. O instalador do faz alguns permissão de pedidos e **Verificar a pré-requisitos** para parar o serviço VMM para começar a configuração do fornecedor. O serviço de VMM vai ser reiniciado automaticamente quando termina de configuração. Se estiver a instalar num será solicitado para parar a função de Cluster de computação VMM.

4. No **Microsoft Update** pode optar pelas atualizações. Com esta definição ativada fornecedor actualizações serão instaladas de acordo com a política do Microsoft Update.

    ![Atualizações da Microsoft](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. A localização de instalação está definida para ** <SystemDrive>\Programas\Microsoft sistema Centro 2012 R2\Virtual máquina Manager\bin**. Clique no botão instalar para iniciar a instalação do fornecedor.

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. Depois do fornecedor é instalado clique em **Registe-se** para registar o servidor no cofre.

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
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

11.  Em **nome de servidor**, especifique um nome amigável para identificar o servidor VMM no cofre. Na configuração de cluster especifique o nome da função cluster VMM.
12.  Em **sincronizar na nuvem metadados** selecionar se pretende sincronizar metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só tem de ocorrer uma vez por cada servidor. Se não pretender sincronizar todas as nuvens, pode deixar esta definição desmarcada e sincronizar cada nuvem individualmente nas propriedades na nuvem na consola do VMM.

13.  Clique em **seguinte** para concluir o processo. Após o registo, metadados do servidor VMM são obtido por Azure recuperação de sites. O servidor é apresentado nos **Servidores VMM** > **servidores** no cofre.

    ![Servidores de](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>Instalação de linha de comandos

Também pode ser instalado o fornecedor de recuperação de Site do Azure da linha de comandos. Este método pode ser utilizado para instalar o fornecedor num servidor principais para o Windows Server 2012 R2.

1. Transferir a chave de ficheiro e registo de instalação do fornecedor para uma pasta. Por exemplo C:\ASR.
2. Parar o serviço de Gestor de Máquina Virtual do Centro de sistema
3. Extrai o instalador do fornecedor ao executar estes comandos a partir de uma linha de comandos com privilégios de **administrador** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale o fornecedor ao executar:

        C:\ASR> setupdr.exe /i

5. Registe-se ao executar o fornecedor de:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Onde estão os parâmetros:

 - **/Credentials**: parâmetro obrigatório que especifica a localização na qual está localizado o ficheiro de chave de registo  
 - **/FriendlyName**: parâmetro obrigatório para o nome do servidor de anfitrião Hyper-V que aparece no portal do Azure recuperação de sites.
 - **/EncryptionEnabled**: parâmetro opcional que tem de utilizar apenas em VMM Azure cenário, se precisar de encriptação do seu máquinas virtuais em repouso no Azure. Certifique-se de que o nome do ficheiro que fornece tem a extensão **. pfx** .
 - **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
 - **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
 - **/proxyUsername**: parâmetro opcional que especifica o nome de utilizador do Proxy (se proxy requer autenticação).
 - **/proxyPassword**: parâmetro opcional que especifica a palavra-passe para autenticar com o servidor de proxy (se proxy requer autenticação).  

## <a name="step-4-configure-cloud-protection-settings"></a>Passo 4: Configurar nuvem definições de proteção

Depois de servidores de VMM estão registados, pode configurar definições de proteção de nuvem. Se ativar a opção **sincronizar dados do nuvem com o Cofre** quando instalou o fornecedor por isso, todas as nuvens no servidor VMM irão aparecer no separador **Itens protegida** no cofre. Se não tiver podem ser sincronizadas uma nuvem específica com recuperação de sites do Azure no separador **Geral** da página de propriedades da nuvem na consola do VMM.

![Nuvem publicada](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. Na página de início rápido, clique em **Configurar proteção para nuvens VMM**.
2. No separador **VMM nuvens** , selecione na nuvem que pretende configurar e aceda ao separador **configuração** .
3. Em **destino**, selecione **VMM**.
4. Na **localização de destino**, selecione servidor VMM no local que gere a nuvem que pretende utilizar para a recuperação.
4. Na **nuvem de destino**, selecione a nuvem de destino que pretende utilizar para activação pós-falha de máquinas virtuais na nuvem origem. Tenha em atenção que:

    - Recomendamos que selecione uma nuvem de destino que cumpra os requisitos de recuperação do máquinas virtuais que irá proteger.
    - Uma nuvem só pode pertencer a um par de única na nuvem — como um principal ou uma nuvem de destino.

5. Na **Copiar frequência**, especifique com que frequência os dados devem ser sincronizados entre 5he localizações de origem e destino. Tenha em atenção que esta definição só é relevante ao anfitrião do Hyper-V está a executar o Windows Server 2012 R2. Para outros servidores de uma predefinição de cinco minutos é utilizada.
6. Em **pontos de recuperação adicionais**, especifique se pretende criar pontos de recuperação adicionais. O valor predefinido zero indica que apenas o ponto de recuperação mais recente para uma máquina virtual primária está armazenado num servidor réplica anfitrião. Repare que ativar vários pontos de recuperação requer armazenamento adicional para os instantâneos que estão armazenados em cada ponto de recuperação. Por predefinição, pontos de recuperação são criados cada hora, para que cada ponto de recuperação contém património uma hora de dados. O valor de ponto de recuperação atribuir para a máquina virtual na consola do VMM não deve ser menor do que o valor que atribuir na consola de recuperação de Site do Azure.
7. Na **frequência de instantâneos aplicação consistentes**, especifique com que frequência deve criar instantâneos aplicação consistente. Hyper-V utiliza dois tipos de instantâneos — um instantâneo padrão que fornece um instantâneo utilizarão da máquina virtual toda e um instantâneo de aplicação consistentes tira um instantâneo de ponto em vez dos dados da aplicação dentro da máquina virtual. Aplicação consistentes instantâneos utilizam Volume sombra cópia serviço (VSS) para garantir que aplicações estão num estado consistente quando o instantâneo é disponibilizado. Tenha em atenção que se ativar a aplicação consistente instantâneos, irá afetar o desempenho das aplicações em execução em máquinas virtuais de origem. Certifique-se de que o valor que definiu for menor que o número de pontos de recuperação adicionais que configura.

    ![Configurar definições de proteção](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. Na **compressão de transferência de dados**, especifique se devem ser comprimidos dados replicados que são transferidos.
9. Na **autenticação**, especifique a forma como o tráfego é autenticado entre os servidores de anfitrião de Hyper-V principal e recuperação. Selecione HTTPS exceto se tiver um ambiente do Kerberos configurado de trabalho. Azure recuperação de Site irá configurar automaticamente certificados para autenticação HTTPS. Não é necessária nenhuma configuração manual. Se selecionar Kerberos, uma permissão Kerberos será utilizada para autenticação comum dos servidores de anfitrião. Por predefinição, será aberta na Firewall do Windows nos servidores de anfitrião Hyper-V porta 8083 e 8084 (para os certificados). Note que esta definição só é relevante para os servidores de anfitrião Hyper-V em execução no Windows Server 2012 R2.
10. Em **porta**, modifique o número de porta no qual os computadores de anfitriões de origem e destino ouvir para o tráfego de replicação. Por exemplo, pode modificar a definição se pretender aplicar a qualidade do serviço (QoS) rede da largura de banda para o tráfego de replicação. Verifique que a porta não é utilizada por qualquer outra aplicação e que é aberto nas definições da firewall.
11. **Método de replicação**, especifique como a replicação inicial de dados de origem para localizações de destino será efetuada, antes de ser iniciado replicação normal:

    - **Rede de**— copiar dados através da rede pode ser moroso e consumir muitos recursos. Recomendamos que utilize esta opção se na nuvem contiver máquinas virtuais com relativamente pequenos discos rígido virtuais e, se o site principal estiver ligado ao site secundário através de uma ligação com largura de banda larga. Pode especificar que a cópia deve de imediato ou selecione um tempo. Se utilizar a replicação de rede, recomendamos que agende a horas de expediente.
    - **Offline**— este método Especifica que a replicação inicial será executada com o suporte de dados externos. É útil se pretender evitar degradação do desempenho da rede ou, para localizações geograficamente remotos. Para utilizar este método pode especificar a localização de exportação na nuvem de origem e a localização de importar na nuvem destino. Quando ativa a proteção por para uma máquina virtual, o disco rígido virtual é copiado para a localização de exportação especificado. Enviá-la para o site de destino e copiá-lo para a localização de importação. O sistema copia a informação importada a máquinas virtuais do réplica.

12. Selecione **Eliminar réplica Virtual Machine** para especificar que o réplica virtual machine deve ser eliminado se deixar de proteger a máquina virtual ao selecionar a opção **Eliminar proteção para a máquina virtual** no separador máquinas virtuais das propriedades na nuvem. Com esta definição ativada, quando desativar a proteção contra a máquina virtual é removida do Azure recuperação de sites, as definições de recuperação de Site para a máquina virtual são removidas na consola do VMM e a réplica é eliminada.

    ![Configurar definições de proteção](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

Depois de guardar as definições de uma tarefa será criada e pode ser monitorizada no separador **tarefas** . Todos os servidores de anfitrião de Hyper-V na nuvem VMM origem serão configurados para a replicação. Definições de nuvem podem ser modificadas no separador **Configurar** . Se pretender modificar a nuvem a localização ou de alvos destino terá de remover a configuração na nuvem e, em seguida, reconfigurar na nuvem.

### <a name="prepare-for-offline-initial-replication"></a>Preparar para a replicação inicial offline

Terá de fazer as seguintes ações para se preparar para offline replicação inicial:

- No servidor de origem, irá especifique uma localização de caminho a partir do qual será executada a exportação de dados. Atribua controlo total para as permissões de NTFS e de partilha ao serviço de VMM no caminho de exportação. No servidor de destino, irá especifique uma localização de caminho a partir do qual irá ocorrer a importação de dados. Atribua as mesmas permissões neste caminho de importação.
- Se o caminho de importação ou exportação for partilhado, atribua membros do grupo de administrador, utilizador avançado, operador de impressão ou operador do servidor para a conta de serviço VMM no computador remoto no qual o partilhada está localizada.
- Se estiver a utilizar todas as contas executar como para adicionar anfitriões, caminhos de importação e exportação, atribua de leitura e permissões de escrita às contas de executar como no VMM.
- As partilhas de importação e exportação não devem estar localizadas em qualquer computador utilizado como um servidor de anfitrião Hyper-V, porque loopback configuração não é suportada pelo Hyper-V.
- No Active Directory, em cada Hyper-V servidor de anfitrião que contém máquinas virtuais que pretende proteger, ativar e configurar delegação restrita para confiar em computadores remotos no qual os caminhos de importação e exportação estão localizados, da seguinte forma:
    1. No controlador de domínio, abra a **utilizadores do Active Directory e computadores**.
    2. Na árvore de consola do clique **NomeDomínio** > **computadores**.
    3. Botão direito do rato no nome do servidor anfitrião Hyper-V > **Propriedades**.
    4. No separador **delegação** clique T**ferrugem neste computador para delegação apenas a serviços especificados**.
    5. Clique em **utilizar qualquer protocolo de autenticação**.
    6. Clique em **Adicionar** > **utilizadores e computadores**.
    7. Escreva o nome do computador que aloja o caminho de exportação > **OK**. Na lista de serviços disponíveis, mantenha premida a tecla CTRL e clique em **cifs** > **OK**. Repita para o nome do computador que aloja o caminho de importação. Repita conforme necessário para os servidores de anfitrião Hyper-V adicionais.

## <a name="step-5-configure-network-mapping"></a>Passo 5: Configurar mapeamento de rede
1. Na página de início rápido, clique em **mapear redes**.
2. Selecione o servidor de VMM origem a partir do qual pretende mapear redes e, em seguida, o servidor VMM alvo para o qual as redes serão mapeadas. A lista de redes de origem e as redes de destino associado são apresentadas. É apresentado um valor em branco para as redes que não estão atualmente mapeados.
3. Selecione uma rede na **rede origem** > **mapa**. O serviço Deteta as redes VM no servidor de destino e apresenta-os. Clique no ícone de informações junto aos nomes de rede origem e destino para ver as sub-redes para cada rede.

    ![Configurar mapeamento de rede](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. Na caixa de diálogo selecione uma das redes VM a partir do servidor VMM de destino.

    ![Selecione uma rede de destino](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. Ao selecionar uma rede de destino, são apresentadas as nuvens protegidas que utilizam a rede de origem. Redes de destino disponíveis que estão associadas nuvens utilizadas para protecção também são apresentados. Recomendamos que selecione uma rede de destino que está disponível para todas as nuvens que estiver a utilizar para protecção. Ou, também pode ir para o servidor VMM e modificar as propriedades da nuvem para adicionar a rede lógica correspondente à rede vm que pretende escolher.
6. Clique na marca de verificação para concluir o processo de mapeamento. Inicia uma tarefa controlar o progresso de mapeamento. Pode vê-lo no separador **tarefas** .


## <a name="step-6-configure-storage-mapping"></a>Passo 6: Configurar mapeamento de armazenamento
Por predefinição quando replica uma máquina virtual num servidor origem Hyper-V anfitrião para um servidor de anfitrião de Hyper-V de destino, dados replicados são armazenados na localização predefinida que está indicada para o anfitrião de Hyper-V de destino no Gestor de Hyper-V. Para ter mais controlo sobre a localização onde armazenou dados replicados, pode configurar mapeamentos de armazenamento da seguinte forma:



1. Defina classificações de armazenamento em servidores VMM de origem e destino. [Saiba mais](https://technet.microsoft.com/library/gg610685.aspx). Classificações de tem de estar disponíveis para os servidores de anfitriões Hyper-V no nuvens de origem e destino. Classificações de não é necessário ter o mesmo tipo de armazenamento. Por exemplo pode mapear uma classificação de origem que contém partilhas SMB para uma classificação de destino que contém CSVs.
2. Depois de classificações estão no local pode criar mapeamentos. Para efetuar esta ação, na página de **Início rápido** > **mapear armazenamento**.
3. Clique no separador **armazenamento** > **mapear classificações de armazenamento**.
4. No separador **mapear classificações de armazenamento** , selecione classificações na origem e VMM servidores de destino. Guarde as suas definições.

    ![Selecione uma rede de destino](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>Passo 7: Ativar proteção de máquina virtual
Depois de servidores, nuvens e redes estão configurados corretamente, pode ativar a proteção por para máquinas virtuais na nuvem.

1. No separador **máquinas virtuais** na nuvem na qual a máquina virtual está localizada, clique em **Ativar proteção** > **Adicionar máquinas virtuais**.
2. Na lista de máquinas virtuais na nuvem, selecione aquele que pretende proteger.

    ![Ativar a proteção de máquina virtual](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. Controlar o progresso da ação ativar proteção no separador **tarefas** , incluindo a replicação inicial. Após a tarefa de proteção finalizar é executada a máquina virtual está pronta para activação pós-falha. Depois de proteção está ativada e máquinas virtuais são replicadas, poderá vê-los no Azure.

    ![Tarefa de proteção de máquina virtual](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] Também pode ativar a proteção por para máquinas virtuais na consola do VMM. Clique em **Ativar proteção** na barra de ferramentas no separador **Recuperação de sites do Azure** as propriedades de máquina virtual.

### <a name="on-board-existing-virtual-machines"></a>Bordo máquinas virtuais existentes

Se tiver existentes máquinas virtuais VMM que são replicação com Hyper-V réplica terá incorporada-los para protecção Azure Site recuperação da seguinte forma:

1. Certifique-se de que tem nuvens principais e secundários. Certifique-se de que o servidor de Hyper-V que aloja a máquina virtual existente está localizado na nuvem principal e que o servidor de Hyper-V que aloja a máquina de virtual réplica está localizado na nuvem secundária. Certifique-se de que configurou definições de proteção para as nuvens. As definições devem correspondem às atualmente configuradas para réplica Hyper-V. Caso contrário, replicação de máquina virtual poderá não funcionar como esperado.
2. Em seguida, ativar proteção para a máquina virtual principal. Azure recuperação de sites e VMM irão garantir que o mesmo anfitrião réplica e máquina virtual é detetado e recuperação de sites do Azure irá reutilizar e restabelecer a replicação utilizando as definições de configurado durante a configuração da nuvem.


## <a name="test-your-deployment"></a>Testar a sua implementação

Para testar a sua implementação pode executar um teste activação pós-falha para uma única máquina virtual, ou criar um plano de recuperação que consiste várias máquinas virtuais e executa um teste activação pós-falha para o plano.  Teste activação pós-falha simula seu mecanismo activação e recuperação numa rede isolada.

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No separador **Recuperação de plano** , clique em **Criar plano de recuperação**.
2. Especifique um nome para o plano de recuperação e os servidores de VMM de origem e destino. O servidor de origem tem de ter máquinas virtuais que estejam ativadas para activação e recuperação. Selecione **Hyper-V** para ver apenas nuvens que estão configuradas para a replicação Hyper-V.

    ![Criar plano de recuperação](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. Na **máquina de Virtual selecione**, selecione grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Estes máquinas virtuais são adicionadas ao grupo predefinido plano recuperação — grupo 1. Pode adicionar mais grupos, se necessário. Tenha em atenção que, depois de máquinas virtuais de replicação será iniciado em conformidade com a ordem dos grupos de plano de recuperação.

    ![Adicionar máquinas virtuais](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

Após uma recuperação plano foi criado, é apresentado na lista no separador **Recuperação de planos** .

###<a name="run-a-test-failover"></a>Executar um teste activação pós-falha

1. No separador **Recuperação de plano** , selecione o plano e clique em **Testar activação pós-falha**.
2. Na página **Activação pós-confirmar teste falha** , selecione **nenhum**. Tenha em atenção que, com esta opção, activados a falha máquinas virtuais de réplica não estar ligada a uma rede. Irá testar-se de que a máquina virtual falha sobre como esperado, mas não é permitido testar o ambiente de rede de replicação. Veja como [executar uma activação de teste pós-falha](site-recovery-failover.md#run-a-test-failover) para obter mais detalhes sobre como utilizar as diferentes opções de redes.
3. O teste virtual machine será criado no anfitrião do mesmo como o anfitrião no qual existe máquina réplica virtual. Esta é adicionada na nuvem mesmo na qual a máquina de virtual réplica está localizada.

### <a name="run-a-recovery-plan"></a>Executar um plano de recuperação
Após a replicação máquina réplica virtual poderá não ter um endereço de IP que é o mesmo que o endereço IP da máquina virtual principal. Máquinas virtuais irá actualizar o servidor DNS que estiverem a utilizar após o início. Também pode adicionar um script para actualizar o servidor de DNS para se certificar de uma atualização atempada.

#### <a name="script-to-retrieve-the-ip-address"></a>Script para obter o endereço IP
Execute este exemplo de script para obter o endereço IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>Script para actualizar o DNS
Execute este exemplo de script para actualizar o DNS, especificando o endereço IP que recuperou utilizando o script do exemplo anterior.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>Informações de privacidade de recuperação de sites

Esta secção fornece informações de privacidade adicionais para a recuperação de Site do Microsoft Azure serviço (""). Para ver a declaração de privacidade dos serviços do Microsoft Azure, consulte a [Declaração de privacidade do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Funcionalidade: registo**

- **O que faz**: regista servidor com o serviço de modo a que podem ser protegidas máquinas virtuais
- **Informações recolhidas**: depois de registar o serviço recolhe, processa e transmite informações de certificado de gestão do servidor VMM que tenha designado para fornecer recuperação de falhas utilizando o nome do serviço do servidor VMM e o nome das nuvens recortada de máquina virtual no servidor VMM.
- **Utilização das informações**:
    - Certificado de gestão da — isto é utilizado para ajudar a identificar e autenticar o servidor VMM registado para aceder ao serviço. O serviço utiliza a parte pública chave do certificado para proteger um token que apenas o servidor VMM registado pode ter acesso a. O servidor tem de utilizar este token para aceder às funcionalidades do serviço.
    - Nome do servidor VMM — nome do servidor VMM de é necessária para identificar e comunicar com o servidor VMM adequado onde estão localizadas as nuvens.
    - Nuvem de nomes do servidor VMM — é necessário o nome de nuvem ao utilizar a nuvem do serviço emparelhar/unpairing funcionalidade descrita abaixo. Se decidir emparelhar o seu nuvem a partir de um centro de dados principal com outra na nuvem no Centro de dados de recuperação, são apresentados os nomes de todas as nuvens a partir do Centro de dados de recuperação.

- **Escolha**: estas informações são uma parte essencial do processo de registo de serviço, porque ajuda a que e o serviço para identificar o servidor VMM para o qual pretende fornecer proteção de recuperação de sites do Azure, bem como para identificar o servidor VMM registado correto. Se não quiser enviar estas informações para o serviço, não utilize este serviço. Se registar o seu servidor e, em seguida, mais tarde pretende anular o registo-la, pode fazê-lo ao eliminar as informações do servidor VMM a partir do portal de serviço (que é o portal do Azure).

**Funcionalidade: Ativar proteção de recuperação de sites do Azure**

- **O que significa**: O fornecedor de recuperação do Site Azure instalados no servidor VMM é a ligação para comunicar com o serviço. O fornecedor é uma biblioteca de ligação dinâmica (DLL) alojada no processo de VMM. Após ter instalado o fornecedor, a funcionalidade de "Recuperação do Centro de dados" obtém ativada na consola do administrador de VMM. Qualquer novas ou existentes máquinas virtuais uma nuvem, pode ativar uma propriedade denominada "Recuperação do Centro de dados" para ajudar a proteger a máquina virtual. Uma vez esta propriedade definida, o fornecedor envia o nome e o ID da máquina virtual para o serviço. A proteção virtual é activada por tecnologia de replicação do Windows Server 2012 ou Windows Server 2012 R2 Hyper-V. Os dados de máquina virtual obtém replicados a partir de um anfitrião do Hyper-V para outra (normalmente localizado num centro de dados diferentes "recuperação").

- **Informações recolhidas**: O serviço de recolhe, processa e transmite metadados para o computador virtual, que inclui o nome, ID, rede virtual e o nome da nuvem ao qual pertence.

- **Utilização das informações**: O serviço de utiliza as informações acima para preencher as informações de máquina virtual no seu portal de serviço.

- **Escolha**: Esta é uma peça essencial do serviço e não pode ser desativada. Se não pretende que este informações enviadas para o serviço, não permitir proteção de recuperação de sites do Azure para qualquer máquinas virtuais. Tenha em atenção que todos os dados enviados pelo fornecedor para o serviço é enviado por HTTPS.

**Funcionalidade: Plano de recuperação**

- **O que faz**: esta funcionalidade ajuda-o a criar um plano do orchestration para o Centro de dados "recuperação". Pode definir a ordem pela qual as máquinas virtuais ou grupo de máquinas virtuais deve ser iniciado no site da recuperação. Também pode especificar quaisquer scripts automatizados qualquer manual ou executar ação a tomar, no momento da recuperação para cada máquina virtual. Normalmente, é acionada activação pós-falha (coberto na secção seguinte) ao nível do plano de recuperação para coordenados recuperação.

- **Informações recolhidas**: O serviço de recolhe, processa e transmite metadados para o plano de recuperação, incluindo os metadados de máquina virtual e metadados da quaisquer scripts de automatização e notas de ação manual.

- **Utilização das informações**: os metadados descrito acima são utilizado para construir o plano de recuperação no seu portal do serviço.

- **Escolha**: Esta é uma peça essencial do serviço e não pode ser desativada. Se não quiser estes informações enviadas para o serviço, não Construa a recuperação de planos neste serviço.

**Funcionalidade: Mapeamento de rede**

- **O que faz**: esta funcionalidade permite-lhe informações de rede no Centro de dados principal do Centro de dados de recuperação de mapear. Quando as máquinas virtuais são recuperadas no site de recuperação, este mapeamento de ajuda no estabelecimento a conectividade da rede das mesmas.

- **Informações recolhidas**: como parte da funcionalidade de mapeamento de rede, o serviço recolhe, processos e transmite os metadados das redes lógicas para cada site (primário e Centro de dados).

- **Utilização das informações**: O serviço de utiliza os metadados para preencher o seu portal do serviço onde pode mapear as informações da rede.

- **Escolha**: Esta é uma peça essencial do serviço e não pode ser desativada. Se não quiser estes informações enviadas para o serviço, não utilize a funcionalidade de mapeamento de rede.

**Funcionalidade: Activação pós-falha - planeada, não planeado, teste**

- **O que faz**: esta funcionalidade ajuda-o a activação pós-falha de uma máquina virtual a partir do Centro de dados geridos VMM para outro centro de dados gerido VMM. A ação de activação pós-falha é acionada pelo utilizador no portal do serviço. Motivos possíveis para um activação pós-falha incluem um evento não planeado (por exemplo, no caso de uma disaster0 natural; um evento planeado (por exemplo Centro de dados balanceamento de carga); activação um teste pós-falha (por exemplo um ensaio de plano de recuperação).

O fornecedor no servidor VMM é notificado do evento de serviço e executa uma ação de activação pós-falha no anfitrião do Hyper-V através de VMM interfaces. Real activação pós-falha da máquina virtual a partir de um anfitrião do Hyper-V para outra (normalmente a ser executado num centro de dados diferentes "recuperação") é processada pela tecnologia de replicação do Windows Server 2012 ou Windows Server 2012 R2 Hyper-V. Depois da activação pós-falha estiver concluída, o fornecedor instalado no servidor VMM do Centro de dados do "recuperação" envia as informações de sucesso ao serviço.

- **Informações recolhidas**: O serviço de utiliza as informações acima para povoar o estado das informações de ação activação pós-falha no seu portal de serviço.

- **Utilização das informações**: O serviço de utiliza as informações acima da seguinte forma:

    - Certificado de gestão da — isto é utilizado para ajudar a identificar e autenticar o servidor VMM registado para aceder ao serviço. O serviço utiliza a parte pública chave do certificado para proteger um token que apenas o servidor VMM registado pode ter acesso a. O servidor tem de utilizar este token para aceder às funcionalidades do serviço.
    - Nome do servidor VMM — nome do servidor VMM de é necessária para identificar e comunicar com o servidor VMM adequado onde estão localizadas as nuvens.
    - Nuvem de nomes do servidor VMM — é necessário o nome de nuvem ao utilizar a nuvem do serviço emparelhar/unpairing funcionalidade descrita abaixo. Se decidir emparelhar o seu nuvem a partir de um centro de dados principal com outra na nuvem no Centro de dados de recuperação, são apresentados os nomes de todas as nuvens a partir do Centro de dados de recuperação.

- **Escolha**: Esta é uma peça essencial do serviço e não pode ser desativada. Se não quiser estes informações enviadas para o serviço, não utilize este serviço.

## <a name="next-steps"></a>Próximos passos

Após ter executado um teste activação pós-falha para verificar o que seu ambiente está a funcionar como esperado, [Saiba mais sobre](site-recovery-failover.md) diferentes tipos de activações pós-falha.
