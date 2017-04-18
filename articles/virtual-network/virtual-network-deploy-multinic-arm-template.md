<properties
   pageTitle="Implementar multi NIC VMs utilizando um modelo no Gestor de recursos | Microsoft Azure"
   description="Saiba como implementar multi NIC VMs utilizando um modelo no Gestor de recursos"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>Implementar multi NIC VMs através de um modelo

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Uma vez que neste momento na hora não pode ter VMs com uma única NIC e VMs com vários NIC no mesmo grupo de recursos, que irá implementar os servidores de back-end num grupo de recursos e todos os outros componentes outro grupo de segurança. Os passos abaixo utilizam um grupo de recursos *IaaSStory* o nome do grupo de recursos principal e *IaaSStory-back-end* para os servidores de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder implementar os servidores de back-end, é necessário implementar o grupo de recursos principal com todos os recursos necessários para este cenário. Para implementar estes recursos, siga os passos abaixo.

1. Navegue para [a página de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na página de modelo, à direita do **grupo de recursos principal**, clique em **implementar para Azure**.
3. Se for necessário, altere os valores de parâmetro, em seguida, siga os passos no portal do Azure pré-visualização para implementar o grupo de recursos.

> [AZURE.IMPORTANT] Certifique-se que os nomes de conta de armazenamento são exclusivos. Não pode ter nomes de conta de armazenamento duplicados no Azure.

## <a name="understand-the-deployment-template"></a>Compreender o modelo de implementação

Antes de implementar o modelo fornecido com este documento, certifique-se de que compreende o que faz. Os passos abaixo fornecem uma boa visão geral do modelo em questão.

1. Navegue para [a página de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Clique em **azuredeploy.json** para abrir o ficheiro de modelo.
3. Repare que o parâmetro *osType* listado abaixo. Este parâmetro é utilizado para selecionar a imagem que VM para utilizar para o servidor de base de dados, juntamente com o sistema operativo várias definições relacionadas com.

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. Desloque para baixo para a lista de variáveis e verifique a definição para as variáveis **dbVMSetting** , listados abaixo. Recebe um dos elementos matriz contidos na variável **dbVMSettings** . Se estiver familiarizado com a terminologia de desenvolvimento de software, pode ver a variável de **dbVMSettings** como um hashtable ou um dictionay.

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Suponha que decide implementar o Windows VMs executa o SQL no back-end. Em seguida, o valor para **osType** seria *Windows*e a variável de **dbVMSetting** iria conter o elemento indicado abaixo, que representa o primeiro valor na variável **dbVMSettings** .

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. Repare que o **vmSize** contém o valor *Standard_DS3*. Apenas determinados tamanhos VM permitem a utilização de vários NIC. Pode verificar quais os tamanhos de VM são multi NIC activada acedendo a [Descrição geral NIC multi](virtual-networks-multiple-nics.md).
7. Desloque para baixo para **recursos** e repare o primeiro elemento. Descreve uma conta de armazenamento. Esta conta de armazenamento será utilizada para manter os discos de dados utilizados por cada base de dados VM. Neste cenário, cada base de dados VM tem um disco SO armazenados no armazenamento regular e duas discos de dados armazenados no armazenamento SSD (premium).

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. Desloque para baixo para o recurso seguinte, conforme indicado abaixo. Este recurso representa o NIC utilizado para o acesso de base de dados em cada base de dados VM. Repare que a utilização da função **cópia** neste recurso. O modelo permite-lhe implementar tantas VMs que desejar, com base no parâmetro **dbCount** . Por isso, tem de criar a mesma quantidade de NIC para aceder a base de dados, um para cada VM.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. Desloque para baixo para o recurso seguinte, conforme indicado abaixo. Este recurso representa o NIC utilizado para gestão de cada base de dados VM. Mais uma vez, precisa de um dos seguinte NIC para cada base de dados VM. Repare que o elemento **networkSecurityGroup** , ligar um NSG que permita aceder ao RDP/SSH para apenas este NIC.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. Desloque para baixo para o recurso seguinte, conforme indicado abaixo. Este recurso representa uma disponibilidade definida para ser partilhado por todos os VMs de base de dados. Desta forma, garante que sempre haverá um VM no conjunto de executar durante a manutenção.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. Desloque para baixo para o recurso seguinte. Este recurso representa a base de dados VMs, conforme visto no primeiro algumas linhas listadas abaixo. Repare que a utilização da função **cópia** novamente, garantindo que VMs vários são criados com base no parâmetro **dbCount** . Além disso, repare a coleção de **dependsOn** . Listas de duas NIC a ser necessárias para ser criados antes da VM é implementada, juntamente com o conjunto de disponibilidade e a conta de armazenamento.

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. Desloque-se para baixo no recurso VM ao elemento **networkProfile** , conforme indicado abaixo. Repare que existem duas NIC a ser referência para cada VM. Quando cria várias NIC para uma VM, tem de definir a propriedade **principal** de um da NIC a *Verdadeiro*e o resto *Falso*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Implementar o modelo de processador utilizando o clique para implementar

> [AZURE.IMPORTANT] Certifique-se de que segue os passos de [Pré-requisitos](#Pre-requisites) antes de seguir as instruções abaixo.

O modelo de exemplo disponível no repositório de público utiliza um ficheiro de parâmetro que contenha a predefinição valores utilizados para gerar o cenário descrito acima. Para implementar este modelo utilizando o clique para implementar, siga [esta ligação](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), à direita do **grupo de recursos de back-end (consulte a documentação)** clique em **Implementar Azure**, substitua os valores de parâmetro de predefinido, se necessário e siga as instruções no portal.

A figura seguinte mostra o conteúdo do novo grupo de recursos, após a implementação.

![Grupo de recursos de back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo utilizando o PowerShell

Para implementar o modelo que transferiu utilizando o PowerShell, siga os passos abaixo.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Executar o **`New-AzureRmResourceGroup`** cmdlet para criar um grupo de recursos utilizando o modelo.

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    Saída esperada:

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo utilizando o clip do Azure

Para implementar o modelo utilizando o clip do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.
2. Executar o **`azure config mode`** comando para mudar para modo de Gestor de recursos, conforme apresentado abaixo.

        azure config mode arm

    Eis a saída esperada para o comando acima:

        info:    New mode is arm

3. Abra o [ficheiro de parâmetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), selecione os seus conteúdos e guardá-lo para um ficheiro no seu computador. Neste exemplo, vamos guardou o ficheiro de parâmetros *parameters.json*.

4. Executar o **`azure group deployment create`** cmdlet para implementar o novo VNet utilizando o modelo e o parâmetro de ficheiros que transferiu e modificação acima. A lista apresentada depois da saída explica os parâmetros utilizados.

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    Saída esperada:

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
