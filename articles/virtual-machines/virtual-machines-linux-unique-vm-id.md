<properties
   pageTitle="Aceder a VM ID"
   description="Descreve aceder e utilizar a ID exclusivo Azure VM"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# <a name="accessing-and-using-azure-vm-unique-id"></a>Aceder e utilizar a ID exclusivo Azure VM

ID exclusivo de VM Azure é um identificador de 128 codificado e armazenadas no SMBIOS do VM do todos os Azure IaaS e atualmente pode ser lidos com comandos de BIOS plataforma.

ID exclusivo de VM Azure é uma propriedade só de leitura. Não altera Azure ID exclusivo da VM aquando do encerramento reinicie (quer planeados para não planeado), início/fim retirar a atribuição, a reparação do serviço ou restaurar no local. No entanto, se a VM é um instantâneo e copiados para criar uma nova instância, novo Azure VM ID está configurado.

> [AZURE.NOTE] Se tiver mais antigos VMs criado e executar uma vez que esta nova funcionalidade tem lançada (18 de Setembro de 2014), consulte reiniciar a VM para obter um Azure automaticamente exclusivo ID.


Para aceder ao Azure VM ID exclusivo de dentro da VM:


## <a name="create-a-vm"></a>Criar uma VM
 

Para mais informações, consulte o artigo [criar uma Máquina Virtual](virtual-machines-linux-creation-choices.md)


## <a name="connect-to-the-vm"></a>Ligar para a VM
 

Para obter mais informações, consulte o artigo [SSH a partir do Linux](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="query-vm-unique-id"></a>ID exclusivo de VM de consulta

Comando (exemplo utiliza **Ubuntu**):

    sudo dmidecode | grep UUID
    
Exemplo resultados esperados:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Devido ao grande Endian bit corrigida, o ID exclusivo da VM real neste caso será:

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
ID exclusivo de VM Azure pode ser utilizado em cenários diferentes se a VM está em execução no Azure ou no local e pode ajudar os seus requisitos de controlo de licenciamento, relatórios ou gerais que tiver no seu implementações Azure IaaS. Muitos fornecedores de software independentes criação de aplicações e certificá-los no Azure precisem para identificar um VM Azure durante o ciclo de vida e indicar ao se a VM está em execução no Azure, no local ou em outros fornecedores de nuvem. Por exemplo pode ajudar este identificador de plataforma detetar se o software está devidamente licenciado ou a ajuda a quaisquer dados VM a sua origem, como se relacionam que o ajudarão sobre a definição de métricas de à direita para a plataforma certa e com controlar e correlacionar estes métricas a outras utilizações.
