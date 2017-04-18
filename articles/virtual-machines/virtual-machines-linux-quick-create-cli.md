<properties
   pageTitle="Criar uma VM Linux no Azure utilizando o clip | Microsoft Azure"
   description="Crie uma VM Linux no Azure utilizando o clip."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="v-livech"/>


# <a name="create-a-linux-vm-on-azure-by-using-the-cli"></a>Criar uma VM Linux no Azure utilizando o clip

Este artigo mostra como implementar rapidamente uma máquina de virtual Linux (VM) no Azure utilizando o `azure vm quick-create` comando na interface de linha de comandos do Azure (CLI). O `quick-create` uma VM dentro de uma infraestrutura básica e segura que pode utilizar para protótipo ou testar rapidamente um conceito implementa o comando. Requer o artigo:

- uma conta Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Clip do Azure](../xplat-cli-install.md) tem sessão iniciada com `azure login`.

- o modo de Gestor de recursos do Azure _tem de estar no_ Azure clip `azure config mode arm`.

Pode implementar também rapidamente uma VM Linux utilizando o [Azure portal](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-commands"></a>Comandos rápidos

O exemplo seguinte mostra como implementar uma VM CoreOS e anexar a sua chave de Shell seguro (SSH) (os argumentos podem ser diferentes):

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Obter instruções detalhadas sobre

O seguinte tutorial tem um VM UbuntuLTS a ser implementada, passo a passo, com explicações sobre o que cada passo é efetuar.

## <a name="vm-quick-create-aliases"></a>VM introdução-criar aliases

Uma forma rápida para escolher uma distribuição é utilizar os aliases Azure clip mapeados para as distribuições SO mais comuns. A tabela seguinte lista os aliases (a partir do Azure CLI versão 0,10). Todas as implementações que utilizam `quick-create` predefinição para VMs de segurança abrange os ao armazenamento de unidade de estado sólido (SSD), que oferece um acesso mais rápido aprovisionamento e alto desempenho disco. (Estes aliases representam uma parte muito pequena das distribuições disponíveis no Azure. Localizar mais imagens no Azure Marketplace ao [Procurar uma imagem no PowerShell](virtual-machines-linux-cli-ps-findimage.md), [na web](https://azure.microsoft.com/marketplace/virtual-machines/)ou [carregar a sua própria imagem personalizada](virtual-machines-linux-create-upload-generic.md).)

| Alias     | Publisher | Oferta        | SKU         | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | mais recente  |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  |
| Debian    | credativ  | Debian       | 8           | mais recente  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  |
| RHEL      | Chapéu vermelho    | RHEL         | 7.2         | mais recente  |
| UbuntuLTS | Canónico | Servidor de Ubuntu | 14.04.4-LTS | mais recente  |

Utilizar as secções seguintes a `UbuntuLTS` alias para a opção **ImageURN** (`-Q`) para implementar um servidor de LTS Ubuntu 14.04.4.

Os últimos `quick-create` exemplo apenas realçado a `-M` Sinalizar para identificar a chave pública SSH para carregar ao desativar as palavras-passe SSH, para que lhe for pedido para os seguintes argumentos:

- nome do grupo de recursos (qualquer cadeia é normalmente fina para o primeiro grupo de recursos Azure)
- Nome VM
- localização (`westus` ou `westeurope` são boas predefinições)
- Linux (para permitir que o Azure saber quais OS que pretende)
- nome de utilizador

O exemplo seguinte especifica todos os valores de modo a que sem perguntar ainda é necessário. Desde que tenha uma `~/.ssh/id_rsa.pub` como um ssh rsa formato ficheiro da chave pública, funciona como é:

```bash
azure vm quick-create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--admin-username myAdminUser \
--ssh-public-file ~/.ssh/id_rsa.pub \
--image-urn UbuntuLTS
```

O resultado deve aspeto o bloco de saída seguinte:

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Iniciar sessão para a nova VM

Inicie sessão na sua VM utilizando o endereço IP público listado no resultado. Também pode utilizar o nome de domínio completamente qualificado (FQDN) que se encontra listado:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

O processo de início de sessão deverá ter um aspeto semelhante o bloco de saída seguinte:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Próximos passos

O `azure vm quick-create` comando é a forma de implementar rapidamente uma VM para que possa iniciar sessão numa festa shell e começar a trabalhar. No entanto, utilizando `vm quick-create` não dar-lhe controlo extenso nem que permitem-lhe criar um ambiente mais complexo.  Para implementar uma VM Linux que é personalizado para sua infraestrutura, pode seguir qualquer um dos seguintes artigos:

- [Utilizar um modelo de Gestor de recursos do Azure para criar uma implementação específica](virtual-machines-linux-cli-deploy-templates.md)
- [Criar o seu próprio ambiente personalizado para uma VM Linux utilizando os comandos do Azure clip diretamente](virtual-machines-linux-create-cli-complete.md)
- [Criar um SSH protegido Linux VM no Azure utilizar modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Também pode [utilizar o `docker-machine` controlador Azure com vários comandos para criar rapidamente uma VM Linux como um anfitrião docker](virtual-machines-linux-docker-machine.md).
