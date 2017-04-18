<properties
    pageTitle="Configurar o Cofre chave para máquinas virtuais no Gestor de recursos do Azure | Microsoft Azure"
    description="Como configurar o Cofre chave para utilizar com uma máquina de virtual do Gestor de recursos do Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o Cofre chave para máquinas virtuais no Gestor de recursos do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássico

Gestor de recursos do Azure empilhados, segredos/certificados são considerados recursos que são fornecidos pelo fornecedor de recursos de chave do cofre. Para saber mais sobre Azure chave cofre, consulte o artigo [o que é o Azure chave cofre?](../key-vault/key-vault-whatis.md)

Ordem para cofre chave ser utilizado com máquinas virtuais de Gestor de recursos do Azure, a propriedade do *EnabledForDeployment* Cofre de chave tem de estar definida como verdadeiro. Pode fazê-lo em vários clientes."

## <a name="use-cli-to-set-up-key-vault"></a>Utilize o clip para configurar o Cofre chave
Para criar uma chave cofre utilizando a interface de linha de comandos (CLI), consulte o artigo [Gerir Cofre de palavras chave utilizando clip](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Para clip, tem de criar o Cofre chave antes de atribuir a política de implementação. Pode fazê-lo utilizando o seguinte comando:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilize modelos para configurar o Cofre chave
Quando utilizar um modelo, tem de definir o `enabledForDeployment` propriedade para `true` para o recurso de chave cofre.

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
