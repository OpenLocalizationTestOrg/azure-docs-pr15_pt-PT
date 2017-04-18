<properties
   pageTitle="Criação de modelos com as extensões de Windows VM | Microsoft Azure"
   description="Saiba mais sobre a criação de modelos de Gestor de recursos do Azure com as extensões para VMs do Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Criação de modelos de Gestor de recursos do Azure com as extensões de VM do Windows

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

A partir do Azure PowerShell, execute o seguinte cmdlet do Azure PowerShell:

      Get-AzureVMAvailableExtension


Este cmdlet devolve o nome do publisher, o nome da extensão e a versão da seguinte forma:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Estas três propriedades mapear "o publisher", "tipo" e "typeHandlerVersion", respetivamente o fragmento de modelo acima.

>[AZURE.NOTE]É recomendado sempre para utilizar a versão mais recente da extensão para obter a funcionalidade mais atualizada.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificar o esquema para os parâmetros de configuração de extensão

Com a criação de um modelo de extensão, o próximo passo é identificar o formato para fornecer os parâmetros de configuração. Cada extensão suporta o seu próprio conjunto de parâmetros.

Para ver configurações de exemplo para extensões do Windows, consulte o artigo [exemplos de extensões do Windows](virtual-machines-windows-extensions-configuration-samples.md).


Consulte o seguinte para obter um modelo com as extensões de VM totalmente concluído.

[Extensão de Script personalizado num Windows VM](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Depois do modelo de criação, pode implemente-o através do Azure PowerShell.
