<properties 
   pageTitle="Automatizar DR para partilhas de ficheiros no StorSimple utilizando a recuperação de Site do Azure | Microsoft Azure"
   description="Descreve os passos e práticas recomendadas para criar uma solução de recuperação de falhas para partilhas de ficheiros alojados em StorSimple armazenamento."
   services="storsimple"
   documentationCenter="NA"
   authors="vidarmsft"
   manager="syadav"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/16/2016"
   ms.author="vidarmsft" />

# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Solução de recuperação de falhas automatizada utilizando a recuperação de Site do Azure para partilhas de ficheiros alojadas no StorSimple

## <a name="overview"></a>Descrição geral

Microsoft Azure StorSimple é uma solução de armazenamento de nuvem híbrido endereços complexidade de dados não estruturados geralmente associados partilhas de ficheiros. StorSimple utiliza armazenamento em nuvem como uma extensão da solução no local e automaticamente camadas dados ao longo de armazenamento no local e armazenamento em nuvem. Integrado proteção de dados, com o local e na nuvem instantâneos, elimina a necessidade de uma sprawling infraestrutura de armazenamento.

[Recuperação de Site do Azure](../site-recovery/site-recovery-overview.md) é um serviço baseadas no Azure que fornece capacidades de recuperação (DR) de falhas por orquestrar replicação, activação pós-falha e recuperação de máquinas virtuais. Recuperação de Site Azure suporta um número de tecnologias de replicação de forma consistente criar uma réplica da, proteger e forma totalmente integrada falhar ao longo do máquinas virtuais e aplicações para o público/privado ou alojados nuvens.

Através do Azure recuperação de sites, replicação de máquina virtual e capacidades de instantâneo StorSimple na nuvem, pode proteger o ambiente de ficheiro completo do servidor. Trabalho uma interrupção, pode utilizar um único clique para colocar o seu partilhas de ficheiros online no Azure em apenas alguns minutos.

Este documento explica detalhadamente como pode criar uma solução de recuperação de falhas para sua partilhas de ficheiros alojadas no armazenamento StorSimple e executar planeadas, não planeado e testar activações pós-falha utilizar um plano de recuperação com um clique. Na sua essência mostra como é possível modificar o plano de recuperação no cofre Azure recuperação de sites para ativar activações StorSimple pós-falha durante cenários de falhas. Além disso, descreve configurações suportadas e pré-requisitos. Este documento assume que está familiarizado com os princípios básicos de recuperação de sites do Azure e StorSimple arquitecturas.

## <a name="supported-azure-site-recovery-deployment-options"></a>Opções de implementação de recuperação de sites do Azure suportadas

Os clientes podem implementar os servidores de ficheiro como servidores físicos ou máquinas virtuais (VMs) em execução no Hyper-V ou VMware e, em seguida, criar partilhas de ficheiros a partir de volumes gravados StorSimple armazenamento. Azure recuperação de sites pode proteger físicas e virtuais híbridas, para um site de secundário ou para Azure. Este documento abrange os detalhes de uma solução de DR com Azure como o site de recuperação para um servidor de ficheiros que VM alojado no Hyper-V e com partilhas de ficheiros no StorSimple armazenamento. Outras situações em que o servidor de ficheiro VM está numa VM VMware ou uma máquina física podem ser implementadas de forma semelhante.

## <a name="prerequisites"></a>Pré-requisitos

Implementar uma solução de recuperação com um clique falhas que utiliza o Azure recuperação de sites para partilhas de ficheiros alojadas no StorSimple armazenamento tem os pré-requisitos seguintes:

-   Servidor de ficheiro do Windows Server 2012 R2 que VM alojado em Hyper-V ou VMware ou uma máquina física no local

-   Registado StorSimple armazenamento dispositivo no local com o Gestor de StorSimple do Azure

-   StorSimple nuvem aparelho criado no Gestor de Azure StorSimple (Isto pode ser mantido num estado encerramento)

-   Partilhas de ficheiros alojadas no volumes configurados no dispositivo de armazenamento de StorSimple

-   [Cofre de serviços de recuperação do azure Site](../site-recovery/site-recovery-vmm-to-vmm.md) criado numa subscrição do Microsoft Azure

Além disso, se Azure for o seu site de recuperação, execute a [ferramenta de avaliação do Azure Máquina Virtual prontidão](http://azure.microsoft.com/downloads/vm-readiness-assessment/) no VMs para se certificar de que sejam compatíveis com os serviços Azure VMs e recuperação de sites do Azure.

Para evitar problemas de (o que poderão resultar em custos mais elevados), certifique-se de que cria o StorSimple nuvem aparelho, conta de automatização e armazenamento de latência (s) na mesma região.

## <a name="enable-dr-for-storsimple-file-shares"></a>Ativar DR para StorSimple partilhas de ficheiros  

Cada componente do ambiente no local tem de ser protegidos para activar a replicação concluída e recuperação. Esta secção descreve como:

-   Configurar o Active Directory e DNS replicação (opcional)

-   Utilizar a recuperação de Site do Azure para ativar a proteção do servidor de ficheiros VM

-   Ativar a proteção de volumes de StorSimple

-   Configurar a rede

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Configurar o Active Directory e DNS replicação (opcional)

Se pretende proteger os computadores com o Active Directory e DNS, para que fiquem disponíveis no site de DR, terá de explicitamente protegê-las (para que os servidores de ficheiro são acessíveis após falhas através com autenticação). Existem duas opções recomendadas, com base na complexidade da-ambiente no local do cliente.

#### <a name="option-1"></a>Opção 1

Se o cliente tem um pequeno número de aplicações, um controlador de domínio simples para todo o site no local e irá falhar ao longo de todo o site, em seguida, recomendamos que utilize replicação Azure recuperação de Site para criar uma réplica da máquina de controlador de domínio para um site secundário (isto é aplicável para o site para o site e site para Azure).

#### <a name="option-2"></a>Opção 2

Se o cliente tem um grande número de aplicações, está a ser executado uma floresta do Active Directory e será a falhar sobre algumas aplicações de cada vez, em seguida, recomendamos que para configurar o controlador de domínio adicional no site de DR (um site de secundário ou no Azure).

Consulte a [solução de DR automática para Active Directory e DNS utilizando Azure a recuperação de Site](../site-recovery/site-recovery-active-directory.md) para obter instruções quando disponibilizar um controlador de domínio no site de DR. Para o resto do documento, podemos irá assumir que um controlador de domínio está disponível no site de DR.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Utilizar a recuperação de Site do Azure para ativar a proteção do servidor de ficheiros VM

Este passo requer que preparar o ambiente de servidor do ficheiro no local, criar e preparar uma Cofre de recuperação de sites do Azure e ativar proteção de ficheiros da VM.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>Para preparar o ambiente de servidor do ficheiro no local

1.  Configurar o **controlo de conta de utilizador** para **nunca notificar**. O que é necessário para que possa utilizar Azure automatização de scripts para ligar os destinos iSCSI após falhas início ao Azure recuperação de sites.

    1.  Prima a tecla Windows + Q e procurar **UAC**.

    2.  Selecione **as definições de controlo de conta de utilizador de alterar**.

    3.  Arraste a barra para a parte inferior no sentido **Nunca notificar**.

    4.  Clique em **OK** e, em seguida, selecione **Sim** quando lhe for pedido.

        ![](./media/storsimple-dr-using-asr/image1.png)

1.  Instale o agente VM em cada uma das VMs de servidor de ficheiros. O que é necessário para que pode executar scripts de automatização Azure na falha ao longo do VMs.

    1.  [Transfira o agente](http://aka.ms/vmagentwin) para `C:\\Users\\<username>\\Downloads`.

    2.  Abra o Windows PowerShell no modo de administrador (executar como administrador) e, em seguida, introduza o seguinte comando para navegar para a localização da transferência:

        `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

        > [AZURE.NOTE] O nome do ficheiro podem ser diferentes dependendo da versão.

1.  Clique em **seguinte**.

2.  Aceite os **Termos do contrato** e, em seguida, clique em **seguinte**.

3.  Clique em **Concluir**.


1.  Crie partilhas de ficheiros utilizando volumes gravados StorSimple armazenamento. Para mais informações, consulte o artigo [utilizar o serviço do Gestor de StorSimple para gerir volumes](storsimple-manage-volumes.md).

    1.  No seu VMs no local, prima a tecla Windows + Q e procurar **iSCSI**.

    2.  Selecione **iniciador iSCSI**.

    3.  Selecione o separador **configuração** e copie o nome do iniciador.

    4.  Inicie a sessão [portal clássica Azure](https://manage.windowsazure.com/).

    5.  Selecione o separador **StorSimple** e, em seguida, selecione o serviço do Gestor de StorSimple que contém o dispositivo físico.

    6.  Criar recipiente de volume e, em seguida, Criar volume (s). (Estes volumes destinam-se a partilha de ficheiro no servidor de ficheiros VMs). Copie o nome do iniciador e atribua um nome apropriado para os registos de controlo de acesso, quando criar os volumes.

    7.  Selecione o separador de **Configurar** e a nota para baixo o endereço IP do dispositivo.

    8.  No seu VMs no local, vá para o **iniciador iSCSI** novamente e introduza o período de inquérito na secção ligar rápida. Clique em **Ligar rápida** (o dispositivo deverá agora estar ligado).

    9.  Abra o Portal de gestão do Azure e selecione o separador **Volumes e dispositivos** . Clique em **configurar automaticamente**. Deverá aparecer o volume que acabou de criar.

    10. No portal do, selecione o separador **dispositivos** e, em seguida, selecione **criar um novo dispositivo Virtual.** (Este dispositivo virtual será utilizado caso ocorra um activação pós-falha). Este novo dispositivo virtual pode ser mantido em estado offline para evitar custos adicionais. Para colocar o dispositivo virtual offline, aceda à secção **máquinas virtuais** no Portal do e encerrá-lo.

    11. Regresse ao VMs no local e abra a gestão de disco (prima a tecla Windows + X e selecione **Gestão de disco**).

    12. Vai notar alguns discos extra (consoante o número de volumes de que ter criado). A primeira parte de contexto, selecione **Iniciar no disco**e selecione **OK**. A secção **Unallocated** de contexto, selecione **Novo Volume simples**, atribuir-lhe uma letra de unidade e concluir o assistente.

    13. Repita o passo l para todos os discos. Agora pode ver todos os discos no **Este PC** no Explorador do Windows.

    14. Utilize a função de serviços de armazenamento de ficheiros e criar partilhas de ficheiros nestes volumes.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>Para criar e preparar uma Cofre de recuperação de sites do Azure

Consulte a [documentação de recuperação de sites do Azure](../site-recovery/site-recovery-hyper-v-site-to-azure.md) para começar a utilizar o Azure Site recuperação antes de proteger o servidor de ficheiros VM.

#### <a name="to-enable-protection"></a>Para ativar proteção

1.  Desliga o destino de iSCSI (s) de VMs no local que pretende proteger através da Azure recuperação de Site:

    1.  Prima a tecla Windows + Q e procurar **iSCSI**.

    2.  Selecione **configurar o iniciador iSCSI**.

    3.  Desligue o dispositivo de StorSimple ligado anteriormente. Em alternativa, pode alternar desativar o servidor de ficheiros para alguns minutos quando activar a proteção.

    > [AZURE.NOTE] Isto vai fazer com que as partilhas de ficheiros estar temporariamente indisponível

1.  [Ativar a proteção de máquina virtual](../site-recovery/site-recovery-hyper-v-site-to-azure.md##step-6-enable-replication) do servidor VM ficheiro a partir do portal de recuperação de sites do Azure.

2.  Quando começa a sincronização inicial, pode voltar a ligar o destino novamente. Ir para o iniciador iSCSI, selecione o dispositivo StorSimple e clique em **Ligar**.

3.  Quando a sincronização está concluída e o estado da VM estiver **protegida**, selecione a VM, selecione o separador de **Configurar** e atualizar a rede da VM em conformidade (isto é a rede que falha ao longo do VM(s) será uma parte do). Se a rede não for apresentada, significa que a sincronização é ainda o problema?.

### <a name="enable-protection-of-storsimple-volumes"></a>Ativar a proteção de volumes de StorSimple

Se não tiver selecionado a opção **Ativar uma cópia de segurança predefinidos para este volume** para os volumes de StorSimple, aceda às **Políticas de cópia de segurança** no serviço StorSimple gestor e criar uma política de cópia de segurança adequada para todos os volumes. Recomendamos que o utilizador defina a frequência das cópias de segurança do objectivo de ponto de recuperação (RPO) que pretende ver para a aplicação.

### <a name="configure-the-network"></a>Configurar a rede

Para o servidor do ficheiro VM, configure as definições de rede Azure recuperação de sites para que as redes VM estão ligadas à rede de DR correcta após activação pós-falha.

Pode selecionar a VM no **VMM nuvem** ou no **Grupo proteção** para configurar as definições de rede, conforme apresentado na seguinte ilustração.

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

Pode criar um plano de recuperação de recuperação automática para automatizar o processo de activação pós-falha de partilhas de ficheiros. Se uma interrupção ocorrer, pode trazer consigo partilhas de ficheiros para cima de alguns minutos com apenas um único clique. Para permitir que este automatização, terá uma conta de automatização Azure.

#### <a name="to-create-the-account"></a>Para criar a conta

1.  Aceda ao portal clássico do Azure e aceda à secção de **Automatização** .

1.  Crie uma nova conta de automatização. Mantenha-a na mesma geo/região em que as contas de armazenamento e StorSimple nuvem aparelho foram criadas.

2.  Clique em **Novo** &gt; **Serviços de aplicação** &gt; **Automatização** &gt; **livro execuções** &gt; **a partir da Galeria** para importar todas as runbooks necessários para a conta de automatização.

    ![](./media/storsimple-dr-using-asr/image3.png)

1.  Adicione o seguintes runbooks a partir do painel de **Recuperação de falhas** na Galeria de:

    -   Falha ao longo do StorSimple contentores de volume

    -   Limpar o de volumes de StorSimple depois de teste activação pós-falha (TFO)

    -   Montagem volumes no dispositivo StorSimple após activação pós-falha

    -   Iniciar aparelho Virtual StorSimple

    -   Desinstalar a extensão de scripts personalizados na Azure VM

        ![](./media/storsimple-dr-using-asr/image4.png)


1.  Publica todos os scripts ao selecionar livro de execuções na conta de automatização e ir para o separador de **autor** . Após neste passo, o separador **Runbooks** aparecerá da seguinte forma:

     ![](./media/storsimple-dr-using-asr/image5.png)

1.  Na conta de automatização aceda ao separador **recursos** , clique em **Adicionar definição** &gt; **Adicionar credenciais**e adicionar as suas credenciais Azure – nome do elemento AzureCredential.

    Utilize as credenciais do Windows PowerShell. Isto deve ser uma credencial que contém um nome de utilizador do ID da Org e a palavra-passe com acesso a esta subscrição Azure e com autenticação multifator desativado. Isto é necessário para autenticar em nome de utilizador durante a activações pós-falha e para ativar os volumes de servidor do ficheiro no site de DR.

1.  Na conta de automatização, selecione o separador de **recursos** e, em seguida, clique em **Adicionar definição** &gt; **variável de adicionar** e adicione as seguintes variáveis. Pode escolher encriptar estes elementos. Estas variáveis são recuperação plano – específicos. Se a recuperação planeia (que irá criar no próximo passo) nome é TestPlan, em seguida, variáveis devem ser TestPlan StorSimRegKey, TestPlan AzureSubscriptionName e assim sucessivamente.

    -   *RecoveryPlanName* **-StorSimRegKey**: A chave de registo para o serviço do Gestor de StorSimple.

    -   *RecoveryPlanName* **-AzureSubscriptionName**: O nome da subscrição do Azure.

    -   *RecoveryPlanName* **-NomeRecurso**: O nome do recurso StorSimple que tem o dispositivo StorSimple.

    -   *RecoveryPlanName* **Nome de dispositivo-**: O dispositivo que tem de ser Ocorreu uma falha ao longo do.

    -   *RecoveryPlanName* **-TargetDeviceName**: O StorSimple nuvem aparelho no qual os contentores são de falha ao longo do.

    -   *RecoveryPlanName* **-VolumeContainers**: uma cadeia separados por vírgulas de contentores de volume presentes no dispositivo que precisam de ser Ocorreu uma falha ao longo do; Por exemplo, volcon1, volcon2, volcon3.

    -   RecoveryPlanName**-TargetDeviceDnsName**: O nome do serviço do dispositivo de destino (Isto pode ser encontrado na secção de **Máquina Virtual** : o nome do serviço é o mesmo que o nome de DNS).

    -   *RecoveryPlanName* **-StorageAccountName**: O nome da conta de armazenamento na qual o script (que tem de ser executada na falha ao longo do VM) será armazenado. Isto pode ser qualquer conta de armazenamento tem algum espaço para armazenar temporariamente o script.

    -   *RecoveryPlanName* **-StorageAccountKey**: A tecla de acesso para a conta de armazenamento acima.

    -   *RecoveryPlanName* **-ScriptContainer**: O nome do contentor na qual o script será armazenado na nuvem. Se o contentor não existir, será criado.

    -   *RecoveryPlanName* **-VMGUIDS**: relativamente a proteger uma VM, recuperação de sites do Azure atribui cada VM um ID exclusivo que lhe dá os detalhes da falha ao longo do VM. Para obter o VMGUID, selecione o separador **Serviços de recuperação** e, em seguida, clique em **Item protegido por** &gt; **Grupos de proteção** &gt; **máquinas** &gt; **Propriedades**. Se tiver várias VMs, em seguida, adicione os GUID como uma cadeia separados por vírgulas.

    -   *RecoveryPlanName* **-AutomationAccountName** – o nome da conta de automatização em que tiver adicionado o runbooks e os ativos.

    Por exemplo, se o nome do plano de recuperação for fileServerpredayRP, em seguida, o separador de **activos** deverá aparecer da seguinte forma depois de adicionar todos os ativos.

    ![](./media/storsimple-dr-using-asr/image6.png)


1.  Aceda à secção **Serviços de recuperação** e selecione o Cofre Azure recuperação de sites que criou anteriormente.

2.  Selecione o separador **Planos de recuperação** e crie um novo plano de recuperação da seguinte forma:

    um.  Especifique um nome e selecione o **Grupo proteção**de adequado.

    b.  Selecione os VMs do grupo de proteção que pretende incluir no plano de recuperação.

    c.  Após a recuperação ter é criado plano, selecione-o para abrir a vista de personalização de plano de recuperação.

    d.  Selecione **todas as encerramento de grupos**, clique em **Script**e selecione **Adicionar um script do lado primária antes de todos os encerramento do grupo**.

    "e".  Selecione a conta de automatização (na qual adicionou a runbooks) e, em seguida, selecione o livro de execuções **falhar Volume-contentores de sobre-StorSimple** .

    f.  Clique em **grupo 1: Iniciar**, selecione **máquinas virtuais**e adicionar os VMs que estão a ser protegidos no plano de recuperação.

    g.  Clique em **grupo 1: Iniciar**, escolha o **Script**e adicione os seguintes scripts por ordem como passos **após 1 de grupo** .

    - Livro de execuções do início-StorSimple-Virtual-aparelho
    - Livro de execuções de volume-contentores de sobre-StorSimple de falhar
    - Livro de execuções montagem com volumes após falha
    - Livro de execuções desinstalar-personalizada-script-extensão

1.  Adicionar uma acção manual após os scripts de 4 acima na mesma **grupo 1: passos pós** secção. Esta ação é o ponto no qual pode verificar que tudo está a funcionar corretamente. Esta ação tem de ser adicionados apenas como uma parte do activação de teste pós-falha (apenas por isso, seleccione a **Testar activação pós-falha** caixa de verificação).

2.  Após a acção manual, adicione o script de limpeza utilizando o mesmo processo que utilizou para as outras runbooks. Guarde o plano de recuperação.

    > [AZURE.NOTE] Quando executa um teste activação pós-falha, deve verificar tudo no passo ação manual porque os volumes de StorSimple tivessem sido clonar no dispositivo destino serão eliminados como parte da limpeza do depois de concluída a ação manual.

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>Realizar um ensaio activação pós-falha

Consulte o Guia do Assistente de [Solução de DR do Active Directory](../site-recovery/site-recovery-active-directory.md) considerações específicas para o Active Directory durante a mudança de teste. A configuração no local não é dividida em todos os quando ocorre o teste activação pós-falha. Os volumes de StorSimple que estavam anexados à VM no local são clonar ao aparelho nuvem StorSimple no Azure. Uma VM para fins de teste for colocada para cima no Azure e os volumes clonados estão anexados à VM.

#### <a name="to-perform-the-test-failover"></a>Para executar o teste activação pós-falha

1.  No portal do Azure clássico, selecione Cofre de recuperação de site.

1.  Clique no plano de recuperação criado para o servidor do ficheiro VM.

2.  Clique em **Testar activação pós-falha**.

3.  Selecione a rede virtual para iniciar o processo de activação pós-falha de teste.

    ![](./media/storsimple-dr-using-asr/image8.png)

1.  Quando o ambiente secundário é para cima, pode executar as validações.

2.  Depois das validações estiverem concluídas, clique em **Validações concluída**. O ambiente de activação pós-falha de teste será possível limpar e, a operação de TFO vai ser concluída.

## <a name="perform-an-unplanned-failover"></a>Executar uma activação não planeado pós-falha

Durante uma falha na ligação não planeada, são os volumes de StorSimple Ocorreu uma falha ao longo do dispositivo virtual, uma réplica VM será importada para cima no Azure e os volumes estão anexados à VM.

#### <a name="to-perform-an-unplanned-failover"></a>Para efetuar uma activação não planeado pós-falha

1.  No portal do Azure clássico, selecione Cofre de recuperação de site.

1.  Clique no plano de recuperação criado para servidor de ficheiros VM.

2.  Clique em **activação pós-falha** e, em seguida, selecione **Não planeado activação pós-falha**.

    ![](./media/storsimple-dr-using-asr/image9.png)

1.  Selecione a rede de destino e, em seguida, clique no ícone de verificação ✓ para iniciar o processo de activação pós-falha.

## <a name="perform-a-planned-failover"></a>Executar uma activação planeada pós-falha

Durante uma falha na ligação planeada, no local servidor de ficheiros que VM encerrar correctamente e uma nuvem instantâneo cópia de segurança dos volumes no dispositivo StorSimple é disponibilizado. Os volumes de StorSimple são Ocorreu uma falha ao longo do dispositivo virtual, uma réplica VM seja trazida Azure e os volumes estão anexados à VM.

#### <a name="to-perform-a-planned-failover"></a>Para efetuar uma activação planeada pós-falha

1.  No portal do Azure clássico, selecione Cofre de recuperação de site.

1.  Clique no plano de recuperação criado para o servidor do ficheiro VM.

2.  Clique em **activação pós-falha** e, em seguida, selecione **Planeado activação pós-falha**.

3.  Selecione a rede de destino e, em seguida, clique no ícone de verificação ✓ para iniciar o processo de activação pós-falha.

## <a name="perform-a-failback"></a>Executar uma reposição de recurso

Durante a reposição de uma recurso, contentores de volume StorSimple são Ocorreu uma falha ao longo do novamente para o dispositivo físico depois de uma cópia de segurança é disponibilizada.

#### <a name="to-perform-a-failback"></a>Para efetuar uma reposição de recurso

1.  No portal do Azure clássico, selecione Cofre de recuperação de site.

1.  Clique no plano de recuperação criado para o servidor do ficheiro VM.

2.  Clique em **activação pós-falha** e selecione **planeada activação pós-falha** ou **não planeado activação pós-falha**.

3.  Clique em **alterar a direcção**.

4.  Selecione a sincronização de dados adequado e opções de criação de VM.

5.  Clique no ícone de verificação ✓ para iniciar o processo de reposição de recurso.

    ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>Melhores práticas

### <a name="capacity-planning-and-readiness-assessment"></a>Capacidade de planeamento e disponibilidade de avaliação


#### <a name="hyper-v-site"></a>Site de Hyper-V

Utilize a [ferramenta de Planeador de capacidade de utilizador](http://www.microsoft.com/download/details.aspx?id=39057) para estruturar o servidor, armazenamento e infraestrutura de rede para o seu ambiente de réplica Hyper-V.

#### <a name="azure"></a>Azure

Pode executar a [ferramenta de avaliação do Azure Máquina Virtual preparação](http://azure.microsoft.com/downloads/vm-readiness-assessment/) no VMs para se certificar de que estes provêm compatíveis com o Azure VMs e dos serviços de recuperação do Azure Site. A ferramenta de avaliação de preparação verifica configurações VM e avisa quando configurações são compatíveis com o Azure. Por exemplo, emite um aviso se uma unidade c: for maior que 127 GB.


Planeamento de capacidades é constituída, pelo menos, dois processos importantes:

-   Mapeamento no local VMs Hyper-V para tamanhos de Azure VM (tal como A6, A7, A8 e A9).

-   Determinar a largura de banda de Internet necessária.

## <a name="limitations"></a>Limitações

- Atualmente, apenas 1 StorSimple dispositivo pode ser Ocorreu uma falha ao longo do (para uma única aparelho de nuvem StorSimple). O cenário de servidor de ficheiros que abrange vários dispositivos de StorSimple ainda não é suportado.

- Se obtiver um erro durante a ativação de proteção para uma VM, certifique-se de que tem desligado os destinos iSCSI.

- Todos os contentores de volume terem sido agrupados em conjunto devido a cópia de segurança políticas que abrangem em contentores de volume vai ser Ocorreu uma falha ao longo em conjunto.

- Todos os volumes os contentores de volume que escolheu Ocorreu uma falha ao longo do.

- Volumes que adicionar até mais do que 64 TB não podem ser Ocorreu uma falha ao longo do porque a capacidade de um único nuvem aparelho de StorSimple máxima é 64 TB.

- Se o planeado/planeado activação pós-falha falha e os VMs são criados no Azure, em seguida, não limpar os VMs. Em alternativa, faça reposição de uma recurso. Se eliminar as VMs, em seguida, os VMs no local não podem ser ativadas novamente.

- Após uma falha na ligação, se não for possível ver os volumes, vá para os VMs, abra a gestão de disco, analisar novamente os discos e, em seguida, coloque-os online.

- Em alguns casos, as letras de unidade no site de DR podem ser diferentes de letras no local. Se tal acontecer, terá de manualmente corrigir o problema depois da activação pós-falha está concluída.

- Autenticação multifator deve ser desactivada para a credencial Azure que é introduzida na conta automatização como um ativo. Se não estiver desactivada esta autenticação, scripts não são permitidos para ser executada automaticamente e o plano de recuperação irá falhar.

- Tempo limite da tarefa de activação pós-falha: StorSimple o script irá expirar se a activação pós-falha de contentores de volume demora mais tempo do que o limite de recuperação de sites do Azure por script (atualmente 120 minutos).

- Tempo limite da tarefa de cópia de segurança: StorSimple o script vezes se a cópia de segurança de volumes demora mais tempo do que o limite de recuperação de sites do Azure por script (atualmente 120 minutos).
 
    > [AZURE.IMPORTANT] Executar a cópia de segurança manualmente a partir do portal do Azure e, em seguida, execute novamente o plano de recuperação.

- Clonar tempo limite do trabalho: StorSimple o script vezes se clonar de volumes demora mais tempo do que o limite de recuperação de sites do Azure por script (atualmente 120 minutos).

- Erro de sincronização de tempo: StorSimple de scripts de erros de saída dizer que as cópias de segurança foram concluídas com êxito, apesar da cópia de segurança é bem sucedida no portal. Uma causa possível para que esta poderá que tempo do aparelho StorSimple poderá ser sincronizado com a hora atual no fuso horário.
 
    > [AZURE.IMPORTANT] Sincronize a hora do aparelho com a hora atual no fuso horário.

- Erro de activação pós-falha do aparelho: StorSimple o script poderá falhar se existir um aparelho activação pós-falha quando o plano de recuperação está em execução.
    
    > [AZURE.IMPORTANT] Volte a executar o plano de recuperação depois de concluída a aparelho activação pós-falha.

## <a name="summary"></a>Resumo

Utilizar o Azure recuperação de sites, pode criar um plano de recuperação de falhas automatizado completa para um servidor de ficheiros VM tendo partilhas de ficheiros alojadas no StorSimple armazenamento. Pode iniciar o activação pós-falha em segundos a partir de qualquer lugar trabalho uma interrupção e obter a aplicação de cima e a execução de alguns minutos.
