<properties
   pageTitle="Utilizar imagens de cliente do Windows para cenários de Dev Center/teste | Microsoft Azure"
   description="Como utilizar benefícios da subscrição Visual Studio para implementar o Windows 7/8/10 no Azure para Dev Center/testar cenários"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>Utilizar o cliente do Windows no Azure para cenários de teste/Dev Center

Pode utilizar o Windows 7, Windows 8, ou o Windows 10 no Azure para Dev Center/testar cenários desde que tenha uma subscrição do Visual Studio (anteriormente MSDN) adequada. Este artigo descreve os requisitos de elegibilidade para o cliente do Windows em execução no Azure e a utilização das imagens Azure galeria.


## <a name="subscription-eligibility"></a>Elegibilidade da subscrição
Ativos subscritores do Visual Studio (pessoas que tem adquirido uma licença de subscrição do Visual Studio) podem utilizar o cliente do Windows para desenvolvimento efeitos e teste. Cliente do Windows pode ser utilizado no seu próprio hardware e Azure máquinas virtuais a ser executada em qualquer tipo de subscrição Azure. Cliente do Windows não pode ser implementado ou utilizado no Azure para uso de produção normal ou utilizado por pessoas que não estão subscritores do Visual Studio ativos.

Para sua comodidade, podemos efetuadas determinadas imagens do Windows 10 disponível a partir da Galeria de Azure dentro [oferece elegível Dev Center/testar](#eligible-offers). Visuais Studio os subscritores dentro de qualquer tipo de oferta também podem [adequadamente preparar e criar](virtual-machines-windows-prepare-for-upload-vhd-image.md) um 64-bit Windows 7, Windows 8, ou imagem do Windows 10 e, em seguida, [carregar para o Azure](virtual-machines-windows-upload-image.md). A utilização permanece limitada a Dev Center/testar pelo subscritores do Visual Studio ativos.


## <a name="eligible-offers"></a>Ofertas elegíveis
A tabela seguinte apresenta detalhes sobre a oferta IDs são elegíveis para implementar o Windows 10 através da Galeria de Azure. As imagens do Windows 10 só estão visíveis para as seguintes ofertas. Os subscritores do visuais Studio que precisa para executar o Windows client num tipo de oferta diferentes necessitam que [adequadamente preparar e criar](virtual-machines-windows-prepare-for-upload-vhd-image.md) uma 64-bit Windows 7, Windows 8 ou Windows 10 imagem e [, em seguida, carregar para o Azure](virtual-machines-windows-upload-image.md).

| Nome da oferta | Número de oferta | Imagens de cliente disponíveis |
|:-----------|:------------:|:-----------------------:|
| [Repartição Dev Center/testar](https://azure.microsoft.com/offers/ms-azr-0023p/)                          | 0023P | Windows 10 |
| [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)      | 0029P | Windows 10 |
| [Subscritores do visuais Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          | 0059P | Windows 10 |
| [Subscritores do visuais Studio teste Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     | 0060P | Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/)       | 0061P | Windows 10 |
| [Subscritores do visuais Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            | 0063P | Windows 10 |
| [Subscritores do visuais Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Enterprise Dev Center/testar](https://azure.microsoft.com/ofers/ms-azr-0148p/)                              | 0148P | Windows 10 |


## <a name="check-your-azure-subscription"></a>Verificar a sua subscrição do Azure
Se não souber o seu ID de oferta, pode obtê-lo através do portal do Azure ou o portal de conta.

O ID da subscrição oferta é assinalado nos pá 'Subscrições' no interior do Azure portal:

![Detalhes de ID de oferta a partir do portal do Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

Também pode ver o ID de oferta no [separador 'Subscrições'](http://account.windowsazure.com/Subscriptions) do portal de conta do Azure:

![Detalhes de ID de oferta a partir do portal de conta do Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 


## <a name="next-steps"></a>Próximos passos
Agora pode implementar os seus VMs utilizando o [PowerShell](virtual-machines-windows-ps-create.md), [modelos de Gestor de recursos](virtual-machines-windows-ps-template.md)ou [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
