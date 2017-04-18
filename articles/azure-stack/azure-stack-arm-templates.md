<properties
    pageTitle="Utilizar os modelos de Gestor de recursos do Azure no Azure pilha (os programadores de inquilino) | Microsoft Azure"
    description="Saiba como utilizar modelos de Gestor de recursos do Azure Azure empilhados para implementar e aprovisionar todos os recursos para a sua aplicação numa operação de única e coordenada."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utilizar modelos de Gestor de recursos do Azure Azure empilhados

Modelos de Gestor de recursos Azure implementar e aprovisionar todos os recursos para a sua aplicação numa operação de única e coordenada. Definir os recursos para a aplicação e como será implementada.  Também pode implementar modelos para efetuar alterações aos recursos no grupo de recursos.

Podem ser implementados estes modelos com o portal do Microsoft Azure pilha, PowerShell, a linha de comandos e Visual Studio.

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

Os seguintes modelos estão disponíveis em [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Implementar o SharePoint (que não sejam alta disponibilidade)

Utilize a extensão do PowerShell DSC para criar um farm do SharePoint 2013 que inclui o seguinte:

-   Uma rede virtual

-   Três contas de armazenamento

-   Duas balanceadores de carga externos

-   Um VM configurado como um controlador de domínio para uma nova floresta com um único domínio

-   Um VM configurado como um servidor de versão autónomo do SQL Server 2014

-   Um VM configurado como um farm do SharePoint 2013 de um computador

## <a name="deploy-ad-non-high-availability"></a>Implementar AD (que não sejam alta disponibilidade)

Utilize a extensão do PowerShell DSC para criar um servidor do controlador de domínio AD que inclui o seguinte:

-   Uma rede virtual

-   Uma conta de armazenamento

-   Um balanceador de carga externos

-   Um VM configurado como um controlador de domínio para uma nova floresta com um único domínio

## <a name="deploy-adsql-non-high-availability"></a>Implementar AD/SQL (que não sejam alta disponibilidade)

Utilize a extensão do PowerShell DSC para criar um servidor de autónomo 2014 do SQL Server que inclui o seguinte:

-   Uma rede virtual

-   Duas contas de armazenamento

-   Um balanceador de carga externos

-   Um VM configurado como um controlador de domínio para uma nova floresta com um único domínio

-   Um VM configurado como um servidor de versão autónomo do SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utilize a extensão do PowerShell DSC para configurar uma máquina virtual existente Gestor de configuração Local (MMC) e registe-o para uma Azure automatização DSC separar servidor da conta.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Criar uma máquina virtual a partir de uma imagem de utilizador

Crie uma máquina virtual a partir de uma imagem de utilizador personalizada. Este modelo também ser implementada uma rede virtual (com DNS), o endereço IP público e uma interface de rede.

## <a name="simple-vm"></a>Simple VM

Implemente uma VM Windows simples que inclui uma rede virtual (com DNS), o endereço IP público e uma interface de rede.

## <a name="cancel-a-running-template-deployment"></a>Cancelar uma implementação do modelo em execução

Para cancelar uma implementação do modelo em execução, utilize o `Stop-AzureRmResourceGroupDeployment` cmdlet do PowerShell.


## <a name="next-steps"></a>Próximos passos

[Implementar modelos com o portal](azure-stack-deploy-template-portal.md)

[Descrição geral do Gestor de recursos Azure](../azure-resource-manager/resource-group-overview.md)

