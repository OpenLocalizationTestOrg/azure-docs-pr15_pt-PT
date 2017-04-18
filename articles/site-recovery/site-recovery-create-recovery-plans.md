<properties
    pageTitle="Criar planos de recuperação | Microsoft Azure" 
    description="Crie os planos de recuperação com Azure recuperação de Site para falhar ao longo e recuperar os grupos de máquinas virtuais e servidores físicos." 
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

# <a name="create-recovery-plans"></a>Criar planos de recuperação

O serviço de recuperação de sites do Azure contribui para a sua estratégia de recuperação (BCDR) empresas continuidade e falhas por orquestrar replicação, activação e recuperação de máquinas virtuais e servidores físicos. Máquinas podem ser replicadas para Azure ou para um centro de dados secundária no local. Para obter uma descrição geral rápida ler [o que é o Azure recuperação de Site?](site-recovery-overview.md).


## <a name="overview"></a>Descrição geral

Este artigo fornece informações sobre como criar e personalizar os planos de recuperação. 

Planos de recuperação é constituída por um ou mais grupos ordenados que contêm máquinas virtuais ou servidores físicos que pretende que provocam a falha ao longo em conjunto. Planos de recuperação faça o seguinte:

- Defina grupos de máquinas que falharem sobre e, em seguida, iniciar o em conjunto.
- Modelos dependências entre máquinas agrupando-os em conjunto num grupo de plano de recuperação. Por exemplo, se pretender falhar ao longo e abra uma aplicação específica queira grupo máquinas virtuais para essa aplicação no mesmo grupo plano de recuperação.
- Automatizar e expandir activação pós-falha. Pode executar um teste, planeado, ou não planeado activação pós-falha num plano de recuperação. Pode personalizar os planos de recuperação com scripts, automatização Azure e ações manuais.

São apresentados os planos de recuperação sobre os **Planos de recuperação** no portal do recuperação de sites.


Registe quaisquer comentários ou perguntas na parte inferior deste artigo ou no [Fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de começar

Tenha em atenção o seguinte procedimento:

- Um plano de recuperação não deva misturar VMs com adaptadores de rede únicos ou múltiplos. Isto acontece porque misturar estes VMs não é permitido um serviço em nuvem Azure.
- É possível expandir os planos de recuperação com scripts e ações manuais. Tenha em atenção o seguinte procedimento:
    - Escreva scripts através do Windows PowerShell.
    - Certifique-se de que os scripts utilizar blocos de capturas de experimentar, para que as exceções são processadas correctamente. Se existir uma exceção no script deixará de ser executada e a tarefa mostra como falhou.  Se ocorrer um erro, não executa qualquer parte do script restante. Se tal acontecer quando estiver a executar uma activação de pós-falha não planeada, o plano de recuperação irão continuar. Se tal acontecer quando estiver a executar uma activação planeada pós-falha, irá parar o plano de recuperação. Se tal acontecer, corrigir o script, certifique-se de que é executada conforme esperado e, em seguida, execute novamente o plano de recuperação.
    - O comando de anfitrião de escrita não funciona num script do plano de recuperação e o script irá falhar. Se pretender criar saída, criar um script de proxy que por sua vez executa o script principal e certifique-se de que todos os resultados for encaminhado saída utilizando o >> comando.
    - O script o tempo limite se não devolver dentro de 600 segundos.
    - Se nada é escrito para STDERR, o script ser classificado como falhou. Esta informação será apresentada os detalhes de execução de script.
    - Se estiver a utilizar o VMM na sua implementação tenha em atenção que:

        - Executar scripts num plano de recuperação no contexto da conta de serviço de VMM. Certifique-se que esta conta tem permissões de leitura na partilha remota no qual se encontra o script e testar o script para executar ao nível de privilégios de conta do serviço VMM.
        - Cmdlets VMM são entregues num módulo do Windows PowerShell. O módulo VMM Windows PowerShell está instalado quando instala consola do VMM. O módulo VMM pode ser carregado para o seu script utilizando o seguinte comando no script: Importar módulo-virtualmachinemanager de nome. [Obter mais detalhes](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Certifique-se de que tem pelo menos um servidor da biblioteca na sua implementação VMM. Por predefinição, o caminho de partilha de biblioteca para um servidor VMM encontra localmente no servidor VMM com o nome da pasta MSCVMMLibrary.
        - Se o caminho para partilhar a biblioteca é remoto (ou local mas não partilhadas com MSCVMMLibrary, configurar a partilha da seguinte forma (utilizando \\libserver2.contoso.com\share\ como exemplo):
            - Abra o Editor de registo e navegue para HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration.
            -  Edite o valor ScriptLibraryPath e coloque o valor como \\libserver2.contoso.com\share\. especificar o FQDN completo. Fornece permissões para a localização de partilha.
            -  Certifique-se de que a teste o script com uma conta de utilizador que tem as mesmas permissões que a conta de serviço VMM, para se certificar de que executar scripts de testado autónomas da mesma forma que aparecerão no planos de recuperação. No servidor VMM, defina a política de execução para ignorar da seguinte forma:
                -  Abra a consola do Windows PowerShell de 64 bits com privilégios elevados.
                -  Tipo: **conjunto executionpolicy ignorar**. [Obter mais detalhes](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

A forma onde a criar um plano de recuperação depende da sua implementação de recuperação de sites.

- **Replicação VMs VMware e servidores físicos**, crie um plano e adicionar grupos de replicação que contêm máquinas virtuais e servidores físicos para um plano de recuperação.
- **Replicação VMs Hyper-V (no nuvens VMM)**— crie um plano e adicione protegidas Hyper-V virtual máquinas a partir de uma nuvem VMM um plano de recuperação.
- **Replicação VMs Hyper-V (não está na nuvens VMM)**— criar um plano e adicionar protegidas Hyper-V virtual máquinas de um grupo de proteção para um plano de recuperação.
- **Replicação de SAN**— um plano de criar e adicionar um grupo de replicação que contém máquinas virtuais para o plano de recuperação. Selecione um grupo de replicação em vez de máquinas virtuais específicas, porque o todas as máquinas virtuais num grupo de replicação tem falham ao longo em conjunto (activação pós-falha ocorre na camada do armazenamento pela primeira vez).


Crie um plano de recuperação da seguinte forma:

1. Clique em separador de **Recuperação de plano** > **Criar plano de recuperação**.
Especifique um nome para o plano de recuperação e uma origem e destino. O servidor de origem tem de ter máquinas virtuais que estejam ativadas para activação e recuperação.

    - Se estiver a replicação de VMM para VMM selecione **Tipo de origem** > **VMM**e os servidores VMM origem e destino. Clique em **Hyper-V** para ver nuvens estiverem configurados para utilizarem o Hyper-V réplica. 
    - Se estiver a replicação de VMM para VMM utilizando SAN selecionar **Tipo de origem** > **VMM**e os servidores VMM origem e destino. Clique em **SAN** para ver nuvens que estão configuradas para a replicação de SAN.
    - Se estiver a replicação de VMM para Azure selecione **Tipo de origem** > **VMM**.  Selecione o servidor VMM de origem e o **Azure** como de destino.
    - Se estiver a replicação de um site de Hyper-V selecione **Tipo de origem** > **Hyper-V site**. Selecione o site como a origem e o **Azure **como o destino.
    - Se estiver a replicação de VMware ou um servidor físico no local para Azure, selecione um servidor de configuração como a origem e o **Azure** como alvo

2. Em **Selecione máquinas virtuais** selecione máquinas virtuais (ou grupo de replicação) que pretende adicionar ao grupo predefinido (grupo 1) no plano de recuperação.

## <a name="customize-recovery-plans"></a>Personalizar os planos de recuperação

Depois de adicionar protegidos máquinas virtuais ou grupos de replicação ao grupo predefinido recuperação plano e criado o plano que pode personalizá-lo:

- **Adicionar novos grupos**— pode adicionar os grupos de plano de recuperação adicionais. Grupos que adicionar são numerados pela ordem em que adicioná-los. Pode adicionar até sete grupos. Pode adicionar mais máquinas ou grupos de replicação para estes novos grupos. Tenha em atenção que uma máquina virtual ou grupo de replicação apenas pode ser incluído no grupo de plano de uma recuperação.
- **Adicionar um script **— pode adicionar scripts que, antes ou depois de uma recuperação, planear grupo. Quando adiciona um script que adiciona um novo conjunto de ações para o grupo. Por exemplo será criado um conjunto de passos pré para 1 de grupo com o nome: grupo 1: pré-passos. Todos os passos anteriores ao serão listados dentro este conjunto. Tenha em atenção que pode adicionar apenas um script no site principal, se tiver um servidor VMM implementado.
- **Adicionar uma acção manual**— pode adicionar ações manuais que são executadas antes ou depois de um grupo de plano de recuperação. Quando o plano de recuperação for executado, parar no ponto no qual inseriu a ação manual e uma caixa de diálogo lhe pede para especificar que a ação manual foi concluída.

## <a name="extend-recovery-plans-with-scripts"></a>Expandir os planos de recuperação com scripts

Pode adicionar um script para o seu plano de recuperação:

- Se tiver um site de origem VMM pode criar um script no servidor VMM e inclui-los no seu plano de recuperação.
- Se estiver a ser replicadas para Azure pode integrar o Azure automatização runbooks para o seu plano de recuperação

### <a name="create-a-vmm-script"></a>Criar um script VMM


Crie o script da seguinte forma:

1. Criar uma nova pasta na partilha de biblioteca, por exemplo \<VMMServerName > \MSSCVMMLibrary\RPScripts. Colocá-lo na origem e destino servidores VMM.
2. Crie o script (por exemplo RPScript) e verificar funciona conforme esperado.
3. Colocar o script na localização \<VMMServerName > \MSSCVMMLibrary nos servidores VMM origem e destino.

### <a name="create-an-azure-automation-runbook"></a>Criar um livro de execuções automatização Azure

É possível expandir o seu plano de recuperação através da execução de um livro de execuções do Azure automatização como parte do plano. [Leia mais](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Adicionar as definições personalizadas para um plano de recuperação

1. Abra o plano de recuperação que pretende personalizar.
2. Clique para adicionar um máquinas virtuais ou um novo grupo.
3. Para adicionar um script ou manual e ação clique em qualquer item na lista **passo** e, em seguida, clique em **Script** ou **Ação Manual**. Especifique se pretende adicionar o script ou ação antes ou depois do item selecionado. Utilize os botões de comando **Mover para cima** e **Mover para baixo** para mover a posição do script para cima ou para baixo.
4. Se estiver a adicionar um script VMM, selecione **activação pós-falha ao VMM script**e no **Caminho do Script** , escreva o caminho relativo à partilha. Sim, para o nosso exemplo onde se encontra a partilhar na \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, especifique o caminho: \RPScripts\RPScript.PS1.
5. Se estiver a adicionar uma automatização Azure executar o livro de endereços, especificar a **Conta de automatização do Azure** na qual está localizado o livro de execuções e selecione o adequado **Azure livro execuções Script**.
5. Faça uma activação pós-falha do plano de recuperação para se certificar de que o script funciona conforme esperado.


## <a name="next-steps"></a>Próximos passos

Pode executar diferentes tipos de activações pós-falha no plano de recuperação, incluindo um teste activação pós-falha para verificar o seu ambiente e activações pós-falha planeada ou não planeada. [Saiba mais](site-recovery-failover.md).


 
