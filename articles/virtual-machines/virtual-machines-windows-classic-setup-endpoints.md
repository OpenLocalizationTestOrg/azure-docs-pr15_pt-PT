<properties
    pageTitle="Configurar os pontos finais numa VM clássica do Windows | Microsoft Azure"
    description="Saiba como configurar os pontos finais para um VM do Windows no portal do Azure clássico para permitir a comunicação com uma máquina de virtual do Windows no Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Como configurar os pontos finais numa máquina de virtual clássica do Windows no Azure


Todas as janelas máquinas virtuais que criar no Azure utilizando o modelo de implementação clássica automaticamente pode comunicam através de um privado canal de rede com outros máquinas virtuais no serviço na nuvem mesmo ou rede virtual. No entanto, os computadores na Internet ou noutras redes virtuais requerem os pontos finais para direcionar o tráfego de rede de entrada para uma máquina virtual. Este artigo também está disponível para [máquinas virtuais de Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]No modelo de implementação do **Gestor de recursos** , pontos finais são configurados utilizando **Grupos de segurança de rede (NSGs)**. Para obter mais informações, consulte o artigo [permitir externo acesso ao seu VM utilizando o Portal do Azure] (virtual-machines-windows-nsg-quickstart-portal.md).

Quando cria uma máquina de virtual do Windows no portal do Azure clássico, pontos finais comuns, tal como aqueles para ambiente de trabalho remoto e o Windows PowerShell remoto são normalmente criados por si automaticamente. Pode configurar os pontos finais adicionais ao criar a máquina virtual ou posteriormente, conforme seja necessário.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Próximos passos

* Para utilizar um cmdlet do PowerShell do Azure para configurar um ponto final de VM, consulte o artigo [Adicionar AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Para utilizar um cmdlet do PowerShell do Azure para gerir uma ACL num ponto final, consulte o artigo [Gerir o controlo de acesso listas (ACL) para os pontos finais ao utilizar o PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Se tiver criado uma máquina virtual no modelo de implementação de Gestor de recursos, pode utilizar o Azure PowerShell para [criar grupos de segurança de rede](../virtual-network/virtual-networks-create-nsg-arm-ps.md) para o tráfego de controlo para a VM.
