<properties
   pageTitle="Resolução de problemas de falhas de extensão Windows VM | Microsoft Azure"
   description="Saiba mais sobre resolução de problemas de falhas de extensão VM de Windows Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Resolução de problemas falhas de extensão VM de Windows Azure

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Ver o estado da extensão
Azure modelos de Gestor de recursos podem ser executados a partir do Azure PowerShell. Assim que o modelo é executado, pode ser visualizado o estado de extensão a partir do Azure recurso Explorer ou as ferramentas de linha de comandos.

Eis um exemplo:

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Eis o resultado do exemplo:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>As falhas de extensão de resolução de problemas

### <a name="re-running-the-extension-on-the-vm"></a>Executar novamente a extensão na VM

Se estiver a executar scripts na VM utilizando extensão de Script personalizado, por vezes, podem ocorrer um erro onde VM foi criado com êxito mas o script falhou. Nestas condições, o caminho recomendado para recuperar deste erro é remover a extensão e volte a executar o modelo novamente.
Nota: no futuro, esta funcionalidade seria melhorada para remover a necessidade de desinstalar a extensão.


#### <a name="remove-the-extension-from-azure-powershell"></a>Remover a extensão do PowerShell do Azure

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Assim que a extensão de ter sido removida, o modelo pode ser voltar a executar para executar os scripts na VM.
