<properties
    pageTitle="Implementar uma VM utilizando uma palavra-passe armazenada num Azure pilha chave Cofre | Microsoft Azure"
    description="Saiba como implementar uma VM utilizando uma palavra-passe armazenada num Azure pilha chave Cofre"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Implementar uma VM através da obtenção a palavra-passe armazenada num cofre de tecla

Quando precisar de transmitir um valor seguro (como uma palavra-passe) como um parâmetro durante a implementação, pode armazenar esse valor como uma palavra-passe num cofre de chave pilha de Azure e fazer referência o valor de outros modelos de Gestor de recursos do Azure. Incluir apenas uma referência para o segredo no seu modelo para que o segredo nunca é exposto. Não necessita de introduzir manualmente o valor para o segredo sempre que implementar os recursos. Pode especificar quais os utilizadores ou principais de serviço podem aceder a palavra-passe.

## <a name="reference-a-secret-with-static-id"></a>Referência de uma palavra-passe com o ID estático

Referenciar o segredo a partir de um ficheiro de parâmetros, o que transmite valores para o modelo. Referenciar o segredo passando o identificador de recursos do cofre chave e o nome do segredo. Neste exemplo, o segredo cofre chave tem de existir. Utilizar um valor estático para o seu ID do recurso.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]O parâmetro que aceita o segredo deve ser um *securestring*.

## <a name="next-steps"></a>Próximos passos
[Implementar uma aplicação de exemplo com chave Cofre](azure-stack-kv-sample-app.md)

[Implementar uma VM com um certificado de chave Cofre](azure-stack-kv-push-secret-into-vm.md)

