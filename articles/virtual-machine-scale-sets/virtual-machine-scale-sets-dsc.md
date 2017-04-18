<properties
   pageTitle="Utilizar pretendido configuração estada com conjuntos de escala de Máquina Virtual | Microsoft Azure"
   description="Utilizar a escala de Máquina Virtual conjuntos com a extensão de DSC Azure"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Utilizar a escala de Máquina Virtual conjuntos com a extensão de DSC Azure

[Conjuntos de escala de máquina virtual (VMSS)](virtual-machine-scale-sets-overview.md) podem ser utilizados com a alça de extensão [Azure pretendido estado configuração (DSC)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md) . VMSS fornece uma maneira de implementar e gerir grandes quantidades de máquinas virtuais e pode elastically dimensionar e reduzir em resposta ao carregar. DSC é utilizada para configurar os VMs como eles ficar online, de modo que estão a executar o software de produção.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Diferenças entre implementá-lo na VM e VMSS

A estrutura de modelo subjacente para VMSS é ligeiramente diferente a partir de uma única VM. Especificamente, uma única VM implementa extensões sob o nó "virtualMachines". Existe uma entrada do tipo "extensões" onde DSC é adicionado ao modelo

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Um nó VMSS tem uma secção "Propriedades" com o "VirtualMachineProfile", "extensionProfile" atributo. DSC é adicionada em "extensões"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Comportamento do VMSS

O comportamento do VMSS é idêntico ao comportamento de uma única VM. Quando é criada uma nova VM, está aprovisionado automaticamente com a extensão de DSC. Se for necessário uma versão mais recente da WMF pela extensão de nome, a VM reinicia antes de ficar online. Assim que estiver online, transferências. zip DSC configuração e aprovisionar-na VM. Obter mais detalhes podem ser encontrados na [Descrição geral do Azure DSC extensão](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Próximos passos ##
Examine o [modelo de Gestor de recursos do Azure para a extensão de DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Saiba como a [extensão de DSC segura processa credenciais](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Para mais informações sobre a alça de extensão Azure DSC, consulte [Introdução ao processador de extensão Azure configuração de estado pretendido](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Para mais informações sobre DSC PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 


