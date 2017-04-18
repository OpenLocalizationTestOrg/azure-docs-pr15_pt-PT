<properties
   pageTitle="Implementar recursos com PowerShell e modelo | Microsoft Azure"
   description="Utilize o Gestor de recursos do Azure e Azure PowerShell para implementar um recursos Azure. Os recursos são definidos num modelo de Gestor de recursos."
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

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementar recursos com os modelos de Gestor de recursos e PowerShell do Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Clip Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

Este tópico explica como utilizar o Azure PowerShell com modelos de Gestor de recursos para implementar os recursos do Azure.  

> [AZURE.TIP] Para obter ajuda com a depuração de um erro durante a implementação, consulte:
>
> - [Operações de implementação de vista com o Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) para saber mais sobre como obter as informações que o ajuda a resolver o erro
> - [Resolver problemas de erros comuns quando implementar recursos para Azure com o Gestor de recursos do Azure](resource-manager-common-deployment-errors.md) para saber como resolver erros comuns de implementação

O modelo pode ser um ficheiro local ou um ficheiro externo que está disponível através de um URI. Quando o seu modelo encontra-se numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um token de assinatura (SA) de acesso partilhado durante a implementação.

## <a name="quick-steps-to-deployment"></a>Passos rápidos para implementação

Este artigo descreve a todas as diferentes opções disponíveis para si durante a implementação. No entanto, muitas vezes só necessita de dois comandos simples. Para começar rapidamente com a implementação, utilize os comandos seguintes:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Para saber mais sobre as opções para implementação que poderá ser mais adequados para o seu cenário, continue a ler neste artigo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>Implementar com PowerShell

1. Inicie sessão na sua conta Azure.

        Add-AzureRmAccount

     É devolvido um resumo da sua conta.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Se tem múltiplas subscrições, fornece o ID da subscrição que pretende utilizar para a implementação com o comando **Definir AzureRmContext** . 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Normalmente, quando implementar um novo modelo, que pretende criar um grupo de recursos para conter os recursos. Se tiver um grupo de recursos existente que pretende implementar, pode ignorar este passo e utilizar esse grupo de recursos. 

     Para criar um grupo de recursos, forneça um nome e uma localização para o seu grupo de recursos. Tem de fornecer uma localização para o grupo de recursos, uma vez que o grupo de recursos armazena metadados sobre os recursos. Por razões de conformidade, poderá querer especificar a localização onde armazenou esse metadados. Em geral, recomendamos que especifique uma localização onde a maior parte dos seus recursos residirão. Utilizar a mesma localização, pode simplificar o modelo.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     É devolvido um resumo do novo grupo de recursos.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Antes de executar a sua implementação, pode Valide as definições de implementação. O cmdlet **Teste AzureRmResourceGroupDeployment** permite-lhe detetar problemas antes de criar os recursos reais. O exemplo seguinte mostra como validar uma implementação.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Para implementar recursos ao seu grupo de recursos, execute o comando **Novo AzureRmResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros de incluem um nome para a sua implementação, o nome do seu grupo de recursos, o caminho ou URL para o modelo que criou e quaisquer outros parâmetros necessários para o seu cenário. Se o **modo de** parâmetro não for especificado, é utilizado o valor predefinido de **incremento** . Para executar uma implementação concluída, defina o **modo** como **concluída**. Tenha cuidado quando utilizar o modo completo como inadvertidamente pode eliminar recursos que não estão no seu modelo.

     Para implementar um modelo local, utilize o parâmetro **modeloEtiquetas** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Para implementar um modelo de externo, utilize o parâmetro **TemplateUri** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Tem as seguintes opções para fornecer valores de parâmetros: 
   
     1. Utilize parâmetros inline.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Utilize um objeto de parâmetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Utilize um ficheiro de parâmetro local. Para obter informações sobre o ficheiro de modelo, consulte o artigo [ficheiro parâmetro](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Utilize um ficheiro de parâmetro externos. Para obter informações sobre o ficheiro de modelo, consulte o artigo [ficheiro parâmetro](#parameter-file). 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Quando utiliza um ficheiro de parâmetro externos, não pode ser efetuada com outros valores de um dos inline ou a partir de um ficheiro local. Para obter mais informações, consulte [a precedência de parâmetro](#parameter-precendence).

     Depois dos recursos foram implementados, irá ver um resumo da implementação.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Se o seu modelo incluir um parâmetro com o mesmo nome como um dos parâmetros no comando PowerShell, lhe for pedido para fornecer um valor para esse parâmetro. O parâmetro a partir do seu modelo irá incluir o postfix **FromTemplate**. Por exemplo, um parâmetro com o nome **ResourceGroupName** no seu modelo está em conflito com o parâmetro **ResourceGroupName** no cmdlet [AzureRmResourceGroupDeployment novo](https://msdn.microsoft.com/library/azure/mt679003.aspx) . Lhe for pedido para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, deverá evitar este confusões atribuindo não parâmetros com o mesmo nome como parâmetros utilizados para operações de implementação.

6. Se pretender registar informações adicionais sobre a implementação que poderão ajudá-lo a resolver problemas de erros de implementação, utilize o parâmetro **DeploymentDebugLogLevel** . Pode especificar que conteúdo pedido, o conteúdo de resposta ou ambos ter sessão iniciada com a operação de implementação.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Para mais informações sobre como utilizar este conteúdo depuração para resolver problemas de híbridas, consulte o artigo [implementações de grupo de recursos de resolução de problemas com o Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-template-from-storage-with-sas-token"></a>Implementar o modelo a partir do armazenamento com token de SA

Pode adicionar os seus modelos para uma conta de armazenamento e ligar as respectivas durante a implementação com um token de SA.

> [AZURE.IMPORTANT] Ao seguir os passos abaixo, o blob que contém o modelo fica acessível a apenas o proprietário da conta. No entanto, quando cria um token de SA para o blob, o blob fica acessível a qualquer pessoa com essa URI. Se outro utilizador intercepta o URI, esse utilizador é possível aceder ao modelo. Utilizar um token de SA é uma boa forma de limitar o acesso aos seus modelos, mas não deve incluir dados importantes, como palavras-passe diretamente no modelo.

### <a name="add-private-template-to-storage-account"></a>Adicionar a modelo privado a conta de armazenamento

Os passos seguintes configurar uma conta de armazenamento para os modelos:

1. Crie um grupo de recursos.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Crie uma conta de armazenamento. O nome da conta de armazenamento tem de ser exclusivo ao longo do Azure, por isso, forneça o seu próprio nome da conta.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Defina a conta de armazenamento atual.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Crie um contentor. A permissão está definida para **desativar** o que significa que o contentor só está acessível para o proprietário.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Adicione o seu modelo ao contentor.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Fornecer token de SA durante a implementação

Para implementar um modelo privado numa conta de armazenamento, obter um token de SA e inclua-o no URI para o modelo.

1. Se tiver alterado a conta de armazenamento atual, defina a conta de armazenamento atual para aquele que contém os modelos.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Crie um token de SA com permissões de leitura e um tempo de expiração para limitar o acesso. Obter o URI completo do modelo, incluindo o token de SA.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Implemente o modelo fornecendo o URI que inclui o token de SA.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Para obter um exemplo de utilização de um token de SA com modelos ligados, consulte o artigo [utilizar modelos ligados com o Gestor de recursos do Azure](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>Precedência de parâmetro

Pode utilizar um ficheiro de parâmetro local na mesma operação de implementação e parâmetros de inline. Por exemplo, pode especificar alguns valores no ficheiro de parâmetro local e adicionar outros valores inline durante a implementação. Se fornecer valores um parâmetro no ficheiro de parâmetro local e no inline, o valor de inline terá precedência.

No entanto, não é possível utilizar parâmetros inline com um ficheiro de parâmetro externos. Quando especificar um ficheiro de parâmetro o parâmetro **TemplateParameterUri** , todos os parâmetros de inline são ignorados. Tem de fornecer todos os valores de parâmetros do ficheiro externo. Se o seu modelo incluir um valor confidencial que não pode incluir no ficheiro de parâmetro, adicionar esse valor a chave cofre e referenciar o Cofre de palavras chave no seu ficheiro de parâmetro externos ou fornecer dinamicamente todos inline de valores de parâmetro.

Para obter detalhes sobre como utilizar uma referência de KeyVault para passar valores seguros, consulte o artigo [passar valores seguros durante a implementação](resource-manager-keyvault-parameter.md).

## <a name="next-steps"></a>Próximos passos
- Para obter um exemplo de implementação do recursos através da biblioteca de cliente .NET, consulte o artigo [Implementar recursos utilizar bibliotecas .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parâmetros no modelo, consulte o artigo [modelos de criação](resource-group-authoring-templates.md#parameters).
- Para obter orientações sobre como implementar a solução para diferentes ambientes, consulte o artigo [desenvolvimento e ambientes de teste no Microsoft Azure](solution-dev-test-environments.md).

