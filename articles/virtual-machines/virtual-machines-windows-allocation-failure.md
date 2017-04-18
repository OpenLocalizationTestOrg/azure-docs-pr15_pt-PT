<properties
    pageTitle="Resolução de problemas de falhas de alocação Windows VM | Microsoft Azure"
    description="Resolver problemas de falhas de alocação quando criar, reinicie ou redimensionar um VM do Windows no Azure"
    services="virtual-machines-windows, azure-resource-manager"
    documentationCenter=""
    authors="JiangChen79"
    manager="felixwu"
    editor=""
    tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cjiang"/>

# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Resolver problemas de falhas de alocação quando criar, reinicie ou redimensionar Windows VMs no Azure

Quando criar uma VM, reinicie o VMs parados (desatribuídos) ou redimensionar uma VM, Microsoft Azure atribui recursos de cluster à sua subscrição. Poderá, ocasionalmente, receber erros ao efetuar estas operações – mesmo antes de atingir os limites de subscrição Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possível remediação. As informações também poderão ser útil quando planear a implementação dos seus serviços. Também pode [resolver problemas de alocação falhas quando cria, reiniciar ou redimensionar Linux VMs no Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]
