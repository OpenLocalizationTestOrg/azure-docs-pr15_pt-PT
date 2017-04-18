<properties
    pageTitle="Configurar o Cofre chave para máquinas virtuais no Gestor de recursos do Azure | Microsoft Azure"
    description="Como configurar o Cofre chave para utilizar com uma máquina de virtual do Gestor de recursos do Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o Cofre chave para máquinas virtuais no Gestor de recursos do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássico

Gestor de recursos do Azure empilhados, segredos/certificados são considerados recursos que são fornecidos pelo fornecedor de recursos de chave do cofre. Para saber mais sobre cofre chave, consulte o artigo [o que é o Azure chave cofre?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Ordem para cofre chave ser utilizado com máquinas virtuais de Gestor de recursos do Azure, a propriedade do **EnabledForDeployment** Cofre de chave tem de estar definida como verdadeiro. Pode fazê-lo em vários clientes.
>
>2. O Cofre de chave tem de ser criada na mesma subscrição e localização como a Máquina Virtual.

## <a name="use-powershell-to-set-up-key-vault"></a>Utilizar o PowerShell para configurar o Cofre chave
Para criar uma chave cofre utilizando o PowerShell, consulte o artigo [Introdução ao Azure chave cofre](../key-vault/key-vault-get-started.md#vault).

Para o novos cofres chaves, pode utilizar este cmdlet do PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para cofres chaves existentes, pode utilizar este cmdlet do PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>CLIP para configurar o chave cofre-nos
Para criar uma chave cofre utilizando a interface de linha de comandos (CLI), consulte o artigo [Gerir Cofre de palavras chave utilizando clip](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Para clip, tem de criar o Cofre chave antes de atribuir a política de implementação. Pode fazê-lo utilizando o seguinte comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilize modelos para configurar o Cofre chave
Enquanto a utilizar um modelo, tem de definir o `enabledForDeployment` propriedade para `true` para o recurso de chave cofre.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para obter outras opções que pode configurar quando cria uma chave cofre utilizando modelos, consulte o artigo [criar uma chave cofre](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
