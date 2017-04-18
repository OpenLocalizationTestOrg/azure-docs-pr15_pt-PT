<properties
    pageTitle="Monitorizar e resolver problemas de proteção para máquinas virtuais e servidores físicos | Microsoft Auzre" 
    description="Recuperação de Site Azure coordenadas a replicação, activação pós-falha e recuperação de máquinas virtuais localizados nos servidores no local para Azure ou um centro de dados secundário. Utilize este artigo para monitorizar e resolver problemas de proteção VMM ou Site de Hyper-V." 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Monitorizar e resolver problemas de proteção para máquinas virtuais e servidores físicos

Este guia de resolução de problemas e monitorização permite-lhe obter informações sobre o estado de funcionamento de replicação a controlar e técnicas de resolução de problemas para Azure recuperação de sites.

## <a name="understanding-the-components"></a>Noções sobre os componentes

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>VMware/física implementação do Site para a replicação entre no local e Azure.
A configuração DR entre máquina de VMware/físico no local; Servidor de configuração, modelo global de destino e processo servidor tem de configurado. Durante a ativação de proteção para o servidor de origem Azure recuperação de Site irá instalar o serviço de mobilidade. Para publicar a indisponibilidade no local assim que o servidor de origem falha com o sobre para Azure, necessidades de clientes para configurar um servidor de processo no Azure e um alvo de modelo global de servidor no local para proteger o servidor de origem para recriado no local. 

![Implementação do Site VMware/física para a replicação no local e do Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>VMM implementação do Site para a replicação entre o site no local.

Como parte da configuração DR entre dois localização no local; Fornecedor de recuperação do Azure Site tem de ser transferida e instalada no servidor VMM. O fornecedor tem ligação à internet para se certificar de que todas as operações acionou a partir do Portal do Azure obtém traduzido para operações de no local como ativar proteção, encerramento lado primária virtual máquinas como parte do activações pós-falha etc.

![VMM implementação do Site para a replicação entre o site no local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>VMM implementação do Site para a replicação no local e Azure.

Como parte da configuração DR no local e Azure; Fornecedor de recuperação do Azure Site tem de ser transferida e instalada no servidor VMM juntamente com o agente de serviços de recuperação do Azure que necessita de ser instalado em cada anfitrião Hyper-V. Consulte o [Site da Noções sobre a proteção do Azure](./site-recovery-understanding-site-to-azure-protection.md) para obter mais informações.

![VMM implementação do Site para a replicação no local e do Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>Implementação do Site Hyper-V para a replicação no local e do Azure

Este é o mesmo que que de implementação de VMM – apenas diferença a ser fornecedor & agente obtém instalada no anfitrião do Hyper-V própria. Consulte o [Site da Noções sobre a proteção do Azure](./site-recovery-understanding-site-to-azure-protection.md) para obter mais informações.

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Monitorizar operações de configuração, proteção e recuperação

Cada operação de recuperação automática obtém auditada e é registada no separador "Tarefas". Em caso de qualquer configuração, proteção ou erro de recuperação navegue para o separador tarefas e ver se há quaisquer falhas.

![Monitorizar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Quando encontrar falhas em vista de tarefas, selecione a tarefa e clique em detalhes do erro para essa tarefa.

![Monitorizar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Os detalhes desse erro irão ajudá-lo a identificar a causa e recomendação para o problema.

![Monitorizar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image5.png)

No caso acima parece ser outra operação que está em curso devido à qual está a falhar de configuração de proteção. Certifique-se de que os resolve o problema de acordo com a recomendação – daí-após clique RESART para voltar a iniciar a operação.

![Monitorizar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Opção para REINICIAR não está disponível para todas as operações – para aqueles que não tem a opção de REINICIAR navegar de volta para o objeto e Refazer a operação mais uma vez. Cada tarefa pode ser cancelada em qualquer ponto de tempo ao utilizar o botão Cancelar em curso.

![Monitorizar operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Monitorizar o estado de funcionamento de replicação para máquina virtual

Fornecedor de recuperação automática central e monitorização através do Portal do Azure para cada uma das entidades protegidas remoto. Navegue para os itens protegida daí-após selecionar VMM NUVENS ou grupos de PROTEÇÃO. Separador de NUVENS VMM está apenas para implementações VMM com base e todas as outras situações tem as entidades protegidas no separador PROTEÇÃO grupos.

![Monitorizar o estado de funcionamento de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Daí-após selecionar a entidade protegida em nuvem respetivas ou do grupo de proteção. Depois de selecionar a entidade protegida todos os permitido operações são apresentadas no painel inferior.

![Monitorizar o estado de funcionamento de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Conforme mostrado acima em caso a máquina virtual que estado de funcionamento é fundamental – pode clicar no botão de detalhes do erro na parte inferior para ver o erro. Com base em "causas possíveis" e "Recomendação" mencionada resolver o problema.

![Monitorizar o estado de funcionamento de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Monitorizar o estado de funcionamento de replicação para máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Nota: Se não existirem quaisquer operações ativas que estão em curso ou falha, em seguida, navegue para a vista de tarefas como mencionado anteriormente para ver o erro específico de tarefa.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Resolução de problemas de Hyper-V no local

Ligar a consola do Gestor de Hyper-V no local, selecione a máquina virtual e ver o estado de funcionamento de replicação.

![Resolução de problemas de Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Neste caso *Estado de funcionamento de replicação* está a ser indicada como crítica – *Estado de funcionamento de replicação de vista* para ver os detalhes.

![Resolução de problemas de Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Para os casos onde replicação está em pausa para a máquina virtual, com o botão direito selecione *replicação*->*replicação de currículo*
![resolução de problemas no local problemas Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image19.png)

No caso de máquina virtual migra um novo anfitrião de Hyper-V (dentro do cluster ou uma máquina de autónomo), que tenha sido configurado através de recuperação automática, para a máquina virtual a replicação não seria afetada. Certifique-se de que o novo anfitrião Hyper-V cumpre todos os os por-requisitos e está configurado utilizando a recuperação automática.

### <a name="event-log"></a>Registo de eventos

| Origens de evento                | Detalhes                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Aplicações e registos de início/Microsoft/VirtualMachineManager/servidor/administrador de serviços** (VMM Server)   |  Este procedimento fornece útil registo para resolução de problemas de muitos problemas VMM diferentes. |
| **Aplicações e registos de início/MicrosoftAzureRecoveryServices/replicação do serviço** (Hyper-V anfitrião)   | Este procedimento fornece útil registo para resolução de problemas de muitos problemas do agente de serviços de recuperação do Microsoft Azure. <br/> ![Origem do evento para o anfitrião Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **As aplicações e o serviço registos/Microsoft/Azure Site recuperação/fornecedor/operacionais avançados** (Hyper-V anfitrião)   | Este procedimento fornece útil registo para resolução de problemas de serviço de recuperação de Site do Microsoft Azure muitos. <br/> ![Origem do evento para o anfitrião Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Aplicações e registos de início/Microsoft/Windows/Hyper-V-VMMS/administrador de serviços** (Hyper-V anfitrião) | Este procedimento fornece útil registo para resolução de problemas de gestão de máquina virtual Hyper-V muitos. <br/> ![Origem do evento para o anfitrião Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>Opções de registo de replicação Hyper-V

Todos os eventos sobre a Hyper-V réplica são registados no Hyper-V-VMMS\\registo administração localizado em **registos de serviços e aplicações\\Microsoft\\Windows**. Além disso, um registo analítico pode ser ativado para Hyper-V-VMMS. Para permitir que este registo, comece pelos registos Analítico e de depuração são visíveis no Visualizador de eventos. Abrir o Visualizador de eventos, em seguida, no **menu Ver**, clique em **Mostrar analíticos e registos de depuração**.

![Resolução de problemas de Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Um registo analítico está visível em Hyper-V-VMMS

![Resolução de problemas de Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image15.png)

No painel de **ações** , clique em **Ativar o registo**. Depois ativada, este é apresentado no **Monitor de desempenho** como uma sessão de rastreio de evento localizado em **os conjuntos de dados Recolectores.**

![Resolução de problemas de Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para ver as informações recolhidas, pela primeira vez parar a sessão de rastreio ao desativar o registo e, em seguida, guarde o registo e voltar a abrir no Visualizador de eventos ou utilize outras ferramentas para convertê-lo conforme pretender.



## <a name="reaching-out-for-microsoft-support"></a>Alcançar Microsoft Support

### <a name="log-collection"></a>Colecção de registos

Para protecção VMM Site, consulte [colecção de registos de recuperação automática utilizando a ferramenta de suporte diagnósticos plataforma SDP ()](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) para recolher os registos necessários.

Para protecção Hyper-V Site, transfira a [ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) & executá-la no anfitrião do Hyper-V para recolher os registos.

Cenários de VMware/física, consulte [Azure recuperação registo coleção de sites para VMware e física proteção de site](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) para recolher os registos necessários.

Ferramenta de recolhe os registos localmente numa subpasta de aleatoriamente com nome em **%LocalAppData%\ElevatedDiagnostics**

![Passos de exemplo apresentados a partir do proteção de site Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Abrir um bilhetes de suporte

Para subir bilhetes de suporte para recuperação automática, comunique para utilizar o URL na <http://aka.ms/getazuresupport> de suporte do Azure

## <a name="kb-articles"></a>Artigos BDC

-   [Como preservar a letra de unidade para máquinas virtuais protegidas que são Ocorreu uma falha ao longo ou migrado para o Azure](http://support.microsoft.com/kb/3031135)
-   [Como gerir no local para a utilização de largura de banda de rede de proteção Azure](https://support.microsoft.com/kb/3056159)
-   [Recuperação automática: erro de "não foi possível encontrar o recurso de cluster" a pedido quando tenta ativar proteção para uma máquina virtual](http://support.microsoft.com/kb/3010979)
-   [Compreender e Hyper-V réplica guia de resolução de problemas](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Erros comuns de recuperação automática e respetivas resoluções

Seguem-se os erros comuns que pode clicar em e respetivas resoluções. Cada um do erro é documentada numa página WIKI em separado.

### <a name="general"></a>Geral
-   <span style="color:green;">Novo</span> Tarefas de [a falhar com o erro "uma operação está em curso." Erro 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">Novo</span> Tarefas de [a falhar com o erro "O servidor não está ligado à Internet". Erro 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Programa de configuração
-   [Não é possível registar o servidor VMM devido a um erro interno. Fazer referência para a vista de tarefas no Portal de recuperação do Site para obter mais detalhes sobre o erro. Execute novamente a configuração para registar o servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Não é possível estabelecer uma ligação para o Gestor de recuperação Hyper-V cofre. Verifique as definições de proxy ou tente novamente mais tarde.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuração
-   [Não é possível criar grupo proteção: Ocorreu um erro ao obter a lista dos servidores.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Cluster de anfitrião Hyper-V contém pelo menos uma placa de rede estático ou sem placas ligadas estão configuradas para utilizar o DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM não tem permissões para concluir uma ação](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Não é possível selecionar a conta de armazenamento na subscrição durante a configuração de proteção](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Proteção
- <span style="color:green;">Novo</span> Falha de [Ativar proteção com erro "proteção não foi possível ser configurada para a máquina virtual". Erro 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">Novo</span> Falha de [Ativar proteção com erro "proteção não foi de estar ativada para a máquina virtual." Erro 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">Novo</span> Erro de migração em directo [23848 - a máquina virtual vai ser movidos utilizando o tipo de Live. Isto pode interromper o estado de proteção de recuperação da máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [Activar a protecção Ocorreu uma falha de uma vez que agente não instalado no computador anfitrião](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Não é possível localizar um anfitrião adequado para a máquina de virtual réplica - devido a recursos de cluster baixa](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Não é possível localizar um anfitrião adequado para a máquina de virtual réplica - devido a nenhuma rede lógico anexado](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Não é possível ligar para o computador do anfitrião de réplica - não foi possível estabelecer ligação](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>Recuperação
- VMM consegue concluir a operação de anfitrião-
    -   [Falha ao longo para o ponto de recuperação selecionada para máquina virtual: geral erro de acesso negado.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Não foi possível falhar ao longo para o ponto de recuperação selecionada para máquina virtual Hyper-V: operação abortada tente um ponto de recuperação mais recente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Uma ligação com o servidor não foi possível estabelecida (0x00002EFD)
        -   [Ocorreu uma falha ao activar a replicação inversa para máquina virtual Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Ocorreu uma falha ao activar a replicação para máquina de virtual máquina virtual Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Não foi possível consolidar activação pós-falha para máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [O plano de recuperação contém máquinas virtuais que não sejam está preparadas para activação pós-falha planeada](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [A máquina virtual não está pronta para ser planeada activação pós-falha](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Máquina virtual não está em execução e não está desligada](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Fora da operação de banda aconteceu num máquina virtual e consolidar activação pós-falha falhou](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Testar activação pós-falha
    -   [Não pode ser iniciado activação pós-falha uma vez que a teste activação pós-falha está em curso](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">Novo</span>  Activação pós-falha o tempo limite com 'PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout' devido às definições de configuração no grupo de segurança de rede associados a Máquina Virtual ou sub-rede ao qual pertence a máquina para. Consulte ['PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) para obter detalhes.


### <a name="configuration-server-process-server-master-target"></a>Configuração servidor, servidor de processo, modelo global de destino
Servidor de configuração (CS), servidor de processo (PS), Targer de modelo global (MT)
-   [Anfitrião do ESXi no qual está alojado PS/CS como uma VM falha com um ecrã púrpura.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Ambiente de trabalho remoto após activação pós-falha de resolução de problemas
-   Muitos clientes se deparam problemas para ligar a falha ao através de VM no Azure. [Utilizar o documento de resolução de problemas para RDP para a VM](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Adicionar um endereço IP público numa máquina de virtual do Gestor de recursos
Se o botão **Ligar** no portal do estiver indisponível e não estiver ligado a Azure através de uma ligação VPN de Site para o Site ou Express encaminhar, tem de criar e atribuir a VM um endereço IP público antes de poder utilizar RDP/SSH. Siga os passos abaixo para adicionar um endereço IP público na interface de rede da máquina virtual.  

![Falha ao adicionar um endereço IP público na interface da rede do sobre máquina virtual](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)