<properties
    pageTitle="Executar a ferramenta de Planeador de capacidade de Hyper-V para a recuperação do Site | Microsoft Azure"
    description="Este artigo contém instruções sobre como utilizar a ferramenta de Planeador de capacidade Hyper-V para a recuperação de Site do Azure"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Executar a ferramenta de Planeador de capacidade de Hyper-V para a recuperação do Site

Como parte da sua implementação do Azure Site recuperação terá descobrir as necessidades de largura de banda e a replicação. A ferramenta de Planeador de capacidade Hyper-V para o Site recuperação ajuda-o a descobrir seus requisitos de largura de banda e a replicação para a replicação de máquina virtual Hyper-V.


Este artigo descreve como executar a ferramenta de Planeador de capacidade Hyper-V. Esta ferramenta deve ser utilizada juntamente com as outras ferramentas de planeamento de capacidade e informações de descrito no [planeamento de capacidade para recuperação de sites](site-recovery-capacity-planner.md).


## <a name="before-you-start"></a>Antes de começar

Execute a ferramenta num nó servidor ou cluster Hyper-V no seu site principal. Para executar a ferramenta os servidores de anfitriões Hyper-V precisa:

- Sistema operativo: Windows Server® 2012 ou Windows Server® 2012 R2
- Memória: 20 MB (mínimo)
- CPU: Sobrecarga de 5 por cento (mínimo)
- Espaço em disco: 5 MB (mínimo)

Antes de executar a ferramenta de terá de preparar o site principal. Se estiver a replicação entre dois locais sites e que pretende verificar a largura de banda, terá de preparar o servidor de réplica.


## <a name="step-1-prepare-the-primary-site"></a>Passo 1: Preparar o site principal
1. No site principal tornar uma lista de todas as máquinas virtuais de Hyper-V que pretende criar uma réplica e anfitriões/clusters de Hyper-V no qual se encontram. A ferramenta pode executada sempre que para vários anfitriões autónomo, ou para um único cluster mas não ambos em conjunto. Também precisa para executar separadamente para cada sistema operativo, pelo que deverá reunir e tenha em atenção os servidores de Hyper-V da seguinte forma:

  - Servidores do Windows Server® 2012 autónomo
  - Windows Server® 2012 clusters
  - Servidores do Windows Server® 2012 R2 autónomo
  - Windows Server® 2012 R2 clusters

3. Ativar o acesso remoto WMI em todos os anfitriões Hyper-V e clusters. Executar este comando em cada/cluster de servidor para se certificar de que regras de firewall e são definir permissões de utilizador:

        netsh firewall set service RemoteAdmin enable

5. Ativar a monitorização de desempenho em servidores e clusters, da seguinte forma:

  - Abra a Firewall do Windows com **Segurança avançada** snap-in e, em seguida, ativar as seguintes regras de entrada: **COM + aceder à rede (DCOM-IN)** e todas as regras no **Gestão remota do registo de eventos do grupo**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Passo 2: Preparar uma réplica de servidor (no local para a replicação no local)

Não precisa de efetuar esta ação se de que está a ser replicadas para o Azure.

Recomendamos que configurar um único anfitrião Hyper-V como um servidor de recuperação para que uma VM fictícia pode ser replicada para a mesma para verificar a largura de banda.  Pode ignorar esta mas não poderá medir a largura de banda, a menos que pode fazê-lo.

1. Se pretende utilizar um nó de cluster como a réplica configurar corretor réplica Hyper-V:

    - No **Gestor de servidor**, abra o **Gestor de Cluster de activação pós-falha**.
    - Ligar ao cluster, realce o nome do cluster e clique em **ações** > **Função configurar** para abrir o Assistente de elevada disponibilidade.
    - Função **Seleccionar** clique **Hyper-V réplica corretor**. No Assistente de fornecer um **nome de NetBIOS** e o **endereço IP** a ser utilizado como o ponto de ligação ao cluster (denominado um ponto de acesso de cliente). O **Corretor de réplica Hyper-V** será configurada, que resulta num nome de ponto de acesso de cliente que deverá anotar.
    - Certifique-se de que a função Hyper-V réplica corretor vem com êxito online e pode falhar ao longo entre todos os nós do cluster. Para fazer isto, a função de clique direito do rato, aponte para **Mover**e, em seguida, clique em **Selecionar nó**. Seleccionar um nó > **OK**.
    - Se estiver a utilizar uma autenticação baseada em certificado, certifique-se de que cada nó de cluster e o cliente de tudo tem o certificado instalado do ponto de acesso.
2.  Ativar um servidor de réplica:

    - Para um cluster de abrir o Gestor de Cluster de falha, ligar ao cluster e clique em **funções** > função seleccionar > **Definição replicação**s > **Ativar este cluster como uma réplica de servidor**. Tenha em atenção que, se estiver a utilizar um cluster de como a réplica terá de ter a função de Hyper-V réplica corretor presente no cluster no sítio primário.
    - Para um servidor autónomo abra o Gestor de Hyper-V. No painel de **ações** , clique em **Definições de Hyper-V** para o servidor que pretende ativar e, na **Configuração de replicação** clique em **Permitir que este computador como uma réplica de servidor**.
3. Configure autenticação:

    - Na **autenticação todas as portas e** Selecione como autenticar o servidor principal e as portas de autenticação. Se estiver a utilizar o certificado clique em **Selecionar certificado** para selecionar uma. Utilize o protocolo Kerberos se anfitriões de Hyper-V principal e recuperação estiverem no mesmo domínio ou domínios fidedignos. Utilize certificados de domínios diferentes ou uma implementação do grupo de trabalho.
    - Na secção **autorização e armazenamento** , permitir que **qualquer** autenticado servidor (primária) enviar dados de replicação para este servidor de réplica. Clique em **OK** ou **Aplicar**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - Execute **netsh http Mostrar servicestate** para verificar que a escuta está em execução para a protocolo/porta que especificou:  
4. Configure o firewalls. Durante a instalação de Hyper-V são criadas as regras de firewall para permitir o tráfego na predefinição portas (HTTPS no 443, Kerberos no 80). Ativar estas regras da seguinte forma:

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>Passo 3: Executar a ferramenta de Planeador de capacidade

Após ter preparado o seu site principal e configurar um servidor de recuperação pode executar a ferramenta.

1. [Transfira](https://www.microsoft.com/download/details.aspx?id=39057) a ferramenta a partir do Microsoft Download Center.
2. Execute a ferramenta a partir de um dos servidores principais (ou um de nós do cluster principal). Com o botão direito no ficheiro .exe e, em seguida, selecione **Executar como administrador**.
3. **Antes de começar** especificar para quanto tempo que pretende recolher dados. Recomendamos que execute a ferramenta durante o horário de produção para se certificar de que os dados são representante. Se apenas está a tentar validar a conectividade da rede, é possível recolher para apenas um minuto.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Em **Detalhes do site principal** especifique o nome do servidor ou o FQDN para um anfitrião autónomo ou para um cluster de especificar o FQDN do cliente de estação de aceitar ponto, nome de cluster ou qualquer nó no cluster e, em seguida, clique em **seguinte**. A ferramenta de Deteta automaticamente o nome do servidor estiver em execução no. A ferramenta de escolhe o VMs que podem ser monitorizadas para os servidores especificados.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Na **Réplica detalhes do Site** se que está a ser replicadas para o Azure ou se estiver a replicação para um centro de dados secundário e tenha configurado o servidor de réplica, selecione **Ignorar testes que envolvam site réplica**. Se o é replicadas para um centro de dados secundário e que configurou um tipo de réplica o FQDN do servidor autónomo ou o ponto de acesso de cliente para o cluster no **nome do servidor (ou) Hyper-V réplica corretor REMATE**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. No **expandido** detalhes de réplica Active **Ignore os testes de site réplica expandido**. Estes não são suportados pelo recuperação de sites.
7. Em **Escolher VMs a replicação** as ferramentas liga-se para o servidor ou cluster e apresenta VMs e discos em execução no servidor principal, em conformidade com as definições que especificou na página **Detalhes do Site principal** . Tenha em atenção que não estão a executar o VMs que já estejam ativados para replicação ou que não será apresentada. Selecione VMs para o qual pretende recolher métricas. Selecionar os VHDs automaticamente, recolhe dados para os VMs demasiado.
9. Se configurou um servidor de réplica ou cluster, informações de **rede** especifique a largura de banda WAN aproximada pensa que será utilizada entre os sites de principal e réplica e selecione os certificados se configurou a autenticação de certificados.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Num **Resumo de** verificar as definições e clique em **seguinte** para começar a recolher métricas. Progresso da ferramenta e o estado é apresentado na página **Capacidade calcular** . Quando terminar a ferramenta a executar o em **Ver relatório** para abordar o resultado. Por predefinição relatórios e registos são armazenados no **%systemdrive%\Users\Public\Documents\Capacity Planeador**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>Passo 4: Interpretar os resultados
Eis as métricas importantes. Pode ignorar métricas que não estejam listadas aqui. Não está relevantes para a recuperação do Site.

### <a name="on-premises-to-on-premises-replication"></a>No local para a replicação no local
  - Impacto da replicação em cluster o anfitrião principal, memória
  - Impacto da replicação na página principal, espaço em disco do anfitriões de recuperação armazenamento, IOPS
  - Largura de banda total necessária para a replicação delta (Mbps)
  - Largura de banda de rede observados entre o anfitrião de principal e o anfitrião de recuperação (Mbps)
  - Sugestão para o número ideal de transferências paralelas ativos entre os dois anfitriões/clusters

### <a name="on-premises-to-azure-replication"></a>No local para a replicação Azure
  - Impacto da replicação em cluster o anfitrião principal, memória
  - Impacto da replicação armazenamento espaço o anfitrião principal em disco, IOPS
  - Largura de banda total necessária para a replicação delta (Mbps)

## <a name="more-resources"></a>Mais recursos

- Para obter informações detalhadas sobre a ferramenta de ler o documento que acompanha a transferência da ferramenta.
- Ver instruções sobre a ferramenta no de Keith Mayer [TechNet blogue](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
- [Obter os resultados](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) da nossa testes de desempenho para no local para a replicação de Hyper-V no local



## <a name="next-steps"></a>Próximos passos

Depois de concluir a planear a capacidade pode começar a implementar o recuperação de sites:

- [Criar uma réplica Hyper-V VMs no nuvens VMM para Azure](site-recovery-vmm-to-azure.md)
- [Criar uma réplica VMs Hyper-V (sem VMM) para Azure](site-recovery-hyper-v-site-to-azure.md)
- [Criar uma réplica Hyper-V VMs entre VMM sites](site-recovery-vmm-to-vmm.md)
- [Criar uma réplica Hyper-V VMs entre sites VMM com SAN](site-recovery-vmm-san.md)
- [Criar uma réplica hyper-V VMs num servidor VMM único](site-recovery-single-vmm.md)