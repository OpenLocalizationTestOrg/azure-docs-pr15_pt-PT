## <a name="how-to-deploy-with-azure-cli"></a>Como implementar o clip do Azure

1. Início de sessão para a sua conta Azure.

        azure login

  Depois de fornecer as suas credenciais, o comando devolve o resultado do seu início de sessão.

        ...
        info:    login command OK

2. Se tem múltiplas subscrições, fornece o id da subscrição que pretende utilizar para implementação.

        azure account set <YourSubscriptionNameOrId>

3. Mudar para o módulo Azure o Gestor de recursos

        azure config mode arm

   Vai receber a confirmação do modo em novo.

        info:     New mode is arm

4. Se não tiver um grupo de recursos existente, crie um novo grupo de recursos. Fornece o nome do grupo de recursos e da localização que precisa para a sua solução.

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

5. Para criar uma nova implementação do seu grupo de recursos, execute o seguinte comando e forneça os parâmetros necessários. Os parâmetros irão incluir um nome para a sua implementação, o nome do seu grupo de recursos, o caminho ou URL para o modelo que criou e quaisquer outros parâmetros necessários para o seu cenário.

   Tem as seguintes opções para fornecer valores de parâmetros:

   - Utilizar parâmetros inline e um modelo local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Utilizar parâmetros inline e uma ligação a um modelo.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Utilize um ficheiro de parâmetro.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Quando o grupo de recursos ter sido implementado, irá ver um resumo da implementação.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Para obter informações sobre a sua implementação mais recente.

         azure group log show -l ExampleResourceGroup

7. Para obter informações detalhadas sobre falhas de implementação.

         azure group log show -l -v ExampleResourceGroup
