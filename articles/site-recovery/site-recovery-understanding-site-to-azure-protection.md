<properties
    pageTitle="Replicação de Hyper-V com recuperação de sites do Azure | Microsoft Azure"
    description="Utilize este artigo para compreender os conceitos técnicos que ajudá-lo com êxito instalar, configurar e gerir Azure recuperação de sites."
    services="site-recovery"
    documentationCenter=""
    authors="Rajani-Janaki-Ram"
    manager="mkjain"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/12/2016"
    ms.author="rajanaki"/>  


# <a name="hyper-v-replication-with-azure-site-recovery"></a>Replicação de Hyper-V com recuperação de sites do Azure

Este artigo descreve os conceitos técnicos que o podem ajudar a configurar e gerir um site de Hyper-V ou um site de sistema Center Máquina Virtual Gestor (VMM) para protecção Azure utilizando o Azure recuperação de sites com êxito.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Configurar o ambiente de origem para a replicação entre no local e do Azure

Como parte da configuração de recuperação de falhas entre no local e Azure, não se esqueça de transferir e instalar o fornecedor de recuperação de sites do Azure no servidor VMM. Instale o Azure agente de serviços de recuperação em cada anfitrião Hyper-V.

![Implementação do site VMM para a replicação entre no local e do Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Configuração do ambiente de origem numa infraestrutura Hyper-V gerido é semelhante a configuração do ambiente de origem numa infraestrutura de geridos VMM. A única diferença é que o fornecedor e agente estão instalados no anfitrião do Hyper-V própria. Num ambiente VMM, o fornecedor é instalado no servidor VMM e o agente é instalado nos anfitriões Hyper-V (em caso de replicação do Azure).

## <a name="workflows"></a>Fluxos de trabalho

### <a name="enable-protection"></a>Activar a protecção
Depois de proteger uma máquina virtual a partir do Azure portal ou no local, inicia uma tarefa de recuperação do Site denominada **Ativar proteção** . Pode monitorizá-lo no separador **tarefas** .

![Tarefa de "Ativar proteção" na lista de tarefas](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

A tarefa de **Ativar proteção** verifica para os pré-requisitos antes de invocar o método de [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Este método cria a replicação para o Azure utilizando entradas que estão configuradas durante a proteção.

A tarefa de **Ativar proteção** começa a replicação inicial no local ao invocar o método de [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Este método envia discos virtuais a máquina virtual a Azure.

![Detalhes para a tarefa "Ativar proteção"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finalizar proteção na máquina virtual
Um [instantâneo Hyper-V VM](https://technet.microsoft.com/library/dd560637.aspx) é disponibilizado quando é acionada replicação inicial. Discos rígido virtuais são processados um a um até que todos os discos são carregados Azure. Isto normalmente leva tempo para concluir, com base no tamanho do disco e a largura de banda. Para otimizar o seu a utilização de rede, consulte o artigo [como gerir no local para a utilização de largura de banda de rede de proteção Azure](https://support.microsoft.com/kb/3056159).

Depois de concluir a replicação inicial, a tarefa de **proteção de Finalize na máquina virtual** configura as definições de rede e a POST replicação. Enquanto a replicação inicial está em curso:

- Todas as alterações aos discos são registadas. 
- Armazenamento adicional no disco é consumido aos ficheiros de registo de réplica Hyper-V (HRL) e instantâneo.

Após a conclusão de replicação inicial, o instantâneo Hyper-V VM é eliminado. Esta eliminação resulta num intercalar as alterações de dados após a replicação inicial para o disco principal.

!["Finalizar proteção na máquina virtual" tarefa na lista de tarefas](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Replicação delta
Controlador de replicação de réplica do Hyper-V, que faz parte do motor de replicação de réplica Hyper-V, faixas as alterações para um disco rígido virtual como ficheiros de registo de réplica Hyper-V (*.hrl). Ficheiros HRL estão no diretório do mesmo como os discos associados.

Cada disco que esteja configurado para a replicação tiver um ficheiro HRL associado. Este registo é enviado para a conta de armazenamento do cliente depois de concluída a replicação inicial. Quando um registo está em trânsito para Azure, as alterações na página principal forem registadas em outro ficheiro de registo no mesmo diretório.

Durante a replicação inicial ou replicação delta, pode monitorizar o estado de funcionamento do VM replicação na vista de VM, tal como mencionado no [Estado de funcionamento do Monitor replicação para máquina virtual](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).  

### <a name="resynchronization"></a>Nova sincronização
Uma máquina virtual está marcada para a nova sincronização quando falha de replicação delta tanto a replicação completa inicial está dispendiosa em termos de largura de banda de rede ou hora. Por exemplo, quando o tamanho do ficheiro HRL pilhas até 50 por cento do tamanho total do disco, a máquina virtual estão marcada para a nova sincronização. Nova sincronização minimiza a quantidade de dados que foi enviados através da rede por computação somas de verificação de discos de máquina virtual origem e destino e enviar apenas a diferença.

Depois de concluir a nova sincronização, deverá continuar a replicação normal delta. Pode retomar a nova sincronização se ocorre uma falha na rede ou falha do outra.

Por predefinição, a nova sincronização agendada automaticamente está configurada para ocorrer fora do horário de trabalho. Se a máquina virtual tem de ser sincronizados manualmente, selecione a máquina virtual a partir do portal e clique em **voltar a sincronizar**.

![Nova sincronização manual](media/site-recovery-understanding-site-to-azure-protection/image04.png)

Nova sincronização utiliza um algoritmo das secções do bloco fixa onde os ficheiros de origem e destino estão divididos em blocos fixos. Somas de verificação para cada segmento são geradas e, em seguida, em comparação com determinar que impede a partir da origem necessita de ser aplicada ao destino.

### <a name="retry-logic"></a>A lógica de repetição
Existe lógica de repetição incorporados para erros de replicação. Esta lógica pode ser classificada em duas categorias:

| Categoria                  | Cenários                                    |
|---------------------------|----------------------------------------------|
| Erro não recuperável     | Não repetir é tentado. Estado de replicação de máquina virtual é **crítico**e é necessária intervenção do administrador. Alguns exemplos incluem: <ul><li>Cadeia VHD quebrada</li><li>Estado inválido para o réplica virtual machine</li><li>Erro de autenticação de rede</li><li>Erro de autorização</li><li>Máquina virtual que não for encontrada, no caso de um servidor autónomo Hyper-V</li></ul>|
| Erro recuperável         | Número de tentativas ocorre cada intervalo de replicação, utilizando um exponencial back-off que aumenta o intervalo de repetição desde o início da primeira tentativa (1, 2, 4, 8, 10 minutos). Se um erro persistir, tente novamente a cada 30 minutos. Alguns exemplos incluem: <ul><li>Erro de rede</li><li>Pouco espaço em disco</li><li>Condição de pouca memória</li></ul>|

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Ciclo de vida de proteção e recuperação de máquina de virtual Hyper-V

![Ciclo de vida de proteção e recuperação de máquina de virtual Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Outras referências

- [Monitorizar e resolver problemas de proteção para os sites de Hyper-V e físicas VMware, VMM,](./site-recovery-monitoring-and-troubleshooting.md)
- [Alcançar Microsoft Support](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Erros comuns de recuperação de sites do Azure e respetivas resoluções](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)
