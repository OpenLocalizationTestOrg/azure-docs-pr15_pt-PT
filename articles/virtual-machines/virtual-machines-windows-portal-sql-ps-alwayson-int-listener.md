<properties
   pageTitle="Configurar o sempre no grupo de disponibilidade Listeners – do Microsoft Azure"
   description="Configure a disponibilidade de grupo Listeners no modelo de Gestor de recursos do Azure, com um balanceador de carga interno com um ou mais endereços IP."
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
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar um ou mais sempre no disponibilidade grupo Listeners - Gestor de recursos 

Este tópico mostra como efetuar dois procedimentos:

- Crie um balanceador de carga interna para grupos de disponibilidade do SQL Server utilizando os cmdlets do PowerShell.

- Adicione endereços IP adicionais a um balanceador de carga para suportar mais do que um grupo de disponibilidade do SQL Server. 

No SQL Server uma escuta de grupo de disponibilidade é um nome de rede virtual que os clientes se ligam para poder aceder a uma base de dados na réplica principal ou secundária. Em máquinas virtuais Azure, um balanceador de carga detém o endereço IP a escutar. O Balanceador de carga encaminha o tráfego para a instância do SQL Server que está a escutar a porta sonda. Na maioria dos casos, um grupo de disponibilidade utiliza um balanceador de carga interno. Um balanceador de carga interno Azure pode alojar um ou vários endereços IP. Cada endereço IP utiliza uma porta sonda específico. Este documento mostra como utilizar o PowerShell para criar um novo Balanceador de carga ou adicione endereços IP para um existente Balanceador de carga para grupos de disponibilidade do SQL Server. 

A capacidade de atribuir vários endereços IP para um balanceador de carga interno é nova utilizador do Azure e só está disponível no modelo de Gestor de recursos. Para concluir esta tarefa, tem de ter um grupo de disponibilidade do SQL Server implementado em máquinas virtuais Azure no modelo de Gestor de recursos. Ambas as máquinas virtuais do SQL Server tem de pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o [modelo do Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Gestor de recursos do Azure. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o Balanceador de carga interno por si. Se preferir, pode [configurar manualmente um grupo de Disponibilidade AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico requer que os seus grupos de disponibilidade já estão configurados.  

Tópicos relacionados incluem:

- [Configurar a grupos de Disponibilidade AlwaysOn no Azure VM (interface gráfica)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [Configurar uma ligação de VNet para VNet através do Gestor de recursos do Azure e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurar a Firewall do Windows

Configure a Firewall do Windows para permitir o acesso do SQL Server. Terá de configurar a firewall para permitir que as ligações para o uso de portas por instância do SQL Server, bem como a porta utilizada pela sonda de escuta TCP. Para obter instruções detalhadas, consulte [Configurar uma Firewall do Windows para o acesso do motor de base de dados](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crie uma regra de entrada para a porta do servidor de SQL e para a porta sonda.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exemplo de Script: Criar um balanceador de carga interno com PowerShell

> [AZURE.NOTE] Se tiver criado o grupo de disponibilidade com o [modelo do Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) a carga não necessita de concluir este passo. 

O seguinte script do PowerShell cria uma Balanceador de carga interno, configura as regras de balanceamento de carga e conjuntos de um endereço IP para o Balanceador de carga. Para executar o script, abra o Windows PowerShell ISE e colar o script no painel de Script. Utilizar `Login-AzureRMAccount` para iniciar sessão PowerShell. Se tiver múltiplas subscrições Azure, utilize `Select-AzureRmSubscription ` para definir a subscrição. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Exemplo de script: adicionar um endereço IP a um balanceador de carga existente com o PowerShell

Para utilizar mais do que um grupo de disponibilidade, utilize o PowerShell para adicionar um endereço IP adicional para uma Balanceador de carga existente. Cada endereço IP requer os suas próprias balanceamento de carga regra, sonda porta e porta frente.

A porta de front-end é a porta que aplicações utilizam para ligar a instância do SQL Server. Endereços IP para grupos de disponibilidade diferente, podem utilizar a mesma porta de front-end.

>[AZURE.NOTE] Grupos de disponibilidade do SQL Server, cada endereço IP exige uma porta sonda específico. Por exemplo, se um endereço IP num Balanceador de carga utiliza sonda porta 59999, sem outros endereços IP que Balanceador de carga podem utilizar sonda porta 59999. 

- Para obter informações sobre Balanceador de carga limites consulte o artigo **frente privada terminar IP por Balanceador de carga** em [Limites de funcionamento em rede - Gestor de recursos do Azure](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Para obter informações sobre a disponibilidade dos limites de grupo Consulte o artigo [restrições (disponibilidade grupos)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O seguinte script adiciona um novo endereço IP para um balanceador de carga existente. Atualize as variáveis para o seu ambiente. O ILB utiliza a porta de escuta da porta de front-end balanceamento de carga. Esta porta pode ser a porta que está a escutar do SQL Server. Para instâncias de predefinidas do SQL Server, este é porta 1433. A regra balanceamento de carga de um grupo de disponibilidade requer um IP flutuante (direto server retorno), de modo a porta de back-end é igual a porta de front-end.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar o cluster para utilizar o endereço IP do Balanceador de carga 

O próximo passo é configurar a escuta no cluster e trazer a escuta online. Para realizar esta tarefa, faça o seguinte: 

1. Criar a escuta de grupo de disponibilidade no cluster activação pós-falha  

2. Trazer a escuta online

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1. Crie a escuta de grupo de disponibilidade no cluster activação pós-falha

Neste passo, adicionar um ponto de acesso de cliente ao cluster activação pós-falha com activação pós-falha Cluster Manager e, em seguida, utilizar o PowerShell para configurar o recurso de cluster para ouvir na porta sonda. 

1. Utilize RDP para ligar à máquina virtual Azure que aloja a réplica principal. 

2. Abra o Gestor de Cluster de activação pós-falha.

3. Selecione o nó de **redes** e tome nota do nome de rede cluster. Utilize este nome na `$ClusterNetworkName` variável no PowerShell script.

4. Expanda o nome do cluster e, em seguida, clique em **funções**.

5. No painel de **funções** , o botão direito do rato no nome do grupo de disponibilidade e, em seguida, selecione **Adicionar recurso** > **Ponto de acesso de cliente**.

6. Na caixa **nome** , criar um nome este escutar nova, em seguida, clique duas vezes em **seguinte** e, em seguida, clique em **Concluir**. Não coloque o recurso online ou escuta neste momento.
 
    O nome a escutar novo é o nome de rede que aplicações irão utilizar para ligar a bases de dados no grupo de disponibilidade do SQL Server.

7. Clique no separador **recursos** , em seguida, expanda o ponto de acesso de cliente que acabou de criar. O recurso de IP com o botão direito e clique em propriedades. Tenha em atenção o nome do endereço IP. Irá utilizar este nome na `$IPResourceName` variável no PowerShell script.

8. Em **Endereço IP** clique em **Endereço IP estático** e defina o endereço IP estático para o mesmo endereço que utilizou quando define o endereço IP do Balanceador de carga no portal do Azure. 

9. Desactivar o NetBIOS para este endereço e clique em **OK**. Repita este passo para cada recurso IP se a solução abranger vários Azure VNets. 

10. Torne o recurso de grupo de disponibilidade do SQL Server dependente o endereço IP. Clique direito no recurso no Gestor de cluster, este é no separador **recursos** em **Outros recursos**. 

11. No nó de cluster que aloja atualmente a réplica principal, abra uma elevados o ISE do PowerShell e cole os seguintes comandos um novo script. No separador **dependências** , clique no nome da escuta.
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. Atualize as variáveis e executar o script do PowerShell para configurar o endereço IP e portas a escutar de novo.

 >[AZURE.NOTE] Se forem seus servidores de SQL em regiões separadas, que precisa para executar o script do PowerShell duas vezes. A primeira vez utilize o nome de rede do cluster, nome do recurso de cluster IP e carregar o endereço IP do Balanceador a partir do primeiro grupo de recursos. Na segunda vez utilize o nome de rede do cluster, nome do recurso de cluster IP e carregar o endereço IP do Balanceador do grupo de recursos segundo.

Cluster tem agora um recurso de escuta do grupo de disponibilidade.

## <a name="2-bring-the-listener-online"></a>2. trazer a escuta online

Com o recurso de escuta de grupo disponibilidade configurado, pode trazer com a escuta online para que podem ligar aplicações para o grupo de disponibilidade com a escuta de bases de dados.

1. Navegue até voltar ao activação pós-falha Cluster gestor. Expanda **funções** e, em seguida, realçar o seu grupo de disponibilidade. No separador **recursos** , botão direito do rato no nome de escuta e clique em **Propriedades**.

1. Clique no separador **dependências** . Se existirem vários recursos listados, certifique-se de que os endereços IP têm ou, não AND, dependências. Clique em **OK**.

1. Botão direito do rato no nome de escuta e clique em **Colocar Online**.

1. Assim que a escuta estiver online, a partir do separador **recursos** , com o botão direito do grupo de disponibilidade e clique em **Propriedades**.

1. Crie uma dependência no recurso de nome escuta (não o IP endereço recursos nome). Clique em **OK**.

1. Inicie o SQL Server Management Studio e ligar-se para a réplica principal.

1. Navegue para a **disponibilidade de alta AlwaysOn** | **grupos de disponibilidade** | **Listeners de grupo de disponibilidade**. 

1. Agora deverá ver o nome de escuta que criou no Gestor de clusters de activação pós-falha. Botão direito do rato no nome de escuta e clique em **Propriedades**.

1. Na caixa **porta** , especifique o número de porta a escutar de grupo de disponibilidade utilizando $EndpointPort que utilizou anteriormente (1433 era a predefinição), em seguida, clique em **OK**.

Tem agora um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure executar no modo de Gestor de recursos. 

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação para a escuta

Para testar a ligação:

1. RDP para SQL Server que está na mesma rede virtual, mas não o proprietário da réplica. Isto pode ser outras do SQL Server no cluster.

2. Utilize **sqlcmd** utilitário para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação de **sqlcmd** com a réplica principal através de escuta com autenticação do Windows:

    ```
    sqlmd -S <listenerName> -E
    ```

    Se a escuta está a utilizar uma porta diferente do predefinido (1433) da porta, especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd liga a uma escuta porto 1435: 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A ligação SQLCMD liga-se automaticamente para independentemente instância do SQL Server aloja a réplica principal. 

>[AZURE.NOTE] Certifique-se de que a porta que especificar está aberta na firewall de ambos os servidores de SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que utiliza. Para mais informações, consulte [Adicionar ou Editar regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx) . 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

Tenha em atenção as seguintes diretrizes escuta de grupo de disponibilidade no Azure utilizando interno Balanceador de carga:

- Com um balanceador de carga interno só aceder a escuta a partir de dentro da mesma rede virtual.

## <a name="for-more-information"></a>Para obter mais informações

Para obter mais informações, consulte [Configurar sempre no disponibilidade agrupar no Azure VM manualmente](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

Utilize os seguintes cmdlets do PowerShell para criar um balanceador de carga interna para máquinas virtuais Azure.

- `New-AzureRmLoadBalancer`cria um balanceador de carga. Para mais informações, consulte [AzureRmLoadBalancer novo](http://msdn.microsoft.com/library/mt619450.aspx) . 

- `New-AzureRMLoadBalancerFrontendIpConfig`cria uma configuração de IP Front-end para um balanceador de carga. Para mais informações, consulte [AzureRMLoadBalancerFrontendIpConfig novo](http://msdn.microsoft.com/library/mt603510.aspx) .

- `New-AzureRmLoadBalancerRuleConfig`cria uma regra de configuração para um balanceador de carga. Para mais informações, consulte [AzureRmLoadBalancerRuleConfig novo](http://msdn.microsoft.com/library/mt619391.aspx) . 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`cria uma configuração de conjunto de dados back-end endereço para um balanceador de carga. Para mais informações, consulte [AzureRmLoadBalancerBackendAddressPoolConfig novo](http://msdn.microsoft.com/library/mt603791.aspx) . 

- `New-AzureRmLoadBalancerProbeConfig`cria uma configuração sonda para um balanceador de carga. Para mais informações, consulte [AzureRmLoadBalancerProbeConfig novo](http://msdn.microsoft.com/library/mt603847.aspx) .

Se precisar de remover um balanceador de carga de um grupo de recursos Azure, utilize `Remove-AzureRmLoadBalancer`. Para mais informações, consulte o artigo [Remover AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).
