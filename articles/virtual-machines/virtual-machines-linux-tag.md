<properties
   pageTitle="Como marcar uma máquina de virtual Linux | Microsoft Azure"
   description="Saiba mais acerca da marcação de uma máquina de virtual Linux criada no Azure utilizando o modelo de implementação do Gestor de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Como marcar uma máquina de virtual Linux no Azure

Este artigo descreve várias formas de marcar uma máquina de virtual Linux no Azure através do modelo de implementação do Gestor de recursos. Etiquetas são pares de chave/valor definido pelo utilizador que podem ser colocados diretamente num recurso ou um grupo de recursos. Azure atualmente suporta até 15 marcas por recurso e grupo de recursos. Etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente. Tenha em atenção, as etiquetas são suportadas para recursos criados através do apenas o modelo de implementação do Gestor de recursos.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Etiquetas com clip Azure

Para começar, [instalar e configurar o clip do Azure](../xplat-cli-azure-resource-manager.md) e certifique-se de que está no modo de Gestor de recursos (`azure config mode arm`).

Pode ver todas as propriedades de um determinado Máquina Virtual, incluindo as etiquetas, utilizar este comando:

        azure vm show -g MyResourceGroup -n MyTestVM

Para adicionar uma nova etiqueta VM através do clip do Azure, pode utilizar o `azure vm set` comando juntamente com o parâmetro de etiqueta **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Para remover todas as etiquetas, pode utilizar o parâmetro **– T** na `azure vm set` comando.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Agora que recomendamos aplicou etiquetas a nossa recursos clip Azure e o Portal, vamos ver os detalhes de utilização para ver as marcas no portal do faturação.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Próximos passos

* Para saber mais acerca da marcação dos recursos do Azure, consulte o artigo [Descrição geral do Gestor de recursos do Azure][] e [Utilizar etiquetas para organizar os recursos do Azure][].
* Para ver como as etiquetas podem ajudar a gerir a utilização de recursos Azure, consulte [Noções sobre a sua fatura do Azure][] e [Obtenha informações para o consumo de recursos do Microsoft Azure][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Descrição geral do Gestor de recursos do Azure]: ../azure-resource-manager/resource-group-overview.md
[Utilizar etiquetas para organizar os recursos do Azure]: ../resource-group-using-tags.md
[Noções sobre a sua fatura Azure]: ../billing/billing-understand-your-bill.md
[Informações de lucro para o consumo de recursos do Microsoft Azure]: ../billing-usage-rate-card-overview.md
