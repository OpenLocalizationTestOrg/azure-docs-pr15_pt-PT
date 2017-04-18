<properties
    pageTitle="Diferentes formas de criar uma VM Windows | Microsoft Azure"
    description="Lista as várias formas de criar uma máquina de virtual do Windows com o Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Diferentes formas de criar uma máquina de virtual do Windows com o Gestor de recursos

Azure oferece várias formas de criar uma máquina virtual porque máquinas virtuais são adequadas para diferentes utilizadores e efeitos. Isto significa que precisa de efetuar algumas opções sobre a máquina virtual e como criá-lo. Este artigo fornece-lhe um resumo destas opções e ligações para instruções.

## <a name="azure-portal"></a>Portal do Azure

Utilizando o portal Azure é uma forma simples para experimentar uma máquina virtual, sobretudo se o é inexperiente com Azure. 

[Criar uma máquina de virtual a executar o Windows através do portal](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Modelo

Máquinas virtuais requerem uma combinação de recursos (tal como uma disponibilidade e conjuntos de contas de armazenamento). Em vez de implementar e gerir cada recurso em separado, pode criar um modelo de Gestor de recursos do Azure que ser implementada e disposições todos os recursos numa única e coordenado operação.

- [Criar uma máquina de virtual do Windows com um modelo de Gestor de recursos](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>Azure PowerShell

Se preferir trabalhar numa shell de comandos, pode utilizar o PowerShell Azure.

- [Criar uma VM do Windows através do PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

Utilize o Visual Studio para criar, gerir e implemente VMs com as ferramentas do Azure para Visual Studio e o SDK do Azure.

[Azure ferramentas para o Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)

