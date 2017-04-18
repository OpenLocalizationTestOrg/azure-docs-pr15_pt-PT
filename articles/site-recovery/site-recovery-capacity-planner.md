<properties
    pageTitle="Planear a capacidade para proteger máquinas virtuais e servidores físicos no Azure Site recuperação | Microsoft Azure"
    description="Recuperação de Site Azure coordenadas a replicação, activação pós-falha e recuperação de máquinas virtuais e servidores físicos localizados no local para Azure ou para um site secundário no local." 
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
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Planear a capacidade para proteger máquinas virtuais e servidores físicos no Azure recuperação de sites

A ferramenta de Planeador de capacidade de recuperação do Azure Site ajuda-o a descobrir as necessidades de capacidade para proteger Hyper-V VMs, VMware VMs e Windows/Linux servidores físicos com Azure recuperação de Site.


## <a name="overview"></a>Descrição geral

Utilize o planeador de capacidade de recuperação de Site para analisar o seu ambiente de origem e das cargas de trabalho e descubra necessidades de largura de banda, recursos do servidor que tem na sua localização de origem e de recursos (máquinas virtuais e armazenamento etc), que terá na sua localização de destino. 

Pode executar a ferramenta de duas modos:

- **Planeamento rápida**: executar a ferramenta de neste modo para obter projecções de rede e server com base no número médio de VMs, discos, armazenamento e alterar a velocidade.
- **Planeamento detalhado**: execute a ferramenta neste modo e forneça detalhes de cada carga de trabalho ao nível VM. Analisar compatibilidade VM e obter projecções de rede e servidor.

## <a name="before-you-start"></a>Antes de começar

Antes de executar a ferramenta de:

1. Reunir informações sobre o ambiente, incluindo VMs, discos por VM, armazenamento por disco.
2. Identifique a taxa de alteração (vasilha) diária para dados replicadas. Para fazer isto:

    - Se estiver a replicação Hyper-V VMs, transfira a [ferramenta de planeamento de capacidade de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obter a taxa de alteração. [Saiba mais](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre esta ferramenta. Recomendamos que execute esta ferramenta através de uma semana para capturar médias.
    - Se estiver a replicação máquinas virtuais de VMware, utilize a [capacidade de vSphere planeamento aparelho](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) descobrir a taxa de vasilha.
    - Se estiver a replicação servidores físicos terá estimar manualmente.

## <a name="run-the-quick-planner"></a>Executar o planeador de rápida
1.  Transfira e abra a ferramenta de [Planeador de capacidade de recuperação do Azure Site](http://aka.ms/asr-capacity-planner-excel) . Terá de executar macros por isso, seleccione para ativar a edição e ativar o conteúdo quando lhe for pedido. 
2.  Em **selecionar um tipo de Planeador** selecione **Planeador rápida** da caixa de listagem.

    ![Introdução](./media/site-recovery-capacity-planner/getting-started.png)

3.  Na folha de cálculo de **Planeador de capacidade** introduza as informações necessárias. Tem de preencher todos os campos dentro de um círculo vermelho na captura de ecrã abaixo.

    - Em **Selecione o seu cenário** escolha **Hyper-V para Azure** ou **VMware/física para Azure**.
    - Em **média dados diária alterar taxa (%)** colocar as informações recolher utilizando a [ferramenta de planeamento de capacidade de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) ou a [capacidade de vSphere planeamento aparelho](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
    - **Compressão** só se aplica à compressão oferecida quando de replicadas VMware VMs ou servidores físicos para o Azure. Vamos estimar 30% ou mais, mas pode modificar a definição conforme necessário. Para a replicação Hyper-V VMs à compressão Azure que pode utilizar um dispositivo de terceiros tal como Riverbed. 
    -  **Entradas de retenção** especificar quanto tempo réplicas devem ser mantidas. Se estiver a replicação VMware ou o valor de entrada de servidores físicos em dias. Se estiver a replicação Hyper-V especificar o tempo em horas.
    -  Em **número de horas na qual a replicação inicial para o lote de máquinas virtuais deve ser concluídas** e o **número de máquinas virtuais por lote de replicação inicial** entrada definições que são utilizadas para calcular os requisitos de replicação inicial.  Quando é implementada recuperação de sites o conjunto de dados inicial inteiro deve carregado. 

    ![Entradas do tipo](./media/site-recovery-capacity-planner/inputs.png)

2.  Depois de ter colocar os valores para o ambiente de origem, saída apresentada inclui:

    - **Largura de banda necessários para a replicação delta** (MB/seg.). Largura de banda de rede para a replicação delta é calculada a taxa de alterar dados diária média.
    - **Largura de banda necessários para a replicação inicial** (MB/seg.). Largura de banda de rede para a replicação inicial é calculada os valores de replicação inicial que coloca na. 
    - **Armazenamento exigido (GB)** é o armazenamento total Azure necessário.
    - **Total IOPS contas de armazenamento padrão** é calculado com base no tamanho da unidade IOPS 8 K contas de armazenamento total de padrão.  Para o planeador rápida do que número é calculado com base em todos os discos VMs de origem e dados diária altere taxa. Para o planeador detalhadas que o número é calculado com base no número total de VMs são mapeados para padrão Azure VMs e dados altere taxa nesses VMs. 
    - **Número de contas de armazenamento padrão** fornece o número total de contas de armazenamento padrão conforme necessário para proteger as VMs. Note que uma conta de armazenamento padrão pode conter até 20000 IOPS através de todas as VMs num armazenamento padrão e máximos 500 IOPS suportados por disco. 
    - **Número de discos blob obrigatório** dá o número de discos que serão criados no Azure armazenamento.
    - **Número de contas de armazenamento premium obrigatório** fornece o número total de contas de armazenamento de premium necessários para proteger as VMs. Tenha em atenção que uma origem de VM com IOPS alta (maior que 20000) necessita de uma conta de armazenamento de premium. Uma conta de armazenamento premium pode conter até 80000 IOPS.
    - **IOPS total sobre o armazenamento de premium** é calculado com base no tamanho da unidade IOPS 256 K contas de armazenamento total premium.  Para o planeador rápida do que número é calculado com base em todos os discos VMs de origem e dados diária altere taxa. Para o planeador detalhadas que o número é calculado com base no número total de VMs são mapeados para premium Azure VM (série DS e s) e os dados altere taxa nesses VMs. 
    - **Número de servidores de configuração necessário** mostra quantas servidores de configuração são necessários para a implementação (1)
    - **Número de servidores de processo adicionais necessários** mostra se os servidores de processo adicionais são necessários para além de servidor processo que é configurado no servidor de configuração por predefinição.
    - **armazenamento adicional de 100% na origem** mostra se armazenamento adicional é necessária na localização de origem.
            
    ![Saída](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>Executar o planeador de detalhadas


1.  Transfira e abra a ferramenta de [Planeador de capacidade de recuperação do Azure Site](http://aka.ms/asr-capacity-planner-excel) . Terá de executar macros por isso, seleccione para ativar a edição e ativar o conteúdo quando lhe for pedido. 
2.  Em **selecionar um tipo de Planeador** selecione **Planeador detalhadas** na caixa de listagem.

    ![Introdução](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  Na folha de cálculo de **Carga de trabalho qualificação** introduza as informações necessárias. Tem de preencher todos os campos marcados.

    - Na **núcleos processador** especifique o número total de núcleos num servidor de origem.
    - Na **alocação de memória em MB** especifique o tamanho de RAM de um servidor de origem. 
    - O **Número de NIC** especificar o número de adaptadores de rede num servidor de origem. 
    -  Em **Armazenamento Total (em GB)** especifique o tamanho total do armazenamento VM. Por exemplo, se o servidor de origem tem 3 discos com 500 GB, em seguida, tamanho do armazenamento total está 1500 GB.
    -  Em **número de discos ligados** especifique o número total de discos de um servidor de origem.
    -  **Utilização das capacidades disco** especificar a utilização da média.
    -  Na **diariamente alterar taxa (%)** especifique os dados diários alterar a taxa de um servidor de origem.
    -  No **tamanho de mapeamento Azure** introduza tamanho da memória virtual de Azure que pretende mapear. Se não quiser fazer isto manualmente, clique na**Calcular VMs IaaS**. Tenha em atenção que se uma definição de manual de introdução e, em seguida, clique em calcular VMs IaaS a definição manual poderá ser substituída uma vez que o processo de cluster identifica automaticamente a melhor correspondência no tamanho da memória virtual do Azure.

    ![Qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  Se clicar em **Calcular VMs IaaS** aqui é o que faz:

    - Validar as entradas obrigatórias.
    - Calcula IOPS e sugere a VM Azure melhor aize correspondência para cada VMs que é elegível para a replicação Azure. Se um tamanho adequado da Azure VM não é possível detetado que um erro é emitido. Por exemplo se o número de discos anexados no 65 um erro, está problemas desde o tamanho mais alto Azure VM é 64.
    - Sugere a uma conta de armazenamento que pode ser utilizada para uma VM Azure.
    - Calcula o número total de armazenamento padrão contas e premium armazenamento necessário para a carga de trabalho. Deslocar para baixo à direita para ver o tipo de armazenamento Azure e a conta de armazenamento que pode ser utilizada para um servidor de origem
    - Conclui e ordena o resto da tabela com base no tipo de armazenamento necessária (padrão ou premium) atribuído para uma VM e o número de discos ligados. Para todos os VMs que cumpra os requisitos para cópia de segurança para Azure, coluna A (é VM completamente qualificado?) mostra Sim. Se uma VM não pode ser cópias até Azure um erro é apresentada.

Colunas AA para SÇ são o resultado e fornecem informações para cada VM.

![Qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>Exemplo
Por exemplo, para seis VMs com os valores mostrados na tabela, a ferramenta calcula e atribui a melhor correspondência Azure VM e os requisitos de armazenamento Azure.

![Qualificação de carga de trabalho](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- No resultado para que o exemplo que se tenha em atenção o seguinte procedimento:
    
    - A primeira coluna é uma coluna de validação para VMs, discos e vasilha.
    - Duas contas de armazenamento padrão e conta de armazenamento de uma premium são necessários para cinco VMs. 
    -  VM3 não qualificar-me para protecção por uma vez que um ou mais discos são mais do que 1 TB.
    -  VM1 e VM2 podem usar a primeira conta de armazenamento padrão
    -  VM4 pode utilizar a segunda conta de armazenamento padrão.
    -  VM5 e VM6 necessitam de uma conta de armazenamento de premium e podem utilizar ambas uma única conta.

    >[AZURE.NOTE]  IOPS armazenamento padrão e premium são calculados ao nível do VM e não ao nível do disco. Até 500 IOPS por disco pode lidar com uma máquina virtual padrão. Se IOPS para um disco sejam maiores do que 500 terá de armazenamento de premium. No entanto se IOPS para um disco forem mais de 500 mas IOPS para os discos VM total dentro suporte padrão Azure VM dos limites (tamanho VM, número de discos, número de memória placas, CPU,), em seguida, o planeador de escolhe um padrão de série VM e não no DS ou s. Terá de atualizar manualmente a célula de tamanho Azure mapeamento com adequado DS ou s Séries VM.

5. Depois de todos os detalhes estão no local, clique em **Submeter dados para a ferramenta de Planeador** de para abrir o **Planeador de capacidade** das cargas de trabalho estão realçadas para mostrar se estiverem elegíveis para protecção ou não.


### <a name="submit-data-in-the-capacity-planner"></a>Submeter dados no Planeador de capacidade

1.  Quando abre a folha de cálculo de **Planeador de capacidade** é povoada com base nas definições que especificou. A palavra 'Carga de trabalho' é apresentado na célula de **origem de entradas do tipo de Infra** para mostrar que a folha de cálculo de **Carga de trabalho qualificação** entrada. 
2.  Se pretender efetuar alterações terá modificar a folha de cálculo **Qualificação carga de trabalho** e clique em submeter dados para a ferramenta de Planeador novamente.  

    ![Planeador de capacidade](./media/site-recovery-capacity-planner/capacity-planner.png)


