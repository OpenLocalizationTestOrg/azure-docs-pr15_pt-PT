<properties
    pageTitle="Implementar uma aplicação web utilizar MSDeploy com o certificado hostname e ssl"
    description="Utilizar um modelo de Gestor de recursos do Azure para implementar uma aplicação web utilizando MSDeploy e configurar o nome do anfitrião personalizado e um certificado SSL"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Implementar uma aplicação web com MSDeploy, o nome do anfitrião personalizado e o certificado SSL

Este guia explica a criação de uma implementação do fim para fim para uma aplicação Web do Azure, tirar partido da MSDeploy, bem como adicionar um nome de anfitrião personalizado e um certificado SSL ao modelo de processador.

Para mais informações sobre a criação de modelos, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md).

###<a name="create-sample-application"></a>Criar a aplicação de exemplo

Implementar uma aplicação web do ASP.NET. O primeiro passo é criar uma aplicação web simples (ou pode optar por utilizar existente - nesse caso pode ignorar este passo).

Abrir o Visual Studio 2015 e escolha ficheiro > novo projeto. Na caixa de diálogo que aparece escolher a Web > aplicação Web do ASP.NET. Em modelos selecione Web e selecione o modelo MVC. Selecione _alterar o tipo de autenticação_ para _A autenticação sem_. Este é apenas para tornar a aplicação de exemplo tão simples quanto possível.

Neste momento terá uma básica aplicação de web do ASP.Net pronta para utilizar como parte do processo de implementação.

###<a name="create-msdeploy-package"></a>Criar pacote de MSDeploy

Passo seguinte é criar o pacote para implementar a aplicação web Azure. Para fazer isto, guarde o seu projeto e, em seguida, execute o seguinte na linha de comandos:

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Isto irá criar um pacote ZIP na pasta PackageLocation. A aplicação está agora pronta para ser implementada, que pode construir um modelo de Gestor de recursos do Azure para fazê-lo agora.

###<a name="create-arm-template"></a>Criar o modelo de processador
Primeiro, vamos começar com um modelo de processador básico que vai criar uma aplicação web e um plano de alojamento (note que parâmetros e variáveis não são apresentadas para uma questão de brevidade).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Em seguida, terá de modificar o recurso de aplicação web para colocar um recurso de MSDeploy aninhado. Isto permitirá a referência o pacote criada anteriormente e indicar ao Gestor de recursos do Azure utilizar MSDeploy para implementar o pacote para a Web App Azure. A imagem seguinte mostra o recurso Microsoft.Web/sites com o recurso MSDeploy aninhado:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Agora irá Repare que o recurso MSDeploy leva-o até uma propriedade de **packageUri** que é definida da seguinte forma:

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Este **packageUri** leva-o até o uri de conta de armazenamento que aponta para a conta de armazenamento onde irá carregar os seus zip de pacote para. O Gestor de recursos do Azure irá tirar partido [Partilhado assinaturas de acesso](../storage/storage-dotnet-shared-access-signature-part-1.md) para separar o pacote para baixo localmente da conta de armazenamento quando implementar o modelo. Este processo será automatizado através de um PowerShell script que irá carregar o pacote e chamar a API de gestão do Azure para criar as teclas obrigatório e transmitir aqueles para o modelo como parâmetros (*_artifactsLocation* e *_artifactsLocationSasToken*). Terá de definir parâmetros para a pasta e nome de ficheiro do pacote de é carregado para dentro do contentor de armazenamento.

Seguinte que tem de adicionar a outro recurso aninhado para configurar os enlaces hostname tirar partido de um domínio personalizado. Primeiro terá de se certificar de que o proprietário do nome de anfitrião e configurá-lo para ser verificado por Azure que é o proprietário - consulte [configurar um nome de domínio personalizado no Azure aplicação de serviço](web-sites-custom-domain-name.md). Uma vez que tenha feito pode adicionar ao seu modelo na secção de recurso Microsoft.Web/sites o seguinte:

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Por fim tem de adicionar outro superior nível recurso, Microsoft.Web/certificates. Este recurso irá conter seu certificado SSL e existirão no mesmo nível da aplicação web e o plano de alojamento.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Terá de ter um certificado SSL válido para poder configurar este recurso. Assim que tiver esse certificado válido, em seguida, é necessário extrair os bytes pfx como uma cadeia base64. Uma das opções para extrair isto é utilizar o comando PowerShell seguinte:

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Pode, em seguida, transmitir tal como um parâmetro ao seu modelo de implementação de processador.

Neste momento o modelo de processador está pronto.

###<a name="deploy-template"></a>Implementar o modelo

Os passos finais são a peça isto tudo em conjunto para uma implementação completa fim para fim. Para facilitar a implementação que pode tirar partido o script do PowerShell **Implementar AzureResourceGroup.ps1** que é adicionado ao criar um projeto de grupo de recursos do Azure no Visual Studio para o ajudar com a carregar qualquer artefactos obrigatório no modelo. Necessita de ter criado uma conta de armazenamento que pretende utilizar antecedência. Neste exemplo, posso criada uma conta de armazenamento partilhado para package.zip ser carregado. O script irá tirar partido AzCopy para carregar o pacote para a conta de armazenamento. Passar na sua localização de pasta artefacto e o script irá carregar automaticamente todos os ficheiros que diretório para o contentor de armazenamento com nome. Depois de chamar implementar AzureResourceGroup.ps1 tem de em seguida, atualize os enlaces SSL para mapear o nome do anfitrião personalizado com o seu certificado SSL.

O PowerShell seguinte mostra a implementação de concluída a implementar a chamar-AzureResourceGroup.ps1:

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

Neste momento a aplicação deverá foram implementada e deverá conseguir navegar para o mesmo através do https://www.yourcustomdomain.com
