<properties
   pageTitle="Implementar uma VM com um endereço IP público estático utilizando um modelo no Gestor de recursos | Microsoft Azure"
   description="Saiba como implementar VMs com um endereço IP público estático utilizando um modelo no Gestor de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-a-template"></a>Implementar uma VM com um endereço IP público estático através de um modelo

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-resources-in-a-template-file"></a>Recursos IP públicos num ficheiro de modelo

Pode ver e transferir o [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

A secção abaixo mostra a definição do recurso IP público, com base no cenário acima.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Repare que a propriedade **publicIPAllocationMethod** , que está definida para *estático*. Esta propriedade pode ser *dinâmicas* (valor predefinido) ou *estático*. Defini-la para garantias estáticas que nunca irá alterar o endereço IP público atribuído.

A secção abaixo mostra a associação do endereço IP público com uma interface de rede.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Repare que a propriedade **publicIPAddress** apontar para o **Id** de um recurso denominado **variables('webVMSetting').pipName**. Que é o nome do recurso IP público mostrado acima.

Por fim, a interface de rede acima é listada na propriedade **networkProfile** da VM a ser criado.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar o modelo utilizando o clique para implementar

O modelo de exemplo disponível no repositório de público utiliza um ficheiro de parâmetro que contenha a predefinição valores utilizados para gerar o cenário descrito acima. Para implementar este modelo utilizando o clique para implementar o, clique em **implementar para Azure** no ficheiro Readme.md para o modelo [VM com PIP estático](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) . Substitua os valores de parâmetro predefinida se pretender e introduza os valores para os parâmetros em branco.  Siga as instruções no portal para criar uma máquina virtual com um endereço IP público estático.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo utilizando o PowerShell

Para implementar o modelo que transferiu utilizando o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) e siga as instruções nos passos 1 a 3.

2. Na consola do PowerShell, execute o cmdlet **AzureRmResourceGroup novo** para criar um novo grupo de recursos, se necessário. Se já tiver criado um grupo de recursos, avance para o passo 3.

        New-AzureRmResourceGroup -Name PIPTEST -Location westus

    Saída esperada:

        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Na consola do PowerShell, execute o cmdlet **AzureRmResourceGroupDeployment novo** para implementar o modelo.

        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

    Saída esperada:

        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0

        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         

        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo utilizando o clip do Azure

Para implementar o modelo utilizando o clip do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure clip, siga os passos no artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md) e, em seguida, os passos para ligar o clip à sua subscrição na secção "Utilizar o início de sessão do azure para autenticar de forma interativa" do artigo [ligar a uma subscrição do Azure a partir da linha de comandos Interface o Azure (Azure CLI)](../xplat-cli-connect.md) .
2. Execute o comando de **modo azure config** para mudar para modo de Gestor de recursos, conforme apresentado abaixo.

        azure config mode arm

    Eis a saída esperada para o comando acima:

        info:    New mode is arm

3. Abra o [ficheiro de parâmetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), selecione o respetivo conteúdo e guardá-lo para um ficheiro no seu computador. Neste exemplo, os parâmetros são guardados num ficheiro denominado *parameters.json*. Alterar os valores de parâmetro dentro do ficheiro, se pretender, mas no mínimo, recomendamos que altere o valor para o parâmetro adminPassword para uma palavra-passe exclusiva e complexa.

4. Execute o cmdlet **criar de implementação do azure grupo** para implementar o novo VNet utilizando os ficheiros de modelo e um parâmetro transferido e modificação acima. Comando abaixo, substitua <path> com o caminho guardou o ficheiro. 

        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

    Resultado esperado (listas de valores de parâmetros utilizados):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
