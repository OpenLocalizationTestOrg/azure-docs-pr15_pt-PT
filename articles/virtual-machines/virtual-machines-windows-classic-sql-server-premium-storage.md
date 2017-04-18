<properties
    pageTitle="Utilizar o armazenamento do Azure Premium com o SQL Server | Microsoft Azure"
    description="Este artigo utiliza recursos criados com o modelo clássico de implementação e dá orientação sobre como utilizar o armazenamento do Windows Azure Premium com o SQL Server em máquinas virtuais do Azure de executar."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilizar o armazenamento do Azure Premium com o SQL Server em máquinas virtuais


## <a name="overview"></a>Descrição geral

[Armazenamento do Windows Azure Premium](../storage/storage-premium-storage.md) é a próxima geração de armazenamento que fornece latência baixa e o alto débito es. Funciona melhor para chaves IO intensivos das cargas de trabalho, como SQL Server no IaaS [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Este artigo fornece planeamento e orientação para migrar uma máquina de Virtual com o SQL Server para utilizar o armazenamento de Premium. Isto inclui infraestrutura Azure (armazenamento de rede,) e os passos do Windows VM de convidado. O exemplo no [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) mostra uma migração de ponto a ponto completa de como mover VMs maiores para tirar partido das melhorada armazenamento SSD local com o PowerShell.

É importante compreender o processo de ponto a ponto de recorrendo armazenamento do Windows Azure Premium com o SQL Server no IAAS VMs. Isto inclui:

- Identificação das pré-requisitos para utilizar o armazenamento de Premium.
- Exemplos de implementação do SQL Server no IaaS ao armazenamento de Premium para implementações novas.
- Exemplos de migrar implementações existentes, servidores autónomos e implementações utilizar SQL sempre no disponibilidade grupos.
- Abordagens de migração possíveis.
- Exemplo de completa ponto a ponto com passos Azure, Windows e do SQL Server para a migração de uma implementação sempre no existente.

Para mais informações de fundo no SQL Server em máquinas virtuais do Azure, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Diogo Sol **Revisores técnicos:** Luís Carlos Martins arenque, Sanjay Mishra, Pravin Mital, Juergen Ferreira, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para o armazenamento do Premium

Existem várias pré-requisitos para utilizar o armazenamento de Premium.

### <a name="machine-size"></a>Tamanho de máquina

Para utilizar o armazenamento de Premium terá de utilizar DS série máquinas virtuais (VM). Se não tiver utilizado máquinas DS série no seu serviço de nuvem antes, tem eliminar a VM existente, manter os discos anexados e, em seguida, crie um novo serviço de nuvem antes de recriar a VM como tamanho do papel DS *. Para mais informações sobre tamanhos de Máquina Virtual, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure](virtual-machines-linux-sizes.md).

### <a name="cloud-services"></a>Serviços em nuvem

Só pode utilizar DS * VMs com armazenamento Premium quando são criadas num novo serviço de nuvem. Se estiver a utilizar SQL Server sempre no Azure, a sempre no escuta referir-se para o endereço IP do Balanceador de carga externo ou internos Azure que está associado a um serviço na nuvem. Este artigo foca-se em como migrar mantendo disponibilidade neste cenário.

> [AZURE.NOTE] Uma série DS * tem de ser a primeira VM que é implementada para o novo serviço na nuvem.

### <a name="regional-vnets"></a>VNETS regionais

Para DS * VMs tem de configurar o Virtual rede (VNET) que aloja a sua VMs para ser regionais. Isto "alarga" a VNET é permitir que o maiores VMs ser aprovisionada outros clusters e permitir a comunicação entre elas. Na captura de ecrã seguinte, a localização realçada mostra VNETs regionais, Considerando que o primeiro resultado mostra um VNET "estreita".

![RegionalVNET][1]

Pode aumentar uma bilhetes de suporte da Microsoft para migrar para um VNET regional, Microsoft irá efetuar uma alteração, em seguida, para concluir a migração para VNETs regionais, altere a propriedade AffinityGroup na configuração de rede. Exportar primeiro a configuração de rede no PowerShell e, em seguida, substitua a propriedade **AffinityGroup** no elemento de **VirtualNetworkSite** uma propriedade de **localização** . Especificar `Location = XXXX` onde `XXXX` é uma região Azure. Em seguida, importe a configuração da nova.

Por exemplo, considerar a configuração de VNET seguinte:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Para mover esta para um VNET regional da Europa Ocidental, altere a configuração para o seguinte:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Contas de armazenamento

Terá de criar uma nova conta de armazenamento está configurada para o armazenamento do Premium. Tenha em atenção que a utilização de armazenamento de Premium é definida a conta de armazenamento, não no VHDs individuais, no entanto, quando utiliza uma VM série DS * pode anexar do VHD a partir de contas Premium e armazenamento padrão. Poderá considerar tal se não pretende colocar o VHD SO para a conta de armazenamento de Premium.

O seguinte comando **Novo AzureStorageAccountPowerShell** com o **tipo** de "Premium_LRS" cria uma conta de armazenamento Premium:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Definições de Cache VHDs

A diferença entre a criação de discos que fazem parte de uma conta de armazenamento de Premium principal é a definição de cache de disco. Para discos de volume de dados do SQL Server é recomendável que utilize '**Colocação em cache de leitura**'. Para volumes de registo da transação, a definição de cache de disco deve ser definida '**nenhum**'. Este é diferente das recomendações para contas de armazenamento padrão.

Depois de tem sido anexados os VHDs, a definição de cache não pode ser alterada. Que precisa para desligar e voltar a ligar o VHD com uma definição de cache atualizado.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Pode utilizar o [Windows armazenamento espaços](https://technet.microsoft.com/library/hh831739.aspx) como fez com anterior armazenamento padrão, isto irá permitir-lhe migrar uma VM que já está a utilizar o armazenamento espaços. O exemplo no [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (passo 9 e reencaminhar) demonstra o código do Powershell para extrair e importar uma VM com vários VHDs anexados.

Agrupamentos de armazenamento eram utilizados com a conta de armazenamento do Azure padrão para melhorar a débito e reduzir a latência. Poderá encontrar o valor no testes conjuntos de armazenamento de dados com o armazenamento de Premium para implementações novas, mas adicionam complexidade adicional com a configuração de armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como encontrar mapa que discos virtuais Azure para conjuntos de armazenamento

À medida que existem recomendações de definição de cache diferentes para VHDs anexados, pode decidir copiar os VHDs para uma conta de armazenamento de Premium. No entanto, quando voltar a ligá-las para a nova série de DS VM, poderá ter de alterar as definições de cache. É mais simples aplicar o armazenamento de Premium recomendado definições de cache quando tiver VHDs separados para os ficheiros de dados do SQL e ficheiros de registo (em vez de um único VHD que contém ambas).

> [AZURE.NOTE] Se tiver ficheiros de dados e de registo do SQL Server no mesmo volume, a opção de colocação em cache que escolher depende os padrões de acesso de es para sua cargas de trabalho de base de dados. Apenas testes podem demonstram qual a opção de colocação em cache o melhor para este cenário.

No entanto, se estiver a utilizar espaços de armazenamento do Windows que são constituídos por vários VHDs terá observe os scripts originais para identificar qual anexado VHDs são na qual o conjunto específico, por isso, pode, em seguida, definir as definições de cache, consoante adequado para cada disco.

Se não tiver script original disponível para mostrar-lhe que mapeiam VHDs ao agrupamento de armazenamento, pode utilizar os seguintes passos para determinar o mapeamento de agrupamento de armazenamento em/disco.

Para cada disco, utilize os seguintes passos:

1. Obter a lista de discos anexados a VM com o comando **Get-AzureVM** :

    Get AzureVM - ServiceName <servicename> -nome <vmname> | Get-AzureDataDisk

1. Tenha em atenção Diskname e LUN.

    ![DisknameAndLUN][2]

1. Ambiente de trabalho remoto para a VM. Em seguida, aceda a **Gestão de computador** | **Gestor de dispositivos** | **unidades de disco**. Observe as propriedades de cada um dos 'Microsoft Virtual discos'

    ![VirtualDiskProperties][3]

1. O número LUN aqui é uma referência para o número LUN que especificar quando anexar o VHD para a VM.
1. Para o 'Microsoft disco Virtual' aceda ao separador **Detalhes** , em seguida, na lista de **Propriedades** , vá para **o controlador de chave.** O **valor**, tenha em atenção **deslocamento**, que é 0002 na captura de ecrã seguinte. O 0002 indica o PhysicalDisk2 que referencia o agrupamento de armazenamento.

    ![VirtualDiskPropertyDetails][4]

2. Para cada agrupamento de armazenamento de informação saída discos associados:

    Get StoragePool - FriendlyName AMS1pooldata | Get-físico

    ![GetStoragePool][5]

Agora, pode utilizar estas informações para associar anexados VHDs a discos físicos na agrupamentos de armazenamento.

Depois de ter mapeado VHDs para discos físicos na agrupamentos de armazenamento, em seguida, pode separar e copiá-los sobre a uma conta de armazenamento de Premium, em seguida, anexe-os com a definição de cache correto. Consulte o artigo o exemplo do [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), os passos 8 a 12. Estes passos mostram como extrair uma configuração de disco anexado VM VHD para um ficheiro CSV, copie os VHDs, alterar as definições de cache de configuração do disco e finalmente implementar a VM como uma série de DS VM com todos os discos ligados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM armazenamento da largura de banda e o débito de armazenamento VHD

A quantidade de desempenho de armazenamento depende tamanhos VHD e o tamanho da memória virtual DS * especificado. Os VMs tem diferentes das permissões para o número de VHDs que pode ser anexado e a largura de banda máxima será suporta a (MB/s). Para os números de largura de banda específica, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure](virtual-machines-linux-sizes.md).

Maiores IOPS são obtidos com tamanhos de disco maiores. Deve considerar tal ao refletir acerca do seu caminho de migração. Para obter detalhes, [consulte a tabela para IOPS e tipos de disco](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

Por fim, considere que VMs têm larguras de banda do disco máximo diferente será de suporte para todos os discos anexados. Em caso de carga elevado, poderia saturar a largura de banda máximo de disco disponível para esse tamanho da função memória virtual. Por exemplo um Standard_DS14 irá suportar até 512 MB/s; Por conseguinte, com três P30 discos poderia saturar a largura de banda do disco da VM. Mas, neste exemplo, o limite de débito pode ser excedido consoante a mistura de leitura e escrita IOs.

## <a name="new-deployments"></a>Novas híbridas

As duas secções seguintes demonstram como implementar o SQL Server VMs ao armazenamento de Premium. Tal como mencionado antes, não seja necessariamente tem de colocar o disco SO para armazenamento Premium. Pode escolher efetuar esta ação se estiver a planeia colocar qualquer cargas de trabalho IO intensivos no VHD SO.

O exemplo primeiro demonstra recorrendo imagens da Galeria Azure existente. O segundo exemplo mostra como utilizar uma imagem personalizada do VM que tiver uma conta existente do armazenamento padrão.

> [AZURE.NOTE] Estes exemplos partem do pressuposto de que já criou um VNET regionais.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Criar uma nova VM com armazenamento Premium com imagem de galeria

O exemplo abaixo mostra como colocar o VHD SO para armazenamento premium e anexar Premium armazenamento VHDs. No entanto, pode também colocar o disco SO numa conta armazenamento padrão e, em seguida, anexar VHDs que residem numa conta de armazenamento Premium. Ambos os cenários são demonstrados.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Passo 1: Criar uma conta de armazenamento Premium


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Passo 2: Criar um novo serviço em nuvem

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Passo 3: Reservar um VIP do serviço de nuvem (opcional)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Passo 4: Criar um contentor VM
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Passo 5: Colocar o sistema operativo VHD na padrão ou armazenamento Premium
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Passo 6: Criar VM
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Criar uma nova VM para utilizar o armazenamento de Premium com uma imagem personalizada

Este cenário demonstra onde tem imagens personalizadas existentes que residem numa conta armazenamento padrão. Tal como mencionado se pretende colocar o VHD SO Premium armazenamento terá de copiar a imagem que existe na conta de armazenamento padrão e transferi-los ao armazenamento de Premium antes de poder ser utilizada. Se tiver uma imagem no local, também pode utilizar este método para copiar que diretamente para a conta de armazenamento de Premium.

#### <a name="step-1-create-storage-account"></a>Passo 1: Criar a conta de armazenamento
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Passo 2 criar serviço na nuvem
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Passo 3: Utilizar a imagem existente
Pode utilizar uma imagem existente. Em alternativa, pode [tirar uma imagem de uma máquina existente](virtual-machines-windows-classic-capture-image.md). Tenha em atenção máquina imagem não tem de ser DS* máquina. Assim que tiver a imagem, os seguintes passos mostram como copiá-lo para a conta de armazenamento de Premium com o * *Iniciar AzureStorageBlobCopy** PowerShell commandlet.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Passo 4: Copiar Blob entre contas de armazenamento
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Passo 5: Verificar regularmente copiar Estado:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Passo 6: Adicionar o disco de imagens ao Azure disco repositório na subscrição
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] Poderá constatar que, apesar dos relatórios de estado como sucesso, ainda pode obter um erro de locação financeira disco. Neste caso, aguarde cerca de 10 minutos.

#### <a name="step-7--build-the-vm"></a>Passo 7: Criar a VM
Aqui está a criar a VM a partir da imagem e como anexar duas VHDs de armazenamento de Premium:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implementações existentes que não utilizam sempre no grupos de disponibilidade

> [AZURE.NOTE] Para implementações existentes, consulte pela primeira vez a secção de [Pré-requisitos](#prerequisites-for-premium-storage) deste tópico.

Existem diferentes considerações para implementações do SQL Server que não utilizam o sempre no grupos de disponibilidade e aquelas que efetuar. Se não estiver a utilizar sempre no e tiver um autónomo existente do SQL Server, pode atualizar para o armazenamento de Premium utilizando uma conta nova do serviço e de armazenamento na nuvem. Considere as seguintes opções:

- **Criar uma nova VM de servidor de SQL**. Pode criar uma nova SQL Server VM que utiliza uma conta de armazenamento de Premium, conforme é documentado implementações novo. Em seguida, efetue cópias de segurança e restaurar as bases de dados do utilizador e configuração do SQL Server. A aplicação terá de ser atualizadas para referenciar o SQL Server nova se está a ser acedido interna ou externamente. Que precisa para copiar todos os objetos 'Terminar db' como se estava a fazer uma migração de (SxS) SQL Server lado a lado. Isto inclui objetos como inícios de sessão, certificados e servidores ligados.
- **Migrar uma existente VM de servidor de SQL**. Este procedimento vai requerer demorar a VM do SQL Server offline e, em seguida, transferi-lo para um novo serviço de nuvem, o que inclui todas as suas VHDs anexados a copiar para a conta de armazenamento de Premium. Quando a VM fica online, a aplicação irá referenciar o nome de anfitrião do servidor como antes. Tenha em atenção que o tamanho do disco existente irá afetar as características do desempenho. Por exemplo, um disco 400 GB é arredondado por excesso para um p 20. Se souber que não necessita que o desempenho do disco, poderia de recriar a VM como uma VM de série DS e anexe Premium VHDs de armazenamento da especificação de tamanho/desempenho que exigir. Em seguida, poderia desanexar e voltar a ligar os ficheiros de SQL DB.

> [AZURE.NOTE] Quando copiar discos VHD que deve ter em mente o tamanho, dependendo do tamanho significa que tipo de disco de armazenamento Premium abrange, isto determina a especificação de desempenho do disco. Tamanho do Azure irá arredondar por excesso para o disco mais próximo, para que se tiver um disco 400 GB, isto irá ser arredondado por excesso para um p 20. Dependendo do seu requisitos IO existentes do SO VHD, não poderá ter de migrar isto para uma conta de armazenamento de Premium.

Se o SQL Server é acedido externamente, em seguida, o VIP do serviço de nuvem será alterada. Também terá pontos finais de atualização, ACL e DNS definições.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implementações existentes que utilizar sempre no grupos de disponibilidade

> [AZURE.NOTE] Para implementações existentes, consulte pela primeira vez a secção de [Pré-requisitos](#prerequisites-for-premium-storage) deste tópico.

Inicialmente nesta secção vamos abordar como sempre no interage com Azure de rede. Vamos será, depois, divide migrações na dois cenários: migrações onde pode ser admitido algum tempo de inatividade e migrações onde tem alcançar o tempo de inatividade mínimo.

No local SQL Server sempre no grupos de disponibilidade utilize uma escuta no local que regista um nome DNS virtual juntamente com um endereço de IP que é partilhado entre um ou mais servidores de SQL. Quando os clientes se ligam são encaminhadas através de IP escuta para o SQL Server principal. Este é o servidor que possui o recurso sempre IP nesse momento.

![DeploymentsUseAlways no][6]

No Microsoft Azure que pode ter apenas um endereço IP atribuído a uma imagem na VM, por isso, para poder alcançar a mesma camada de produção como no local, Azure utiliza o endereço IP que está atribuído a balanceadores de carga interna/externa (ILB/ELB). O recurso de IP que é partilhado entre os servidores está definido para o mesmo PI como ILB/ELB. Isto é publicado no DNS e, o tráfego de cliente lhe é transmitido através de ILB/ELB réplicas principal do SQL Server. ILB/ELB sabe que é principal do SQL Server uma vez que utiliza sondas para sonda o recurso sempre IP. No exemplo anterior,-sondas cada nó que tem um ponto final referenciado por ELB/ILB, consoante a que responde é o SQL Server principal.

> [AZURE.NOTE] O ILB ELB são ambos atribuídos e num serviço na nuvem Azure específico, por conseguinte, qualquer migração da nuvem no Azure provavelmente significa que o que irá alterar o período de inquérito do Balanceador de carga.

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrar sempre no implementações que podem permitir que algumas tempo de inatividade

Existem duas estratégias para migrar sempre no implementações para alguns tempo de inatividade permitem que:

1. **Adicionar mais réplicas secundárias a um sempre no Cluster existente**
1. **Migrar para um novo sempre no Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. adicionar mais réplicas secundário a um sempre no Cluster existente

Uma estratégia é adicionar mais secundários ao sempre no disponibilidade de grupo. Tem de adicioná-los para um novo serviço de nuvem e atualizar a escuta com o novo IP do Balanceador de carga.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade:

- Cluster de validação.
- Testar activações pós-falha sempre no novo secundários.

Se estiver a utilizar o Windows agrupamentos de armazenamento dentro da VM para débito de IO superior, em seguida, estes serão colocados offline durante uma validação Cluster completo. O teste de validação é necessário quando adicionar nós ao cluster. O tempo que demora a executar o teste de pode variar, para que deverá testar isto no seu ambiente de teste representante para obter um tempo de aproximado de quanto tempo isto terão.

Deve aprovisionar tempo onde pode executar activação manual e caos testes os nós recentemente adicionados para se certificar de sempre no disponibilidade elevada funciona conforme esperado.

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] Deve parar de todas as instâncias do SQL Server, onde são utilizados os conjuntos de armazenamento antes do será executado validação.
##### <a name="high-level-steps"></a>Passos de alto nível

1. Crie duas novas SQL Servers no novo serviço em nuvem com anexados Premium armazenamento.
1. Copie sobre completos cópias de segurança e restaurar com **NORECOVERY**.
1. Copiar sobre 'fora do utilizador DB' objetos dependentes, tais como os inícios de sessão etc.
1. Criar novas um novo interno carga balanceador (ILB) ou utilize um balanceador de carga externos (ELB) e, em seguida, configurar os pontos finais de balanceamento de carga em ambos os nós de novos.
> [AZURE.NOTE] Verificar a todos os nós têm a configuração de ponto final correta antes de continuar

1. Impedir o acesso de aplicação do utilizador para o SQL Server (se utilizar agrupamentos de armazenamento).
1. Pare o SQL Server motor Services em todos os nós (se utilizar agrupamentos de armazenamento).
1. Adicione novos nós de cluster e executar a validação completa.
1. Depois de validação for bem sucedida, começar a todos os serviços do SQL Server.
1. Registos de transações de cópia de segurança e restaurar as bases de dados de utilizador.
1. Adicionar novos nós para o sempre no grupo de disponibilidade e coloque replicação para **síncrono**.
1. Adicione o recurso de endereço IP do novo nuvem serviço ILB/ELB através do PowerShell para o sempre com base de exemplo de site com várias no [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). No Windows clusters, defina os **possíveis proprietários** do recurso **Endereço IP** para os novos nós antigos. Consulte a secção de 'Adicionar recurso de endereço IP na mesma sub-rede' do [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Activação pós-falha para um dos novos nós.
1. Tornar os nós novos activações pós-falha automática activação pós-falha parceiros e teste.
1. Remova nós originais do grupo de disponibilidade.

##### <a name="advantages"></a>Vantagens

- Os servidores de SQL novo podem ser testado (SQL Server e aplicação) antes de serem adicionadas a sempre no.
- Pode alterar o tamanho da memória virtual e personalizar o armazenamento com os seus requisitos exatos. No entanto, ser vantajoso manter todos os caminhos de ficheiro SQL da mesma.
- Pode controlar quando a transferência das cópias de segurança da base de dados para as réplicas secundário são iniciados. Isto é diferente a partir do Azure **Iniciar AzureStorageBlobCopy** commandlet a utilizar para copiar VHDs, uma vez que é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens
- Ao utilizar agrupamentos de armazenamento do Windows, existe tempo de inatividade Cluster durante a validação de Cluster completo para os novos nós adicionais.
- Consoante a versão do SQL Server e o número existente de réplicas secundários, não poderá adicionar mais réplicas secundárias sem Remover secundários existentes.
- Pode ser muito tempo de transferência de dados SQL enquanto estiver a configurar os secundários.
- Existe custo adicional durante a migração enquanto tiver máquinas novas executados em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migrar para um novo sempre no Cluster

Estratégia outra é criar um novo sempre no Cluster connosco de novos no novo serviço na nuvem e, em seguida, redireccionar clientes para utilizá-la.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

Existe tempo de inatividade quando são transferidas aplicações e os utilizadores para o novo escuta sempre no. O tempo de inatividade depende da:

- O tempo despendido para restaurar cópias de segurança do final da transação registo para bases de dados em servidores de novos.
- O tempo despendido atualizar aplicações de cliente para utilizar escuta sempre no nova.

##### <a name="advantages"></a>Vantagens

- Pode testar o ambiente de produção real, SQL Server, e alterações de compilação do SO.
- Tem a opção para personalizar o armazenamento e potencialmente reduzir o tamanho de VM. Isto poderá resultar em redução de custo.
- Pode atualizar o seu compilação do SQL Server ou versão durante este processo. Também pode atualizar o sistema operativo.
- O anterior sempre no Cluster pode agir como um alvo de anulação sólida.

##### <a name="disadvantages"></a>Desvantagens

- Tem de alterar o nome DNS da escuta se pretender ambas as clusters sempre no ser executado em simultâneo. Esta ação adiciona administração sobrecarga durante a migração, tal como cadeias da aplicação cliente tem refletir o novo nome escuta.
- Tem de implementar um mecanismo de sincronização entre dois ambientes para mantê-las mais próximo possível minimizar os requisitos de sincronização final antes da migração.
- Não existem é adicionado custo durante a migração apesar de ter o novo ambiente em execução.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrar sempre no híbridas para o tempo de inatividade mínimo

Existem duas estratégias para implementações sempre no migrar para o tempo de inatividade mínimo:

1. **Utilizam um secundário existente: único de sites**
1. **Utilizam réplica secundário existente (s): Site com várias**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. utilizam um secundário existente: único de sites

Uma estratégia de tempo de inatividade mínima é tirar uma nuvem existente secundário e removê-lo do serviço em nuvem atual. Em seguida, copie os VHDs à nova conta de armazenamento de Premium e criar a VM no novo serviço em nuvem. Em seguida, atualize a escuta no clusters e activação pós-falha.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

- Existe tempo de inatividade quando atualiza o nó final com o ponto final de balanceamento de carga.
- Poderá ser atrasada restabelecer a ligação seu cliente dependendo da sua configuração de cliente/DNS.
- Existe um tempo de inatividade adicional se optar por ter o grupo sempre Cluster offline trocar os endereços IP. Para evitar esta situação, utilizando uma dependência ou e proprietários possíveis para o recurso de endereço IP adicionado. Consulte a secção de 'Adicionar recurso de endereço IP na mesma sub-rede' do [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Quando pretende que o nó adicionado para partake no como sempre no activação pós-falha Partner, tem de adicionar um ponto final Azure com uma referência para a carga balanceamento de definir. Quando executa o comando **Adicionar AzureEndpoint** para fazer isto, ligações atuais para permanecer abertas, mas novas ligações para a escuta não poderão ser estabelecida até o Balanceador de carga atualizou. Testar este foi visto para últimos 90-120seconds, isto deve ser testado.

##### <a name="advantages"></a>Vantagens

- Sem extra despesas durante a migração.
- Uma migração de uma.
- Complexidade reduzida.
- Permite IOPS maior Premium armazenamento SKUs. Quando os discos são destaquem da VM e copiado para o novo serviço de nuvem, um 3 uma das partes ferramenta pode ser utilizada para aumentar o tamanho VHD, que fornece débitos superiores. Para tamanhos VHD a aumentar, consulte esta [debate fórum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desvantagens

- Existe uma perda temporária de HA e DR durante a migração.
- Como este é uma migração de 1:1, terá de utilizar um tamanho mínimo da memória virtual que irá suportar o seu número de VHDs, pelo que não poderá conseguir downsize seu VMs.
- Este cenário utilizaria commandlet Azure **AzureStorageBlobCopy iniciar** , que é assíncrona. Não existe nenhuma SLA na conclusão de cópia. A hora das cópias varia enquanto Isto depende espera na fila também serão dependem a quantidade de dados para o qual transferir. A hora de cópia aumenta se outro centro de dados Azure que suporta o armazenamento de Premium na região outra vai a transferência. Se tiver apenas 2 nós, considere uma mitigação possível caso a cópia demora mais de testar. Isto pode incluir ideias que se seguem.
    - Adicione um nó de SQL Server 3º temporário para HA antes da migração com indisponibilidade aprovada.
    - Execute a migração fora do Azure manutenção agendada.
    - Certifique-se de que configurou corretamente o quórum cluster.  

##### <a name="high-level-steps"></a>Passos de alto nível

Este documento não demonstram um exemplo de ponto a ponto completo, no entanto, o [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) fornece detalhes que podem ser utilizadas para executar esta.

![MinimalDowntime][8]

- Reunir configuração do disco e remover o nó (não elimine VHDs anexados).
- Criar uma conta de armazenamento de Premium e copiar VHDs da conta de armazenamento padrão
- Criar novo serviço na nuvem e implementar a VM SQL2 desse serviço na nuvem. Crie a VM utilizar copiada VHD SO original e anexar os VHDs copiados.
- Configurar ILB / ELB e adicione os pontos finais.
- Atualize escuta por um:
    - Colocar offline sempre no grupo e atualizar a sempre no escuta com ILB novo / endereço ELB IP.
    - Ou ao adicionar o recurso de endereço IP de novo na nuvem serviço ILB/ELB através do PowerShell para clusters do Windows. Em seguida, defina os possíveis proprietários do recurso de endereço IP ao nó migrados, SQL2 e defina este como dependência ou no nome da rede. Consulte a secção de 'Adicionar recurso de endereço IP na mesma sub-rede' do [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Verifique a configuração de DNS/propagação aos clientes.
- Migrar SQL1 VM e os passos 2 a 4.
- Se utilizar os passos 5ii, em seguida, adicione SQL1 como proprietário de um possíveis para o recurso de endereço IP adicionado
- Teste activações pós-falha.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. utilizam réplica secundário existente (s): Site com várias

Se tiver nós na mais do que um Azure Centro de dados (CC) ou se tiver um ambiente híbrido, em seguida, pode utilizar uma configuração sempre neste ambiente para minimizar o tempo de inatividade.

A abordagem é alterar a sincronização sempre para síncrono para no local ou Cc do Azure secundário e, em seguida, activação em pós-falha ao longo para que o SQL Server. Em seguida, copie os VHDs a uma conta de armazenamento de Premium e implementar o computador para um novo serviço na nuvem. Atualize a escuta e, em seguida, voltar a falhar.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

O tempo de inatividade é composta pelo tempo para activação pós-falha para o Centro de dados e vice-versa alternativo. Depende também a configuração de cliente/DNS e pode ser atrasada restabelecer a ligação seu cliente.
Considere o seguinte exemplo de uma configuração híbrida sempre no:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens

- Pode utilizar infraestrutura existente.
- Tem a opção para anterior à atualização do armazenamento Azure sobre o Centro de dados do DR Azure pela primeira vez.
- Pode ser reconfigurar o armazenamento de DR Azure Cc.
- Existe um mínimo de duas activações pós-falha durante a migração, excluindo activações pós-falha de teste.
- Não é necessário mover dados do SQL Server com cópia de segurança e restaurar.

##### <a name="disadvantages"></a>Desvantagens

- Dependendo do acesso de cliente para o SQL Server, poderá haver aumento da latência quando o SQL Server é executado um corrente alternativo para a aplicação.
- A hora de cópia da VHDs ao armazenamento de Premium pode ser extensas. Isto poderá afetar a sua decisão no se pretende manter o nó no grupo disponibilidade. Considere o seguinte para quando estiver a executar o cargas durante a migração um trabalho intenso registo é necessário, uma vez que o nó primário irá tem de manter as transações demoradas no respetivo registo de transações do. Por conseguinte, este foi possível aumentar significativamente.
- Este cenário utilizaria commandlet Azure **AzureStorageBlobCopy iniciar** , que é assíncrona. Não existe nenhuma SLA na conclusão. A hora das cópias varia, enquanto Isto depende espera na fila, também será dependem a quantidade de dados para o qual transferir. Assim que só tem um nó no Centro de dados de 2., deverá tomar medidas de mitigação caso a cópia demora mais de testar. Isto pode incluir ideias que se seguem.
    - Adicione um nó SQL 2º temporário para HA antes da migração com indisponibilidade aprovada.
    - Execute a migração fora do Azure manutenção agendada.
    - Certifique-se de que configurou corretamente o quórum cluster.

Este cenário assume que tem documentado a sua instalação e saber como o armazenamento é mapeado para poder efetuar alterações para definições de cache ideal disco.

##### <a name="high-level-steps"></a>Passos de alto nível
![Multisite2][10]

- Tornar no local / alternativo CC Azure SQL Server primária e torná-lo a outras automática activação pós-falha parceiro (AFP).
- Recolher informações de configuração de disco do SQL2 e remover o nó (não elimine VHDs anexados).
- Criar uma conta de armazenamento de Premium e copie VHDs da conta de armazenamento padrão.
- Crie um novo serviço de nuvem e crie a VM SQL2 com os seus discos prémios armazenamento ligados.
- Configurar ILB / ELB e adicione os pontos finais.
- Atualizar a sempre no escuta com nova ILB / ELB IP endereço e teste activação pós-falha.
- Verifique a configuração de DNS.
- Alterar o AFP para SQL2 e, em seguida, migrar SQL1 e os passos 2 a 5.
- Teste activações pós-falha.
- Mude o AFP novamente para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Anexo: Migrar um Multisite sempre num Cluster ao armazenamento de Premium

O resto deste tópico fornece um exemplo detalhado da conversão de um cluster de sempre no site com várias armazenamento Premium. Também converte a escuta de utilizar uma Balanceador de carga externos (ELB) para um balanceador de carga interna (ILB).

### <a name="environment"></a>Ambiente

- Windows 2k 12 / SQL 2k 12
- Ficheiros de 1 DB no SP
- 2 x agrupamentos de armazenamento por nó

![Appendix1][11]

### <a name="vm"></a>VM:

Neste exemplo vamos para demonstrar mover a partir de um ELB para ILB. ELB foi disponível antes de ILB, para que esta opção mostra como mudar a esta durante a migração.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Passos para tipo de ficheiro: Ligar a subscrição

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Passo 1: Criar nova conta de armazenamento e serviço em nuvem
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Passo 2: Aumentar as permitido falhas de recursos<Optional>
Em determinados recursos que pertence à sua sempre no grupo de disponibilidade existem limites quantos falhas que podem ocorrer durante um período, onde o serviço de cluster tentará reiniciar o grupo de recursos. Recomenda-se que aumentar isto, embora o é observar este procedimento, desde caso não pretenda manualmente activações pós-falha activação e acionador por encerrar máquinas que pode obter perto este limite.

Seria prudente ao dobro a compensação de em falha, para fazer isto no Gestor de activação pós-falha Cluster, aceda às propriedades do grupo de recursos sempre no:

![Appendix3][13]

Altere as falhas máximas 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Passo 3: Recurso de endereço de IP da adição para Cluster de grupo<Optional>

Se tiver apenas um endereço IP para o grupo de Cluster e este está alinhada à sub-rede na nuvem, tenha em atenção, se acidentalmente em offline todos os nós cluster na nuvem que, em seguida, o recurso de IP de Cluster de rede e nome de rede Cluster não poderá ficar online. Trabalho Isto irá impedir que as atualizações para outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Passo 4: Configuração de DNS

Para implementar um bom transição depende como DNS está a ser eficácia de utilização e atualizados.
Quando o sempre está instalado, cria um grupo de recursos de Cluster do Windows, se abrir Gestor de clusters de activação pós-falha, irá ver que no mínimo terá três recursos, as duas que o documento que se refere a são:

- Nome de rede virtual (VNN) – este é o nome de DNS que o cliente ligar quando pretendem ligar aos servidores SQL através de sempre.
- Recurso de endereço IP – este é o endereço IP que associada a VNN, pode ter mais do que um e numa configuração realizado tiver um endereço IP por site/sub-rede.

Quando ligar para o SQL Server, o SQL Server Client controlador irá obter os registos DNS associados a escuta e tente ligar-se para cada sempre no associadas endereço IP, abaixo discutimos algumas fatores que podem influenciar esta.

O número de registos DNS em simultâneo, que estão associadas com o nome de escuta depende não apenas o número de endereços IP associado, mas o ' RegisterAllIpProviders'setting no activação pós-falha clusters para o recurso sempre ON VNN.

Quando implementar sempre no Azure existem diferentes passos para criar as escuta e endereços IP, tem de configurar manualmente o RegisterAllIpProviders a 1, isto é diferente para um local no sempre na implementação onde já está definido para 1.

Se 'RegisterAllIpProviders' for 0, em seguida, só verá um registo DNS em DNS no associada a escuta:

![Appendix4][14]

Se 'RegisterAllIpProviders' for 1:

![Appendix5][15]

O código abaixo será as definições de VNN de falha de sistema e defini-lo para si, tenha em atenção, para que a alteração entre em vigor, terá de colocar o VNN offline e torná-lo novamente online, este demorar a escuta offline a causar interrupção de conectividade de cliente.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

Num passo de migração posterior, terá de atualizar a escuta sempre com um endereço IP actualizado que fizer referência a um balanceador de carga, isto irá envolver um remoção de recursos do endereço IP e adição. Após a atualização IP, tem de garantir que os novos endereços IP foi atualizado na zona de DNS e os clientes estão a atualizar os respetivos cache DNS local.

Se os seus clientes residem um segmento de rede diferente e fazer referência um servidor DNS diferente, é necessário considerar o que acontece sobre DNS Zone transferir durante a migração, tal como a aplicação reestabelecer ligação vez será restringida por, pelo menos, a hora de transferência de zona de qualquer novos endereços IP a escutar. Se estiver em aqui restrição de tempo, deve discutir e testar forçar uma transferência de zona utilizarão com as suas equipas do Windows e colocar também o registo de anfitrião DNS para uma baixa TTL Time To Live (), por isso, os clientes de atualizar. Para mais informações, consulte [As transferências de zona utilizarão](https://technet.microsoft.com/library/cc958973.aspx) e [DnsServerZoneTransfer iniciar](https://technet.microsoft.com/library/jj649917.aspx).

Por predefinição, o TTL para o registo DNS que está associado a escuta no sempre no Azure é 1200 segundos. Pode optar por reduzir este se estiver em horário de restrição durante a sua migração para garantir que os clientes actualizar o seu DNS com o endereço IP atualizado a escutar. Pode ver e modificar a configuração por operações terminar a configuração da VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Tenha em atenção, menor o 'HostRecordTTL', irá ocorrer uma superior quantidade de tráfego de DNS.

##### <a name="client-application-settings"></a>Definições da aplicação de cliente

Se a sua aplicação de cliente SQL suportar o .net 4,5 SQLClient, em seguida, que pode utilizar ' MULTISUBNETFAILOVER = TRUE' palavra-chave, este procedimento é recomendado para serem aplicados dado que permite a ligação mais rápida para SQL sempre no grupo de disponibilidade durante activação pós-falha. Enumera através de todos os endereços IP associados a escuta sempre em paralelo e executa uma velocidade de repetição de ligação TCP mais agressiva durante uma activação pós-falha.

Para mais informações sobre as definições acima, consulte o artigo [MultiSubnetFailover de palavra-chave e funcionalidades associadas ao](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte também [SqlClient suporte para elevada disponibilidade, recuperação de falhas](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Passo 5: Cluster as definições de quórum

À medida que vai ser tendo saída pelo menos uma do SQL Server para baixo, uma vez, deverá modificar a definição de quórum cluster, se utilizar o ficheiro partilhar fé (FSW) com 2 nós, deverá definir quórum permitir maioria de nós e utilizem-las de voto dinâmicos e este é para permitir a um único nó para permanecer permanente.


    Set-ClusterQuorum -NodeMajority  

Para mais informações sobre como gerir e configurar o quórum cluster, consulte o artigo [configurar e gerir o quórum num Cluster de activação pós-falha do Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Passo 6: Extrair os pontos finais existentes e ACL
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Guarde estas definições para um ficheiro de texto.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Passo 7: Alterar activação pós-falha parceiros e modos de replicação

Se tiver mais do que 2 servidores SQL Server, deverá alterar o activação pós-falha de outro secundário na outra Cc ou local para 'Síncrono' e torná-lo um parceiro de activação pós-falha automática (AFP), esta é, de modo a manter HA enquanto estiver a fazer alterações. Pode fazê-lo através de TSQL de modificar embora SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Passo 8: Remover VM secundário do serviço na nuvem

Se deve ser a planear migrar um nó secundário nuvem em primeiro lugar, se este for atualmente principal, deve iniciar um manual activação pós-falha.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 9: Alterar as definições no ficheiro CSV de colocação em cache de disco e guardar

Para volumes de dados estes devem estar definidas como só de leitura.

Para volumes TLOG estes devem ser definidos nenhum.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Passo 10: Copiar VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Pode verificar o estado de cópia de VHDs para a conta de armazenamento de Premium:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Aguarde até que todos estes são registados como sucesso.

Para obter informações para blobs individuais:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Passo 11: Disco de registar OS

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Passo 12: Importar secundário para o novo serviço em nuvem

O código abaixo também utiliza a opção adicionada aqui pode importar o computador e utilizar o VIP retainable.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Passo 13: Criar ILB no novo nuvem Svc, adicionar carga balanceamento pontos finais e ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>Passo 14: Atualizar sempre no
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Agora remova o serviço de nuvem antigo endereço IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Passo 15: Verificação de actualização de DNS

Agora deve verificar os servidores DNS no seu redes de cliente do SQL Server e certifique-se de que a clusters tem adicionado o registo de anfitrião extra para o endereço IP adicionado. Se esses servidores DNS não tenham actualizados, considere forçar uma transferência de zona de DNS e certifique-se de que os clientes no aí sub-rede forem capazes de resolver para ambos os sempre no endereços IP, esta é pelo que não necessita de esperar que as automática replicação de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Passo 16: Sempre reconfigurar On

Neste momento Aguarde para secundária nesse nó que foi migrado para o totalmente voltar a sincronizar com o nó no local e mude para o nó replicação síncrono e torná-lo a AFP.  

#### <a name="step-17-migrate-second-node"></a>Passo 17: Migrar segundo nó
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 18: Alterar as definições no ficheiro CSV de colocação em cache de disco e guardar

Para volumes de dados estes devem estar definidas como só de leitura.

Para volumes TLOG estes devem ser definidos nenhum.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Passo 19: Criar a nova conta de armazenamento independentes para nó secundário
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Passo 20: Copiar VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Pode verificar o estado de cópia VHD para todos os VHDs: ForEach ($disk no $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. Nomedodisco $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Aguarde até que todos estes são registados como sucesso.

Para obter informações para blobs individuais: Estado de Blobs do #Check induvidual Get AzureStorageBlobCopyState-Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd"-contentor $containerName-contexto $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Passo 21: Disco de registar OS
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Passo 22: Adicionar carga balanceamento pontos finais e ACL
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Passo 23: Activação pós-falha de teste

Agora deve informar o nó migrado sincronizar com o nó do sempre no local, colocá-la no modo de replicação síncrono e aguarde até que é sincronizada. Em seguida, activação em pós-falha a partir no local para o primeiro nó migradas, que é o AFP. Assim que tiver funcionado, altere o último nó migrado para o AFP.

Deve testar activações pós-falha entre todos os nós e executar apesar caos testes para se certificar de trabalho activações pós-falha como esperado e um manor atempada.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Passo 24: Colocar novamente as definições de quórum cluster / DNS TTL / activação pós-falha Pntrs / as definições de sincronização
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Adicionar o recurso de endereço IP na mesma sub-rede

Se tiver apenas 2 servidores de SQL e quer migrá-los para um novo serviço de nuvem, mas pretende mantê-las na mesma sub-rede, pode evitar está a demorar a escuta offline a eliminar o original sempre no endereço IP e adicione os novos endereços IP. Se estiver a migrar as VMs para outra sub-rede não terá de fazer isto como será uma rede de cluster adicionais que fizer referência a esse sub-rede.

Assim que tiver importados o secundária migrada e adicionado o novo recurso de endereço IP para o novo serviço de nuvem antes de activação pós-falha a página principal existente, deve seguir estes passos dentro de Cluster activação pós-falha do Gestor de:

Para adicionar em endereço IP, consulte o artigo do [anexo](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), passo 14.

1. Para o recurso de endereço IP atual, altere o proprietário possível para 'Existente primária SQL Server', no exemplo abaixo, 'dansqlams4':

    ![Appendix13][23]

1. Para o novo recurso endereço IP, altere o proprietário do possível 'Migrated secundário SQL Server', no exemplo abaixo, 'dansqlams5':

    ![Appendix14][24]

1. Quando isto é definido pode activação pós-falha e, quando o último nó é migrado possíveis proprietários deve ser editados, de modo que o nó é adicionado como um possível proprietário:

    ![Appendix15][25]

## <a name="additional-resources"></a>Recursos adicionais
- [Armazenamento de Azure Premium](../storage/storage-premium-storage.md)
- [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/)
- [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
