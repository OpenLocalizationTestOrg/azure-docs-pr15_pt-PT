<properties
    pageTitle="Adicionar um disco Linux VM | Microsoft Azure"
    description="Saiba como adicionar um disco persistente ao seu VM Linux"
    keywords="máquina de virtual Linux, adicionar disco de recurso"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM Linux

Este artigo mostra como anexar um disco persistente a sua VM para que possa pode manter os dados de - mesmo se o seu VM é reprovisioned devido a manutenção ou redimensionar. Para adicionar um disco, terá de [O clip do Azure](../xplat-cli-install.md) configurado no modo de Gestor de recursos (`azure config mode arm`).  

## <a name="quick-commands"></a>Comandos rápidos

Nos exemplos seguintes comando, substitua os valores entre &lt; e &gt; com os valores a partir do seu ambiente.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>Anexar um disco

Anexar um novo disco é rápido. Tipo de `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` para criar e anexar um novo disco GB para sua VM. Se não explicitamente identificar uma conta de armazenamento, qualquer disco que criar é colocado na mesma conta de armazenamento onde reside o disco SO.  -Deve ter um aspeto semelhante ao seguinte:

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Saída

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ligar para a VM Linux para o novo disco de montagem

> [AZURE.NOTE] Este tópico liga-se para uma VM utilizando nomes de utilizador e palavras-passe. Para utilizar os pares de chaves públicos e privados para comunicar com o seu VM, veja [como utilizar SSH com Linux no Azure](virtual-machines-linux-mac-create-ssh-keys.md). Pode modificar a conectividade **SSH** do VMs criados com o `azure vm quick-create` comando utilizando o `azure vm reset-access` comando para repor o **SSH** access completamente, adicionar ou remover utilizadores ou adicionar ficheiros chaves públicos para proteger o acesso.

Que necessita para SSH para o seu VM Azure partição, formatar e montagem disco novo para a VM Linux pode utilizá-la. Se não estiver familiarizado com ligarem-se com **ssh**, o comando leva-o até o formulário `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`e aspeto semelhante ao seguinte:

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Saída

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

ops@myuniquevmname:~$
```

Agora que estiver ligado a sua VM, está pronto para anexar um disco.  Primeiro localize o disco, utilizar `dmesg | grep SCSI` (o método que utilizar para descobrir o seu novo disco pode variar). Neste caso, tenha aspeto:

```bash
dmesg | grep SCSI
```

Saída

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

e, no caso de neste tópico, o `sdc` disco é aquele que pretendemos. Agora a partições o disco com `sudo fdisk /dev/sdc` – partindo do princípio que no seu caso o disco estava `sdc`, tornar um disco principal na partição 1 e aceite as outras predefinições.

```bash
sudo fdisk /dev/sdc
```

Saída

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Criar a partição escrevendo `p` na linha de comandos:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

E escrever um sistema de ficheiros para a partição utilizando o comando de **mkfs** , especificando o seu tipo de sistema de ficheiros e o nome do dispositivo. Neste tópico, estamos a utilizar o `ext4` e `/dev/sdc1` dos acima:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Saída

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Agora, vamos criar um diretório para o ficheiro através do sistema de montagem `mkdir`:

```bash
sudo mkdir /datadrive
```

E montagem a utilizar o diretório `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

O disco de dados está agora pronto para utilizar como `/datadrive`.

```bash
ls
```

Saída

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

Para garantir que a unidade é remontada automaticamente após reiniciar o computador tem de ser adicionada para o ficheiro de fstab/etc /. Além disso, recomenda-se vivamente que o UUID (universal Identificador exclusivo) é utilizado na/etc/fstab para referir-se a unidade de em vez de apenas o nome do dispositivo (, tais como `/dev/sdc1`). Se o SO Deteta um erro no disco durante o arranque, utilizar o UUID evita o disco incorreto a ser instalado para uma determinada localização. Restante discos de dados, em seguida, será atribuída essas mesmas IDs de dispositivo. Para localizar o UUID da nova unidade, utilize o utilitário **blkid** :

```bash
sudo -i blkid
```

O resultado é semelhante ao seguinte:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] Editar um ficheiro **/etc/fstab** indevidamente poderá resultar em com que o sistema. Se não souber, consulte a documentação da distribuição para obter informações sobre como editar corretamente este ficheiro. Também é recomendável que é criada uma cópia de segurança do ficheiro /etc/fstab antes de o editar.

Em seguida, abra o ficheiro de **/etc/fstab** num editor de texto:

```bash
sudo vi /etc/fstab
```

Neste exemplo, vamos utilizar o valor UUID para o novo dispositivo **/dev/sdc1** que foi criado nos passos anteriores e o ponto de montagem **/datadrive**. Adicione a linha seguinte para o fim do ficheiro **/etc/fstab** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] Remover mais tarde um disco de dados sem o editarem fstab pode causar a VM Falha ao arrancar. A maior parte dos distribuições fornecem quer o `nofail` e/ou `nobootwait` fstab opções. Estas opções permitem um sistema para iniciar o mesmo se o disco falhar para montagem durante o arranque. Documentação a distribuição para mais informações sobre estes parâmetros.

>[AZURE.NOTE] A opção de **nofail** garante que a VM é iniciado, mesmo se o sistema de ficheiros está danificado ou que o disco não existir durante o arranque. Sem esta opção, que poderá encontrar comportamento conforme descrito no [é possível SSH Linux VM devido a erros FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte de cortar/UNMAP para Linux no Azure
Algumas kernels Linux suportam operações de cortar/UNMAP para eliminar blocos não utilizados no disco. Este é útil principalmente no armazenamento padrão para informar Azure eliminados páginas já não são válida e podem ser eliminados. Isto pode guardar o custo se criar ficheiros grandes e, em seguida, elimine-as.

Existem duas formas de ativar cortar suportam no seu VM Linux. Como habitualmente, consulte a distribuição para a abordagem recomendada:

- Utilizar o `discard` montagem opção no `/etc/fstab`, por exemplo:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Em alternativa, pode executar o `fstrim` manualmente do comando da linha de comandos ou adicioná-lo à sua crontab para executar regularmente:

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Resolução de problemas
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Próximos passos

- Lembre-se de que o novo disco não está disponível para a VM se for uma reiniciado, a menos que escrever essas informações para o seu ficheiro [fstab](http://en.wikipedia.org/wiki/Fstab) .
- Para assegurar que a VM Linux está configurada corretamente, reveja as recomendações [otimizar o desempenho do seu computador Linux](virtual-machines-linux-optimization.md) .
- Expanda a sua capacidade de armazenamento adicionando discos adicionais e [Configurar RAID](virtual-machines-linux-configure-raid.md) para obter um desempenho adicional.
