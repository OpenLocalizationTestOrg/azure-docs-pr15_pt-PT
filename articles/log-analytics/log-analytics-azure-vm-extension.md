<properties
    pageTitle="Ligar o Azure máquinas virtuais a análise de registo | Microsoft Azure"
    description="Para Windows e Linux máquinas virtuais em execução no Azure, o caminho recomendado de recolher registos de início e de métricas é instalando a extensão do registo Analytics Azure VM. Pode utilizar o Azure portal ou o PowerShell para instalar a extensão de máquina virtual de análise de registo para Azure VMs."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="richrund"/>

# <a name="connect-azure-virtual-machines-to-log-analytics"></a>Ligar o Azure máquinas virtuais a análise de registo

Para Windows e Linux computadores, o método recomendado para recolher registos e métricas é ao instalar o agente de análise de registo.

É a forma mais fácil de instalar o agente de análise de registo em máquinas virtuais Azure através a extensão de VM de análise de registo.  Utilizando a extensão de simplifica o processo de instalação e configura automaticamente o agente para enviar dados para a área de trabalho de análise de registo que especificar. O agente também é actualizado automaticamente, garantindo que tem as funcionalidades e correções mais recente.

Para o Windows máquinas virtuais ativar a extensão de máquina virtual *Do Microsoft Agent monitorização* .
Para máquinas virtuais de Linux, ativar a extensão de máquina virtual *OMS agente para Linux* .

Saiba mais sobre [as extensões de máquina virtual Azure](../virtual-machines/virtual-machines-windows-extensions-features.md) e o [agent Linux] (... / virtual-machines/virtual-machines-linux-agent-user-guide.md).

Quando utiliza a coleção de agente para os dados do registo, tem de configurar [origens de dados no registo Analytics](log-analytics-data-sources.md) para especificar os registos e métricas que pretende recolher.

>[AZURE.IMPORTANT] Se configurar o registo de análise para dados do registo de índice remissivo utilizando os [Diagnósticos Azure](log-analytics-azure-storage.md)e configurar o agente para recolher registos do mesmo, em seguida, os registos são recolhidos duas vezes. São cobradas para ambas as origens de dados. Se tiver o agente instalado, deverá recolher dados de registo ao utilizar o agente sozinho - não configurar análises de registo para recolher dados de registo de diagnóstico Azure.

Existem três formas fáceis de activar a extensão de máquina virtual de análise de registo:

+ Utilizando o portal do Azure
+ Ao utilizar o PowerShell do Azure
+ Utilizando um modelo de Gestor de recursos do Azure

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Activar a extensão VM no portal do Azure

Pode instalar o agente para análise de registo e ligar a máquina virtual Azure que é executada no utilizando o [Azure portal](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Para instalar o agente de análise de registo e ligar a máquina virtual a uma área de trabalho de análise de registo

1.  Inicie sessão no [portal do Azure](http://portal.azure.com).
2.  Selecione a **Procurar** no lado esquerdo do portal e, em seguida, aceda a **Análise de registo (OMS)** e selecione-lo.
3.  Na sua lista de áreas de trabalho de análise de registo, selecione a que pretende utilizar com a VM Azure.  
    ![Áreas de trabalho OMS](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4.  Em **Gestão de análise de registo**, selecione **máquinas virtuais**.  
    ![Máquinas virtuais](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5.  Na lista de **máquinas virtuais**, selecione a máquina virtual no qual pretende instalar o agente. O **Estado da ligação OMS** para a VM indica que é **não ligado**.  
    ![VM não ligado](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6.  Os detalhes do seu máquina virtual, selecione **Ligar**. O agente automaticamente é instalado e configurado para a área de trabalho de análise de registo. Este processo demora alguns minutos, durante os quais o estado da ligação OMS está a *ligar-se...*  
    ![Ligar VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7.  Depois de instalar e ligar o agente, o estado da **ligação OMS** será atualizado para mostrar **Esta área de trabalho**.  
    ![Ligado](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)


## <a name="enable-the-vm-extension-using-powershell"></a>Activar a extensão VM através do PowerShell

Existem comandos diferentes para Azure máquinas virtuais clássicas e máquinas virtuais de Gestor de recursos. Seguem-se exemplos para clássica e máquinas virtuais do Gestor de recursos.

Para máquinas virtuais clássicas, utilize o exemplo seguinte do PowerShell:

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Para máquinas virtuais de Gestor de recursos, utilize o exemplo seguinte do PowerShell:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```
Quando configurar a sua máquina de virtual utilizando o PowerShell, tem de fornecer o **ID da área de trabalho** e a **Chave primária**. Pode encontrar o Id e a chave na página **Definições** do OMS portal ou ao utilizar o PowerShell, conforme mostrado no exemplo anterior.

![ID de área de trabalho e de chave primária](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

## <a name="deploy-the-vm-extension-using-a-template"></a>Implementar a extensão VM através de um modelo

Ao utilizar o Gestor de recursos do Azure, pode criar um modelo simple (no formato JSON) que define a implementação e a configuração da sua aplicação. Este modelo é conhecido como um modelo de Gestor de recursos e fornece uma forma declarativa para definir a implementação. Ao utilizar um modelo, pode repetidamente implementar a aplicação em todo o ciclo de vida de aplicação e ter confiança que os recursos estão a ser implementados num estado consistente.

Incluindo o agente de análise de registo como parte do seu modelo de Gestor de recursos, pode assegurar que cada máquina virtual ser pré-configurada para um relatório para a área de trabalho de análise de registo.

Para mais informações sobre modelos de Gestor de recursos, consulte [Gestor de recursos do Azure criação de modelos](../resource-group-authoring-templates.md).

Segue-se um exemplo de um modelo de Gestor de recursos que é utilizado para implementar uma máquina virtual que está a executar o Windows com a extensão do Microsoft Agent monitorização instalada. Este modelo é um modelo de máquina virtual típica, com as adições seguintes:

+ parâmetros workspaceId e NomeDaÁreaDeTrabalho
+ Secção de extensão Microsoft.EnterpriseCloud.Monitoring recurso
+ Saídas para procurar a workspaceId e workspaceSharedKey


```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMD workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Pode implementar um modelo, utilizando o comando PowerShell seguinte:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-windows-virtual-machines"></a>Resolução de problemas máquinas de virtuais do Windows

Se a extensão de agente do *Microsoft Agent monitorização* VM não está a instalar ou elaboração de relatórios pode executar os passos seguintes para resolver o problema.

1. Verificar se o agente do Azure VM é instalado e a funcionar corretamente, utilizando os passos no [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
  + Também pode rever o ficheiro de registo do agente VM`C:\WindowsAzure\logs\WaAppAgent.log`
  + Se o registo não existir, o agente VM não está instalado.
    - [Instalar o agente de VM Azure em VMs clássicos](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md)
2. Confirme que a tarefa de heartbeat de extensão Microsoft Agent monitorização está a ser executado através dos seguintes passos:
  + Inicie sessão na máquina virtual
  + Abra o Programador de tarefas e localize o `update_azureoperationalinsight_agent_heartbeat` tarefa
  + Confirme a tarefa está ativada e está a ser executado cada um minuto
  + O ficheiro de registo de heartbeat de entrada`C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Rever os ficheiros de registo do Microsoft monitorização agente VM extensão no`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Certifique-se de que a máquina virtual pode executar scripts de PowerShell
4. Certifique-se de que não tenham sofrido alterações permissões no C:\Windows\temp
5. Ver o estado do agente de monitorização Microsoft escrevendo o seguinte numa janela do PowerShell elevada na máquina virtual`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Rever os ficheiros de registo de configuração do Microsoft Agent monitorização no`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obter mais informações, consulte [resolução de problemas de extensões do Windows](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md).

## <a name="troubleshooting-linux-virtual-machines"></a>Resolução de problemas máquinas virtuais de Linux

Se a extensão do agente de *Agente de OMS para Linux* VM não está a instalar ou elaboração de relatórios pode executar os passos seguintes para resolver o problema.

1. Se o estado de extensão é *desconhecido* verificar se o agente do Azure VM é instalado e a funcionar corretamente por rever o ficheiro de registo do agente VM`/var/log/waagent.log`
  + Se o registo não existir, o agente VM não está instalado.
  - [Instalar o agente de Azure VM no Linux VMs](../virtual-machines/virtual-machines-linux-agent-user-guide.md)
2. Para outras Estados danificados, reveja o agente OMS para extensão Linux VM registos de ficheiros na `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o estado da extensão está em bom estado, mas não estão a ser carregados dados rever o agente OMS para ficheiros de registo de Linux no`/var/opt/microsoft/omsagent/log/omsagent.log`

Para obter mais informações, consulte [Linux extensões de resolução de problemas](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md).


## <a name="next-steps"></a>Próximos passos

+ Configure [origens de dados no registo Analytics](log-analytics-data-sources.md) para especificar o métricas para recolher e registos.
+ Para recolher dados de virtual máquinas [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).
+ [Recolher dados utilizando os diagnósticos do Azure](log-analytics-azure-storage.md) para outros recursos que estiver a executar no Azure.

Para computadores que não estão no Azure, pode instalar o agente de análise de registo através dos métodos que são descritos nos seguintes artigos:

+ [Ligar computadores com o Windows para a análise de registo](log-analytics-windows-agents.md)
+ [Ligar Linux computadores à análise de registo](log-analytics-linux-agents.md)
