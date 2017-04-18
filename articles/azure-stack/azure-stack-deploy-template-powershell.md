<properties
    pageTitle="Implementar modelos com PowerShell Azure empilhados | Microsoft Azure"
    description="Saiba como implementar uma máquina de virtual utilizando um modelo de Gestor de recursos e PowerShell."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Implementar modelos de empilhados Azure através do PowerShell

Utilize o PowerShell para implementar modelos de Gestor de recursos do Azure o conceito de pilha Azure.  Modelos de Gestor de recursos implementar e aprovisionar todos os recursos para a sua aplicação numa operação de única e coordenada.

## <a name="run-azurerm-powershell-cmdlets"></a>Executar os cmdlets do AzureRM PowerShell

Neste exemplo, executar um script para implementar uma máquina virtual Azure pilha conceito através de um modelo de Gestor de recursos.  Antes de continuar, certifique-se que tiver [instalado e configurado o PowerShell](azure-stack-connect-powershell.md)  

O VHD utilizado neste modelo de exemplo é uma imagem predefinida de marketplace (WindowsServer-2012-R2-Centro de dados).

1.  Vá para <http://aka.ms/AzureStackGitHub>, procure o modelo de **101-simples-windows-vm** e guardá-lo para a seguinte localização: c:\\modelos\\azuredeploy-101-simples-windows-vm.json.

2.  No PowerShell, execute o seguinte script de implementação. Substitua o *nome de utilizador* e *palavra-passe* com o seu nome de utilizador e palavra-passe. No utilizações subsequentes, incremente o valor para o parâmetro *$myNum* impedir a substituição a sua implementação.

    ```PowerShell
        # Set Deployment Variables
        $myNum = "001" #Modify this per deployment
        $RGName = "myRG$myNum"
        $myLocation = "local"

        # Create Resource Group for Template Deployment
        New-AzureRmResourceGroup -Name $RGName -Location $myLocation

        # Deploy Simple IaaS Template
        New-AzureRmResourceGroupDeployment `
            -Name myDeployment$myNum `
            -ResourceGroupName $RGName `
            -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
            -NewStorageAccountName mystorage$myNum `
            -DnsNameForPublicIP mydns$myNum `
            -AdminUsername <username> `
            -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
            -VmName myVM$myNum `
            -WindowsOSVersion 2012-R2-Datacenter
    ```

3.  Abra o portal do Azure pilha, clique em **Procurar**, clique em **máquinas virtuais**e procure a sua nova máquina virtual (*myDeployment001*).

## <a name="video-example-hybrid-virtual-machine-deployment"></a>Exemplo de vídeo: implementação híbrida do máquina virtual

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-hybrid-vm-deployment]

## <a name="next-steps"></a>Próximos passos

[Implementar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
