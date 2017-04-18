<properties 
    pageTitle="Configurar LVM num computador a executar o Linux virtual | Microsoft Azure" 
    description="Saiba como configurar o LVM no Linux no Azure." 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configurar o LVM numa VM Linux no Azure

Este documento vai discutir como configurar o Gestor de Volume lógico (LVM) no seu máquina virtual Azure. Enquanto a mesma estiver viável para configurar LVM em qualquer disco anexado à máquina virtual, por predefinição maioria das imagens de nuvem não terá LVM configurado no disco SO. Isto é para evitar problemas com os grupos de volume duplicados se o sistema operativo disco é nunca anexado a outra VM do mesmo distribuição e tipo, ou seja, durante um cenário de recuperação. Por conseguinte, recomenda-se apenas ao utilizar LVM nos discos dados.


## <a name="linear-vs-striped-logical-volumes"></a>Linear vs. às riscas volumes lógicos

LVM pode ser utilizado para combinar um número de discos físicos num volume de armazenamento único. Por predefinição LVM normalmente criará volumes lógicos lineares, o que significa que o armazenamento físico é concatenado em conjunto. Neste caso operações de leitura/escrita serão normalmente apenas ser enviadas para um único disco. Em contrapartida, recomendamos também pode criar às riscas volumes lógicos onde é distribuída operações de leitura e escrita para vários discos contidos no grupo em volume (ou seja, semelhante ao RAID0). Por razões de desempenho é provável que será que pretende faixas seu volumes lógicos para que lê e escritas utilizam em todos os seus dados anexados discos.

Neste documento serão descrevem como combinar várias discos de dados para um grupo de único volume e, em seguida, crie às riscas volume lógico. Os passos abaixo são um pouco generalized para trabalhar com a maioria dos distribuições. Na maioria dos casos os utilitários e fluxos de trabalho para gerir LVM no Azure não são bastante diferentes que outros ambientes. Como habitualmente, também, consulte o fornecedor do seu Linux para documentação e práticas recomendadas para utilizar LVM com a sua distribuição específica.


## <a name="attaching-data-disks"></a>Anexar discos de dados
Um pretenderá normalmente iniciar com dois ou mais discos de dados vazia ao utilizar LVM. Com base nas suas necessidades IO, pode optar por anexar discos que estão armazenados no nosso armazenamento padrão, com até 500 IO/ps por disco ou nosso armazenamento Premium com até 5000 IO por ps por disco. Este artigo não subscreve detalhadamente sobre como pode aprovisionar e anexar discos de dados para uma máquina de virtual Linux. Consulte o artigo o Microsoft Azure artigo [anexar um disco](virtual-machines-linux-add-disk.md) para obter instruções detalhadas sobre como anexar um disco de dados vazia para uma máquina de virtual Linux no Azure.

## <a name="install-the-lvm-utilities"></a>Instalar os utilitários LVM

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL, CentOS & Oracle Linux**

        # sudo yum install lvm2

- **SLES 12 e openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    No SLES11 também tem de editar /etc/sysconfig/lvm e definir `LVM_ACTIVATED_ON_DISCOVERED` para "ativar":

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>Configurar LVM
Neste guia podemos irá assumir tiver anexado três discos de dados, que irá a referência é feita como `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Tenha em atenção que estes podem não sempre ser os mesmos nomes de caminho no seu VM. Pode executar '`sudo fdisk -l`' ou comando semelhante para listar seus discos disponíveis.

1. Prepare os volumes físicos:

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  Crie um grupo de volume. Neste exemplo estamos está a telefonar volume grupo "dados-vg01":

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. Crie os volumes lógicos. O comando abaixo estamos irá criar um único volume lógico denominado "dados-lv01" para o grupo de todo o volume do intervalo, mas tenha em atenção que também é viável para criar múltiplas volumes lógicos no grupo volume.

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. Formatar o volume de lógico

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] Com a utilização de SLES11 "-t ext3" em vez de ext4. SLES11 suporta apenas acesso só de leitura para ext4 filesystems.


## <a name="add-the-new-file-system-to-etcfstab"></a>Adicionar o novo sistema de ficheiros a /etc/fstab

**Atenção:** Editar um ficheiro /etc/fstab indevidamente poderá resultar em com que o sistema. Se certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente este ficheiro. Também é recomendável que é criada uma cópia de segurança do ficheiro /etc/fstab antes de o editar.

1. Crie o ponto de montagem pretendida para o seu novo sistema de ficheiros, por exemplo:

        # sudo mkdir /data


2. Localizar o caminho de volume lógico

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. Abra /etc/fstab num editor de texto e adicionar uma entrada para o novo sistema de ficheiros, por exemplo:

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    Em seguida, guarde e feche /etc/fstab.


4. Teste a que a entrada de fstab/etc/está correta:

        # sudo mount -a

    Se este comando resulta numa mensagem de erro Verifique a sintaxe no ficheiro de fstab/etc /.

    Junto a executar o `mount` comando para garantir que o sistema de ficheiros:

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Opcional) Parâmetros de arranque isenta de falhas no /etc/fstab

    Muitos distribuições incluem quer o `nobootwait` ou `nofail` montagem parâmetros que podem ser adicionados para o ficheiro de fstab/etc /. Estes parâmetros permitem para falhas quando montagem um sistema de ficheiros específico e permitir que o sistema de Linux continuar a mesmo se for não é possível montagem corretamente o sistema de ficheiros RAID de arranque. Consulte a documentação da distribuição para mais informações sobre estes parâmetros.

    Exemplo (Ubuntu):

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
