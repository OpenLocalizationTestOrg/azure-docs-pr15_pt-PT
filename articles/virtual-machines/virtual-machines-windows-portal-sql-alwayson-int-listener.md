<properties
   pageTitle="Criar escuta para AlwaysOn availabilty grupo para o SQL Server em máquinas virtuais do Azure"
   description="Instruções passo a passo para criar uma escuta de um grupo do availabilty AlwaysOn para SQL Server em máquinas virtuais do Azure"
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="07/12/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Configurar um balanceador de carga interna para um grupo de Disponibilidade AlwaysOn no Azure

Este tópico explica como criar um balanceador de carga interna para um grupo de Disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure a ser executada em modelo do Gestor de recursos. Um grupo de Disponibilidade AlwaysOn requer um balanceador de carga quando são instâncias do SQL Server em máquinas virtuais Azure. O Balanceador de carga armazena o endereço IP a escutar de grupo de disponibilidade. Se um grupo de disponibilidade abranger múltipla regiões, cada uma das regiões necessita de um balanceador de carga.

Para concluir esta tarefa, tem de ter um grupo de disponibilidade do SQL Server AlwaysOn implementado em máquinas virtuais Azure no modelo de Gestor de recursos. Ambas as máquinas virtuais do SQL Server tem de pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o [modelo do Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar o grupo de Disponibilidade AlwaysOn automaticamente no Gestor de recursos Azure. Este modelo cria automaticamente o Balanceador de carga interno por si. 

Se preferir, pode [configurar manualmente um grupo de Disponibilidade AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico requer que os seus grupos availablity já estão configurados.  

Tópicos relacionados incluem:

 - [Configurar a grupos de Disponibilidade AlwaysOn no Azure VM (interface gráfica)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
 
 - [Configurar uma ligação de VNet para VNet através do Gestor de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Passos

Por observar este documento irá criar e configurar um balanceador de carga no portal do Azure. Depois de que estiver concluída, irá configurar o cluster para utilizar o endereço IP do Balanceador de carga a escutar de grupo do disponibilidade de AlwaysOn.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o Balanceador de carga no portal do Azure

Nesta parte da tarefa será efetue os seguintes passos no portal do Azure:

1. Criar o Balanceador de carga e configurar o endereço IP

1. Configurar o conjunto de back-end

1. Criar a sonda 

1. Definir as regras de balanceamento de carga

>[AZURE.NOTE] Se os servidores de SQL na diferentes grupos de recursos e regiões, que irá fazer todos estes passos de duas vezes, uma vez no cada grupo de recursos.

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Balanceador de carga de criar e configurar o endereço IP

O primeiro passo é criar Balanceador de carga. No portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. No grupo de recursos, clique em **Adicionar**.

- Procure **Balanceador de carga**. Nos resultados da pesquisa, selecione **Balanceador de carga**, que é publicado pela **Microsoft**.

- No pá **Balanceador de carga** , clique em **Criar**.

- No **Balanceador de carga de criar**, configure o Balanceador de carga da seguinte forma:

| Definição | Valor |
| ----- | ----- |
| **Nome** | Um nome de texto que representa o Balanceador de carga. Por exemplo, **sqlLB**. |
| **Esquema** | **Interno** |
| **Rede virtual** | Selecione a rede virtual que estão os servidores de SQL.   |
| **Sub-rede**  | Selecione a sub-rede que estão os servidores de SQL. |
| **Subscrição** | Se tem múltiplas subscrições, pode aparecer neste campo. Selecione a subscrição da qual pretende associar este recurso. Normalmente, é a mesma subscrição como todos os recursos para o grupo de disponibilidade.  |
| **Grupo de recursos** | Selecione o grupo de recursos que estão os servidores de SQL. | 
| **Localização** | Escolha a localização Azure que estão os servidores de SQL. |

- Clique em **Criar**. 

Azure cria o Balanceador de carga que configurou acima. O Balanceador de carga pertence a uma rede específica, sub-rede, grupo de recursos e localização. Após completa o Azure, verifique as definições do Balanceador de carga no Azure. 

Agora, configure o endereço IP do Balanceador de carga.  

- No pá de **definições do** Balanceador de carga, clique em **endereço IP**. O **endereço IP** pá mostra que este é um balanceador de carga privado na mesma rede virtual como seus servidores de SQL. 

- Defina as seguintes definições: 

| Definição | Valor |
| ----- | ----- |
| **Sub-rede** | Selecione a sub-rede que estão os servidores de SQL. |
| **Atribuição** | **Estático** |
| **Endereço IP** | Escreva um endereço IP virtual não utilizado da sub-rede.  |

- Guarde as definições.

O Balanceador de carga tem agora um endereço IP. Registo este endereço IP. Irá utilizar este endereço IP quando cria uma escuta no cluster. Num script PowerShell neste artigo, utilizar este endereço para a `$ILBIP` variável.



## <a name="2-configure-the-backend-pool"></a>2. a configurar o conjunto de back-end

O passo seguinte é criar um conjunto de endereços de back-end. Azure chamadas de agrupamento de endereços back-end *agrupamento de back-end*. Neste caso, o conjunto de back-end é os endereços dos servidores SQL dois no seu grupo de disponibilidade. 

- No seu grupo de recursos, clique no balanceador de carga que criou. 

- Em **Definições**, clique em **conjuntos de dados back-end**.

- No **back-end conjuntos de endereços**, clique em **Adicionar** para criar um conjunto de endereços de back-end. 

- No **conjunto de back-end adicionar** em **nome**, escreva um nome para o conjunto de dados back-end.

- Em **máquinas virtuais** clique em **+ Adicionar uma máquina virtual**. 

- Em **máquinas virtuais de escolher** clique em **Escolher um conjunto de disponibilidade** e especifique o availablity conjunto pertence, para que as máquinas virtuais do SQL Server.

- Após ter escolhido o conjunto de disponibilidade, clique em **Escolher as máquinas virtuais**. Clique em máquinas virtuais duas que alojam instâncias do SQL Server, no grupo disponibilidade. Clique em **Selecionar**. 

- Clique em **OK** para fechar as lâminas para **máquinas virtuais de escolher**e **Adicionar conjunto de back-end**. 

Azure atualiza as definições para o conjunto de endereços de back-end. O conjunto de disponibilidade tem agora um conjunto de dois servidores de SQL.

## <a name="3-create-a-probe"></a>3. cria uma sonda

O passo seguinte é criar uma sonda. A sonda define como Azure verificar quais os servidores de SQL possui atualmente a escuta disponibilidade do grupo. Azure irá pesquisar o serviço baseado no endereço IP numa porta que definem quando cria a sonda.

- No pá de **definições do** Balanceador de carga, clique em **sondas**. 

- No pá **sondas** , clique em **Adicionar**.

- Configure a sonda no pá a **sonda de adicionar** . Utilize os seguintes valores para configurar a sonda de:

| Definição | Valor |
| ----- | ----- |
| **Nome** | Um nome de texto que representa a sonda. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
| **Protocolo** | **TCP** |
| **Porta** | Pode utilizar qualquer porta disponível. Por exemplo, *59999*.    |
| **Intervalo**  | *5* | 
| **Limiar danificado**  | *2* | 

- Clique em **OK**. 

>[AZURE.NOTE] Certifique-se de que a porta que especificar está aberta na firewall de ambos os servidores de SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que utiliza. Para mais informações, consulte [Adicionar ou Editar regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx) . 

Azure cria a sonda. Azure irá utilizar a sonda para testar a qual o SQL Server tem a escuta para o grupo de disponibilidade.

## <a name="4-set-the-load-balancing-rules"></a>4. definir as regras de balanceamento de carga

Defina as regras de balanceamento de carga. As regras de balanceamento de carga configurar como Balanceador de carga encaminha o tráfego para os servidores de SQL. Para este Balanceador de carga irá ativar direta servidor remetente porque apenas um dos dois servidores de SQL alguma vez será o proprietário do recurso de escuta do grupo de disponibilidade uma vez.

- No pá de **definições do** Balanceador de carga, clique em **regras de balanceamento de carga**. 

- No pá **balanceamento de carga em regras** , clique em **Adicionar**.

- Utilize o separador **regras de balanceamento de carga de adicionar** para configurar a regra balanceamento de carga. Utilize as seguintes definições: 

| Definição | Valor |
| ----- | ----- |
| **Nome** | Um nome de texto que representa as regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
| **Protocolo** | **TCP** |
| **Porta** | *1433*   |
| **Porta de back-end** | *1433*. note que esta será desativada em porque esta regra utiliza **Flutuantes IP (devolver directo server)**.   |
| **Sonda** | Utilize o nome da sonda que criou para esta Balanceador de carga. |
| **Persistência de sessão**  | **Nenhum** | 
| **Tempo limite inactivo (minutos)**  | *4* | 
| **Flutuantes IP (devolver directo server)**  | **Ativado** | 

 >[AZURE.NOTE] Poderá ter de deslocar para baixo no pá para ver todas as definições.

- Clique em **OK**. 

- Azure configura a regra balanceamento de carga. Agora o Balanceador de carga está configurado para encaminhar tráfego para o SQL Server que aloja a escuta para o grupo de disponibilidade. 

Neste momento o grupo de recursos tem um balanceador de carga, ligar-se ao ambas as máquinas do SQL Server. O Balanceador de carga também contém um endereço IP a escutar de grupo do availablity de AlwaysOn do SQL Server para que um dos máquina pode responder a pedidos para os grupos de disponibilidade.

>[AZURE.NOTE] Se os servidores de SQL estiverem em duas áreas em separado, repita os passos na outras região. Cada região requer um balanceador de carga. 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar o cluster para utilizar o endereço IP do Balanceador de carga 

O próximo passo é configurar a escuta no cluster e trazer a escuta online. Para realizar esta tarefa, faça o seguinte: 

1. Criar a escuta do grupo availablity no cluster activação pós-falha 

1. Trazer a escuta online

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. Crie a escuta do grupo availablity no cluster activação pós-falha

Neste passo, criar manualmente a escuta de grupo de disponibilidade no Gestor de clusters de activação pós-falha e o SQL Server Management Studio (SSMS).

- Utilize RDP para ligar à máquina virtual Azure que aloja a réplica principal. 

- Abra o Gestor de Cluster de activação pós-falha.

- Selecione o nó de **redes** e tome nota do nome de rede cluster. Este nome irá utilizado na `$ClusterNetworkName` variável no PowerShell script.

- Expanda o nome do cluster e, em seguida, clique em **funções**.

- No painel de **funções** , o botão direito do rato no nome do grupo de disponibilidade e, em seguida, selecione **Adicionar recurso** > **Ponto de acesso de cliente**.

- Na caixa **nome** , criar um nome este escutar nova, em seguida, clique duas vezes em **seguinte** e, em seguida, clique em **Concluir**. Não coloque o recurso online ou escuta neste momento.

 >[AZURE.NOTE] O nome a escutar novo é o nome de rede que aplicações irão utilizar para ligar a bases de dados no grupo de disponibilidade do SQL Server.

- Clique no separador **recursos** , em seguida, expanda o ponto de acesso de cliente que acabou de criar. O recurso de IP com o botão direito e clique em propriedades. Tenha em atenção o nome do endereço IP. Irá utilizar este nome na `$IPResourceName` variável no PowerShell script.

- Em **Endereço IP** clique em **Endereço IP estático** e defina o endereço IP estático para o mesmo endereço que utilizou quando define o endereço IP do Balanceador de carga no portal do Azure. Activar NetBIOS para este endereço e clique em **OK**. Repita este passo para cada recurso IP se a solução abranger vários Azure VNets. 

- No nó de cluster que aloja atualmente a réplica principal, abra uma elevados o ISE do PowerShell e cole os seguintes comandos um novo script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    
        Import-Module FailoverClusters
    
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

- Atualize as variáveis e executar o script do PowerShell para configurar o endereço IP e portas a escutar de novo.

 >[AZURE.NOTE] Se forem seus servidores de SQL em regiões separadas, que precisa para executar o script do PowerShell duas vezes. A primeira vez utilize o nome de rede do cluster, nome do recurso de cluster IP e carregar o endereço IP do Balanceador a partir do primeiro grupo de recursos. Na segunda vez utilize o nome de rede do cluster, nome do recurso de cluster IP e carregar o endereço IP do Balanceador do grupo de recursos segundo.

Cluster tem agora um recurso de escuta do grupo de disponibilidade.

## <a name="2-bring-the-listener-online"></a>2. trazer a escuta online

Com o recurso de escuta de grupo disponibilidade configurado, pode trazer com a escuta online para que podem ligar aplicações para o grupo de disponibilidade com a escuta de bases de dados.

- Navegue até voltar ao activação pós-falha Cluster gestor. Expanda **funções** e, em seguida, realçar o seu grupo de disponibilidade. No separador **recursos** , botão direito do rato no nome de escuta e clique em **Propriedades**.

- Clique no separador **dependências** . Se existirem vários recursos listados, certifique-se de que os endereços IP têm ou, não AND, dependências. Clique em **OK**.

- Botão direito do rato no nome de escuta e clique em **Colocar Online**.


- Assim que a escuta estiver online, a partir do separador **recursos** , com o botão direito do grupo de disponibilidade e clique em **Propriedades**.

- Crie uma dependência no recurso de nome escuta (não o IP endereço recursos nome). Clique em **OK**.


- Inicie o SQL Server Management Studio e ligar-se para a réplica principal.


- Navegue para a **disponibilidade de alta AlwaysOn** | **grupos de disponibilidade** | **Listeners de grupo de disponibilidade**. 


- Agora deverá ver o nome de escuta que criou no Gestor de clusters de activação pós-falha. Botão direito do rato no nome de escuta e clique em **Propriedades**.


- Na caixa **porta** , especifique o número de porta a escutar de grupo de disponibilidade utilizando $EndpointPort que utilizou anteriormente (1433 era a predefinição), em seguida, clique em **OK**.

Tem agora um grupo de Disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure executar no modo de Gestor de recursos. 

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação para a escuta

Para testar a ligação:

1. RDP para SQL Server que está na mesma rede virtual, mas não o proprietário da réplica. Isto pode ser outras do SQL Server no cluster.

1. Utilize **sqlcmd** utilitário para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação de **sqlcmd** com a réplica principal através de escuta com autenticação do Windows:

        sqlcmd -S <listenerName> -E

A ligação SQLCMD ligar automaticamente ao independentemente instância do SQL Server aloja a réplica principal. 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

Tenha em atenção as seguintes diretrizes availablity grupo de escuta no Azure utilizando interno Balanceador de carga:

- Apenas uma escuta de grupo interno availablity é suportada por serviço na nuvem porque a está configurado para o Balanceador de carga e existe apenas uma Balanceador de carga interno. No entanto é possível criar multipe listeners externos. 

- Com um balanceador de carga interno só aceder a escuta a partir de dentro da mesma rede virtual.
 
