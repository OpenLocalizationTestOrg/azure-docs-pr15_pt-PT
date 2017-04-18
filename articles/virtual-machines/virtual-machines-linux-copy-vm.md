<properties
    pageTitle="Criar uma cópia da sua VM de Linux Azure | Microsoft Azure"
    description="Saiba como criar uma cópia do seu máquina de virtual Azure Linux no modelo de implementação de Gestor de recursos"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="cynthn"/>

# <a name="create-a-copy-of-a-linux-virtual-machine-running-on-azure"></a>Criar uma cópia de uma máquina de virtual Linux em execução no Azure


Este artigo mostra-lhe como criar uma cópia do seu Azure máquina virtual (VM) com o Linux utilizando o modelo de implementação do Gestor de recursos. Primeiro copiar sobre o sistema operativo e discos de dados para um novo contentor, em seguida, configure os recursos de rede e criar a nova máquina virtual.

Também é possível [carregar e criar uma VM da imagem do disco personalizada](virtual-machines-linux-upload-vhd.md).


## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que cumpre os pré-requisitos seguintes antes de começar os passos:

- Tiver [clip do Azure] (... / xplat-clip-install.md) transferidas e instaladas no seu computador. 

- Também precisa de algumas informações sobre a VM de Linux Azure existente:

| Informações de origens de VM | Onde obtê-lo |
|------------|-----------------|
| Nome VM | `azure vm list` |
| Nome do grupo de recursos | `azure vm list` |
| Localização | `azure vm list` |
| Nome da conta de armazenamento | `azure storage account list -g <resourceGroup>` |
| Nome do contentor | `azure storage container list -a <sourcestorageaccountname>` |
| Nome do ficheiro VM VHD origem | `azure storage blob list --container <containerName>` |



- Terá de efetuar algumas opções sobre a nova VM:   <br> -Nome do contentor   <br> Nome - VM   <br> Tamanho da memória virtual-   <br> nome - vNet   <br> -Nome de sub-rede   <br> -IP nome   <br> Nome - NIC
    

## <a name="login-and-set-your-subscription"></a>Início de sessão e configurar a sua subscrição

1. Início de sessão para o clip.
        
        azure login

2. Certifique-se de que está no modo de Gestor de recursos.
    
        azure config mode arm

3. Defina a subscrição correta. Pode utilizar '' lista conta azure' para ver todas as suas subscrições.

        azure account set <SubscriptionId>



## <a name="stop-the-vm"></a>Parar a VM 

Interromper e retirar a origem VM. Pode utilizar 'azure vm ' lista ' para obter uma lista de todos os VMs na sua subscrição e os respetivos recurso nomes de grupo.
    
        azure vm stop <ResourceGroup> <VmName>
        azure vm deallocate <ResourceGroup> <VmName>




## <a name="copy-the-vhd"></a>Copie o VHD


Pode copiar o VHD a partir do armazenamento origem para o destino utilizando o `azure storage blob copy start`. Neste exemplo, vamos para copiar o VHD para a mesma conta de armazenamento, mas um contentor diferente.

Para copiar o VHD para outro contentor na mesma conta de armazenamento, escreva:

        azure storage blob copy start https://<sourceStorageAccountName>.blob.core.windows.net:8080/<sourceContainerName>/<SourceVHDFileName.vhd> <newcontainerName>
        

## <a name="set-up-the-virtual-network-for-your-new-vm"></a>Configurar a rede virtual para o seu novo VM

Configure um rede virtual e NIC para a nova VM. 

    azure network vnet create <ResourceGroupName> <VnetName> -l <Location>

    azure network vnet subnet create -a <address.prefix.in.CIDR/format> <ResourceGroupName> <VnetName> <SubnetName>

    azure network public-ip create <ResourceGroupName> <IpName> -l <yourLocation>

    azure network nic create <ResourceGroupName> <NicName> -k <SubnetName> -m <VnetName> -p <IpName> -l <Location>


## <a name="create-the-new-vm"></a>Criar a nova VM 

Agora pode criar uma VM a partir do seu disco virtual carregados [através de um modelo de Gestor de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) ou através do clip, especificando o URI para o seu disco copiado ao escrever:

```bash
azure vm create -n <newVMName> -l "<location>" -g <resourceGroup> -f <newNicName> -z "<vmSize>" -d https://<storageAccountName>.blob.core.windows.net/<containerName/<fileName.vhd> -y Linux
```



## <a name="next-steps"></a>Próximos passos

Para saber como utilizar o clip do Azure para gerir a sua nova máquina virtual, consulte o artigo [Azure clip comandos para o Gestor de recursos do Azure](azure-cli-arm-commands.md).
