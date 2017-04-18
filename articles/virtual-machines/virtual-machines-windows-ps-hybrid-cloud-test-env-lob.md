<properties 
    pageTitle="Ambiente de teste de aplicação LOB | Microsoft Azure" 
    description="Saiba como criar uma linha baseada na web, de aplicação empresarial num ambiente híbrido na nuvem para o IT pro testes ou desenvolvimento." 
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

# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurar uma aplicação de LOB baseada na web numa nuvem híbrido para testar a ligação

Este tópico irá guiá-criar um ambiente híbrido simulada de nuvem para testar a uma linha baseados na web e de aplicação de negócio (LOB) alojada no Microsoft Azure. Segue-se a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Esta configuração consiste em:

- Uma rede simulada no local alojada no Azure (TestLab VNet).
- Uma publicação em local rede virtual alojada no Azure (TestVNET).
- Uma ligação VPN VNet para VNet.
- Servidor LOB baseada na web, SQL server e controlador de domínio secundário na rede virtual TestVNET.

Esta configuração fornece uma base e o ponto de partida comuns a partir do qual pode:

- Desenvolver e testar aplicações LOB alojadas serviços de informação Internet (IIS) um base de dados do SQL Server 2014 back-end no Azure.
- Efetue testes deste híbrido simulada baseado na nuvem IT carga de trabalho.

Existem três fases principais para configurar o ambiente de teste este híbrido nuvem:

1.  Configure o ambiente de nuvem do híbrido simulada.
2.  Configure o computador do SQL server (SQL1).
3.  Configure o servidor LOB (LOB1).

Este carga de trabalho necessita de uma subscrição do Azure. Se tiver uma subscrição do MSDN ou Visual Studio, consulte o artigo [crédito Azure mensal para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Para obter um exemplo de uma aplicação LOB alojada no Azure de produção, consulte o artigo o plano de arquitectura de **linha de aplicações empresariais** em [diagramas de arquitectura de Software Microsoft e plantas](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Fase 1: Configurar o ambiente de nuvem do simulada híbrido

Crie [simulado ambiente híbrido de teste na nuvem](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). Uma vez que este ambiente de teste não requer a presença do servidor APP1 na sub-rede Corpnet, pode encerrá-la por agora.

Esta é a sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)
 
## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Fase 2: Configurar o computador do SQL server (SQL1)

A partir do portal do Azure, inicie o computador de DC2 caso seja necessário.

Em seguida, crie uma máquina virtual para SQL1 com estes comandos uma linha de comandos do Azure PowerShell no seu computador local. Antes de executar estes comandos, preencher os valores de variáveis e remover a < e > carateres.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Utilizar o portal do Azure para ligar à SQL1 utilizando a conta de administrador local da SQL1.

Em seguida, configure regras de Firewall do Windows para permitir o tráfego do SQL Server e de testes de conectividade básica. A partir de um nível de administrador do Windows PowerShell comandos SQL1, execute estes comandos.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas com êxito, a partir do endereço IP 192.168.0.4.

Em seguida, adicione o disco de dados extra no SQL1 como um novo volume com a letra f:.

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

Execute estes comandos na linha de comandos do Windows PowerShell no SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

Em seguida, participar SQL1 para o domínio do Active Directory do CORP Windows Server com estes comandos na linha de comandos do Windows PowerShell no SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Utilize a conta CORP\User1 quando lhe for pedido para fornecer as credenciais de conta do domínio para o comando **Adicionar computador** .

Após reiniciar, utilize o portal do Azure para ligar à SQL1 *com a conta de administrador local da SQL1*.

Em seguida, configure o SQL Server 2014 para utilizar a unidade de f para novas bases de dados e para as permissões de conta de utilizador.

1.  A partir do ecrã Iniciar, escreva a **Gestão do SQL Server**e, em seguida, clique em **SQL Server 2014 Management Studio**.
2.  Em **ligar ao servidor**, clique em **Ligar**.
3.  No painel de árvore de Explorador de objectos, com o botão direito **SQL1**e, em seguida, clique em **Propriedades**.
4.  Na janela de **Propriedades do servidor** , clique em **Definições de base de dados**.
5.  Localize as **localizações predefinidas de base de dados** e definir estes valores: 
    - Para os **dados**, escreva o caminho **f:\Data**.
    - Para o **registo**, escreva o caminho **f:\Log**.
    - **Cópia de segurança**, escreva o caminho **f:\Backup**.
    - Nota: Apenas novas bases de dados, utilize estas localizações.
6.  Clique em **OK** para fechar a janela.
7.  No painel de árvore de **Objeto Explorer** , abra a **segurança**.
8.  Com o botão direito **inícios de sessão** e, em seguida, clique em **Novo início de sessão**.
9.  Em **nome de início de sessão**, escreva **CORP\User1**.
10. Na página de **Funções de servidor** , clique em **sysadmin**e, em seguida, clique em **OK**.
11. Feche o Microsoft SQL Server Management Studio.

Esta é a sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## <a name="phase-3-configure-the-lob-server-lob1"></a>Fase 3: Configurar o servidor LOB (LOB1)

Em primeiro lugar, crie uma máquina virtual para LOB1 com estes comandos na linha de comandos do Azure PowerShell no seu computador local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"
    
    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, utilize o portal do Azure para ligar a LOB1 com as credenciais da conta de administrador local do LOB1.

Em seguida, configure uma regra de Firewall do Windows para permitir o tráfego para testes de conectividade básica. A partir de um nível de administrador do Windows PowerShell comandos LOB1, execute estes comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas com êxito, a partir do endereço IP 192.168.0.4.

Em seguida, participar LOB1 para o domínio do Active Directory CORP com estes comandos na linha de comandos do Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Utilize a conta CORP\User1 quando lhe for pedido para fornecer as credenciais de conta do domínio para o comando **Adicionar computador** .

Após reiniciar, utilize o portal do Azure para ligar a LOB1 com a conta de CORP\User1 e a palavra-passe.

Em seguida, configure LOB1 para o IIS e testar o acesso do CLIENT1.

1.  A partir do Gestor de servidor, clique em **Adicionar funções e funcionalidades**.
2.  Na página **antes de começar** , clique em **seguinte**.
3.  Na página **Selecionar o tipo de instalação** , clique em **seguinte**.
4.  Na página **Selecionar o servidor de destino** , clique em **seguinte**.
5.  Na página de **funções de servidor** , clique em **Web Server (IIS)** na lista de **funções**.
6.  Quando lhe for pedido, clique em **Adicionar funcionalidades**e, em seguida, clique em **seguinte**.
7.  Na página **Selecione funcionalidades** , clique em **seguinte**.
8.  Na página **Web Server (IIS)** , clique em **seguinte**.
9.  Na página **Selecionar serviços de função** , selecione ou desmarque as caixas de verificação para os serviços de que necessita para testar a sua aplicação LOB e, em seguida, clique em **seguinte**.
10. Na página **Confirmar selecções de instalação** , clique em **instalar**.
11. Aguarde até que a instalação dos componentes foi concluída e, em seguida, clique em **Fechar**.
12. A partir do portal do Azure, ligar para o computador CLIENT1 com as credenciais da conta CORP\User1 e, em seguida, inicie o Internet Explorer.
13. Na barra de endereço, escreva **http://lob1/** e, em seguida, prima ENTER. Deverá ver a página de web do IIS 8 predefinida.

Esta é a sua configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Este ambiente está agora pronto implementar a aplicação baseada na web no LOB1 e testar a funcionalidade de CLIENT1 na sub-rede Corpnet.

## <a name="next-step"></a>Passo seguinte

- Adicione uma nova máquina virtual utilizando o [Azure portal](virtual-machines-windows-hero-tutorial.md).
