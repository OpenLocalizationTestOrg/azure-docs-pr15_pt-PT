<properties
   pageTitle="A automatização de implementação de aplicações com as extensões de Máquina Virtual | Microsoft Azure"
   description="Tutorial do Azure Máquina Virtual DotNet Core"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Implementação da aplicação com modelos de Gestor de recursos do Azure

Depois de todos os requisitos de infra-estruturas Azure tem sido identificados e traduzidos um modelo de implementação, a implementação de aplicação propriamente dita tem de ser resolvidos. Implementação da aplicação aqui é que fazem referência a instalar os binários da aplicação real para recursos Azure. Para a amostra de arquivo de música, .net Core e IIS têm de estar instalado e configurado em cada máquina virtual. Os binários loja de música precisa de estar instalado para a máquina virtual e a base de dados do arquivo de música previamente criado.

Este documento apresenta detalhes sobre como as extensões de Máquina Virtual podem automatizar implementação da aplicação e configuração para máquinas virtuais Azure. Todas as dependências e exclusivas configurações estão realçadas. Para obter a melhor experiência, previamente implemente uma instância da solução a sua subscrição do Azure e funcionam juntamente com o modelo de Gestor de recursos do Azure. O modelo concluído pode ser encontrado aqui – [Implementação de arquivo de música no Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows).

## <a name="configuration-script"></a>Script de configuração

Extensões de máquina virtual são especializados programas que executam contra máquinas virtuais para fornecer a automatização de configuração. Extensões estão disponíveis para muitos fins específicos como anti-virus, configuração do registo e configuração Docker. A extensão de Script personalizado pode ser utilizada para executar qualquer script contra uma máquina virtual. Com a amostra de arquivo de música, é até a extensão de scripts personalizados para configurar as máquinas virtuais do Windows e instale a aplicação da loja de música.

Antes de com detalhes sobre como as extensões de máquina virtual são declaradas num modelo de Gestor de recursos do Azure, examine o script que é executado. Este script configura a máquina de virtual do Windows para a aplicação de arquivo de música do anfitrião. Quando executar, o script instala todos necessário software, instalar a aplicação de arquivo de música a partir de controlo de origem e preparar a base de dados. 

> Este exemplo é para fins de demonstração.

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>Extensão de Script VM

Extensões VM podem ser executadas contra uma máquina virtual ao tempo de compilação incluindo o recurso de extensão no modelo de Gestor de recursos do Azure. Pode ser adicionada a extensão com o assistente Visual Studio adicionar recursos ou inserindo JSON válido para o modelo. O recurso de Script extensão estiver aninhado no interior do recurso Máquina Virtual; Isto pode ser visto no seguinte exemplo.

Siga esta ligação para ver a amostra JSON no modelo de Gestor de recursos – [VM Script extensão](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Repare na abaixo JSON que o script está armazenado no GitHub. Este script também pode ser armazenado numa armazenamento de Blobs do Azure. Além disso, os livros de Gestor de recursos do Azure permitem a cadeia de execução de script ser construídas assim que os valores de parâmetros de modelo podem ser utilizados como parâmetros para execução de script. Neste caso os dados são fornecidos quando implementar os modelos e estes valores, em seguida, podem ser utilizadas quando executar o script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Para mais informações sobre como utilizar a extensão de scripts personalizados, consulte o artigo [extensões de script personalizado com modelos de Gestor de recursos](./virtual-machines-windows-extensions-customscript.md).

## <a name="next-step"></a>Passo seguinte

<hr>

[Explorar mais Azure modelos de Gestor de recursos](https://github.com/Azure/azure-quickstart-templates)
