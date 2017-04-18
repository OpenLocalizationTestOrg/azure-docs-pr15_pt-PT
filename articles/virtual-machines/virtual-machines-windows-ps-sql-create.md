<properties
    pageTitle="Criar uma máquina de Virtual do SQL Server no Azure PowerShell (Gestor de recurso) | Microsoft Azure"
    description="Fornece passos e scripts de PowerShell para criar um VM Azure com imagens da Galeria de máquina virtual do SQL Server."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Aprovisionar uma máquina de virtual do SQL Server através do PowerShell Azure (Gestor de recurso)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como criar uma única máquina virtual Azure utilizando o modelo de implementação do **Gestor de recursos do Azure** com os cmdlets do Azure PowerShell. Neste tutorial, criamos uma única máquina de virtual utilizando uma única unidade de disco a partir de uma imagem na Galeria de SQL. Vamos criar novos fornecedores de armazenamento, rede e recursos de cluster que serão utilizados pela máquina virtual. Se tiver fornecedores existentes para qualquer uma destes recursos, pode utilizar essas fornecedores em vez disso.

Se precisar da versão clássica neste tópico, consulte o artigo [aprovisionar uma máquina de virtual do SQL Server utilizando Azure PowerShell clássica](virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial terá de:

- Uma conta Azure e de subscrição antes de começar. Se não tiver uma, inscrever-se para uma [versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Azure PowerShell)](../powershell-install-configure.md), versão mínima do 1.4.0 ou posterior (Este tutorial escrito com a versão 1.5.0).
    - Para obter a sua versão, escreva **ListAvailable Get-módulo Azure**.

## <a name="configure-your-subscription"></a>Configurar a sua subscrição

Abra o Windows PowerShell e estabelecer acesso à sua conta Azure ao executar o cmdlet seguinte. Será apresentada com no ecrã Iniciar sessão para introduzir as suas credenciais. Utilize o e-mail e palavra-passe que utiliza para iniciar sessão portal do Azure mesmo.

    Add-AzureRmAccount

Depois de iniciar sessão com êxito verá algumas informações no ecrã que inclui SubscriptionId com o qual iniciou sessão. Esta é a subscrição na qual os recursos para este tutorial serão criados, a menos que altere para uma subscrição diferente. Se tiver várias SubscriptionIds, execute o cmdlet seguinte para devolver uma lista de todos os seus SubscriptionIds:

    Get-AzureRmSubscription

Para alterar para outra SubscriptionID, execute o cmdlet seguinte com o seu SubscriptionId pretendida.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Definir as variáveis de imagem

Para simplificar a textuais e compreensão do script concluído a partir deste tutorial, vamos será iniciado através da definição de um número de variáveis. Altere os valores de parâmetros como consulte o artigo ajustar, mas tenha cuidado com naming restrições relacionados com comprimentos de nome e carateres especiais ao modificar os valores fornecidos.

### <a name="location-and-resource-group"></a>Localização e o grupo de recursos
Utilize duas variáveis para definir a região de dados e o grupo de recursos no qual irá criar os outros recursos para a máquina virtual.

Modificar a conforme pretendido e, em seguida, execute os seguintes cmdlets para inicialização estas variáveis.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Propriedades de armazenamento

Utilize as seguintes variáveis para definir a conta de armazenamento e o tipo de armazenamento para ser utilizado por máquina virtual.

Modificar a conforme pretendido e, em seguida, execute o cmdlet seguinte a inicialização estas variáveis. Tenha em atenção que neste exemplo, estamos a utilizar o [Armazenamento de Premium](../storage/storage-premium-storage.md), que seja recomendado para os cargas de trabalho de produção. Para obter detalhes sobre estas orientações e outras recomendações, consulte o artigo [melhores práticas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Propriedades de rede

Utilize as seguintes variáveis para definir a interface de rede, o método de alocação TCP/IP, o nome de rede virtual, o nome de sub-rede virtual, o intervalo de endereços IP para a rede virtual, o intervalo de endereços IP para a sub-rede e a etiqueta de nome de domínio público para ser utilizado por rede na máquina virtual.

Modificar a conforme pretendido e, em seguida, execute o cmdlet seguinte a inicialização estas variáveis.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>Propriedades de máquina virtual

Utilize as seguintes variáveis para definir o nome de máquina virtual, o nome do computador, o tamanho de máquina virtual e o nome do disco sistema operativo para a máquina virtual.

Modificar a conforme pretendido e, em seguida, execute o cmdlet seguinte a inicialização estas variáveis.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Propriedades da imagem

Utilize as seguintes variáveis para definir a imagem para utilizar para a máquina virtual. Neste exemplo, é utilizada a imagem do SQL Server 2016 Enterprise.

Modificar a conforme pretendido e, em seguida, execute o cmdlet seguinte a inicialização estas variáveis.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Tenha em atenção que pode obter uma lista completa de ofertas de imagem do SQL Server com o comando Get-AzureRmVMImageOffer:

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

E pode ver as Skus disponíveis para uma oferta com o comando Get-AzureRmVMImageSku. O comando seguinte mostra que todos os Skus disponíveis para o **SQL2014SP1 WS2012R2** oferecer.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Com o modelo de implementação do Gestor de recursos, no primeiro objeto que criar é o grupo de recursos. Vamos irá utilizar o cmdlet [AzureRmResourceGroup novo](https://msdn.microsoft.com/library/mt759837.aspx) para criar um grupo de recursos Azure e os seus recursos com o nome do grupo de recursos e a localização definidas pelas variáveis que inicializado anteriormente.

Execute o cmdlet seguinte para criar o novo grupo de recursos.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A máquina virtual requer recursos de armazenamento para o disco do sistema operativo e para os ficheiros de dados e de registo do SQL Server. Para simplificar, podemos irá criar um único disco para ambos. Pode anexar discos adicionais mais tarde, utilizando o cmdlet [Adicionar Azure disco](https://msdn.microsoft.com/library/azure/dn495252.aspx) para colocar dados do SQL Server e os ficheiros em discos dedicados de registo. Vamos irá utilizar o cmdlet [AzureRmStorageAccount novo](https://msdn.microsoft.com/library/mt607148.aspx) para criar uma conta de armazenamento padrão no seu novo grupo de recursos e com o nome da conta de armazenamento, o nome do Sku de armazenamento e a localização definido utilizando as variáveis que inicializado anteriormente.

Execute o cmdlet seguinte para criar a sua nova conta de armazenamento.  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Criar recursos de rede

A máquina virtual requer um número de recursos de rede para a conectividade da rede.

- Cada máquina virtual necessita de uma rede virtual.
- Uma rede virtual tem de ter pelo menos uma sub-rede definida.
- Uma interface de rede tem de ser definida com um público ou um endereço IP privado.

### <a name="create-a-virtual-network-subnet-configuration"></a>Criar uma configuração de sub-rede rede virtual

Vamos será iniciado através da criação de uma configuração de sub-rede para os nossos rede virtual. Para os nossos tutorial, criamos uma sub-rede predefinido utilizando o cmdlet [AzureRmVirtualNetworkSubnetConfig novo](https://msdn.microsoft.com/library/mt619412.aspx) . Vamos irá criar os nossos configuração de sub-rede rede virtual com o prefixo de nome e endereço de sub-rede definido utilizando as variáveis que inicializado anteriormente.

>[AZURE.NOTE] Pode definir propriedades adicionais da configuração sub-rede rede virtual utilizando este cmdlet, mas que está fora do âmbito deste tutorial.

Execute o cmdlet seguinte para criar a sua configuração sub-rede virtual.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Em seguida, vamos criar nosso rede virtual utilizando o cmdlet [AzureRmVirtualNetwork novo](https://msdn.microsoft.com/library/mt603657.aspx) . Vamos criar nossa virtual rede no seu novo grupo de recursos, com o nome, localização e endereço o prefixo definido utilizar as variáveis que inicializado anteriormente e utilizar a configuração de sub-rede que definiu no passo anterior.

Execute o cmdlet seguinte para criar a sua rede virtual.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

Agora que temos nosso rede virtual definido, precisamos de configurar um endereço IP para conetividade na máquina virtual. Para este tutorial, criamos um endereço IP público utilizando IP dinâmico endereçar para suportar a ligação à Internet. Vamos irá utilizar o cmdlet [AzureRmPublicIpAddress novo](https://msdn.microsoft.com/library/mt603620.aspx) para criar o endereço IP público, no grupo de recursos criado prevously e com o nome, localização, método de alocação e etiqueta de nome de domínio DNS definido utilizando as variáveis que inicializado anteriormente.

>[AZURE.NOTE] Pode definir propriedades adicionais do endereço IP público utilizando este cmdlet, mas que está fora do âmbito deste tutorial inicial. Também pode criar um endereço privado ou um endereço com um endereço estático, mas que também é para além do âmbito deste tutorial.

Execute o cmdlet seguinte para criar o seu endereço IP público.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Criar a interface de rede

Vamos agora está prontos criar a interface de rede que irá utilizar os nossos máquina virtual. Vamos irá utilizar o cmdlet [AzureRmNetworkInterface novo](https://msdn.microsoft.com/library/mt619370.aspx) para criar os nossos interface da rede no grupo de recursos criado anterior e com o nome, localização, sub-rede e endereço IP público definido anteriormente.

Execute o cmdlet seguinte para criar a interface de rede.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Configurar um objecto VM

Agora que temos recursos de armazenamento e de rede definidos, podemos está prontos para definir cluster de recursos para a máquina virtual. Para os nossos tutorial, vamos irá especificar o tamanho de máquina virtual e várias propriedades do sistema operativo, especifique a interface de rede que recomendamos criados anteriormente, definir armazenamento de BLOBs e, em seguida, especifique o disco do sistema operativo.

### <a name="create-the-vm-object"></a>Criar o objeto VM

Vamos será iniciado ao especificar o tamanho de máquina virtual. Para este tutorial, vamos está a especificar um DS13. Vamos irá utilizar o cmdlet [AzureRmVMConfig novo](https://msdn.microsoft.com/library/mt603727.aspx) para criar um objeto de máquina virtual configuráveis com o nome e tamanho definido utilizando as variáveis que inicializado anteriormente.

Execute o cmdlet seguinte para criar o objeto de máquina virtual.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Criar um objeto de credenciais para colocar em espera o nome e palavra-passe para as credenciais de administrador local

Antes de podemos pode definir as propriedades do sistema operativo para a máquina virtual, precisamos de fornecer as credenciais para a conta de administrador local como uma cadeia segura. Para realizar esta tarefa, utilizamos o cmdlet [Get-credenciais](https://technet.microsoft.com/library/hh849815.aspx) .

Executar o cmdlet seguinte e, na janela de pedido de credenciais do Windows PowerShell, escreva o nome e palavra-passe para utilizar para a conta de administrador local na máquina de virtual do Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Definir as propriedades do sistema operativo para a máquina virtual

Agora podemos está prontos definir propriedades do sistema operativo a máquina virtual. Vamos irá utilizar o cmdlet [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) para predefinir o tipo de sistema operativo Windows, exigir o [agente de máquina virtual](virtual-machines-windows-classic-agents-and-extensions.md) para ser instalado, especifique o cmdlet permite a atualização automática e definir o nome de máquina virtual, o nome do computador e a credencial utilizando as variáveis que inicializado anteriormente.

Execute o cmdlet seguinte para definir as propriedades do sistema operativo do seu máquina virtual.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adicionar a interface de rede à máquina virtual

Em seguida, vamos adicionar a interface de rede que criámos anteriormente à máquina virtual. Vamos irá utilizar o cmdlet [AzureRmVMNetworkInterface adicionar](https://msdn.microsoft.com/library/mt619351.aspx) para adicionar a interface de rede utilizando a variável de interface de rede que definiu anteriormente.

Execute o cmdlet seguinte para definir a interface de rede para a sua máquina virtual.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Definir a localização de armazenamento de Blobs do disco ser utilizado por máquina virtual

Em seguida, podemos irá definir a localização de armazenamento de Blobs do disco ser utilizado por máquina virtual utilizando as variáveis que definiu anteriormente.

Execute o cmdlet seguinte para definir a localização de armazenamento de Blobs.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Definir o sistema operativo propriedades do disco para a máquina virtual

Em seguida, podemos definirá o sistema operativo propriedades do disco para a máquina virtual. Vamos irá utilizar o cmdlet [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) para especificar que o sistema operativo para a máquina virtual será provenientes de uma imagem, para definir a colocação em cache para ler apenas (porque o SQL Server está a ser instalado no mesmo disco) e definir o nome de máquina virtual e o disco de sistema operativo definido utilizando as variáveis que definido anteriormente.

Execute o cmdlet seguinte para definir o sistema operativo propriedades do disco para a sua máquina virtual.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especifique a imagem de plataforma para a máquina virtual

É nosso último passo de configuração especificar a imagem de plataforma para os nossos máquina virtual. Para os nossos tutorial, estamos a utilizar a imagem mais recente do SQL Server 2016 CTP. Vamos irá utilizar o cmdlet [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) para utilizar esta imagem conforme definido pelas variáveis que definiu anteriormente.

Execute o cmdlet seguinte para especificar a imagem de plataforma para o seu computador virtual.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Criar a VM de SQL

Agora que tiver concluído os passos de configuração, está pronto para criar a máquina virtual. Vamos irá utilizar o cmdlet [AzureRmVM novo](https://msdn.microsoft.com/library/mt603754.aspx) para criar a máquina virtual utilizando as variáveis que tenha definido.

Execute o cmdlet seguinte para criar o seu máquina virtual.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

É criada a máquina virtual. Repare que é criada uma conta de armazenamento padrão para diagnósticos de arranque, porque a conta de armazenamento especificada para disco a máquina virtual é uma conta de armazenamento premium.

Agora pode ver este computador no Portal do Azure para ver o [seu endereço IP público e o seu nome de domínio completamente qualificado](virtual-machines-windows-portal-sql-server-provision.md#Connect).  

## <a name="example-script"></a>Exemplo de script

O seguinte script contém o script do PowerShell completo para este tutorial. Parte do princípio de que já tiver o programa de configuração a subscrição Azure para utilizar com os comandos **AzureRmAccount adicionar** e **Selecione AzureRmSubscription** .


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Próximos passos
Quando a máquina virtual estiver criada, está pronto para se ligar à máquina virtual utilizando conectividade RDP e o programa de configuração. Para mais informações, consulte o artigo [ligar a uma SQL Server Máquina Virtual no Azure (Gestor de recurso)](virtual-machines-windows-sql-connect.md).
