<properties 
    pageTitle="Azure PowerShell com o Gestor de recursos | Microsoft Azure" 
    description="Introdução à utilização do Azure PowerShell para implementar vários recursos como um grupo de recursos para Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="tomfitz"/>

# <a name="using-azure-powershell-with-azure-resource-manager"></a>Utilizar o Azure PowerShell com o Gestor de recursos Azure

> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [Clip Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST API](resource-manager-rest-api.md)


Gestor de recursos do Azure implementa uma abordagem moderna para o controlo de ciclo de vida do Azure recursos. Em vez de criação e gestão de recursos individuais, comece por imaginando uma solução inteira, tal como um blogue, uma galeria de fotografias, um portal do SharePoint ou um wiki. Utilize um modelo – uma representação declarativa da solução – para definir um grupo de recursos que contém todos os recursos que necessários para suportar a solução. Em seguida, implementar e gerir esse grupo de recursos, como uma unidade lógico. 

Neste tutorial, saiba como utilizar o Azure PowerShell com o Gestor de recursos do Azure. -Orienta-o processo de implementação de uma solução e trabalhar com esta solução. Irá utilizar o PowerShell do Azure e um modelo de Gestor de recursos para implementar:

- SQL server - para a base de dados do anfitrião
- Base de dados do SQL - para armazenar os dados
- Regras de firewall - para permitir que a aplicação web ligar à base de dados
- Plano de serviço de aplicação - para definir as capacidades e o custo da aplicação web
- Web site - para executar a aplicação web
- Config Web - para armazenar a cadeia de ligação à base de dados 
- Regras de alertas - para monitorizar o desempenho e erros
- Informações de aplicação - para as definições de escala automática

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário:

- Uma conta do Azure
  + Pode [Abrir uma conta Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): obter o créditos pode utilizar para experimentar o nosso pagos serviços Azure e mesmo depois de que estão habituados até pode manter a conta e utilização livre Azure serviços, como sites. O cartão de crédito nunca será cobrado, exceto se alterar as definições e pedir para ser cobrada explicitamente.
  
  + Pode [Ativar benefícios de subscritor do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): subscrição da sua MSDN fornece-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.
- Azure PowerShell 1.0. Para obter informações sobre este lançamento e como instalar, consulte o artigo [como instalar e configurar o Azure PowerShell](powershell-install-configure.md).

Neste tutorial foi concebido para principiantes do PowerShell, mas parte do princípio de que compreender os conceitos básicos, tais como módulos, cmdlets e sessões.

## <a name="get-help-for-cmdlets"></a>Obter ajuda para os cmdlets do

Para obter ajuda detalhada sobre qualquer cmdlet que vê neste tutorial, utilize o cmdlet Get-Help. 

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet Get-AzureRmResource, escreva:

    Get-Help Get-AzureRmResource -Detailed

Para obter uma lista de cmdlets no módulo recursos com um resumo de ajuda, escreva: 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

O resultado terá o aspeto semelhante ao excerto seguinte:

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Para obter ajuda completa para um cmdlet, escreva um comando com o formato:

    Get-Help <cmdlet-name> -Full
  
## <a name="login-to-your-azure-account"></a>Iniciar sessão na sua conta Azure

Antes de trabalhar na sua solução, tem de iniciar sessão na sua conta.

Para iniciar sessão para a sua conta Azure, utilize o cmdlet **AzureRmAccount adicionar** .

    Add-AzureRmAccount

O cmdlet pede-lhe as credenciais de início de sessão para a sua conta Azure. Depois de início de sessão, transfere as definições de conta para que fiquem disponíveis para o Azure PowerShell. 

As definições da conta expirarem, pelo que necessita, ocasionalmente, actualizá-los. Para atualizar as definições da conta, execute novamente a **Adicionar AzureRmAccount** . 

>[AZURE.NOTE] Os módulos de Gestor de recursos requer AzureRmAccount adicionar. Um ficheiro de definições de publicar não é suficiente.     

Se tiver mais do que uma subscrição, fornece o id da subscrição que pretende utilizar para implementação com o cmdlet **Set-AzureRmContext** .

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de implementar quaisquer recursos à sua subscrição, terá de criar um grupo de recursos que irá conter os recursos. 

Para criar um grupo de recursos, utilize o cmdlet **AzureRmResourceGroup novo** .

O comando utiliza o parâmetro **nome** para especificar um nome para o grupo de recursos e o parâmetro de **localização** para especificar a sua localização. Com base no Microsoft descobriu na secção anterior, vai utilizamos "US Ocidental" para a localização.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
O resultado será semelhante a:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

O grupo de recursos foi criado com êxito.

## <a name="deploy-your-solution"></a>Implementar a sua solução

Este tópico não mostrar-lhe como criar o seu modelo ou discutir a estrutura do modelo. Para essas informações, consulte o artigo [modelos de criação de Gestor de recursos do Azure](resource-group-authoring-templates.md) e [Tutorial do Gestor de recursos do modelo](resource-manager-template-walkthrough.md). Irá implementar o modelo de [aprovisionar uma aplicação Web com uma base de dados do SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) predefinido a partir de [Modelos de guia de introdução do Azure](https://azure.microsoft.com/documentation/templates/).

Tiver o seu grupo de recursos e que tem o seu modelo, para que está agora pronto para implementar a infraestrutura de definido no seu modelo para o grupo de recursos. Implementar recursos com o cmdlet **AzureRmResourceGroupDeployment novo** . O modelo especifica muitos valores predefinidos, que irá utilizamos pelo que não necessita de fornecer valores para os parâmetros. A sintaxe básica tem a seguinte apresentação:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Especifique o grupo de recursos e a localização do modelo. Se o modelo de um ficheiro local, utilize o parâmetro **- ModeloEtiquetas** e especifique o caminho para o modelo. Pode definir a **-modo** parâmetro para **incremento** ou **concluída**. Por predefinição, o Gestor de recursos executa uma atualização utilizarão durante a implementação; Consequentemente, não é essencial para definir **-modo** quando quiser **incremento**. Para compreender as diferenças entre destes modos de implementação, consulte o artigo [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md). 

###<a name="dynamic-template-parameters"></a>Parâmetros de modelo dinâmico

Se estiver familiarizado com o PowerShell, sabe que pode percorrer os parâmetros disponíveis para um cmdlet, escrevendo um sinal de subtração (-) e, em seguida, premindo a tecla de tabulação. Esta funcionalidade mesmo também funciona com parâmetros definidos no seu modelo. Assim que escrever o nome do modelo, o cmdlet obtém o modelo, analisa-lo e adiciona os parâmetros de modelo para o comando dinamicamente. Isto torna muito fácil especificar os valores de parâmetro de modelo.

Quando introduz o comando, lhe for pedido para o parâmetro obrigatório em falta, **administratorLoginPassword**. E, quando escrever a palavra-passe, o valor de cadeia seguro é ocultado. Esta estratégia elimina o risco de fornecer uma palavra-passe em texto simples.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Se o modelo incluir um parâmetro com um nome que corresponde a um dos parâmetros no comando para implementar o modelo (por exemplo, incluindo um parâmetro com o nome **ResourceGroupName** no seu modelo que é o mesmo que o parâmetro **ResourceGroupName** no cmdlet [Novo AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) ), ser-lhe-á pedido para fornecer um valor de um parâmetro com o postfix **FromTemplate** (como **ResourceGroupNameFromTemplate**). Em geral, deverá evitar este confusões atribuindo não parâmetros com o mesmo nome como parâmetros utilizados para operações de implementação.

O comando é executado e devolve mensagens, tal como os recursos são criados. Finalmente, verá o resultado da sua implementação.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

Em apenas alguns passos, criado e implementado os recursos necessários para um Web site complexo. 

### <a name="log-debug-information"></a>Informações de depuração de registo

Quando implementar um modelo, pode registar informações adicionais sobre os pedidos e respostas ao especificar o parâmetro **- DeploymentDebugLogLevel** quando a ser executado **AzureRmResourceGroupDeployment novo**. Estas informações podem ajudá-lo a resolver problemas de erros de implementação. O valor predefinido é **nenhum** que significa que nenhum pedido ou conteúdo da resposta tem sessão iniciado. Pode especificar o conteúdo de registo do pedido, resposta ou ambos.  Para mais informações sobre implementações de resolução de problemas e informações de depuração de registo, consulte o artigo [implementações de grupo de recursos de resolução de problemas com o Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). O exemplo seguinte regista o conteúdo do pedido e o conteúdo de resposta para a implementação.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Quando definir o parâmetro DeploymentDebugLogLevel, considere cuidadosamente o tipo de informação que está transmitir durante a implementação. Por informações sobre a resposta ou um pedido de registo, poderia expor potencialmente confidenciais dados que são obtidos através de operações de implementação. 


## <a name="get-information-about-your-resource-groups"></a>Obter informações sobre os grupos de recursos

Depois de criar um grupo de recursos, pode utilizar os cmdlets no módulo Gestor de recursos para gerir grupos de recursos.

- Para obter um grupo de recursos na sua subscrição, utilize o cmdlet **Get-AzureRmResourceGroup** :

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
    
    Que devolve as seguintes informações:

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
        
        ...

    Se não especificar um nome de grupo de recursos, o cmdlet devolve todos os grupos de recursos na sua subscrição.

- Para obter os recursos no grupo de recursos, utilize o cmdlet **AzureRmResource localizar** e respectivo parâmetro **ResourceGroupNameContains** . Sem parâmetros, localizar AzureRmResource obtém todos os recursos na sua subscrição do Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
    
     Que devolve uma lista dos recursos formatado como:
        
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
            
- Pode utilizar etiquetas para logicamente organizar os recursos na sua subscrição e obter recursos com os cmdlets **AzureRmResource localizar** e **Localizar AzureRmResourceGroup** .

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>Adicionar a um grupo de recursos

Para adicionar um recurso ao grupo de recursos, pode utilizar o cmdlet **AzureRmResource novo** . No entanto, adicionar um recurso desta forma poderão causar confusões futuras porque o novo recurso não existe no seu modelo. Se voltar a implementado modelo antigo, teria implementar uma solução incompleta. Se estiver a implementar muitas vezes, irá encontrar mais fácil e mais fiável para adicionar o novo recurso ao seu modelo e implemente-o novamente.

## <a name="move-a-resource"></a>Deslocar-se um recurso

Pode mover os recursos existentes para um novo grupo de recursos. Para obter exemplos, consulte o artigo [Mover recursos para novo grupo de recursos ou subscrição](resource-group-move-resources.md).

## <a name="export-template"></a>Modelo de exportação

Para um grupo de recursos existente (implementado através do PowerShell ou um dos outros métodos de como o portal), pode ver o modelo de Gestor de recursos para o grupo de recursos. Exportar o modelo oferece duas vantagens:

1. Pode facilmente automatizar implementações futuras da solução porque todos infraestrutura do está definido no modelo.

2. Podem ficar familiarizado com a sintaxe de modelo procurando na JavaScript objeto notação (JSON) que representa a solução.

Através do PowerShell, pode gerar um modelo que representa o estado atual do seu grupo de recursos ou obter o modelo que foi utilizado para uma implementação específica.

Exportar o modelo para um grupo de recursos é útil quando tiverem efetuado alterações a um grupo de recursos e precisa de obter a representação de JSON do seu estado atual. No entanto, o modelo gerado contém um número mínimo de parâmetros e sem variáveis. A maioria dos valores no modelo são codificada. Antes de implementar o modelo gerado, poderá pretender converter mais dos valores de parâmetros para que pode personalizar a implementação para diferentes ambientes.

Exportar o modelo para uma determinada implementação é útil quando precisar de ver o modelo real que foi utilizado para implementar recursos. O modelo irá incluir todos os parâmetros e variáveis definidas para a implementação original. No entanto, se alguém na sua organização tiver efetuado alterações ao grupo de recursos fora o que é definido no modelo, este modelo irá não representar o estado atual do grupo de recursos.

> [AZURE.NOTE] A funcionalidade de modelo de exportação é na pré-visualização e não em todos os tipos de recursos atualmente, exportar um modelo. Ao tentar exportar um modelo, poderá ver um erro que diz que não foram exportados alguns recursos. Se for necessário, pode definir manualmente estes recursos no seu modelo após transferi-lo.

###<a name="export-template-from-resource-group"></a>Exportar modelo a partir do grupo de recursos

Para ver o modelo para um grupo de recursos, execute o cmdlet **AzureRmResourceGroup de exportação** .

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###<a name="download-template-from-deployment"></a>Transferir o modelo a partir de implementação

Para transferir o modelo utilizado para uma implementação específica, execute o cmdlet **AzureRmResourceGroupDeploymentTemplate guardar** .

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>Eliminar recursos ou grupo de recursos

- Para eliminar um recurso do grupo de recursos, utilize o cmdlet **AzureRmResource remover** . Este cmdlet elimina o recurso, mas não elimina o grupo de recursos.

    Este comando remove o Web site TestSite do grupo de recursos TestRG1.

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Para eliminar um grupo de recursos, utilize o cmdlet **AzureRmResourceGroup remover** . Este cmdlet elimina o grupo de recursos e seus recursos.

        Remove-AzureRmResourceGroup -Name TestRG1
        
    São-lhe pedido para confirmar a eliminação.
        
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>Script de implementação

Os exemplos de implementação anteriores neste tópico mostrava apenas os cmdlets individuais necessários para implementar recursos Azure. O exemplo seguinte mostra um script de implementação que cria o grupo de recursos e implementa os recursos.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre a criação de modelos de Gestor de recursos, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](./resource-group-authoring-templates.md).
- Para obter informações sobre como implementar modelos, consulte o artigo [Implementar uma aplicação com Azure Gestor de recursos do modelo](./resource-group-template-deploy.md).
- Para um exemplo detalhado de implementação de um projeto, consulte o artigo [Implementar microservices seja previsível no Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para saber mais sobre uma implementação que ocorreu uma falha de resolução de problemas, consulte o artigo [implementações de grupo de recursos de resolução de problemas no Azure](./resource-manager-troubleshoot-deployments-powershell.md).

