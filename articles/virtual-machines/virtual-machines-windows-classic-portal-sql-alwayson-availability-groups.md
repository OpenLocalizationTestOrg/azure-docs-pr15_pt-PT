<properties
    pageTitle="Configurar sempre no grupo de disponibilidade no Azure VM - clássico"
    description="Crie um grupo de disponibilidade sempre com máquinas virtuais Azure. Neste tutorial utiliza principalmente as ferramentas de interface de utilizador e em vez de scripting."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Configurar sempre no grupo de disponibilidade no Azure VM - clássico

> [AZURE.SELECTOR]
- [Gestor de recursos: modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gestor de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clássico: IU](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Neste tutorial de ponto a ponto mostra-lhe como implementar o grupos de disponibilidade com SQL Server sempre em execução no Azure máquinas virtuais.

No fim do tutorial, irá consistir a solução do SQL Server sempre no Azure os seguintes elementos:

- Uma rede virtual que contém vários sub-redes, incluindo um front-end e uma sub-rede back-end

- Controlador de domínio com um domínio do Active Directory (AD)

- Duas SQL Server VMs implementado à sub-rede back-end e aderido ao domínio de AD

- Um cluster WSFC 3 nó com o modelo de quórum maioria de nós

- Um grupo de disponibilidade com duas réplicas síncrono consolidação de uma base de dados de disponibilidade

A figura seguinte é uma representação gráfica da solução.

![Testar arquitetura de laboratório para AG no Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Tenha em atenção que se trata de uma configuração possível. Por exemplo, pode minimizar o número de VMs para um grupo de disponibilidade de duas réplicas para poder guardar num cluster de horas no Azure utilizando o controlador de domínio como o fé para partilhar o ficheiro quórum num cluster de WSFC nós de 2. Este método reduz a contagem VM por um partir da configuração acima.

Neste tutorial assume o seguinte procedimento:

- Já tem uma conta Azure.

- Já sabe como pode aprovisionar um VM de servidor SQL clássica a partir da Galeria de máquina virtual utilizando a interface gráfica.

- Já tem uma compreensão sólida sempre no disponibilidade dos grupos de. Para mais informações, consulte o artigo [Sempre no grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se estiver interessado em utilizar sempre no grupos de disponibilidade com o SharePoint, consulte o artigo também [Configurar o SQL Server 2012 sempre On disponibilidade grupos para o SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Criar a rede Virtual e servidor do controlador de domínio

Começar uma nova conta de avaliação Azure. Quando tiver acabado de configuração de conta, deverá no ecrã principal do portal do Azure clássico.

1. Clique no botão de **Novo** no canto inferior esquerdo da página, conforme apresentado abaixo.

    ![Clique em novo no portal](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)

1. Clique em **Serviços de rede**, em seguida, clique em **Rede Virtual** e, em seguida, clique em **Criar personalizado**, conforme apresentado abaixo.

    ![Criar rede Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)

1. Na caixa de diálogo **Criar uma VIRTUAL rede** , crie uma nova rede virtual ao percorrendo as páginas com as definições de abaixo. 

  	|Página|Definições|
|---|---|
|Detalhes de rede virtual|**NOME = ContosoNET**<br/>**REGIÃO = US Ocidental**|
|Os servidores DNS e grupo análise|Nenhum|
|Espaços de endereços de rede virtual|Definições são apresentadas na captura de ecrã abaixo: ![Criar rede Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png)|

1. Em seguida, pode criar a VM que irá utilizar como o controlador de domínio (CC). Clique em **Novo** novamente, em seguida, **Calcular**, em seguida, **Máquina Virtual**e, em seguida, **a partir da Galeria**, conforme apresentado abaixo.

    ![Criar uma VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)

1. Na caixa de diálogo **Criar A VIRTUAL MACHINE** , configure uma nova VM percorrendo as páginas com as definições de abaixo. 

  	|Página|Definições|
|---|---|
|Selecione o sistema operativo de máquina virtual|Centro de dados do Windows Server 2012 R2|
|Configuração de máquina virtual|**Data de lançamento de versão** = (mais recente)<br/>**Nome da máquina VIRTUAL** = ContosoDC<br/>**Camada** = padrão<br/>**Tamanho** = A2 (2 núcleos)<br/>**Novo nome de utilizador** = AzureAdmin<br/>**Nova palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000|
|Configuração de máquina virtual|**Serviço em NUVEM** = cria um novo serviço em nuvem<br/>**Nome de DNS do serviço de NUVEM** = um nome de serviço de nuvem exclusivo<br/>**Nome de DNS** = um nome exclusivo (ex: ContosoDC123)<br/>**Região/grupo AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**VIRTUAL rede sub-redes** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = utilizar uma conta de armazenamento gerados automaticamente<br/>**Definir disponibilidade** = (nenhum)|
|Opções de máquina virtual|Utilizar as predefinições|

Depois de concluir a configuração da nova VM, aguarde que a VM para ser provsioned. Este processo demora algum tempo para concluir e, se clicar para o separador de **Máquina Virtual** no portal do Azure clássico, pode ver ContosoDC repetidos Estados-membros a partir do **início (aprovisionar)** **parado**, **inicial**, **a trabalhar rapidamente (aprovisionar)**e finalmente **começar a trabalhar**.

O servidor do Centro de dados agora está aprovisionado com êxito. Em seguida, irá configurar o domínio do Active Directory neste servidor do Centro de dados.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio

Os passos seguintes, irá configurar a máquina ContosoDC como controlador de domínio para corp.contoso.com.

1. No portal do, selecione a máquina **ContosoDC** . No separador **Dashboard** , clique em **Ligar** para abrir um ficheiro RDP para o acesso de ambiente de trabalho remoto.

    ![Ligar a Vritual máquina](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)

1. Iniciar sessão com o seu configurado conta de administrador (**\AzureAdmin**) e palavra-passe (**Contoso! 000**).

1. Por predefinição, o dashboard do **Gestor de servidor** deve ser apresentado.

1. Clique na ligação **Adicionar funções e funcionalidades** no dashboard.

    ![Servidor Explorer adicionar funções](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)

1. Selecione **seguinte** até chegar da secção de **Funções de servidor** .

1. Selecione os direitos do **Active Directory Domain Services** e **Servidor DNS** . Quando lhe for pedido, adicione quaisquer funcionalidades adicionais necessárias por estas funções.

    >[AZURE.NOTE] Irá obter uma validação aviso que não existe nenhuma endereço IP estático. Se estiver a testar a configuração, clique em continue. Para produção cenários [utilizar o PowerShell para definir o endereço IP estático do computador do controlador de domínio](./virtual-network/virtual-networks-reserved-private-ip.md).

    ![Funções caixa de diálogo Adicionar](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)

1. Clique em **seguinte** até chegar a secção de **confirmação** . Selecione a caixa de verificação **reiniciar o servidor de destino automaticamente se necessário** .

1. Clique em **instalar**.

1. Depois das funcionalidades de concluir a instalação, regresse ao dashboard do **Gestor de servidor** .

1. Selecione a opção de **AD DS** nova no painel do lado esquerdo.

1. Clique na ligação **mais** na barra de amarelo de aviso.

    ![Caixa de diálogo do AD DS sobre DNS Server VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)

1. Na coluna **ação** da caixa de diálogo de **Todos os detalhes de tarefa de servidor** , clique em **promover este servidor para um controlador de domínio**.

1. No **Assistente do configuração de serviços de domínio do Active Directory**, utilize os seguintes valores:

  	|Página|Definição|
|---|---|
|Configuração de implementação|**Adicionar uma nova floresta** = seleccionado<br/>**Nome de domínio de raiz** = corp.contoso.com|
|Opções do controlador de domínio|**Palavra-passe** = Contoso! 000<br/>**Confirmar palavra-passe** = Contoso! 000|

1. Clique em **seguinte** para percorrer as outras páginas no assistente. Na página **Verificar a pré-requisitos** , verifique se ver a seguinte mensagem: **todas as verificações pré-requisito passou com êxito**. Tenha em atenção que devem consultar quaisquer mensagens de aviso aplicável, mas é possível continuar com a instalação.

1. Clique em **instalar**. A máquina de virtual **ContosoDC** será reiniciado automaticamente.

## <a name="configure-domain-accounts"></a>Configurar contas de domínio

Próximos passos configure as contas do Active Directory (AD) para utilizar posteriormente.

1. Inicie sessão novamente no computador **ContosoDC** .

1. No **Gestor de servidor** selecione **Ferramentas** e, em seguida, clique em **Centro de administração do Active Directory**.

    ![Centro de administração do Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)

1. No **Centro de administração do Active Directory** selecione **corp (local)** a partir do painel esquerdo.

1. No painel de **tarefas** da direita, selecione **Novo** e, em seguida, clique em **utilizador**. Utilize as seguintes definições:

  	|Definição|Valor|
|---|---|
|**Nome próprio**|Instalar|
|**SamAccountName de utilizador**|Instalar|
|**Palavra-passe**|Contoso! 000|
|**Confirmar palavra-passe**|Contoso! 000|
|**Outras opções de palavra-passe**|Selecionada|
|**Palavra-passe nunca expirar**|Selecionada|

1. Clique em **OK** para criar o utilizador **instalar** . Esta conta será utilizada para configurar o cluster de activação pós-falha e o grupo de disponibilidade.

1. Crie dois utilizadores adicionais com os mesmos passos: **CORP\SQLSvc1** e **CORP\SQLSvc2**. Nestas contas serão utilizadas para instâncias do SQL Server. Em seguida, tem de conceder **CORP\Install** as permissões necessárias para configurar o Windows serviço activação pós-falha clusters (WSFC).

1. No **Centro de administração do Active Directory**, selecione **corp (local)** no painel esquerdo. No painel de **tarefas** direito, clique em **Propriedades**.

    ![Propriedades de utilizador de Emp.](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)

1. Selecione **extensões**e, em seguida, clique no botão **Avançadas** no separador **segurança** .

1. Na caixa de diálogo **Definições avançadas de segurança para corp** . Clique em **Adicionar**.

1. Clique em **selecionar um principal**. Em seguida, procure **CORP\Install**. Clique em **OK**.

1. Selecione as permissões de **Ler todas as propriedades** e **Criar computador objetos** .

    ![Permissões de utilizador de Emp.](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)

1. Clique em **OK**e, em seguida, clique novamente em **OK** . Feche a janela de propriedades corp.

Agora que tiver terminado a configuração do Active Directory e os objetos de utilizador, irá criar três SQL Server VMs e associá-los neste domínio.

## <a name="create-the-sql-server-vms"></a>Criar as VMs do servidor SQL

Em seguida, crie três VMs, incluindo um nó de cluster WSFC e duas SQL Server VMs. Para criar cada um das VMs, regresse ao portal do Azure clássico, clique em **Novo**, **Calcular**, **Máquina Virtual**e, em seguida, **A partir da Galeria**. Em seguida, utilize os modelos na seguinte tabela para ajudar a criar os VMs.

|Página|VM1|VM2|VM3|
|---|---|---|---|
|Selecione o sistema operativo de máquina virtual|**Centro de dados do Windows Server 2012 R2**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configuração de máquina virtual|**Data de lançamento de versão** = (mais recente)<br/>**Nome da máquina VIRTUAL** = ContosoWSFCNode<br/>**Camada** = padrão<br/>**Tamanho** = A2 (2 núcleos)<br/>**Novo nome de utilizador** = AzureAdmin<br/>**Nova palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000|**Data de lançamento de versão** = (mais recente)<br/>**Nome da máquina VIRTUAL** = ContosoSQL1<br/>**Camada** = padrão<br/>**Tamanho** = A3 (4 núcleos)<br/>**Novo nome de utilizador** = AzureAdmin<br/>**Nova palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000|**Data de lançamento de versão** = (mais recente)<br/>**Nome da máquina VIRTUAL** = ContosoSQL2<br/>**Camada** = padrão<br/>**Tamanho** = A3 (4 núcleos)<br/>**Novo nome de utilizador** = AzureAdmin<br/>**Nova palavra-passe** = Contoso! 000<br/>**Confirmar** = Contoso! 000|
|Configuração de máquina virtual|**Serviço em NUVEM** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**Região/grupo AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**VIRTUAL rede sub-redes** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = utilizar uma conta de armazenamento gerados automaticamente<br/>**Definir disponibilidade** = cria uma disponibilidade definir<br/>**Nome do conjunto de disponibilidade** = SQLHADR|**Serviço em NUVEM** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**Região/grupo AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**VIRTUAL rede sub-redes** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = utilizar uma conta de armazenamento gerados automaticamente<br/>**Definir disponibilidade** = SQLHADR (também pode configurar a disponibilidade definida após a criação de computador. Todos os três computadores devem ser atribuídos ao conjunto de disponibilidade SQLHADR.)|**Serviço em NUVEM** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**Região/grupo AFINIDADE/VIRTUAL de rede** = ContosoNET<br/>**VIRTUAL rede sub-redes** = Back(10.10.2.0/24)<br/>**Conta de armazenamento** = utilizar uma conta de armazenamento gerados automaticamente<br/>**Definir disponibilidade** = SQLHADR (também pode configurar a disponibilidade definida após a criação de computador. Todos os três computadores devem ser atribuídos ao conjunto de disponibilidade SQLHADR.)|
|Opções de máquina virtual|Utilizar as predefinições|Utilizar as predefinições|Utilizar as predefinições|

<br/>

>[AZURE.NOTE] A configuração anterior sugere máquinas virtuais de camada padrão, porque máquinas camada básicas não suportam balanceamento de carga os pontos finais necessários para criar mais tarde listeners um grupo de disponibilidade. Além disso, os tamanhos de máquina aqui sugeridos destinam-se para testar a disponibilidade de grupos no Azure VMs. Para um melhor desempenho das cargas de trabalho de produção, consulte as recomendações para a configuração no [práticas recomendadas do desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)e tamanhos de máquina do SQL Server.

Uma vez as três VMs totalmente são aprovisionados, tem de participá-los para o domínio **corp.contoso.com** e conceder CORP\Install direitos administrativos computadores. Para executar esta tarefa, utilize os seguintes passos para cada um das três VMs.

1. Em primeiro lugar, altere o endereço do servidor DNS preferido. Comece por transferi ficheiro de (RDP) de ambiente de trabalho remoto de cada VM para o seu diretório local ao selecionar a VM na lista e clicar no botão **Ligar** . Para selecionar uma VM, clique em qualquer parte mas a primeira célula na linha, conforme apresentado abaixo.

    ![Transferir o ficheiro RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)

1. Inicie o ficheiro RDP que transferiu e inicie sessão no VM com o seu configurado conta de administrador (**BUILTIN\AzureAdmin**) e palavra-passe (**Contoso! 000**).

1. Assim que iniciou sessão, deverá ver o dashboard do **Gestor de servidor** . Clique em **Local Server** no painel esquerdo.

1. Selecione a ligação **endereço IPv4 atribuído pelo DHCP, IPv6 ativado** .

1. Na janela de **Ligações de rede** , selecione o ícone de rede.

    ![Alterar o servidor DNS preferido VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)

1. Na barra de comandos, clique em **alterar as definições desta ligação** (dependendo do tamanho da janela do seu, poderá ter de clique na seta dupla direita para ver este comando).

1. Selecione **Internet Protocol versão 4 (TCP/IPv4)** e clique em propriedades.

1. Selecione utilizar o servidor DNS seguinte endereços e especificar **10.10.2.4** no **servidor de DNS preferido**.

1. O endereço **10.10.2.4** é o endereço atribuído uma VM na sub-rede 10.10.2.0/24 numa rede virtual Azure e essa VM é **ContosoDC**. Para verificar o endereço IP **ContosoDC**do, utilize o **nslookup contosodc** de comandos, conforme apresentado abaixo.

    ![Utilize o NSLOOKUP para localizar o endereço IP do Centro de dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)

1. Clique em the**K** e, em seguida, **Fechar** para consolidar as alterações. Está agora possível associar a VM ao **corp.contoso.com**.

1. Novamente na janela **Servidor Local** , clique na ligação do **grupo de trabalho** .

1. Na secção **Nome do computador** , clique em **Alterar**.

1. Selecione a caixa de verificação do **domínio** e escreva **corp.contoso.com** na caixa de texto. Clique em **OK**.

1. Na caixa de diálogo de pop-up de **Segurança do Windows** , especifique as credenciais para a conta de administrador de domínio predefinido (**CORP\AzureAdmin**) e a palavra-passe (**Contoso! 000**).

1. Quando vir a mensagem "Bem-vindo ao domínio corp.contoso.com", clique em **OK**.

1. Clique em **Fechar**e, em seguida, clique em **Reiniciar agora** na caixa de diálogo de pop-up.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Adicione o utilizador Corp\Install como administrador no cada VM:

1. Aguarde até que a VM for reiniciada, em seguida, iniciação RDP novamente o ficheiro para iniciar sessão para a VM através da conta **BUILTIN\AzureAdmin** .

1. No **Gestor de servidor** selecione **Ferramentas**e, em seguida, clique em **Gestão de computador**.

    ![Gestão de computador](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)

1. Na janela de **Gestão do computador** , expanda a **utilizadores e grupos locais**e, em seguida, selecione **grupos**.

1. Faça duplo clique sobre o grupo de **administradores** .

1. Na caixa de diálogo **Propriedades de administradores** , clique no botão **Adicionar** .

1. Introduza o utilizador **CORP\Install**e, em seguida, clique em **OK**. Quando lhe for pedido para introduzir as credenciais, utilize a conta de **AzureAdmin** com o **Contoso! 000** palavra-passe.

1. Clique em **OK** para fechar a caixa de diálogo **Propriedades de administrador** .

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Adicione a funcionalidade **Activação pós-falha clusters** para cada VM.

1. No dashboard do **Gestor de servidor** , clique em **Adicionar funções e funcionalidades**.

1. No **Assistente de funcionalidades e adicionar funções**, clique em **seguinte** até chegar à página de **funcionalidades** .

1. Selecione **clusters activação pós-falha**. Quando lhe for pedido, adicione quaisquer outras funcionalidades dependentes.

    ![Adicionar activação pós-falha clusters funcionalidade VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)

1. Clique em **seguinte**e, em seguida, clique em **instalar** na página de **confirmação** .

1. Quando a instalação da funcionalidade **Clusters de activação pós-falha** for concluída, clique em **Fechar**.

1. Termine a VM sessão.

1. Repita os passos nesta secção para todos os servidores de três – **ContosoWSFCNode**, **ContosoSQL1**e **ContosoSQL2**.

Agora configurações para o VMs de servidor SQL e em execução, mas são instalados com o SQL Server com as opções predefinidas.

## <a name="create-the-wsfc-cluster"></a>Criar o Cluster WSFC

Nesta secção, crie o cluster WSFC que será o anfitrião o grupo de disponibilidade que irá criar mais tarde. Por agora, deve ter feito o seguinte procedimento para cada um das três VMs que irá utilizar no WSFC cluster:

- Totalmente aprovisionado no Azure

- VM associado ao domínio

- Adicionado **CORP\Install** ao grupo de administradores local

- Adicionado a funcionalidade activação pós-falha clusters

All pré-requisitos no cada VM antes de poder aderi-lo ao WSFC cluster.

Além disso, tenha em atenção que a rede virtual Azure não comportam-se da mesma forma como uma rede no local. Precisa de criar o cluster pela seguinte ordem:

1. Crie um cluster de nó único dos nós (**ContosoSQL1**).

1. Modificar o endereço IP cluster para um endereço IP não utilizado (**10.10.2.101**).

1. Coloque o nome do cluster online.

1. Adicione os nós (**ContosoSQL2** e **ContosoWSFCNode**).

Siga os passos abaixo para efetuar estas tarefas que configura totalmente cluster.

1. Inicie o ficheiro RDP para **ContosoSQL1** e inicie sessão com a conta do domínio **CORP\Install**.

1. No dashboard do **Gestor de servidor** , selecione **Ferramentas**e, em seguida, clique em **Gestor de Cluster de activação pós-falha**.

1. No painel esquerdo, **Gestor de clusters de activação pós-falha**com o botão direito e, em seguida, clique em **criar um Cluster**, conforme apresentado abaixo.

    ![Criar Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)

1. No Assistente de Cluster de criar, crie um cluster de um nó ao percorrendo as páginas com as definições de abaixo:

  	|Página|Definições|
|---|---|
|Antes de começar|Utilizar as predefinições|
|Selecione Servers|Escreva **ContosoSQL1** em **nome do servidor Enter** e clique em **Adicionar**|
|Aviso de validação|Selecione **n. º não requer o suporte da Microsoft para este cluster e, consequentemente, não pretende executar os testes de validação. Ao clicar em seguinte, continuar a criar o cluster**.|
|Ponto de acesso para administrar o Cluster|Tipo **Cluster1** no **nome do Cluster**|
|Confirmação|Utilize as predefinições, a menos que estiver a utilizar o armazenamento espaços. Consulte a Nota Esta tabela seguinte.|

    >[AZURE.WARNING] Se estiver a utilizar [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739), que agrupa vários discos agrupamentos de armazenamento, tem de desmarcar a caixa de verificação **Adicionar todos os armazenamento elegível para o cluster** na página de **confirmação** . Se não desmarcar esta opção, os discos virtuais irão ser desligados durante o processo de clusters. Como resultado, também não será apresentado no Gestor de discos ou Explorer até os espaços de armazenamento são removidos de cluster e verificaçoes através do PowerShell.

1. No painel da esquerda, expanda **Activação pós-falha Cluster Manager**e, em seguida, clique em **Cluster1.corp.contoso.com**.

1. No painel central, desloque-se para baixo para **Recursos de Core Cluster de** secção e expandir o **nome: Clutser1** detalhes. Deverá ver o **nome** e **Endereço IP** recursos presentes no estado de **Falha** . O recurso endereço IP não pode ficar online porque o cluster está atribuído o mesmo endereço IP como que o computador propriamente dito, que é um endereço duplicado.

1. O recurso falhado do **Endereço IP** com o botão direito e, em seguida, clique em **Propriedades**.

    ![Propriedades do cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)

1. Selecione o **Endereço IP estático** e especificar **10.10.2.101** na caixa de texto endereço. Em seguida, clique em **OK**.

1. Na secção **Cluster Core recursos** , botão direito do rato **nome: Cluster1** e clique em **Colocar Online**. Em seguida, aguarde até que ambos os recursos estão online. Quando o nome do recurso cluster fica online, atualiza o servidor do Centro de dados com uma nova conta de computador do AD. Esta conta AD será utilizada para executar o serviço de grupo agrupado de disponibilidade mais tarde.

1. Por fim, adicionar os restantes nós ao cluster. Na árvore do browser, com o botão direito **Cluster1.corp.contoso.com** e clique em **Adicionar nó**, conforme apresentado abaixo.

    ![Adicionar nó ao Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)

1. No **Assistente para nó adicionar**, clique em **seguinte**. Na página **Selecionar servidores** , adicione **ContosoSQL2** e **ContosoWSFCNode** à lista de escrever o nome de servidor em **nome do servidor Enter** e, em seguida, clicando em **Adicionar**. Quando tiver terminado, clique em **seguinte**.

1. Na página de **Aviso de validação** , clique em **não** (num cenário de produção deve executar os testes de validação). Em seguida, clique em **seguinte**.

1. Na página de **confirmação** , clique em **seguinte** para adicionar os nós.

    >[AZURE.WARNING] Se estiver a utilizar [Espaços de armazenamento](https://technet.microsoft.com/library/hh831739), que agrupa vários discos agrupamentos de armazenamento, tem de desmarcar a caixa de verificação **Adicionar todos os armazenamento elegível para o cluster** . Se não desmarcar esta opção, os discos virtuais irão ser desligados durante o processo de clusters. Como resultado, também não será apresentado no Gestor de discos ou Explorer até os espaços de armazenamento são removidos de cluster e verificaçoes através do PowerShell.

1. Assim que os nós são adicionados ao cluster, clique em **Concluir**. Gestor de clusters de activação pós-falha agora deve mostrar que o seu cluster tem três nós e listá-los no contentor de **nós** .

1. Termine a sessão de ambiente de trabalho remota sessão.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Preparar as instâncias do SQL Server para o grupo de disponibilidade

Nesta secção, que irá fazer o seguinte no **ContosoSQL1** e **contosoSQL2**:

- Adicionar um início de sessão para **NT\Sistema** com um conjunto para a instância do SQL Server predefinida de permissões necessárias

- Adicionar **CORP\Install** como uma função sysadmin a instância do SQL Server predefinido

- Abra a firewall para acesso remoto do SQL Server

- Ativar a funcionalidade de sempre no grupos de disponibilidade

- Alterar a conta de serviço do SQL Server para **CORP\SQLSvc1** e **CORP\SQLSvc2**, respetivamente

Estas ações podem ser executadas numa ordem. No entanto, os passos abaixo irão guiá-lo através dos mesmos na ordem. Siga os passos para **ContosoSQL1** e **ContosoSQL2**:

1. Se não tiver iniciado terminar a sessão remota do ambiente de trabalho para a VM, fazê-lo agora.

1. Inicie os ficheiros RDP para **ContosoSQL1** e **ContosoSQL2** e inicie sessão como **BUILTIN\AzureAdmin**.

1. Em primeiro lugar, adicione **NT\Sistema** para os inícios de sessão do SQL Server e com permissões necessárias. Inicie o **SQL Server Management Studio**.

1. Clique em **Ligar** para ligar a instância do SQL Server predefinido.

1. No **Explorador de objectos**, expanda a **segurança**e, em seguida, expanda **os inícios de sessão**.

1. Botão direito do rato login **NT\Sistema** e clique em **Propriedades**.

1. Na página de **Securables** , para o servidor local, selecione **conceder** para as seguintes permissões e clique em **OK**.

    - Alterar qualquer grupo de disponibilidade

    - Ligar SQL

    - Ver o estado do servidor

1. Em seguida, adicione **CORP\Install** como uma função **sysadmin** a instância do SQL Server predefinido. No **Explorador de objectos**, com o botão direito **inícios de sessão** e clique em **Novo início de sessão**.

1. Escreva **CORP\Install** no **nome de início de sessão**.

1. Na página de **Funções de servidor** , selecione **sysadmin**. Em seguida, clique em **OK**. Quando estiver criado o início de sessão, pode vê-la expandindo **inícios de sessão** no **Explorador do objeto**.

1. Em seguida, criar uma regra de firewall para o SQL Server. No ecrã **Iniciar** , inicie **A Firewall do Windows com segurança avançada**.

1. No painel esquerdo, selecione **Regras de entrada**. No painel direito, clique em **Nova regra**.

1. Na página **Tipo de regra** , selecione o **programa**, em seguida, clique em **seguinte**.

1. Na página de **programa** , selecione **este caminho de programa** e escreva %ProgramFiles%\Microsoft **Server\MSSQL12 de SQL. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** na caixa de texto (se estiver a seguir estas indicações mas utilizar o SQL Server 2012, diretório do SQL Server é **MSSQL11. MSSQLSERVER**). Em seguida, clique em **seguinte**.

1. Na página de **ação** , manter a **Permitir a ligação** selecionada e clique em **seguinte**.

1. Na página de **perfil** , aceite as predefinições e clique em **seguinte**.

1. Na página de **nome** , especifique um nome da regra, como **SQL Server (regra programa)** na caixa de texto **nome** , em seguida, clique em **Concluir**.

1. Em seguida, ativar a funcionalidade de **Sempre no grupos de disponibilidade** . No ecrã **Iniciar** , inicie o **Gestor de configuração do SQL Server**.

1. Na árvore do browser, clique em **SQL Server Services**, em seguida, o serviço do **SQL Server (MSSQLSERVER)** com o botão direito e clique em **Propriedades**.

1. Clique no separador **Sempre no disponibilidade elevada** , em seguida, selecione **Ativar sempre no grupos de disponibilidade**, conforme apresentado abaixo e, em seguida, clique em **Aplicar**. Clique em **OK** na caixa de diálogo pop-up e não fechar a janela de propriedades ainda. Reinicie o serviço do SQL Server depois de alterar a conta de serviço.

    ![Ativar sempre o grupos de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)

1. Em seguida, alterar a conta de serviço do SQL Server. Clique em **Iniciar sessão no** separador, em seguida, escreva **CORP\SQLSvc1** (para **ContosoSQL1**) ou **CORP\SQLSvc2** (para **ContosoSQL2**) no **Nome da conta**, em seguida, preencha e confirme a palavra-passe e, em seguida, clique em **OK**.

1. Na janela de pop-up, clique em **Sim** para reiniciar o serviço do SQL Server. Depois de é reiniciado o serviço do SQL Server, as alterações que efetuou na janela Propriedades são eficazes.

1. Termine as VMs sessão.

## <a name="create-the-availability-group"></a>Criar um grupo de disponibilidade

Agora está pronto para configurar um grupo de disponibilidade. Abaixo encontra-se um destaque de o que vai fazer:

- Criar uma nova base de dados (**MyDB1**) no **ContosoSQL1**

- Levar uma cópia de segurança completa e uma cópia de segurança do registo da transação da base de dados

- Restaurar completo e inicie sessão cópias de segurança **ContosoSQL2** com a opção de **NORECOVERY**

- Criar um grupo de disponibilidade (**AG1**) com síncrono consolidar, activação automática e legíveis réplicas secundárias

### <a name="create-the-mydb1-database-on-contososql1"></a>Crie a base de dados MyDB1 ContosoSQL1:

1. Se não já iniciou sessão terminar as sessões de ambiente de trabalho remotas para **ContosoSQL1** e **ContosoSQL2**, fazê-lo agora.

1. Inicie o ficheiro RDP para **ContosoSQL1** e inicie sessão como **CORP\Install**.

1. No **Explorador de ficheiros**, em * *c:\**, crie um directório denominado * *cópia de segurança**. Irá utilizar esta utilização de diretórios para criar cópias de segurança e restaurar a base de dados.

1. Com o botão direito do diretório do novo, aponte para **partilhar com**e, em seguida, clique em **pessoas específicas**, conforme apresentado abaixo.

    ![Criar uma pasta de cópia de segurança](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)

1. Adicionar **CORP\SQLSvc1** e dar-lhe a permissão de **Leitura/escrita** , em seguida, adicionar **CORP\SQLSvc2** e dar-lhe permissão de **leitura** , conforme apresentado abaixo e, em seguida, clique em **partilhar**. Assim que o processo de partilha de ficheiros existam concluído, clique em **concluído**.

    ![Conceder permissões de pasta de cópia de segurança](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)

1. Em seguida, criar a base de dados. No menu **Iniciar** , iniciação **SQL Server Management Studio**, em seguida, clique em **Ligar** a ligar a instância do SQL Server predefinido.

1. No **Explorador de objectos**, **bases de dados** com o botão direito e clique em **Nova base de dados**.

1. Em **nome da base de dados**, escreva **MyDB1**, em seguida, clique em **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Ter um total cópia de segurança de MyDB1 e restaurá-la num ContosoSQL2:

1. Em seguida, pode demorar um inteiro cópia de segurança da base de dados. No **Explorador de objectos**, expanda **bases de dados**, em seguida, **MyDB1**, com o botão direito, em seguida, aponte para **tarefas**e, em seguida, clique em criar **Cópia**.

1. Na secção de **origem** , mantenha o **tipo de cópia de segurança** definidos para **completa**. Na secção de **destino** , clique em **Remover** para remover o caminho de ficheiro predefinido para o ficheiro de cópia de segurança.

1. Na secção de **destino** , clique em **Adicionar**.

1. Na caixa de texto **nome de ficheiro** , escreva ** \\ContosoSQL1\backup\MyDB1.bak**. Em seguida, clique em **OK**e, em seguida, clique em **OK** novamente para a base de dados de cópia de segurança. Quando concluir a operação de cópia de segurança, clique em **OK** novamente para fechar a caixa de diálogo.

1. Em seguida, pode demorar um registo de transações cópia de segurança da base de dados. No **Explorador de objectos**, expanda **bases de dados**, em seguida, **MyDB1**, com o botão direito, em seguida, aponte para **tarefas**e, em seguida, clique em criar **Cópia**.

1. Em tipo de **cópia de segurança** , selecione o **Registo de transações**. Manter o caminho do ficheiro de **destino** definido para aquele especificado anteriormente e clique em **OK**. Uma vez concluída a operação de cópia de segurança, clique novamente em **OK** .

1. Em seguida, pode restaura as total da transação registo cópias de segurança e no **ContosoSQL2**. Inicie o ficheiro RDP para **ContosoSQL2** e inicie sessão como **CORP\Install**. Deixe a sessão de ambiente de trabalho remota para **ContosoSQL1** aberto.

1. No menu **Iniciar** , iniciação **SQL Server Management Studio**, em seguida, clique em **Ligar** a ligar a instância do SQL Server predefinido.

1. No **Explorador de objectos**, **bases de dados** com o botão direito e clique em **Restaurar a base de dados**.

1. Na secção de **origem** , selecione o **dispositivo**e, clique em … **** botão.

1. Em **Selecione dispositivos de cópia de segurança**, clique em **Adicionar**.

1. Na localização do ficheiro de cópia de segurança, escreva \\ContosoSQL1\backup, em seguida, clique em Atualizar, em seguida, selecione MyDB1.bak, em seguida, clique em OK e, em seguida, clique novamente em OK. Deverá agora visualizar a cópia de segurança completa e define a cópia de segurança do registo da cópia de segurança para restaurar o painel.

1. Ir para a página de opções, em seguida, selecione Restaurar com NORECOVERY no estado de recuperação e, em seguida, clique em OK para restaurar a base de dados. Uma vez concluída a operação de restaurar, clique em OK.

### <a name="create-the-availability-group"></a>Crie um grupo de disponibilidade:

1. Regresse a sessão de ambiente de trabalho remota para **ContosoSQL1**. No **Explorador de objectos** no SSMS, **Sempre na disponibilidade elevada** com o botão direito e clique em **Assistente de novo grupo de disponibilidade**, conforme apresentado abaixo.

    ![Inicie o Assistente de novo grupo de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)

1. Na página de **Introdução** , clique em **seguinte**. Na página **Especificar nome do grupo de disponibilidade** , escreva **AG1** em **nome do grupo de disponibilidade**, em seguida, clique em **seguinte** novamente.

    ![Assistente de AG novo, especifique o nome de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)

1. Na página **Selecionar bases de dados** , selecione **MyDB1** e clique em **seguinte**. A base de dados cumpre os pré-requisitos para um grupo de disponibilidade uma vez que reencaminhado pelo menos uma cópia de segurança completa na réplica principal pretendida.

    ![Assistente de AG novo, selecione as bases de dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)

1. Na página **Especificar réplicas** , clique em **Adicionar réplica**.

    ![Assistente de AG novo, especificar réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)

1. Caixa de diálogo **ligar ao servidor** é apresentado. Escreva **ContosoSQL2** em **nome do servidor**e, em seguida, clique em **Ligar**.

    ![Assistente de AG novo, ligar ao servidor](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)

1. Novamente na página **Especificar réplicas** , deverá ver agora **ContosoSQL2** listado em **Réplicas disponíveis**. Configure as réplicas conforme apresentado abaixo. Quando tiver terminado, clique em **seguinte**.

    ![Assistente de AG novo, especificar réplicas (concluído)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)

1. Na página **Selecionar sincronização de dados inicial** , selecione **apenas aderir** e clique em **seguinte**. Já tiver executado a sincronização de dados manualmente quando que tirou com as cópias de segurança completa e da transação **ContosoSQL1** e restaurada-los no **ContosoSQL2**. Em vez disso, pode escolher não executar a cópia de segurança e restaurar operações na sua base de dados e selecione **completo** para permitir que o Assistente de novo disponibilidade grupo efetuar a sincronização de dados por si. No entanto, esta não é recomendada para muito grandes bases de dados que sejam encontram em algumas empresas.

    ![Assistente de AG novo, selecione a sincronização inicial dos dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)

1. Na página de **validação** , clique em **seguinte**. Esta página deverá ter um aspeto semelhante ao abaixo. Existe um aviso para a configuração de escuta porque não tiver configurado uma escuta de grupo de disponibilidade. Pode ignorar este aviso, uma vez que este tutorial não configura uma escuta. Para configurar o serviço de escuta depois de concluir este tutorial, consulte o artigo [Configurar uma escuta ILB para sempre no grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

    ![Assistente de AG novo, validação](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)

1. Na página de **Resumo** , clique em **Concluir**, em seguida, aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página de **progresso** , pode clicar em **mais detalhes** para ver o progresso detalhado. Assim que o assistente estiver concluído, verificar se a página de **resultados** para confirmar que é criado com êxito o grupo de disponibilidade, conforme apresentado abaixo, em seguida, clique em **Fechar** para sair do assistente.

    ![Assistente de AG novo, de resultados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)

1. No **Explorador de objectos**, expanda **Sempre no disponibilidade elevada**, em seguida, expanda **Os grupos de disponibilidade**. Agora deverá ver o novo grupo de disponibilidade neste contentor. Botão direito do rato **AG1 (primária)** e clique em **Mostrar Dashboard**.

    ![Mostrar Dashboard de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)

1. O **Sempre Dashboard** deverá ter um aspeto semelhante ao apresentado abaixo. Pode ver as réplicas, o modo de activação pós-falha de cada réplica e o estado de sincronização.

    ![Dashboard de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)

1. Regressar ao **Gestor de servidor**, selecione **Ferramentas**e, em seguida, iniciar **Gestor de clusters de activação pós-falha**.

1. Expanda **Cluster1.corp.contoso.com**e, em seguida, expanda **Serviços e aplicações**. Selecione **funções** e tenha em atenção que a função de grupo de disponibilidade de **AG1** foi criada. Tenha em atenção que AG1 não tem qualquer endereço IP pela base de dados os clientes podem ligar para o grupo de disponibilidade, porque não tiver configurado uma escuta. Pode ligar diretamente para o nó primário para operações de leitura / escrita e o nó secundário para consultas só de leitura.

    ![AG no Gestor de clusters de activação pós-falha](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

>[AZURE.WARNING] Não tente falhar ao longo do grupo a partir do Gestor de Cluster activação pós-falha de disponibilidade. Todas as operações de activação pós-falha devem ser realizadas a partir de dentro **Sempre Dashboard** no SSMS. Para mais informações, consulte o artigo [restrições sobre utilizando o WSFC activação pós-falha Cluster Gestor com grupos de disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).

## <a name="next-steps"></a>Próximos passos
Tiver agora com êxito implementado SQL Server sempre no através da criação de um grupo de disponibilidade no Azure. Para configurar uma escuta para este grupo de disponibilidade, consulte o artigo [Configurar uma escuta ILB para sempre no grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Para obter outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
