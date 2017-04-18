<properties
    pageTitle="Criar um VM Azure através do PowerShell | Microsoft Azure"
    description="Utilizar o PowerShell do Azure e Gestor de recursos do Azure para criar uma nova VM executar o Windows Server facilmente."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Criar uma VM Windows utilizando o Gestor de recursos e PowerShell

Este artigo mostra-lhe como criar rapidamente uma máquina de Virtual Azure que executem o Windows Server e os recursos que necessita utilizando [O Gestor de recursos](../azure-resource-manager/resource-group-overview.md) e PowerShell. 

Todos os passos neste artigo são necessários para criar uma máquina virtual e deve demorar cerca de 30 minutos a execute os passos. Substitua valores de parâmetros de exemplo na comandos com nomes que façam sentido para o seu ambiente.

## <a name="step-1-install-azure-powershell"></a>Passo 1: Instalar o Azure PowerShell

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a sua subscrição e iniciar sessão na sua conta.
        
## <a name="step-2-create-a-resource-group"></a>Passo 2: Criar um grupo de recursos

Todos os recursos têm de estar contidos num grupo de recursos, por isso, vamos criar primeiro lugar.  

1. Obter uma lista das localizações disponíveis onde os recursos podem ser criados.

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Defina a localização para os recursos. Este comando define a localização para **centralus**.

    ```powershell
    $location = "centralus"
    ```
    
3. Crie um grupo de recursos. Este comando cria o grupo de recursos com o nome **myResourceGroup** na localização definida por si.

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>Passo 3: Criar uma conta de armazenamento

É necessária uma [conta de armazenamento](../storage/storage-introduction.md) para armazenar o disco rígido virtual que é utilizado pela máquina virtual que criar. Nomes de conta de armazenamento tem de estar entre 3 e 24 carateres de comprimento e podem conter números e letras em minúsculas apenas.

1. Teste o nome da conta de armazenamento para a exclusividade. Este comando testa o nome **myStorageAccount**.

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    Se este comando devolver **Verdadeiro**, o seu nome de proposta é exclusivo Azure. 
    
2. Agora, crie a conta de armazenamento.
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>Passo 4: Criar uma rede virtual

Todas as máquinas virtuais fazem parte de uma [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie uma sub-rede para a rede virtual. Este comando cria uma sub-rede denominada **mySubnet** com um prefixo de 10.0.0.0/24 endereço.
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. Agora, crie a rede virtual. Este comando cria uma rede virtual denominada **myVnet** utilizando a sub-rede que criou e um prefixo de **10.0.0.0/16**endereço.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Passo 5: Criar uma interface de endereço e de rede IP pública

Para permitir a comunicação com máquina virtual na rede virtual, necessita de um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o endereço IP público. Este comando cria um endereço IP público com o nome **myPublicIp** com um método de alocação de **dinâmicos**.
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. Crie a interface de rede. Este comando cria uma interface de rede com o nome **myNIC**.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>Passo 6: Criar uma máquina virtual

Agora que tem todas as peças no local, é altura de criar a máquina virtual.

1. Execute este comando para definir o nome de conta de administrador e a palavra-passe para a máquina virtual.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    A palavra-passe tem de estar 12-123 carateres de comprimento e ter pelo menos uma minúsculas caráter, um caráter de maiúsculas, um número e um caráter especial. 
        
2. Crie o objeto de configuração para a máquina virtual. Este comando cria um objeto de configuração denominado **myVmConfig** que define o nome da VM e o tamanho da VM.

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    Consulte o artigo [tamanhos para máquinas virtuais no Azure](virtual-machines-windows-sizes.md) para uma lista de tamanhos disponíveis para uma máquina virtual.
    
3. Configure definições do sistema operativo para a VM. Este comando define o nome do computador, tipo de sistema operativo e as credenciais de conta para a VM.

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. Defina a imagem para a utilizar para aprovisionar a VM. Este comando define a imagem do Windows Server para utilizar para a VM. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    Para mais informações acerca da seleção de imagens a utilizar, consulte o artigo [navegar e selecionadas imagens de máquina virtual do Windows no Azure com PowerShell ou o clip](virtual-machines-windows-cli-ps-findimage.md).
        
5. Adicione a interface de rede que criou para a configuração.

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. Defina o nome e localização do disco rígido VM. O ficheiro de disco rígido virtual está armazenado num contentor. Este comando cria o disco num contentor **vhds/WindowsVMosDisk.vhd** na conta de armazenamento que criou com o nome.

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. Adicione as informações do sistema operativo disco na configuração VM. Substitua o valor de **$diskName** com um nome para o disco de sistema operativo. Crie a variável e adicione as informações do disco na configuração.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. Por fim, crie a máquina virtual.

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>Próximos passos

- Se havia problemas com a implementação, um passo seguinte seria consultá [implementações de grupo de recursos de resolução de problemas com o Azure portal](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerir a máquina virtual que criou ao rever [máquinas virtuais de gerir utilizando o Gestor de recursos do Azure e PowerShell](virtual-machines-windows-ps-manage.md).
- Tirar partido das utilizando um modelo para criar uma máquina virtual utilizando as informações no [artigo criar uma máquina de virtual do Windows com um modelo de Gestor de recursos](virtual-machines-windows-ps-template.md)
