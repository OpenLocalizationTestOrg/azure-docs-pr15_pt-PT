<properties
   pageTitle="Criação de modelos com as extensões de Linux VM | Microsoft Azure"
   description="Saiba mais sobre a criação de modelos de Gestor de recursos do Azure com as extensões para Linux VMs"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>Criação de modelos de Gestor de recursos do Azure com as extensões de Linux VM

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

A partir do Azure clip, execute os seguintes comandos:

      Azure VM extension list

Este comando devolver o nome do publisher, o nome da extensão e a versão como seguinte:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Estas três propriedades mapear "o publisher", "tipo" e "typeHandlerVersion", respetivamente o fragmento de modelo acima.

>[AZURE.NOTE]É recomendado sempre utilizar a versão mais recente da extensão para obter a funcionalidade mais atualizada.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificar o esquema para os parâmetros de configuração de extensão

Com a criação de um modelo de extensão, o próximo passo é identificar o formato para fornecer os parâmetros de configuração. Cada extensão suporta o seu próprio conjunto de parâmetros.

Para ver as configurações de exemplo para as extensões de Linux, clique na documentação para [Linux eExtensions amostras](virtual-machines-linux-extensions-configuration-samples.md), consulte.

Consulte o seguinte para obter um modelo com as extensões de VM totalmente concluído.

[Extensão de scripts personalizados numa VM Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Depois do modelo de criação, pode implementá-lo utilizando o clip do Azure.
