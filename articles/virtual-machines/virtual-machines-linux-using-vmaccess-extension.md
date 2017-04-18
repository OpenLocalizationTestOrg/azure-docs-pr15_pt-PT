<properties
    pageTitle="Repor o access no Azure Linux VMs utilizando a extensão de VMAccess | Microsoft Azure"
    description="Repor o access no Azure Linux VMs utilizando a extensão de VMAccess."
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
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension"></a>Gerir utilizadores, SSH e verificação ou reparar discos no Azure Linux VMs utilizando a extensão de VMAccess

Este artigo mostra-lhe como utilizar a extensão do Azure VMAcesss para verificar ou reparar um disco, repor o acesso do utilizador, gerir contas de utilizador ou repor a configuração de SSHD no Linux. Requer o artigo:

- uma conta Azure ([obter uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Clip do Azure](../xplat-cli-install.md) tem sessão iniciada com `azure login`.

- o modo de Gestor de recursos do Azure _tem de estar no_ Azure clip `azure config mode arm`.

## <a name="quick-commands"></a>Comandos rápidos

Existem duas formas de utilizar VMAccess no seu VMs Linux:

- Utilizar o clip do Azure e os parâmetros necessários.
- Utilizar ficheiros JSON bruto que VMAccess processa e, em seguida, agir em.

Para a secção comando rápido, vamos utilizar o clip do Azure `azure vm reset-access` método. Nos exemplos seguintes comando, substitua os valores que contêm "exemplo" com os valores a partir do seu ambiente.

## <a name="create-a-resource-group-and-linux-vm"></a>Criar um grupo de recursos e Linux VM

```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Criar uma VM Debian

```bash
azure vm quick-create \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-g myResourceGroup \
-l westus \
-y Linux \
-n myVM \
-Q Debian
```

## <a name="reset-root-password"></a>Repor palavra-passe de raiz

Para repor a palavra-passe de raiz:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u root \
-p myNewPassword
```

## <a name="ssh-key-reset"></a>Repor chave SSH

Para repor a chave SSH de um utilizador que não sejam raiz:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Criar um utilizador

Para criar um utilizador:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-u myAdminUser \
-p myAdminUserPassword
```

## <a name="remove-a-user"></a>Remover um utilizador

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM \
-R myRemovedUser
```

## <a name="reset-sshd"></a>Repor SSHD

Para repor a configuração de SSHD:

```bash
azure vm reset-access \
-g myResourceGroup \
-n myVM
-r
```


## <a name="detailed-walkthrough"></a>Obter instruções detalhadas sobre

### <a name="vmaccess-defined"></a>VMAccess definidos:

O disco no seu VM Linux apresenta erros. Ficou repor a palavra-passe de raiz do seu VM Linux ou eliminado acidentalmente a sua chave privada SSH. Se o que aconteceu novamente nos dias do Centro de dados, que precisa para unidade aí e, em seguida, abra o KVM para obter na consola do servidor. Considere a extensão do Azure VMAccess como essa comutador KVM que permite-lhe aceder a consola para repor o acesso ao Linux ou efetuar manutenção de nível do disco.

Para obter instruções detalhado sobre, vamos utilizar a forma longa VMAccess, que utiliza brutos JSON ficheiros.  Também podem ser chamados estes ficheiros VMAccess JSON a partir de modelos Azure.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Utilizar VMAccess para verificar ou reparar o disco de uma VM Linux

Utilizar VMAccess que pode fazer um fsck execute no disco em seu VM Linux.  Também pode fazer uma verificação do disco e uma reparação do disco utilizando um VMAccess.

Para verificar e, em seguida, reparar o disco Utilize este script VMAccess:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Utilizar VMAccess para repor o acesso do utilizador para Linux

Se tiver perdido acesso a raiz no seu VM Linux, pode iniciar um script VMAccess para repor a palavra-passe de raiz.

Para repor a palavra-passe de raiz, utilize este script VMAccess:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword",   
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_root_password.json
```

Para repor a chave SSH de um utilizador que não sejam raiz, utilize este script VMAccess:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM",   
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Utilizar VMAccess para gerir contas de utilizador no Linux

VMAccess é um script de Python que pode ser utilizado para gerir utilizadores no seu VM Linux sem início de sessão e utilizar sudo ou com a conta de raiz.

Para criar um utilizador, utilize este script VMAccess:

`create_new_user.json`

```json
{
"username":"myNewUser",
"ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
"password":"myNewUserPassword",
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path create_new_user.json
```

Para eliminar um utilizador, utilize este script VMAccess:

`remove_user.json`

```json
{
"remove_user":"myDeletedUser",
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Utilizar VMAccess para repor a configuração de SSHD

Se efetuar alterações na configuração Linux VMs SSHD e feche a ligação de SSH antes das alterações a verificar, pode ser impedido de SSH'ing iniciá-la.  VMAccess podem ser utilizados para repor a configuração de SSHD para uma configuração correcta sem a ser tem sessão iniciada em SSH.

Para repor a configuração de SSHD Utilize este script VMAccess:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Execute o script VMAccess com:

```bash
azure vm extension set \
myResourceGroup \
myVM \
VMAccessForLinux \
Microsoft.OSTCExtensions * \
--private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Próximos passos

Atualizar Linux utilizar Azure VMAccess extensões é um método para efetuar alterações numa VM Linux em execução.  Também pode utilizar ferramentas como inicialização na nuvem e o Azure modelos para modificar a VM Linux no arranque.

[Sobre funcionalidades e extensões de máquina virtual](virtual-machines-linux-extensions-features.md)

[Criação de modelos de Gestor de recursos do Azure com as extensões de Linux VM](virtual-machines-linux-extensions-authoring-templates.md)

[Utilizar inicialização na nuvem para personalizar uma VM Linux durante a criação](virtual-machines-linux-using-cloud-init.md)
