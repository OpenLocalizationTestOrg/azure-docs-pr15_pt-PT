<properties
    pageTitle="Linux convidados na pilha Azure | Microsoft Azure"
    description="Saiba como criar baseado em Linux máquinas virtuais no Azure pilha."
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>Implementar máquinas virtuais de Linux na pilha de Azure

Pode implementar Linux máquinas de virtuais no conceito de pilha Azure ao adicionar uma imagem baseada em Linux para a pilha de Azure Marketplace. Vários fornecedores Linux forneceram imagens que podem ser adicionadas para um conceito de pilha Azure ou pode criar o seu próprio.

## <a name="download-an-image"></a>Transferir uma imagem

 1. Transferir e extraia uma imagem Azure pilha compatível com as ligações seguintes ou preparar o seu próprio:
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. Extrai a imagem VHD se for necessário e, em seguida, [Adicionar a imagem de mercado](azure-stack-add-vm-image.md). Certifique-se de que o `OSType` parâmetro estiver definido para `Linux`.
 
 3. Depois de adicionar a imagem para mercado, é criado um item de Marketplace e pode implementar uma máquina de virtual Linux.
  
## <a name="prepare-your-own-image"></a>Preparar a sua própria imagem

1. Prepare a sua própria imagem Linux utilizando um dos seguintes instruções:
 - [Com base em centOS distribuições](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Debian Linux](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Oracle Linux](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [Chapéu vermelho Enterprise Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES & openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. Transferir e instalar o [Azure Linux agente](https://github.com/Azure/WALinuxAgent/)

    O agente de Linux Azure versão 2.1.3 ou superior é necessária para aprovisionar o seu VM Linux na pilha de Azure. Muitos das distribuições listadas em cima já incluem esta versão do agente de ou superior como um pacote de nos seus repositórios (normalmente denominado `WALinuxAgent` ou `walinuxagent`). No entanto, se a versão do pacote do agente Azure é menor que 2.1.3 (ou seja, 2.0.18 ou inferior), em seguida, tem de instalar o agente manualmente. Pode ser determinada a versão instalada a partir do nome do pacote ou através da execução `/usr/sbin/waagent -version` na VM.

    Siga as instruções abaixo para instalar o agente do Azure Linux manualmente-

 - Em primeiro lugar, transfira o agent Azure Linux mais recente a partir de [Github](https://github.com/Azure/WALinuxAgent/releases), exemplo:

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - Descompacta o Azure agent:

            # tar -vzxf v2.2.0.tar.gz

 - Instalar python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - Instale o Azure agent:

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    Sistemas com Python 2. x e Python 3. x instalado lado a lado poderão ter de execute o seguinte comando:

        # sudo python3 setup.py install --register-service

    Para obter mais informações, consulte o agente de Linux Azure [Leia-me](https://github.com/Azure/WALinuxAgent/blob/master/README.md).

3. [Adicionar a imagem para o Marketplace](azure-stack-add-vm-image.md). Certifique-se de que o `OSType` parâmetro estiver definido para `Linux`.

4. Depois de adicionar a imagem para mercado, é criado um item de Marketplace e pode implementar uma máquina de virtual Linux.

## <a name="next-steps"></a>Próximos passos

[Perguntas mais frequentes do Azure pilha](azure-stack-faq.md)