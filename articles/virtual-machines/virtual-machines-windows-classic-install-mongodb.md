<properties
    pageTitle="Instalar MongoDB num Windows VM | Microsoft Azure"
    description="Saiba como instalar MongoDB numa VM Azure criados com o modelo clássico de implementação de executar o Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>Instalar MongoDB num Windows VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para instalar e configurar MongoDB utilizando o modelo de implementação do Gestor de recursos, consulte [Este artigo](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB] [ MongoDB] é uma popular abrir origem alto desempenho NoSQL base de dados. Este artigo orienta-criar uma máquina de virtual do Windows Server (VM) utilizando o [portal clássica Azure][AzurePortal]. Em seguida, criar e anexar um disco de dados para a VM antes de instalar e configurar MongoDB. Se tiver uma VM existente no Azure que pretende utilizar, pode ir diretamente para [instalar e configurar MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Criar uma máquina de virtual com o Windows Server

Siga estas instruções para criar uma máquina virtual.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Pode adicionar um ponto final para MongoDB enquanto cria a máquina virtual e configure-o da seguinte forma:-lhe o nome como **Mongo**, utilizar **TCP** como o protocolo e definir portas públicas e privadas para **27017**.

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Para fornecer armazenamento para a máquina virtual, anexar um disco de dados e, em seguida, iniciá-lo para que o Windows pode utilizá-lo. Se já tiver um disco de dados, pode anexar desse disco existente ou pode anexar um disco vazio.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obter instruções sobre a inicialização do disco, consulte o artigo "como: iniciar um novo disco de dados no Windows Server" no [como anexar um disco de dados para uma máquina de virtual do Windows](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Instalar e executar MongoDB na máquina virtual

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Resumo
Neste tutorial, aprendeu como criar uma máquina de virtual com o Windows Server, remotamente ligá-la e anexar um disco de dados.  Aprendeu também como instalar e configurar MongoDB na máquina virtual baseados no Windows. Já pode aceder a MongoDB na máquina de virtual baseados no Windows, seguindo os tópicos avançados na [documentação MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com
