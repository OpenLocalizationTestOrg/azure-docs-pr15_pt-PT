<properties
    pageTitle="Implementar modelos com a linha de comandos na pilha de Azure | Microsoft Azure"
    description="Saiba como utilizar a interface de linha de comandos em diferentes plataformas (CLI) para implementar modelos de dentro de ClientVM ou depois de utilizar a ligação VPN para ligar à Azure pilha."
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
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implementar modelos de empilhados Azure utilizando a linha de comandos

Utilize a linha de comandos para implementar modelos de Gestor de recursos do Azure o conceito de pilha Azure. Modelos de Gestor de recursos Azure implementar e aprovisionar todos os recursos para a sua aplicação numa operação de única e coordenada.

## <a name="download-template"></a>Transferir o modelo        
Para testar uma implementação com o clip, transfira a azuredeploy.json de ficheiros e azuredeploy.parameters.json a partir de [criar o modelo de exemplo de conta de armazenamento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Implementar o modelo
Navegue para a pasta onde estes ficheiros foram transferidos e execute o seguinte comando para implementar o modelo:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Este comando implementa o modelo de grupo de recursos **cliRG** na localização predefinida do conceito de pilha de Azure.

## <a name="validate-template-deployment"></a>Validar a implementação de modelo
Para ver esta conta de grupo e armazenamento de recursos, utilize os comandos seguintes:

    azure group list

    azure storage account list

## <a name="next-steps"></a>Próximos passos

[Gerir permissões de utilizador](azure-stack-manage-permissions.md)
