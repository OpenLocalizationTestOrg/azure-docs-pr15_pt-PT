<properties
    pageTitle="Sobre discos e VHDs para o Windows VMs | Microsoft Azure"
    description="Saiba mais sobre as noções básicas de discos e máquinas virtuais VHDs para Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Sobre discos e VHDs para máquinas virtuais Azure

Tal como faria com qualquer outro computador, máquinas virtuais no Azure utilizar discos como um local para armazenar num sistema operativo, aplicações e dados. Todas as máquinas virtuais Azure ter, pelo menos, dois discos – um disco do sistema operativo Windows e um disco temporário. O disco do sistema operativo é criado a partir de uma imagem e o disco do sistema operativo e a imagem são rígido discos virtuais (VHDs) armazenados numa conta de armazenamento Azure. Máquinas virtuais também pode ter um ou mais discos de dados, que também são armazenados como VHDs. Este artigo também está disponível para [máquinas virtuais de Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



## <a name="operating-system-disk"></a>Disco do sistema operativo

Cada máquina virtual tem um sistema operativo anexados disco. Tem registado como uma unidade SATA e assinalada como na unidade c: por predefinição. Este disco tem uma capacidade máxima de 1023 gigabytes (GB). 

##<a name="temporary-disk"></a>Disco temporário

O disco temporário é automaticamente criado por si. O disco temporário é assinalado como na unidade d: por predefinição e é utilizado para armazenar Pagefile. 

O tamanho do disco temporário variam de acordo com o tamanho da máquina virtual. Para mais informações, consulte o artigo [máquinas virtuais de tamanhos para Windows](virtual-machines-windows-sizes.md).

>[AZURE.WARNING] Não armazene dados no disco temporário. Fornece armazenamento temporário para aplicações e processos e destina-se para armazenar apenas os dados como ficheiros de página ou trocar. Para mapear neste disco para uma letra de unidade diferente, consulte o artigo [alterar a letra da unidade de disco temporário Windows](virtual-machines-windows-classic-change-drive-letter.md).

Para mais informações sobre como o Azure utiliza o disco temporário, consulte o artigo [Compreender a unidade temporária em máquinas virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de dados

Um disco de dados é um VHD que está anexado a uma máquina virtual para armazenar os dados da aplicação ou outros dados de que necessita para manter. Dados discos estão registados como unidades SCSI e são denominados por uma letra que escolher.  Cada disco de dados tem uma capacidade máxima de 1023 GB. O tamanho da máquina virtual determina quantos discos de dados pode anexar às-lo e o tipo de armazenamento pode utilizar para alojar discos.

>[AZURE.NOTE] Para mais informações sobre as capacidades de máquinas virtuais, consulte o artigo [máquinas virtuais de tamanhos para Windows](virtual-machines-windows-sizes.md).

Azure cria um disco do sistema operativo quando cria uma máquina virtual a partir de uma imagem. Se utilizar uma imagem que inclua discos de dados, Azure também cria os discos de dados que cria a máquina virtual. Caso contrário, adicione discos dados depois de criar a máquina virtual.

Pode adicionar discos de dados para uma máquina virtual em qualquer altura, **anexar** o disco à máquina virtual. Pode utilizar um VHD que tenha carregados ou copiadas para a sua conta de armazenamento ou uma que cria Azure por si. Anexar um disco dados associa o ficheiro VHD a VM ao colocar uma locação no VHD para que não podem ser eliminada a partir do armazenamento enquanto este continua anexado.

## <a name="about-vhds"></a>Sobre VHDs

VHDs utilizados no Azure são ficheiros. vhd armazenados como blobs de página numa conta padrão ou premium armazenamento no Azure. Para mais informações sobre blobs de página, consulte o artigo [blobs do bloco de compreensão e blobs de página](https://msdn.microsoft.com/library/ee691964.aspx). Para obter mais informações sobre o armazenamento de premium, consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para das cargas de trabalho do Azure máquina virtual](../storage/storage-premium-storage.md).

Azure suporta o formato VHD disco fixo. O formato fixa define o disco lógico linear dentro do ficheiro, para que esse deslocamento disco X está armazenado no deslocamento blob X. Um pequeno rodapé no final do blob descreve as propriedades do VHD. Muitas vezes, o formato fixo resíduos espaço porque a maioria dos discos tem grandes intervalos não utilizados nos mesmos. No entanto, o Azure armazena ficheiros. VHD num formato dispersos, para que receba as vantagens de discos fixos e dinâmicos ao mesmo tempo. Para mais informações, consulte o artigo [introdução com discos rígido virtuais](https://technet.microsoft.com/library/dd979539.aspx).

Todos os ficheiros. vhd no Azure que pretende utilizar como origem de para criar discos ou imagens são só de leitura. Quando cria uma imagem ou do disco, Azure torna cópias dos ficheiros. vhd. Estas cópias podem ser só de leitura ou de leitura e escrita, dependendo de como utiliza o VHD.

Quando cria uma máquina virtual a partir de uma imagem, Azure cria um disco para a máquina virtual que é uma cópia do ficheiro. VHD de origem. Para proteger contra acidentais, Azure coloca uma locação em qualquer ficheiro. vhd origem utilizados para criar uma imagem, um disco do sistema operativo ou um disco de dados.

Antes de poder eliminar um ficheiro. VHD de origem, terá de remover a locação eliminando o disco ou a imagem. Pode eliminar a máquina virtual, o disco de sistema operativo e o ficheiro de. vhd origem ao mesmo tempo ao eliminar a máquina virtual e eliminar todos associados discos. No entanto, a eliminação de um ficheiro. vhd que é uma origem de um disco dados requer vários passos por uma ordem de conjunto. Em primeiro lugar desligar o disco de máquina virtual, em seguida, eliminar o disco e, em seguida, elimine o ficheiro. vhd.

>[AZURE.WARNING] Se eliminar um ficheiro. vhd origem a partir do armazenamento ou eliminar a sua conta de armazenamento, a Microsoft não pode recuperar esses dados por si.



## <a name="next-steps"></a>Próximos passos
-  [Anexar um disco](virtual-machines-windows-attach-disk-portal.md) adicionar armazenamento adicional para o seu VM.
-  [Carregar uma imagem VM do Windows Azure](virtual-machines-windows-upload-image.md) para utilizar ao criar uma nova VM.
-  [Alterar a letra da unidade de disco temporário Windows](virtual-machines-windows-classic-change-drive-letter.md) para a aplicação possa utilizar na unidade d: para os dados.
