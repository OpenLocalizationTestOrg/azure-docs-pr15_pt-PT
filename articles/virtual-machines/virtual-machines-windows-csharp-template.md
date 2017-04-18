<properties
    pageTitle="Implementar uma VM utilizando c# e um modelo de Gestor de recursos | Microsoft Azure"
    description="Saiba como utilizar c# e um modelo de Gestor de recursos para implementar uma VM Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>

# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implementar uma máquina de Virtual Azure utilizando c# e um modelo de Gestor de recursos

Ao utilizar grupos de recursos e modelos, não conseguir gerir todos os recursos juntos que suportam a sua aplicação. Este artigo mostra-lhe como utilizar o Visual Studio e c# para configurar a autenticação, criar um modelo e, em seguida, implementar recursos Azure utilizando o modelo que criou.

Tem primeiro para se certificar de que efetuou estes passos de configuração:

- Instalar o [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verifique se a instalação do [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [o Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obter um [token de autenticação](../resource-group-authenticate-service-principal.md)
- Crie um grupo de recursos utilizando o [Azure PowerShell](../resource-group-template-deploy.md), [Azure clip](../resource-group-template-deploy-cli.md)ou [Azure portal](../resource-group-template-deploy-portal.md).

Demora cerca de 30 minutos para efetue estes passos.
    
## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Passo 1: Criar o projeto do Visual Studio, o ficheiro de modelo e o ficheiro de parâmetros

### <a name="create-the-template-file"></a>Criar o ficheiro de modelo

Um modelo de Gestor de recursos do Azure torna possíveis para implementar e gerir recursos Azure em conjunto. O modelo é uma descrição de JSON dos recursos e parâmetros de implementação associado.

No Visual Studio, efetue os seguintes passos:

1. Clique em **ficheiro** > **Novo** > **projeto**.

2. Nos **modelos** > **Visual c#**, selecione a **Aplicação de consola**, introduza o nome e localização do projeto e, em seguida, clique em **OK**.

3. Botão direito do rato no nome do projeto no Explorador de solução, clique em **Adicionar** > **Novo Item**.

4. Clique em Web, selecione ficheiro JSON, introduza *VirtualMachineTemplate.json* para o nome e, em seguida, clique em **Adicionar**.

5. De abertura e de parênteses de fecho do ficheiro VirtualMachineTemplate.json, adicione o elemento de esquema necessário e o elemento contentVersion necessário:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Parâmetros](../resource-group-authoring-templates.md#parameters) não são sempre obrigatórios, mas fornecem uma maneira de valores de entrada quando o modelo é implementado. Adicione o elemento de parâmetros e os respetivos elementos subordinados após o elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

7. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser utilizadas num modelo para especificar os valores que podem ser alteradas frequentemente ou valores que precisam de ser criado a partir de uma combinação de valores de parâmetros. Adicione o elemento de variáveis depois da secção de parâmetros:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }

8. [Recursos](../resource-group-authoring-templates.md#resources) , como a máquina virtual, a rede virtual e a conta de armazenamento são definidos seguinte no modelo. Adicione a secção de recursos após a secção de variáveis:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
      
9. Guarde o ficheiro de modelo que criou.

### <a name="create-the-parameters-file"></a>Criar o ficheiro de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, cria um ficheiro de parâmetros que contém os valores que são utilizados quando o modelo é implementado. No Visual Studio, efetue os seguintes passos:

1. Botão direito do rato no nome do projeto no Explorador de solução, clique em **Adicionar** > **Novo Item**.

2. Clique em Web, selecione ficheiro JSON, introduza *Parameters.json* para o nome e, em seguida, clique em **Adicionar**.

3. Abra o ficheiro parameters.json e, em seguida, adicione este conteúdo JSON:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Este artigo cria uma máquina de virtual a executar uma versão do sistema operativo Windows Server. Para saber mais sobre como selecionar outras imagens, consulte o artigo [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e o clip do Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Guarde o ficheiro de parâmetros que criou.

## <a name="step-2-install-the-libraries"></a>Passo 2: Instalar as bibliotecas

Pacotes de NuGet são a forma mais fácil de instalar as bibliotecas de que necessita para concluir este tutorial. Precisa da biblioteca de gestão de recursos do Azure e o biblioteca de autenticação do Active Directory Azure para criar os recursos. Para obter estas bibliotecas no Visual Studio, execute os seguintes passos:

1. Botão direito do rato no nome do projeto no Explorador de solução, clique em **Gerir pacotes de NuGet**e, em seguida, clique em Procurar.

2. Tipo *Do Active Directory* na caixa de pesquisa, clique em **instalar** para o pacote de biblioteca de autenticação do Active Directory e, em seguida, siga as instruções para instalar o pacote.

4. Na parte superior da página, selecione a **Versão de pré-lançamento do incluir**. Tipo *Microsoft.Azure.Management.ResourceManager* na caixa de pesquisa, clique em **instalar** para as bibliotecas de gestão de recursos do Microsoft Azure e, em seguida, siga as instruções para instalar o pacote.

Agora está pronto para começar a utilizar as bibliotecas para criar uma aplicação.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Passo 3: Criar as credenciais que são utilizadas para autenticar pedidos

A aplicação do Azure Active Directory é criada e é instalada a biblioteca de autenticação. Agora pode formata as informações da aplicação para credenciais são utilizadas para autenticar pedidos para o Gestor de recursos do Azure.

1. Abra o ficheiro de Program.cs para o projeto que criou e, em seguida, adicioná-los com instruções para a parte superior do ficheiro:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;

2.  Adicione este método para a classe de programa para obter o token de que tem necessárias para criar as credenciais:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token.");
          }
          return token;
        }

    Substitua {id de cliente} o identificador do Azure Active Directory aplicação, {-segredo cliente} com a tecla de acesso da aplicação de AD e {id de inquilino} com o identificador de inquilino para a sua subscrição. Pode localizar o id do inquilino através da execução Get-AzureRmSubscription. Pode encontrar a tecla de acesso utilizando o portal do Azure.

3. Para criar as credenciais, adicione este código para o método principal no ficheiro Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Guarde o ficheiro Program.cs.

## <a name="step-4-deploy-the-template"></a>Passo 4: Implementar o modelo

Neste passo, utilize o grupo de recursos que criou anteriormente, mas também pode criar um grupo de recursos utilizando o [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e as classes [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) .

1. Adicione variáveis para o método principais a classe de programa para especificar os nomes dos recursos que criou anteriormente, o nome de implementação e o identificador de subscrição de:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    Substitua o valor de nome de grupo com o nome do seu grupo de recursos. Substitua o valor de deploymentName com o nome que pretende utilizar para a implementação. Pode encontrar o identificador de subscrição ao executar Get-AzureRmSubscription.

2. Adicione este método para a classe de programa para implementar os recursos ao grupo de recursos utilizando o modelo que definiu:

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    Se pretende que implementar o modelo a partir de uma conta de armazenamento, pode substituir a propriedade de modelo com a propriedade TemplateLink.

3. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Passo 5: Eliminar os recursos

Uma vez que são cobrados para recursos utilizados no Azure, sempre é aconselhável para eliminar os recursos que já não forem necessárias. Não tem de eliminar a cada recurso separadamente a partir de um grupo de recursos. Eliminar o grupo de recursos e todos os seus recursos são eliminados automaticamente.

1.  Para eliminar o grupo de recursos, adicione este método para a classe de programa:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

##<a name="step-6-run-the-console-application"></a>Passo 6: Executar a aplicação da consola

1.  Para executar a aplicação da consola, clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando as mesmas credenciais que utiliza com a sua subscrição.

2.  Depois do Estado de aceites é apresentada, prima **Enter** .

    Deverá demorar cerca de cinco minutos para esta aplicação consola executar completamente a partir do início para conclusão. Antes de premir Enter para iniciar a remoção de recursos, poderá demorar alguns minutos para verificar a criação de recursos no portal do Azure antes de eliminá-los.

3. Para ver o estado dos recursos, navegue para os registos de auditoria no portal do Azure:

    ![Procurar registos de auditoria no portal do Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Próximos passos

- Se havia problemas com a implementação, um passo seguinte seria consultá [implementações de grupo de recursos de resolução de problemas com o Azure portal](../resource-manager-troubleshoot-deployments-portal.md).
- Saiba como gerir a máquina virtual que criou ao rever [máquinas virtuais de gerir utilizando o Gestor de recursos do Azure e PowerShell](virtual-machines-windows-csharp-manage.md).
