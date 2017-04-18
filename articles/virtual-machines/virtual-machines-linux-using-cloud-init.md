<properties
    pageTitle="Utilizar inicialização na nuvem para personalizar uma VM Linux durante a criação de | Microsoft Azure"
    description="Utilizar inicialização na nuvem para personalizar uma VM Linux durante a criação."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>Utilizar inicialização na nuvem para personalizar uma VM Linux durante a criação

Este artigo mostra como fazer um script de inicialização na nuvem para definir o nome do anfitrião, pacotes de actualização instalada e gerir contas de utilizador.  Os scripts de nuvem-inicialização chamam durante a criação de VM a partir do Azure clip.  Requer o artigo:

- uma conta Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Clip do Azure](../xplat-cli-install.md) tem sessão iniciada com `azure login`.

- o modo de Gestor de recursos do Azure _tem de estar no_ Azure clip `azure config mode arm`.

## <a name="quick-commands"></a>Comandos rápidos

Crie um script init.txt na nuvem que define o nome de anfitrião, actualiza todos os pacotes e adiciona um utilizador sudo Linux.

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Crie um grupo de recursos para iniciar VMs para.

```bash
azure group create myResourceGroup westus
```

Crie uma VM Linux utilizando nuvem-inicialização configurá-lo durante o arranque.

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Obter instruções detalhadas sobre

### <a name="introduction"></a>Introdução

Quando iniciar uma nova VM de Linux, qual está a obter um padrão de Linux VM com nada personalizada ou está preparado para as suas necessidades. [Nuvem-inicialização](https://cloudinit.readthedocs.org) é uma forma padrão de inserção um script ou definições de configuração para esse VM Linux, tal como está a ser iniciado para a primeira vez.

No Azure, existem três formas para efetuar alterações para uma VM Linux à medida que está a ser implementada ou iniciado.

- Inserção scripts utilizando inicialização na nuvem.
- Inserção scripts utilizando o Azure [VMAccess extensão](virtual-machines-linux-using-vmaccess-extension.md).
- Um modelo Azure utilizando inicialização na nuvem.
- Um modelo Azure utilizando [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Para inserir scripts em qualquer altura após o arranque:

- SSH para executar comandos diretamente
- Inserção scripts utilizando a Azure [VMAccess extensão](virtual-machines-linux-using-vmaccess-extension.md), imperatively ou um modelo do Azure
- Ferramentas de gestão de configuração como Ansible, sal, chefe e Puppet.

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilidade de inicialização da nuvem no Azure VM introdução-criar aliases da imagem:

| Alias     | Publisher | Oferta        | SKU         | Versão | nuvem-inicialização |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | Centos       | 7.2         | mais recente  | nenhum         |
| CoreOS    | CoreOS    | CoreOS       | Estável      | mais recente  | Sim        |
| Debian    | credativ  | Debian       | 8           | mais recente  | nenhum         |
| openSUSE  | SUSE      | openSUSE     | 13.2        | mais recente  | nenhum         |
| RHEL      | Sistema Redhat    | RHEL         | 7.2         | mais recente  | nenhum         |
| UbuntuLTS | Canónico | UbuntuServer | 14.04.4-LTS | mais recente  | Sim        |

Microsoft está a trabalhar com os nossos parceiros para obter nuvem-inicialização incluído e trabalhar com as imagens que fornecem Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Adicionar um script de inicialização na nuvem para a criação de VM com o clip do Azure

Para iniciar um script de nuvem-inicialização quando criar uma VM no Azure, especifique o ficheiro de nuvem-inicialização utilizando o clip do Azure `--custom-data` mudar.

Crie um grupo de recursos para iniciar VMs para.

```bash
azure group create myResourceGroup westus
```

Crie uma VM Linux utilizando nuvem-inicialização configurá-lo durante o arranque.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Criar um script de inicialização na nuvem para definir o nome do anfitrião de uma VM Linux

As definições mais simples e mais importantes para qualquer VM Linux seria o nome do anfitrião. Vamos pode facilmente defini-lo na nuvem-inicialização a utilizar com este script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Exemplo de script nuvem-inicialização denominado `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: myservername
```

Durante o arranque inicial da VM, este nuvem-inicialização script define o nome do anfitrião `myservername`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

Início de sessão e verifique se o nome do anfitrião da VM novo.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Criar um script de inicialização na nuvem para atualizar Linux

Segurança, pretende obter a VM Ubuntu para atualizar no primeiro arranque.  Utilizar a nuvem-inicialização que podemos pode fazê-lo com o script a seguir, consoante a distribuição de Linux que estiver a utilizar.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Exemplo de script de nuvem-inicialização `cloud_config_apt_upgrade.txt` para a família Debian

```bash
#cloud-config
apt_upgrade: true
```

Após o ter iniciado Linux, todos os pacotes de instalados são atualizados através do `apt-get`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

Início de sessão e verifique se todos os pacotes são atualizados.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Criar um script de inicialização na nuvem para adicionar um utilizador ao Linux

Uma das tarefas primeiros no qualquer VM Linux novo é para adicionar um utilizador para si próprio ou para evitar utilizar `root`. SSH chaves estão melhores práticas para segurança e para textuais e são adicionadas a `~/.ssh/authorized_keys` ficheiro com este script de inicialização na nuvem.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Exemplo de script de nuvem-inicialização `cloud_config_add_users.txt` para Debian família

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Após o ter iniciado Linux, todos os utilizadores listados são criados e adicionados ao grupo sudo.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

Início de sessão e verifique se o utilizador recentemente criado.

```bash
ssh myVM
cat /etc/group
```

Saída

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Próximos passos

Nuvem-inicialização é tornar-se uma forma padrão para modificar a VM Linux no arranque. Azure também tem extensões de VM, que permitem-lhe modificar a sua LinuxVM no arranque ou enquanto estiver em execução. Por exemplo, pode utilizar o Azure VMAccessExtension para repor informações SSH ou utilizador enquanto a VM estiver em execução. Com a nuvem-inicialização, seria necessário reiniciar o computador para repor a palavra-passe.

[Sobre funcionalidades e extensões de máquina virtual](virtual-machines-linux-extensions-features.md)

[Gerir utilizadores, SSH e verificação ou reparar discos no Azure Linux VMs utilizando a extensão de VMAccess](virtual-machines-linux-using-vmaccess-extension.md)
