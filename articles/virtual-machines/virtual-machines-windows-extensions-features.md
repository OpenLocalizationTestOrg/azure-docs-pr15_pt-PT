<properties
 pageTitle="Funcionalidades e extensões de máquina virtual | Microsoft Azure"
 description="Saiba que extensões estão disponíveis para máquinas virtuais Azure, agrupadas pelo que forneça ou melhorar."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/30/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Sobre funcionalidades e extensões de máquina virtual

## <a name="azure-vm-extensions"></a>Extensões Azure VM

Azure extensões de Máquina Virtual são pequenas aplicações que fornecem post implementação automatização e configuração da tarefa em máquinas virtuais do Azure. Por exemplo, se uma Máquina Virtual necessitar de software para ser instalada, anti-virus proteção ou configuração Docker, uma extensão VM pode ser utilizada para concluir estas tarefas. Azure extensões VM podem ser executadas utilizando o clip do Azure, PowerShell, gerir recurso modelos e o portal do Azure. Extensões podem ser agrupadas com uma nova implementação de máquina virtual ou executar contra qualquer sistema existente.

Este artigo fornece pré-requisitos para a extensão de máquina de Virtual do Azure e orientações sobre como detetar extensões VM disponíveis. 

## <a name="azure-vm-agent"></a>Agente Azure VM

O agente de VM Azure gere interação entre uma máquina de Virtual do Azure e o controlador de ferro do Azure. O agente VM é responsável por muitos aspetos funcionais da implementar e gerir máquinas virtuais do Azure, incluindo com as extensões de VM. O agente de VM Azure previamente estiver instalado no Azure Galeria imagens e pode ser instalado em sistemas operativos suportados. 

Para obter informações sobre os sistemas operativos suportados e instruções de instalação, consulte o artigo [Agente de Máquina Virtual Azure](./virtual-machines-windows-classic-agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Descobrir o VM extensões

Muitos extensões VM diferentes estão disponíveis para utilização com máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando com o clip do Azure, substituindo a localização de uma localização à escolha.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Extensões VM comuns

|Extensão de nome   |Descrição   |Obter mais informações   |
|---|---|---|
|Extensão de scripts personalizados para Windows  | Executar scripts contra uma máquina de Virtual do Azure  |[Extensão de scripts personalizados para Windows](./virtual-machines-windows-extensions-customscript.md)   |
|DSC extensão para Windows | Extensão do PowerShell DSC (configuração pretendida estada).  | [Extensão VM docker](./virtual-machines-windows-extensions-dsc-overview.md)  |
|Extensão de diagnóstico Azure | Gerir diagnósticos do Azure |[Extensão de diagnóstico Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
