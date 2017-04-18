<properties
   pageTitle="Seleccione Linux VM imagens com o clip do Azure | Microsoft Azure"
   description="Saiba como determinar o publisher, oferta e SKU para imagens ao criar uma máquina de virtual Linux com o modelo de implementação do Gestor de recursos."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="select-linux-vm-images-with-the-azure-cli"></a>Seleccione Linux VM imagens com o clip do Azure

Este tópico descreve como localizar fabricantes, ofertas, skus e versões para cada localização em que poderá implementar. Para dar um exemplo, algumas imagens Linux VM utilizadas mais frequentemente são:

**Tabela de utilizadas mais frequentemente Linux imagens**


| PublisherName                        | Oferta                                 | SKU                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| Sistema RedHat                           | RHEL                                       | 7.2                              |
| credativ                         | Debian                                     | 8                                | 
| SUSE                             | openSUSE                                   | 13.2                             |
| SUSE                             | SLES                                       | 12-SP1                           |
| OpenLogic                        | CentOS                                     | 7.1                              |
| Canónico                        | UbuntuServer                               | 14.04.4-LTS                      |
| CoreOS                           | CoreOS                                     | Estável                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
