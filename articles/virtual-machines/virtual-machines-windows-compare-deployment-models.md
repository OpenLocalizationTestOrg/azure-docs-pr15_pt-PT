<properties
   pageTitle="Fornecedores de cluster, rede e armazenamento | Microsoft Azure"
   description="Descrição geral conceptual do cluster, a rede e a fornecedores de recursos de armazenamento (CRP, NRP e SRP) para aplicações do Windows no modelo de implementação do Gestor de recursos do Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="tomfitz"/>

# <a name="azure-compute-network-and-storage-providers-for-windows-applications-under-azure-resource-manager-deployment-model"></a>Aplicações de cluster, rede e armazenamento fornecedores para o Windows Azure em modelo de implementação do Gestor de recursos do Azure

A inclusão de capacidades cluster, rede e armazenamento com o modelo de implementação do Gestor de recursos do Azure bastante irá simplificar a implementação e gestão de aplicações complexas em execução no IaaS. Muitas aplicações requerem uma combinação de recursos, incluindo uma rede Virtual, conta de armazenamento, Máquina Virtual e uma Interface de rede. O modelo de implementação do Gestor de recursos do Azure oferece a capacidade de construir um modelo JSON para implementar e gerir todos estes recursos em conjunto como uma única aplicação.

[AZURE.INCLUDE [virtual-machines-common-compare-deployment-models](../../includes/virtual-machines-common-compare-deployment-models.md)]
