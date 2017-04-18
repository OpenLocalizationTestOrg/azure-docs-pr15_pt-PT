<properties
    pageTitle="Configurar os pontos finais numa VM Linux clássica | Microsoft Azure"
    description="Saiba como configurar os pontos finais para um VM Linux no portal do Azure clássico para permitir a comunicação com uma máquina de virtual Linux no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Como configurar os pontos finais numa Linux clássica máquina virtual no Azure

Máquinas virtuais Linux todos os que criar no Azure utilizando o modelo de implementação clássica automaticamente podem comunicar através de um canal de rede privada com outros máquinas virtuais no serviço na nuvem mesmo ou rede virtual. No entanto, os computadores na Internet ou noutras redes virtuais requerem os pontos finais para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas de virtuais do Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]No modelo de implementação do **Gestor de recursos** , pontos finais são configurados utilizando **Grupos de segurança de rede (NSGs)**. Para obter mais informações, consulte o artigo [abrir portas e os pontos finais] (virtual-máquinas-linux-nsg-quickstart.md).

Quando cria uma máquina de virtual Linux no portal do Azure clássico, um ponto final para Shell seguro (SSH) é normalmente criado por si automaticamente. Pode configurar os pontos finais adicionais ao criar a máquina virtual ou posteriormente, conforme seja necessário.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximos passos

* Também pode criar um ponto final de VM utilizando a [Interface de comandos do Azure](../virtual-machines-command-line-tools.md). Execute o comando **criar de ponto final de azure vm** .

* Se tiver criado uma máquina virtual no modelo de implementação de Gestor de recursos, pode utilizar o clip Azure no modo de Gestor de recursos para [criar grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-cli.md) para o tráfego de controlo para a VM.