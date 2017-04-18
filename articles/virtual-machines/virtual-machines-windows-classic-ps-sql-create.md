<properties
    pageTitle="Criar uma máquina de Virtual do SQL Server no Azure PowerShell (clássico) | Microsoft Azure"
    description="Fornece passos e scripts de PowerShell para criar um VM Azure com imagens da Galeria de máquina virtual do SQL Server. Este tópico utiliza o modo clássico de implementação."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Aprovisionar uma máquina de virtual do SQL Server através do PowerShell Azure (clássico)

## <a name="overview"></a>Descrição geral

Este artigo fornece passos para saber como criar uma máquina de virtual do SQL Server no Azure utilizando os cmdlets do PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para a versão do Gestor de recursos neste tópico, consulte o artigo [aprovisionar uma máquina de virtual do SQL Server utilizando o Gestor de recursos do Azure PowerShell](virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Instalar e configurar o PowerShell:

1. Se não possui uma conta Azure, visite o [Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

2. [Transfira e instale os comandos do Azure PowerShell mais recentes](../powershell-install-configure.md).

3. Inicie o Windows PowerShell e ligá-lo à sua subscrição do Azure com o comando **Adicionar AzureAccount** .

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>Determinar o Azure região de destino

O SQL Server Virtual Machine irá estar alojada num serviço na nuvem que reside uma região Azure específica. Os passos seguintes ajudá-lo a determinar a sua conta de armazenamento, região e na nuvem serviço que será utilizado para o resto do tutorial.

1. Determine o Centro de dados que pretende utilizar para alojar o seu VM de servidor de SQL. Os seguintes comandos do PowerShell irão apresentar as regiões disponíveis em detalhe com uma lista de resumo no final.

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  Uma vez que identificou a sua localização preferida, defina uma variável denominada **$dcLocation** para essa região.

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>Configurar a sua conta de armazenamento e de subscrição

1. Determine a subscrição Azure que irá utilizar para a nova máquina virtual.

        (Get-AzureSubscription).SubscriptionName

1. Atribua Azure subscrição de destino para a variável de **$subscr** . Em seguida, defina esta opção como a sua subscrição atual Azure.

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. Em seguida, selecione para contas de armazenamento existente. O script seguinte apresenta todas as contas de armazenamento que existam na sua região que selecionou:

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] Se necessitar de uma nova conta de armazenamento, de criar primeiro um nome de conta de armazenamento de todas as minúsculas com o comando novo AzureStorageAccount tal como no seguinte exemplo: **Novo AzureStorageAccount - StorageAccountName "<storage account name>"-localização $dcLocation**

1. Atribua o nome de conta de armazenamento de destino para a **$staccount**. Em seguida, utilize **AzureSubscription conjunto** para definir a subscrição e a conta de armazenamento atual.

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>Seleccionar uma imagem de máquina virtual do SQL Server

1. Descubra a lista de imagens de máquinas virtuais do SQL Server disponíveis a partir da galeria. Estas imagens de todos os tem uma propriedade de **ImageFamily** que começa por "SQL". A seguinte consulta apresenta a família imagem disponível para si que tiver o SQL Server pré-instalado.

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Quando encontrar a família de imagem de máquina virtual, podem existir várias imagens publicadas nesta família. Utilize o seguinte script para encontrar o nome da imagem publicados máquina virtual mais recente para a sua família imagem selecionada (como **SQL Server 2016 RTM Enterprise no Windows Server 2012 R2**):

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Por fim, crie a máquina virtual com PowerShell:

1. Crie um serviço na nuvem para alojar o VM novo. Tenha em atenção que também é possível utilizar em vez disso, um serviço de nuvem existente. Crie uma nova variável **$svcname** com o nome abreviado do serviço em nuvem.

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Especifique o nome de máquina virtual e um tamanho. Para mais informações sobre os tamanhos de máquina virtual, consulte o artigo [Tamanhos de Máquina Virtual para Azure](virtual-machines-windows-sizes.md).

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Especificar a conta de administrador local e a palavra-passe.

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Execute o seguinte script para criar a máquina virtual.

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Para explicação adicional e opções de configuração, consulte a secção [Utilizar o PowerShell Azure para criar e pré-configurar baseados no Windows máquinas virtuais](virtual-machines-windows-classic-create-powershell.md) **crie o seu conjunto de comandos** .

## <a name="example-powershell-script"></a>Exemplo de script PowerShell

Fornece o seguinte script e exemplo de um script concluído que cria uma máquina de virtual do **SQL Server 2016 RTM Enterprise no Windows Server 2012 R2** . Se utilizar este script, tem de personalizar as variáveis iniciais com base nos passos anteriores neste tópico.

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>Ligar-se ao ambiente de trabalho remoto

1. Criar a. Ficheiros RDP na pasta de documentos do utilizador actual à iniciação estes máquinas virtuais para concluir a configuração:

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. No directório de documentos, inicie o ficheiro RDP. Ligar-se com o nome de utilizador do administrador e a palavra-passe fornecida anterior (por exemplo, se o seu nome de utilizador foi VMAdmin, especificar "\VMAdmin" como o utilizador e fornecer a palavra-passe).

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Concluir a configuração do SQL Server máquina de acesso remoto

Depois de iniciar sessão no computador com o ambiente de trabalho remoto, configure o SQL Server com base nas instruções nos [passos para configurar a conectividade do SQL Server numa VM Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Próximos passos

Pode encontrar instruções adicionais para aprovisionar máquinas virtuais com PowerShell na [documentação máquinas virtuais](virtual-machines-windows-classic-create-powershell.md). Scripts adicionais relacionados com o SQL Server e Premium armazenamento, consulte o artigo [Utilizar Azure Premium armazenamento com o SQL Server em máquinas virtuais](virtual-machines-windows-classic-sql-server-premium-storage.md).

Em muitos casos, o passo seguinte é migrar as bases de dados para este novo VM de servidor de SQL. Para obter orientações de migração de base de dados, consulte o artigo [migrar uma base de dados para o SQL Server numa VM Azure](virtual-machines-windows-migrate-sql.md).

Se também estiver interessado em através do portal Azure para criar máquinas virtuais de SQL, consulte o artigo [aprovisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md). Note que o tutorial que orienta-o através do portal cria VMs utilizando o modelo de Gestor de recursos recomendado, em vez do modelo clássico de utilizado neste tópico PowerShell.

Para além destes recursos, recomendamos que reveja [outros tópicos relacionados com a executar o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
