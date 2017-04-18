<properties
 pageTitle="Funcionalidades e extensões de máquina virtual | Microsoft Azure"
 description="Saiba que extensões estão disponíveis para máquinas virtuais Azure, agrupadas pelo que forneça ou melhorar."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Sobre funcionalidades e extensões de máquina virtual

## <a name="azure-vm-extensions"></a>Extensões Azure VM

Azure extensões de Máquina Virtual são pequenas aplicações que fornecem post implementação automatização e configuração da tarefa em máquinas virtuais do Azure. Por exemplo, se uma Máquina Virtual necessitar de software para ser instalada, anti-virus proteção ou configuração Docker, uma extensão VM pode ser utilizada para concluir estas tarefas. Azure extensões VM podem ser executadas utilizando o clip do Azure, PowerShell, gerir recurso modelos e o portal do Azure. Extensões podem ser agrupadas com uma nova implementação de máquina virtual ou executar contra qualquer sistema existente.

Este artigo fornece pré-requisitos para a extensão de máquina de Virtual do Azure e orientações sobre como detetar extensões VM disponíveis. 

## <a name="azure-vm-agent"></a>Agente Azure VM

O agente de VM Azure gere interação entre uma máquina de Virtual do Azure e o controlador de ferro do Azure. O agente VM é responsável por muitos aspetos funcionais da implementar e gerir máquinas virtuais do Azure, incluindo com as extensões de VM. O agente de VM Azure previamente estiver instalado no Azure Galeria imagens e pode ser instalado em sistemas operativos suportados. 

Para obter informações sobre os sistemas operativos suportados e instruções de instalação, consulte o artigo [Guia do Azure Linux agente de utilizador](./virtual-machines-linux-agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Descobrir o VM extensões

Muitos extensões VM diferentes estão disponíveis para utilização com máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando com o clip do Azure, substituindo a localização de uma localização à escolha.

```none
azure vm extension-image list westus
```

<br />

## <a name="common-vm-extensions"></a>Extensões VM comuns

|Extensão de nome   |Descrição   |Obter mais informações   |
|---|---|---|
|Extensão de scripts personalizados para Linux  | Executar scripts contra uma máquina de Virtual do Azure  |[Extensão de scripts personalizados para Linux](./virtual-machines-linux-extensions-customscript.md)   |
|Extensão de docker |Instala o daemon Docker para suportar comandos Docker remotos.  | [Extensão VM docker](./virtual-machines-linux-dockerextension.md)  |
|Extensão do Access VM | Recuperar o acesso a máquina de Virtual do Azure  |[Extensão do Access VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
|Extensão de diagnóstico Azure |Gerir diagnósticos do Azure |[Extensão de diagnóstico Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

