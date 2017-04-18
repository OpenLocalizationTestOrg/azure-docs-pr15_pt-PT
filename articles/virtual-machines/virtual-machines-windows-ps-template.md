<properties
    pageTitle="Criar uma VM com um modelo de Gestor de recursos | Microsoft Azure"
    description="Utilize um modelo de Gestor de recursos e o PowerShell para criar facilmente uma nova máquina de virtual do Windows."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Criar uma máquina de virtual do Windows com um modelo de Gestor de recursos

Este artigo apresenta-lhe um modelo de Gestor de recursos do Azure e mostra-lhe como utilizar o PowerShell para implementá-lo. O modelo implementa uma máquina virtual única com o Windows Server numa rede virtual nova com uma única sub-rede.

Deverá demorar cerca de 20 minutos executar os passos neste artigo.

> [AZURE.IMPORTANT] Se pretender que o seu VM façam parte de um conjunto de disponibilidade, adicioná-la para o conjunto quando cria a VM. Atualmente não existe uma maneira de adicionar uma VM para uma disponibilidade definida após ter sido criada.

## <a name="step-1-create-the-template-file"></a>Passo 1: Criar o ficheiro de modelo

Pode criar o seu próprio modelo utilizando as informações que se encontram no [Gestor de recursos do Azure criação de modelos](../resource-group-authoring-templates.md). Também pode implementar modelos que foram criados por si partir de [Modelos de tutoriais Azure](https://azure.microsoft.com/documentation/templates/).

1. Abra o editor de texto favorito e adicione o elemento de esquema necessário e o elemento contentVersion necessário:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [Parâmetros](../resource-group-authoring-templates.md#parameters) não são sempre obrigatórios, mas fornecem uma maneira de valores de entrada quando o modelo é implementado. Adicione o elemento de parâmetros e os respetivos elementos subordinados após o elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser utilizadas num modelo para especificar os valores que podem ser alteradas frequentemente ou valores que precisam de ser criado a partir de uma combinação de valores de parâmetros. Adicione o elemento de variáveis depois da secção de parâmetros:

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
        
4. [Recursos](../resource-group-authoring-templates.md#resources) , como a máquina virtual, a rede virtual e a conta de armazenamento são definidos seguinte no modelo. Adicione a secção de recursos após a secção de variáveis:

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
              "name": "myvn1",
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
          
    >[AZURE.NOTE] Este artigo cria uma máquina de virtual a executar uma versão do sistema operativo Windows Server. Para saber mais sobre como selecionar outras imagens, consulte o artigo [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e o clip do Azure](virtual-machines-linux-cli-ps-findimage.md).  
            
2. Guarde o ficheiro de modelo como *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Passo 2: Criar o ficheiro de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, crie um ficheiro de parâmetros que contém os valores que são utilizados quando o modelo é implementado.

1. No editor de texto, copie este conteúdo JSON para um novo ficheiro denominado *Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Obtenha mais informações sobre [os requisitos de nome de utilizador e palavra-passe](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Guarde o ficheiro de parâmetros.

## <a name="step-3-install-azure-powershell"></a>Passo 3: Instalar o Azure PowerShell

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a sua subscrição e iniciar sessão na sua conta.

## <a name="step-4-create-a-resource-group"></a>Passo 4: Criar um grupo de recursos

Todos os recursos tem de ser implementados num [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

1. Obter uma lista das localizações disponíveis onde os recursos podem ser criados.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Substitua o valor de **$locName** uma localização a partir da lista, por exemplo **Central (EUA)**. Crie a variável.

        $locName = "location name"
        
3. Substitua o valor de **$rgName** com o nome do novo grupo de recursos. Crie a variável e o grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Deverá visualizar algo parecido com este exemplo:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Passo 5: Criar os recursos com o modelo e parâmetros

Substitua o valor de **$templateFile** o caminho e o nome do ficheiro de modelo. Substitua o valor de **$parameterFile** o caminho e o nome do ficheiro parâmetros. Crie as variáveis e, em seguida, implementar o modelo. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] Também pode implementar modelos e os parâmetros de uma conta de armazenamento Azure. Para saber mais, consulte o artigo [Utilizar o PowerShell Azure com armazenamento do Windows Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Próximos passos

- Se havia problemas com a implementação, um passo seguinte seria consultá [implementações de grupo de recursos de resolução de problemas com o Azure portal](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerir a máquina virtual que criou ao rever [máquinas virtuais de gerir utilizando o Gestor de recursos do Azure e PowerShell](virtual-machines-windows-ps-manage.md).
