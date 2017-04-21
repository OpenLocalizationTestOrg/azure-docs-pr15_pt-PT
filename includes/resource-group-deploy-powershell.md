## <a name="how-to-deploy-with-powershell"></a>Como implementar com PowerShell

1. Início de sessão para a sua conta Azure.

          Add-AzureAccount

   Depois de fornecer as suas credenciais, o comando devolve informações sobre a sua conta.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Se tem múltiplas subscrições, fornece o id da subscrição que pretende utilizar para implementação. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Mudar para o módulo Azure o Gestor de recursos.

          Switch-AzureMode AzureResourceManager

4. Se não tiver um grupo de recursos existente, crie um novo grupo de recursos. Fornece o nome do grupo de recursos e da localização que precisa para a sua solução.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

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

5. Para criar uma nova implementação do seu grupo de recursos, execute o comando **Novo AzureResourceGroupDeployment** e forneça os parâmetros necessários. Os parâmetros irão incluir um nome para a sua implementação, o nome do seu grupo de recursos, o caminho ou URL para o modelo que criou e quaisquer outros parâmetros necessários para o seu cenário. 
   
   Tem as seguintes opções para fornecer valores de parâmetros: 
   
   - Utilize parâmetros inline.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Utilize um objeto de parâmetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Utilizar um ficheiro de parâmetro.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Quando o grupo de recursos ter sido implementado, irá ver um resumo da implementação.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Para obter informações sobre as falhas de implementação.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Para obter informações detalhadas sobre falhas de implementação.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
