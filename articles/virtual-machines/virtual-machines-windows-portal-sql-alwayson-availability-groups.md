<properties
    pageTitle="Configurar sempre no grupo de disponibilidade no Azure VM automaticamente - Gestor de recursos"
    description="Crie um grupo de disponibilidade sempre com Azure máquinas virtuais no modo de Gestor de recursos do Azure. Neste tutorial principalmente utiliza a interface de utilizador para criar automaticamente a solução inteira."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurar sempre no grupo de disponibilidade no Azure VM automaticamente - Gestor de recursos

> [AZURE.SELECTOR]
- [Gestor de recursos: modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Gestor de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clássico: IU](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Neste tutorial de ponto a ponto mostra-lhe como criar um grupo de disponibilidade do SQL Server com máquinas virtuais de Gestor de recursos do Azure. O tutorial utiliza pás Azure para configurar um modelo. Irá rever as predefinições, escreva definições necessárias e atualize pás no portal do conforme ajudasse este tutorial.

No fim do tutorial, irá consistir a solução do grupo de disponibilidade do SQL Server no Azure os seguintes elementos:

- Uma rede virtual que contém vários sub-redes, incluindo um front-end e uma sub-rede back-end

- Controlador de domínio duas com um domínio do Active Directory (AD)

- Duas SQL Server VMs implementado à sub-rede back-end e aderido ao domínio de AD

- Um cluster WSFC 3 nó com o modelo de quórum maioria de nós

- Um grupo de disponibilidade com duas réplicas síncrono consolidação de uma base de dados de disponibilidade

A figura seguinte é uma representação gráfica da solução.

![Testar arquitetura de laboratório para AG no Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos nesta solução pertencem a um grupo de recursos único.

Neste tutorial assume o seguinte procedimento:

- Já tem uma conta Azure. Se não tiver uma, [Inscrever-se para uma conta de avaliação](http://azure.microsoft.com/pricing/free-trial/).

- Já sabe como pode aprovisionar um VM de servidor SQL a partir da Galeria de máquina virtual utilizando a interface gráfica. Para obter mais informações, consulte o artigo [aprovisionamento uma máquina de virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Já tem uma cor sólida compreensão dos grupos de disponibilidade. Para mais informações, consulte o artigo [sempre no grupos de disponibilidade (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se estiver interessado em utilizar grupos de disponibilidade com o SharePoint, consulte o artigo também [configurar o SQL Server 2012 sempre no grupos de disponibilidade para o SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

Neste tutorial, que irá utilizar o portal do Azure para:

- Selecione o modelo sempre na partir do portal

- Reveja as definições de modelo e actualizar algumas definições de configuração para o seu ambiente

- Monitorizar Azure à medida que cria todo o ambiente

- Ligar para um dos controladores de domínio e, em seguida, para um dos servidores de SQL

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Aprovisionar o cluster a partir da Galeria

Azure fornece uma imagem da Galeria para a solução inteira. Para localizar o modelo:

1.  Inicie sessão no portal do Azure utilizando a sua conta.
1.  No portal clique Azure **+ novo.** O portal irá abrir o novo pá.
1.  No novo pá procurar **AlwaysOn**.
![Encontrar o modelo de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  Nos resultados da pesquisa localize **Cluster de AlwaysOn do SQL Server**.
![Modelo de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  Em **Selecione um modelo de implementação** escolha **Gestor de recursos**.

### <a name="basics"></a>Noções básicas

Clique em **Noções básicas** e configure o seguinte:

- **Nome de utilizador do administrador** é uma conta de utilizador com permissões de administrador do domínio e um membro do SQL Server fixo sysadmin ambas as instâncias do SQL Server. Para este tutorial utilize **administrador de domínio**.

- **Palavra-passe** é a palavra-passe para a conta de administrador do domínio. Utilize uma palavra-passe complexa. Confirme a palavra-passe.

- **Subscrição** é a subscrição da qual irá cobrar Azure para executar todos os recursos implementados para o grupo de disponibilidade. Pode especificar uma subscrição diferente se a sua conta tem múltiplas subscrições.

- **Grupo de recursos** é o nome para o grupo que todos os recursos Azure criado por este tutorial irão pertencer a. Para este tutorial utilize **SQL-HA-RG**. Para obter mais informações, consulte o artigo (descrição geral do Gestor de recursos do Azure) [recurso-grupo-overview.md / #-grupos de recursos].

- **Localização** é o Azure região onde os recursos para este tutorial serão criados. Selecione uma região para alojar infraestrutura do Azure.

Abaixo encontra-se o aspeto pá as **Noções básicas** :

![Noções básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Clique em **OK**.

### <a name="domain-and-network-settings"></a>Definições de domínio e de rede

Este modelo da Galeria Azure cria um novo domínio com novos controladores de domínio. Também cria uma nova rede e duas sub-redes. O modelo não permitem a criar os servidores de no domínio existente ou de rede virtual. O próximo passo é configurar as definições de domínio e de rede.

Nas **definições de domínio e de rede** pá reveja os valores predefinidos para que as definições de domínio e de rede:

- **Nome de domínio de raiz floresta** é o nome de domínio que será utilizado para o domínio de AD que será o anfitrião o cluster. Utilize o tutorial **contoso.com**.

- **Nome de rede virtual** é o nome de rede para a rede virtual Azure. Para este tutorial utilize **autohaVNET**.

- **Nome do domínio controlador de sub-rede** é o nome de uma parte da rede virtual que aloja o controlador de domínio. Para este tutorial utilize **sub-rede 1**. Esta sub-rede irá utilizar endereço prefixo **10.0.0.0/24**.

- **Nome do SQL Server sub-rede** é o nome de uma parte da rede virtual que aloja os servidores de SQL e o ficheiro partilham fé. Para este tutorial utilize **sub-rede-2**. Esta sub-rede irá utilizar endereço prefixo **10.0.1.0/26**.

Para saber mais sobre as redes virtuais no [Azure consulte o artigo Descrição geral da rede Virtual](../virtual-network/virtual-networks-overview.md).  

As **definições de domínio e de rede** deve ter este aspeto:

![Definições de domínio e de rede](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se for necessário, pode alterar estes valores. Para este tutorial utilizamos os valores predefinidos.

- Reveja as definições e clique em **OK**.

###<a name="availability-group-settings"></a>definições de grupo de disponibilidade

Nas **definições do grupo de disponibilidade** reveja os valores predefinidos para o grupo de disponibilidade e a escuta.

- **Nome do grupo availablity** é o nome do recurso agrupadas para o grupo de disponibilidade. Para este tutorial utilize **Contoso ag**.

- **nome do grupo de disponibilidade escuta** é utilizado por cluster e Balanceador de carga interno. Clientes com uma ligação para o SQL Server podem utilizar este nome para ligar à réplica adequada da base de dados. Para este tutorial utilize **escuta Contoso**.

-  **porta de escuta do grupo de disponibilidade** Especifica que irá utilizar a porta TCP a escuta do SQL Server. Para este tutorial utilize a porta predefinida, **1433**.

Se for necessário, pode alterar estes valores. Para este tutorial utilize os valores predefinidos.  

![definições de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Clique em **OK**.

###<a name="vm-size-storage-settings"></a>Tamanho da memória virtual, definições de armazenamento

Em **tamanho da memória virtual, definições do armazenamento** escolher um tamanho de máquina virtual do SQL Server e reveja todas as outras definições.

- **SQL Server máquina virtual** é o Azure máquina virtual tamanho para ambos os servidores de SQL. Escolha um tamanho de máquina virtual adequado para a sua carga de trabalho. Se estiver a criar este ambiente para o tutorial utilize **DS2**. Para produção das cargas de trabalho escolha um tamanho de máquina virtual que pode suportar a carga de trabalho. Muitos produção das cargas de trabalho irão necessitar **DS4** polegadas ou superior. O modelo irá criar duas máquinas virtuais deste tamanho e instalar o SQL Server em cada um deles. Para mais informações, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure instalará Enterprise Edition do SQL Server. O custo depende da edição e o tamanho de máquina virtual. Para obter informações detalhadas sobre os custos de atuais, consulte o artigo [máquinas virtuais preços](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Tamanho de máquina virtual do controlador de domínio** é o tamanho de máquina virtual para os controladores de domínio. Para este tutorial utilize **D2**.

- **Tamanho de máquina virtual fé para partilhar o ficheiro** é o tamanho de máquina virtual para o fé para partilhar o ficheiro. Para este tutorial utilize **A1**.

- **Conta de armazenamento de SQL** é o nome da conta de armazenamento para armazenar a dados do SQL Server e o sistema operativo discos. Para este tutorial utilize **alwaysonsql01**.

- **Conta de armazenamento do Centro de dados** é o nome da conta de armazenamento para os controladores de domínio. Para este tutorial utilize **alwaysondc01**.

- O **tamanho do disco de dados do SQL Server** no TB é o tamanho do disco de dados do SQL Server na TB. Especificar um número entre 1 a 4. Este é o tamanho do disco dados que será anexado a cada do SQL Server. Para este tutorial utilize **1**.

- **Otimização de armazenamento** define as definições de configuração específicas para armazenamento para as máquinas virtuais do SQL Server com base no tipo de carga de trabalho. Todos os servidores de SQL neste cenário utilize o armazenamento de premium com a cache de anfitrião de disco Azure definida como só de leitura. Além disso, pode otimizar definições do SQL Server para a carga de trabalho ao selecionar uma destas três definições:

    - Conjuntos de **carga de trabalho geral** sem definições de configuração específicas

    - **Processamento de transaccionais** define o sinalizador de rastreio 1117 e 1118

    - **Armazenamento de dados** define o sinalizador de rastreio 1117 e 610

Para este tutorial utilize **Geral carga de trabalho**.

![Definições do armazenamento de tamanho VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Reveja as definições e clique em **OK**.

####<a name="a-note-about-storage"></a>Uma nota sobre o armazenamento

Otimizações adicionais variam consoante o tamanho dos discos de dados do SQL Server. Para cada terabyte do disco de dados, o Azure adiciona um armazenamento incorporado premium 1 TB (SSD) adicionais. Quando um servidor requer 2 TB ou mais, o modelo cria um agrupamento de armazenamento no cada SQL Server. Um agrupamento de armazenamento é um formulário do virtualization armazenamento onde vários discos estiverem configurados para fornecer superior capacidade, RDP e o desempenho.  O modelo, em seguida, cria um espaço de armazenamento no agrupamento de armazenamento e apresenta este como um única dados para o sistema operativo. O modelo designa este disco como o disco de dados para o SQL Server. O modelo sintoniza o agrupamento de armazenamento para SQL Server com as seguintes definições:

- Tamanho da faixa é a definição de intercalação para o disco virtual. Para transaccionais das cargas de trabalho está definido para 64 KB. Armazenamento das cargas de trabalho de dados a definição é 256 KB.

- RDP é um processo simple (sem RDP).

>[AZURE.NOTE] Armazenamento de Azure premium é localmente redundante e mantém três cópias dos dados numa única região, para que não é necessária RDP adicional ao agrupamento de armazenamento.

- Número de colunas é igual ao número de discos no conjunto de armazenamento.

Para obter informações adicionais sobre o armazenamento agrupamentos de espaço e o armazenamento consulte:

- [Descrição geral de espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx).

- [Cópia de segurança do Windows Server e agrupamentos de armazenamento](http://technet.microsoft.com/library/dn390929.aspx)

Para mais informações sobre práticas recomendadas de configuração do SQL Server, consulte o artigo [melhores práticas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>Definições do SQL Server

Em **definições do SQL Server** reveja e modifique o prefixo do nome VM do SQL Server, versão do SQL Server, conta de serviço do SQL Server e palavra-passe e automaticamente SQL corrigir agenda de manutenção.

- **Nome de servidor do SQL prefixo** é utilizada para criar um nome para cada SQL Server. Para este tutorial utilize **Contoso ag**. Os nomes de SQL Server serão *Contoso-ag-0* e *Contoso-ag-1*.

- **Versão do SQL Server** é a versão do SQL Server. Para este tutorial utilize **2014 do SQL Server**. Também pode escolher o **SQL Server 2012** ou **SQL Server 2016**.

- **Nome de utilizador de conta de serviço do SQL Server** é o nome de conta de domínio para o serviço do SQL Server. Para este tutorial utilize **sqlservice**.

- **Palavra-passe** é a palavra-passe da conta de serviço do SQL Server.  Utilize uma palavra-passe complexa. Confirme a palavra-passe.

- **Agenda de manutenção de correcção automática de SQL** identifica no dia da semana que Azure serão automaticamente correcção os servidores de SQL. Para este tutorial escreva **Domingo**.

- **Correcção automática de SQL Hora de início de manutenção** é a hora do dia para a região Azure quando correcção automática será iniciado.

>[AZURE.NOTE]A janela de aplicação de patch de cada VM é escalonada por uma hora. Apenas uma máquina virtual é corrigida uma vez para impedir que o interrupção dos serviços.

![Definições do SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Reveja as definições e clique em **OK**.

###<a name="summary"></a>Resumo

Na página de resumo Azure valida as definições. Também pode transferir o modelo. Reveja o resumo. Clique em **OK**.

###<a name="buy"></a>Comprar

Este pá final contém **termos de utilização**e a **política de privacidade**. Reveja esta informação. Quando estiver pronto para Azure começar a criar as máquinas virtuais e todos os outros recursos necessários para o grupo de disponibilidade, clique em **Criar**.

Portal do Azure irá criar o grupo de recursos e todos os recursos.

##<a name="monitor-deployment"></a>Implementação do monitor

Monitorize o progresso de implementação do Azure portal. Um ícone que representa a implementação automaticamente é afixado ao dashboard portal Azure.

![Dashboard do Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Ligar ao SQL Server

Estão a executar as novas instâncias do SQL Server em máquinas virtuais que não tem ligações à internet. No entanto, os controladores de domínio tem uma ligação de opostas da internet. Para poder ligar aos servidores SQL com um ambiente de trabalho remoto, primeiro RDP para um dos controladores de domínio. Abra o controlador de domínio a uma segunda RDP para o SQL Server.

Para RDP para o controlador de domínio principal, siga estes passos:

1.  A partir do Azure portal dashboard muito que a implementação foi bem sucedido.

1.  Clique em **recursos**.

1.  Na pá **recursos** , clique em **CD de ad principal** que é o nome do computador da máquina virtual para o controlador de domínio principal.

1.  No pá para **CD de ad principal** , clique em **Ligar**. O browser será perguntar se pretende abrir ou guardar o objeto de ligação remoto. Clique em **Abrir**.
![Ligar ao centro de dados](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Ligação ao ambiente de trabalho remoto** poderá avisá-lo de que não pode ser identificado o publicador desta ligação remota. Clique em **Ligar**.

1.  Segurança do Windows pede-lhe para introduzir as suas credenciais para ligar para o endereço IP do controlador de domínio principal. Clique em **utilizar outra conta**. Nome de **utilizador** , escreva **contoso\DomainAdmin**. Esta é a conta que escolher para o nome de utilizador do administrador. Utilize a palavra-passe complexa que escolheu quando configurou o modelo.

1.  **Ambiente de trabalho remoto** poderá avisá-lo de que o computador remoto não foi possível autenticar relacionado com problemas com o seu certificado de segurança. -Lo irá mostrar-lhe o nome do certificado de segurança. Se seguiu o tutorial o nome será **ad-principal-dc.contoso.com**. Clique em **Sim**.

Agora está ligado ao controlador de domínio principal. Para RDP para o SQL Server, siga estes passos:

1.  No controlador de domínio, abra a **Ligação de ambiente de trabalho remoto**.

1.  **Computador**, escreva o nome de um dos servidores de SQL. Neste tutorial, escreva **sqlserver 0**.

1.  Utilize a mesma conta de utilizador e palavra-passe que utilizou para RDP ao controlador de domínio.

Agora está ligado com RDP para o SQL Server. Abra o SQL Server management studio, ligar-se para a instância predefinida do SQL Server e verifique se que o grupo availabilty está configurado.


