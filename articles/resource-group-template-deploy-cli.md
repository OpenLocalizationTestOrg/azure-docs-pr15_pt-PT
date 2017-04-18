<properties
   pageTitle="Implementar recursos clip do Azure e modelo com | Microsoft Azure"
   description="Utilize o Gestor de recursos do Azure e Azure clip para implementar um recursos Azure. Os recursos são definidos num modelo de Gestor de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementar recursos com os modelos de Gestor de recursos e clip do Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Clip Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

Este tópico explica como utilizar o clip do Azure com modelos de Gestor de recursos para implementar os recursos do Azure.  

> [AZURE.TIP] Para obter ajuda com a depuração de um erro durante a implementação, consulte:
>
> - [Operações de implementação de vista com o Azure clip](resource-manager-troubleshoot-deployments-cli.md) para saber mais sobre como obter as informações que o ajuda a resolver o erro
> - [Resolver problemas de erros comuns quando implementar recursos para Azure com o Gestor de recursos do Azure](resource-manager-common-deployment-errors.md) para saber como resolver erros comuns de implementação

O modelo pode ser um ficheiro local ou um ficheiro externo que está disponível através de um URI. Quando o seu modelo encontra-se numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um token de assinatura (SA) de acesso partilhado durante a implementação.

## <a name="quick-steps-to-deployment"></a>Passos rápidos para implementação

Este artigo descreve a todas as diferentes opções disponíveis para si durante a implementação. No entanto, muitas vezes só necessita de dois comandos simples. Para começar rapidamente com a implementação, utilize os comandos seguintes:

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Para saber mais sobre as opções para implementação que poderá ser mais adequados para o seu cenário, continue a ler neste artigo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>Implementar com clip Azure

Se não tiver anteriormente utilizado Azure clip com o Gestor de recursos, consulte o artigo [utilizar o clip do Azure para Mac, Linux e o Windows com o Azure a gestão de recursos](xplat-cli-azure-resource-manager.md).

1. Inicie sessão na sua conta Azure. Depois de fornecer as suas credenciais, o comando devolve o resultado do seu início de sessão.

        azure login
  
        ...
        info:    login command OK

2. Se tem múltiplas subscrições, fornece o id da subscrição que pretende utilizar para implementação.

        azure account set <YourSubscriptionNameOrId>

3. Mude para o módulo Azure o Gestor de recursos. Receber a confirmação do modo em novo.

        azure config mode arm
   
        info:     New mode is arm

4. Se não tiver um grupo de recursos existente, crie um grupo de recursos. Fornece o nome do grupo de recursos e da localização que precisa para a sua solução. Tem de fornecer uma localização para o grupo de recursos, uma vez que o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, poderá querer especificar a localização onde armazenou esse metadados. Em geral, recomendamos que especifique uma localização onde a maior parte dos seus recursos residirão. Utilizar a mesma localização, pode simplificar o modelo.

        azure group create -n ExampleResourceGroup -l "West US"

     É devolvido um resumo do novo grupo de recursos.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Valide a sua implementação antes de executá-lo ao executar o comando **modelo grupo azure validar** . Quando testar a implementação, fornece parâmetros exatamente como faria ao executar a implementação (mostrada no passo seguinte).

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. Para implementar recursos ao seu grupo de recursos, execute o seguinte comando e forneça os parâmetros necessários. Os parâmetros de incluir um nome para a sua implementação, o nome do seu grupo de recursos, o caminho ou URL para o modelo e quaisquer outros parâmetros necessários para o seu cenário. 
   
     Tem as seguintes três opções para fornecer valores de parâmetros: 

     1. Utilizar parâmetros inline e um modelo local. Cada parâmetro está no formato: `"ParameterName": { "value": "ParameterValue" }`. O exemplo seguinte mostra os parâmetros com carateres de escape.

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Utilizar parâmetros inline e uma ligação a um modelo.

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Utilize um ficheiro de parâmetro. Para obter informações sobre o ficheiro de modelo, consulte o artigo [ficheiro parâmetro](#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Depois dos recursos foram implementados através de um dos três métodos acima, irá ver um resumo da implementação.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Para executar uma implementação concluída, defina o **modo** como **concluída**. Tenha cuidado quando utilizar deste modo como pode eliminar inadvertidamente recursos que não estão no seu modelo.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Se pretender obter informações adicionais sobre a implementação que poderão ajudá-lo a resolver problemas de erros de implementação, utilize o parâmetro de **definição de depuração** de sessão. Pode especificar que conteúdo pedido, o conteúdo de resposta ou ambos ter sessão iniciada com a operação de implementação.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>Implementar o modelo a partir do armazenamento com token de SA

Pode adicionar os seus modelos para uma conta de armazenamento e ligar as respectivas durante a implementação com um token de SA.

> [AZURE.IMPORTANT] Ao seguir os passos abaixo, o blob que contém o modelo fica acessível a apenas o proprietário da conta. No entanto, quando cria um token de SA para o blob, o blob fica acessível a qualquer pessoa com essa URI. Se outro utilizador intercepta o URI, esse utilizador é possível aceder ao modelo. Utilizar um token de SA é uma boa forma de limitar o acesso aos seus modelos, mas não deve incluir dados importantes, como palavras-passe diretamente no modelo.

### <a name="add-private-template-to-storage-account"></a>Adicionar a modelo privado a conta de armazenamento

Os passos seguintes configurar uma conta de armazenamento para os modelos:

1. Crie um grupo de recursos.

        azure group create -n "ManageGroup" -l "westus"

2. Crie uma conta de armazenamento. O nome da conta de armazenamento tem de ser exclusivo ao longo do Azure, por isso, forneça o seu próprio nome da conta.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Definir variáveis para a conta de armazenamento e a chave.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Crie um contentor. A permissão está definida para **desativar** o que significa que o contentor só está acessível para o proprietário.

        azure storage container create --container templates -p Off 
        
4. Adicione o seu modelo ao contentor.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fornecer token de SA durante a implementação

Para implementar um modelo privado numa conta de armazenamento, obter um token de SA e inclua-o no URI para o modelo.

1. Crie um token de SA com permissões de leitura e um tempo de expiração para limitar o acesso. Defina o tempo de expiração para permitir que o tempo suficiente concluir a implementação. Obter o URI completo do modelo, incluindo o token de SA.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Implemente o modelo fornecendo o URI que inclui o token de SA.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Para obter um exemplo de utilização de um token de SA com modelos ligados, consulte o artigo [utilizar modelos ligados com o Gestor de recursos do Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Próximos passos
- Para obter um exemplo de implementação do recursos através da biblioteca de cliente .NET, consulte o artigo [Implementar recursos utilizar bibliotecas .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parâmetros no modelo, consulte o artigo [modelos de criação](resource-group-authoring-templates.md#parameters).
- Para obter orientações sobre como implementar a solução para diferentes ambientes, consulte o artigo [desenvolvimento e ambientes de teste no Microsoft Azure](solution-dev-test-environments.md).
- Para obter detalhes sobre como utilizar uma referência de KeyVault para passar valores seguros, consulte o artigo [passar valores seguros durante a implementação](resource-manager-keyvault-parameter.md).

