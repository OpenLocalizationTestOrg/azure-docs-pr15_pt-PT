<properties 
    pageTitle="Ambiente de teste do híbrido simulada nuvem | Microsoft Azure" 
    description="Criar um ambiente híbrido simulada de nuvem para IT pro testes ou desenvolvimento, com duas redes virtuais Azure e uma ligação de VNet para VNet." 
    services="virtual-machines-windows" 
    documentationCenter="" 
    authors="JoeDavies-MSFT" 
    manager="timlt" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="virtual-machines-windows" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-windows" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/30/2016" 
    ms.author="josephd"/>

# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configurar um ambiente híbrido simulada de nuvem para testar a ligação

Este artigo irá guiá-criar um ambiente híbrido simulada de nuvem com Microsoft Azure utilizando duas redes virtuais Azure. Segue-se a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Isto simula um ambiente híbrido de produção na nuvem e consiste em:

- Uma rede simulada e simplificado no local alojada numa rede virtual Azure (a rede virtual do TestLab).
- Uma publicação em local simulada rede virtual alojada no Azure (TestVNET).
- Uma ligação de VNet para VNet entre as duas redes virtuais.
- Controlador de domínio secundário na rede TestVNET virtual.

Este procedimento fornece que uma base e, em seguida, iniciar comuns apontam a partir da qual pode:

- Desenvolver e testar aplicações num ambiente híbrido simulada na nuvem.
- Crie configurações de teste de computadores, algumas dentro da rede virtual TestLab e algumas dentro da rede virtual TestVNET, para simular híbrido baseado na nuvem IT das cargas de trabalho.

Existem quatro fases principais para configurar o ambiente de teste este híbrido nuvem:

1.  Configure a rede virtual TestLab.
2.  Crie a rede virtual cruzada local.
3.  Crie a ligação VPN VNet para VNet.
4.  Configure DC2. 

Esta configuração requer uma subscrição do Azure. Se tiver uma subscrição do MSDN ou Visual Studio, consulte o artigo [crédito Azure mensal para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Máquinas virtuais e gateways de rede virtual no Azure implicam um custo monetário em curso quando estiverem em execução. Este custo é faturado contra a MSDN ou subscrição paga. Um gateway Azure VPN é implementado como um conjunto de duas máquinas virtuais Azure. Para minimizar os custos, crie o ambiente de teste e executar seu testes conforme seja necessário e demonstração sobre o mais rapidamente possível.

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: Configurar a rede virtual TestLab

Utilize as instruções no tópico [da Base de configuração de ambiente de teste](https://technet.microsoft.com/library/mt771177.aspx) para configurar os computadores DC1, APP1 e CLIENT1 na rede virtual Azure denominada TestLab. 

Em seguida, inicie uma linha de comandos do PowerShell do Azure.

> [AZURE.NOTE] O seguinte comando define a utilizar o PowerShell Azure 1.0 e versões posteriores.

Inicie sessão sua conta.

    Login-AzureRMAccount

Obter o nome da sua subscrição utilizando o seguinte comando.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Configure a sua subscrição do Azure. Utilize a mesma subscrição que utilizou para construir a sua configuração base na fase 1. Substituir tudo dentro de aspas, incluindo o < e > carateres, com o nome correto.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Em seguida, adicione uma sub-rede de gateway para a rede virtual TestLab da configuração de base, será utilizada para alojar o gateway Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

O pedido seguinte, um endereço IP público para atribuir ao gateway para a rede virtual TestLab.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Em seguida, crie o gateway.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Tenha em atenção que gateways novos podem demorar 20 minutos ou mais para criar.

A partir do portal do Azure no seu computador local, ligue para DC1 com as credenciais de CORP\User1. Para configurar o domínio CORP para que os computadores e os utilizadores utilizam o controlador de domínio local para autenticação, execute estes comandos a partir de uma linha de comandos do Windows PowerShell de nível de administrador no DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Esta é a sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: Criar a rede virtual TestVNET

Em primeiro lugar, crie a rede virtual TestVNET e protegê-lo com um grupo de segurança de rede.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

O pedido seguinte, um endereço IP público para serem atribuídos para o gateway para a rede virtual TestVNET e criar o gateway.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Esta é a sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##<a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: Criar a ligação VNet para VNet

Em primeiro lugar, obter uma chave de pré-partilhada aleatória, com encriptação forte, 32 carateres a partir do seu administrador de rede ou segurança. Em alternativa, utilize as informações em [criar uma cadeia de aleatória para uma chave pré-partilhada IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) para obter uma chave de pré-partilhada.

Em seguida, utilize estes comandos para criar a ligação VPN VNet para VNet, que pode demorar algum tempo para concluir.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Depois de alguns minutos, deve ser estabelecer a ligação.

Esta é a sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## <a name="phase-4-configure-dc2"></a>Fase de 4: Configurar DC2

Em primeiro lugar, crie uma máquina virtual para DC2. Execute estes comandos na linha de comandos do Azure PowerShell no seu computador local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, ligar para a nova máquina de virtual DC2 a partir do portal do Azure.

Em seguida, configure uma regra de Firewall do Windows para permitir o tráfego para testes de conectividade básica. A partir de um nível de administrador do Windows PowerShell comandos DC2, execute estes comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas com êxito, a partir do endereço IP 10.0.0.4. Este é um teste de tráfego através da ligação VNet para VNet.

Em seguida, adicione o disco de dados extra no DC2 como um novo volume com a letra f:.

1.  No painel esquerdo do Gestor de servidor, clique em **ficheiro e serviços de armazenamento**e, em seguida, clique em **discos**.
2.  No painel de conteúdo, no grupo **discos** , clique em **disco 2** (com a **partições** definido para **desconhecido**).
3.  Clique em **tarefas**e, em seguida, clique em **Novo Volume**.
4.  No antes de começar a página do Assistente de novo Volume, clique em **seguinte**.
5.  Na página selecionar o servidor e disco, clique em **disco 2**e, em seguida, clique em **seguinte**. Quando lhe for pedido, clique em **OK**.
6.  Na especificar o tamanho da página volume, clique em **seguinte**.
7.  Na atribuir a uma página de letra ou a pasta da unidade, clique em **seguinte**.
8.  Na página Definições do sistema selecione ficheiro, clique em **seguinte**.
9.  Na página de seleções confirmar, clique em **Criar**.
10. Quando tiver terminado, clique em **Fechar**.

Em seguida, configure DC2 como uma réplica do controlador de domínio para o domínio corp.contoso.com. Execute estes comandos a partir da linha de comandos do Windows PowerShell no DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Tenha em atenção que lhe for pedido para fornecer a palavra-passe CORP\User1 e uma palavra-passe do diretório serviços restaurar DSRM (modo) e para reiniciar DC2.

Agora que a rede virtual TestVNET tem o seu próprio servidor DNS (DC2), tem de configurar a rede virtual TestVNET para utilizar este servidor DNS.

1.  No painel esquerdo do Azure portal, clique no ícone de redes virtuais e, em seguida, clique em **TestVNET**.
2.  No separador **Definições** , clique em **servidores DNS**.
3.  Em **servidor de DNS principal**, escreva **192.168.0.4** para substituir 10.0.0.4.
4.  Clique em **Guardar**.

Esta é a sua configuração atual. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
O ambiente de nuvem híbrido simulada está agora pronto para testar a ligação.

## <a name="next-step"></a>Passo seguinte

- Configure uma [linha baseada na web, de aplicação empresarial](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) neste ambiente.
