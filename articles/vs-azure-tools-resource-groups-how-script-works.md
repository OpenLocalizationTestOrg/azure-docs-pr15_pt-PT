<properties
    pageTitle="Descrição geral de script de implementação de projeto de grupo de recursos do Azure | Microsoft Azure"
    description="Descreve como funciona o script do PowerShell no projeto de implementação do Azure grupo de recursos."
    services="visual-studio-online"
    documentationCenter="na"
    authors="tfitzmac"
    manager="timlt"
    editor="" />

 <tags
    ms.service="azure-resource-manager"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/26/2016"
    ms.author="tomfitz" />

# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Descrição geral de script de implementação de projeto de grupo de recursos do Azure

Implementação de grupo de recursos do Azure projectos ajudá-lo fase e implementar ficheiros e outros artefactos Azure. Quando cria um projeto de implementação do Azure o Gestor de recursos no Visual Studio, um script PowerShell denominado **Implementar AzureResourceGroup.ps1** é adicionado ao projeto. Este tópico fornece mais informações sobre o que significa este script e como executá-la dentro e fora do Visual Studio.

## <a name="what-does-the-script-do"></a>O que faz o script?

O script implementar AzureResourceGroup.ps1 faz duas coisas que são importantes para o fluxo de trabalho de implementação.

- Carregar todos os ficheiros ou artefactos necessários para a implementação de modelo
- Implementar o modelo

A primeira parte do script carregamentos de ficheiros e artefactos para implementação e o último cmdlet no script realmente implementar o modelo. Por exemplo, se uma máquina virtual tem de ser configurado com um script, o script de implementação pela primeira vez em segurança carrega o script de configuração de uma conta de armazenamento Azure. Isto torna disponível para o Gestor de recursos do Azure para configurar a máquina virtual durante o aprovisionamento.

Porque não em todos os implementações do modelo precisam de ter artefactos extra que necessitam de ser carregado, é avaliado um parâmetro denominado *uploadArtifacts* . Se precisar de qualquer artefactos de ser carregado, defina o parâmetro *uploadArtifacts* ao chamar o script. Tenha em atenção que o ficheiro de modelo principal e o ficheiro de parâmetros não precisam de ser carregado. Apenas outros ficheiros, tal como scripts de configuração, as funções aninhadas modelos de implementação e ficheiros de aplicação precisam de ser carregado.

## <a name="detailed-script-description"></a>Descrição detalhada script

Segue-se uma descrição das secções que selecione fazer de script implementar AzureResourceGroup.ps1 Azure PowerShell.

>[AZURE.NOTE] Este exemplo descreve versão 1.0 do script AzureResourceGroup.ps1 implementar.

1.  Declare parâmetros necessários pelo project de implementação do Azure o Gestor de recursos. Alguns parâmetros têm valores predefinidos que foram definidas quando o projeto foi criado. Pode alterar estes valores predefinidos no script ou adicionar valores de parâmetros diferentes antes de executar o script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

  	|Parâmetro|Descrição|
  	|---|---|
  	|$ResourceGroupLocation|Os dados da região ou centrar a localização para o grupo de recursos, como **EUA Ocidental** ou **Ásia**.|
  	|$ResourceGroupName|O nome do grupo de recursos Azure.|
  	|$UploadArtifacts|Um valor binário que indica se artefactos precisam de ser carregado para Azure a partir do seu sistema.|
  	|$StorageAccountName|O nome da sua conta de armazenamento Azure onde o seu artefactos são carregados.|
  	|$StorageAccountResourceGroupName|O nome do grupo de recursos Azure que contém a conta de armazenamento.|
  	|$StorageContainerName|O nome do contentor de armazenamento utilizado para carregar artefactos.|
  	|$TemplateFile|O caminho para o ficheiro de implementação (`<app name>.json`) no seu projeto de grupo de recursos do Azure.|
  	|$TemplateParametersFile|O caminho para o ficheiro de parâmetros (`<app name>.parameters.json`) no seu projeto de grupo de recursos do Azure.|
  	|$ArtifactStagingDirectory|O caminho no sistema de onde artefactos localmente carregados, incluindo a pasta de raiz de script do PowerShell. Este caminho pode ser referências absolutas ou relativo a localização de script.|
  	|$AzCopyPath|O caminho onde a ferramenta de AzCopy.exe copia os respetivos ficheiros. zip, incluindo a pasta de raiz de script do PowerShell. Este caminho pode ser referências absolutas ou relativo a localização de script.|
  	|$DSCSourceFolder|O caminho para a pasta de origem DSC (pretendido estado configuração), incluindo a pasta de raiz de script do PowerShell. Este caminho pode ser referências absolutas ou relativo a localização de script. Consulte o artigo [Introdução à extensão do Azure PowerShell DSC (pretendido estado configuração)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), se aplicável, para obter mais informações.|

1.  Verifique se artefactos precisam de ser carregado para Azure. Caso contrário, avance para o passo 11. Caso contrário, execute os passos seguintes.

1.  Converta todas as variáveis com caminhos relativas caminhos absolutos. Por exemplo, tais como a alterar um caminho `..\Tools\AzCopy.exe` para `C:\YourFolder\Tools\AzCopy.exe`. Além disso, iniciar variáveis *ArtifactsLocationName* e *ArtifactsLocationSasTokenName* nulo. *ArtifactsLocation* e *SaSToken* podem ser parâmetros para o modelo. Se os respetivos valores nulos depois de leitura no ficheiro de parâmetros, o script gera valores para os mesmos.

    As ferramentas do Azure utilize o parâmetro valores *_artifactsLocation* e *_artifactsLocationSasToken* no modelo para gerir artefactos. Se o script do PowerShell localiza parâmetros com os nomes, mas não são fornecidos os valores de parâmetros, o script os carregamentos pendentes erros e devolve valores adequados para os parâmetros. Em seguida, transmite-los para o cmdlet através do `@OptionsParameters`.

  	|Variável|Descrição|
  	|---|---|
  	|ArtifactsLocationName|O caminho para onde estão localizados os erros Azure.|
  	|ArtifactsLocationSasTokenName|O nome de token SA (partilhados assinatura do Access) que é utilizado pelo script para autenticar Bus de serviço. Para mais informações, consulte [Autenticação de assinatura de acesso partilhado com Bus de serviço](service-bus-shared-access-signature-authentication.md) .|

    ```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.  Esta secção verifica se o <app name>. parameters.json ficheiro (designado "ficheiro de parâmetros") tem um nó principal **parâmetros** com nome (na `else` bloco). Caso contrário, não tem nenhum nó principal. O formato é aceitável.
    
    ```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.  Iteração na coleção de parâmetros JSON. Se tiver sido atribuído um valor de parâmetro para *_artifactsLocation* ou *_artifactsLocationSasToken*, em seguida, defina a variável *$OptionalParameters* com esses valores. Isto impede o script de substituir inadvertidamente os valores de parâmetros fornecidos.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.  Obter a chave de conta de armazenamento e o contexto para o recurso de conta de armazenamento utilizado para colocar em espera erros para implementação.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.  Se estiver a utilizar o PowerShell DSC para configurar uma máquina virtual, a extensão de DSC necessita de erros estar num ficheiro zip único. Sim, crie um ficheiro de arquivo. zip para a configuração de DSC. Para fazer isto, verifique se existe $DSCSourceFolder. Se existir uma configuração DSC, removê-lo e, em seguida, crie um novo ficheiro comprimido denominado dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.  Não se for fornecido nenhum caminho para artefactos Azure no ficheiro de parâmetros, defina um caminho para o script do PowerShell utilizar quando carregar artefactos. Para fazer isto, crie um caminho utilizando uma combinação de caminho de ponto final a conta de armazenamento e o nome de contentor de armazenamento. Em seguida, atualize o ficheiro de parâmetros com este caminho de novo.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.  Utilize o utilitário **AzCopy** (incluído na pasta **Ferramentas** do seu projeto de implementação do grupo de recursos do Azure) para copiar os ficheiros a partir do seu caminho de lista pendente de armazenamento local para a sua conta de armazenamento do Windows Azure online. Se este passo falhar, saia o script desde a implementação não é provável que teve êxito sem erros necessários.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.  Se tiver um token de SA para a localização de artefactos não for fornecido no ficheiro de parâmetros, crie um para fornecer acesso só de leitura temporário ao contentor de armazenamento online. Em seguida, passar esse token de SA para o linhacmd como um "optionalParameter". Tenha em atenção que quaisquer parâmetros passados na linhacmd terá precedência sobre valores, fornecidos no ficheiro de parâmetros.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Crie o grupo de recursos se ainda não existir e dar entrada do ficheiro de modelo e parâmetros para quaisquer erros de validação irá impedir a implementação de que se seguem.

    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Por fim, implemente o modelo. Este código cria um nome exclusivo para a implementação utilizando um carimbo de data/hora.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## <a name="deploy-the-resource-group"></a>Implementar o grupo de recursos

### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Para implementar o grupo de recursos no Visual Studio

1. No menu de atalho do projeto de grupo de recursos do Azure, selecione **Implementar** > **Nova implementação**.

    ![][0]

1. Na caixa de diálogo **Implementar a grupo de recursos** , ou selecione um grupo de recursos existente na caixa de listagem pendente para implementar ou selecione ** &lt;criar novo... &gt;** Para criar um novo grupo de recursos.

    ![][1]

1. Se lhe for pedido, introduza um nome de grupo de recursos e uma localização na caixa de diálogo **Criar grupo de recursos** e, em seguida, selecione o botão **Criar** .

    ![][2]

1. Selecione o botão **Editar parâmetros** para ver a caixa de diálogo **Editar parâmetros** e, em seguida, introduza os valores de parâmetros em falta.

    ![][3]

    >[AZURE.NOTE] Se precisar de quaisquer parâmetros necessários valores, esta caixa de diálogo aparece automaticamente quando implementar.

    ![][4]

1. Quando terminar de introduzir valores de parâmetros, selecione o botão **Guardar** e, em seguida, selecione o botão de **Implementar** .

    O script de implementação (implementar AzureResourceGroup.ps1) é executado e implementa o seu modelo, juntamente com quaisquer artefactos, para Azure.

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>Para implementar o grupo de recursos utilizando o PowerShell

Se pretender executar o script sem utilizar o comando Visual Studio implementar e a IU, no menu de atalho para o script, escolha **Abrir com o ISE do PowerShell**.

![][5]


## <a name="command-deployment-examples"></a>Exemplos de implementação de comando

### <a name="deploy-using-default-values"></a>Implementar utilizando valores predefinidos

Este exemplo mostra como executar o script utilizando os valores de parâmetro predefinidos. (Uma vez que o parâmetro de localização não tiver um valor predefinido, tem de fornecer um.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>Implementar a substituir os valores predefinidos

Este exemplo mostra como executar o script para implementar o modelo e parâmetros de ficheiros que diferem dos valores predefinidos.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>Implementar com UploadArtifacts para transição

Este exemplo mostra como executar o script para carregar artefactos a partir da pasta de lançamento e implementar modelos de não predefinida.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Este exemplo mostra como executar o script numa tarefa Azure PowerShell no Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre o Gestor de recursos do Azure ao ler o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md).

Para obter mais exemplos de como trabalhar com projetos de grupo de recursos do Azure, consulte o artigo [Implementar e gerir recursos Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources) a partir de ligar a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [demonstração](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Para obter mais tutoriais a partir de demonstração HealthClinic.biz, consulte o artigo [Tutoriais de ferramentas de programador do Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png
