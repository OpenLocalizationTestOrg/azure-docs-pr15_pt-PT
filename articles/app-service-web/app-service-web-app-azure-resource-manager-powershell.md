<properties
    pageTitle="Comandos do PowerShell com base no Gestor de recursos do Azure Azure Web App para | Microsoft Azure"
    description="Saiba como utilizar os comandos do PowerShell com base no Gestor de recursos do Azure novos para gerir as suas aplicações Web do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Utilizar o PowerShell de com base no Gestor de recurso Azure para gerir aplicações Azure Web#

> [AZURE.SELECTOR]
- [Clip Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Com o Microsoft Azure PowerShell versão 1.0.0 novos comandos foram adicionadas, de que o utilizador conceder a capacidade de utilizar os comandos do PowerShell com base no Gestor de recursos do Azure para gerir aplicações Web.

Para obter informações sobre a gestão de grupos de recursos, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md). 

Para saber mais sobre a lista completa dos parâmetros e opções para os cmdlets do PowerShell, consulte a [Referência do Cmdlet completo dos cmdlets do PowerShell com base no Gestor de recursos do Azure de aplicação Web](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Gerir a aplicação de serviço de planos ##

### <a name="create-an-app-service-plan"></a>Criar um plano de serviço de aplicação ###
Para criar um plano de serviço de aplicação, utilize o cmdlet **AzureRmAppServicePlan novo** .

Seguem-se descrições dos parâmetros diferentes:

-   **Nome**: nome do plano de serviço de aplicação.
-   **Localização**: a localização de plano de serviço.
-   **ResourceGroupName**: grupo de recursos que inclui o plano de serviço de aplicação recentemente criado.
-   **Camadas**: a camada comparar pretendida (predefinição é gratuito, as outras opções são partilhados, Basic, padrão e Premium).
-   **WorkerSize**: o tamanho dos trabalhadores (a predefinição é pequena se o parâmetro de camada foi especificado como básica, padrão ou Premium. Outras opções são média e grande.)
-   **NumberofWorkers**: o número de trabalhadores na aplicação de serviço plano (valor predefinido é 1). 

Exemplo para utilizar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Criar um plano de serviço de aplicação num ambiente de aplicação de serviço ###
Para criar um plano de serviço de aplicação num ambiente do serviço de aplicação, utilize o mesmo comando **AzureRmAppServicePlan novo** comando com parâmetros adicionais para especificar o Auxiliar e um nome de Auxiliar recurso grupo.

Exemplo para utilizar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Para saber mais sobre o ambiente de serviço de aplicação, consulte [Introdução à aplicação de ambiente de serviço](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Planos do serviço de lista existente de aplicação ###

Para listar os planos do serviço de aplicação existente, utilize o cmdlet **Get-AzureRmAppServicePlan** .

Para listar todos os planos de serviço de aplicação com a sua subscrição, utilize: 

    Get-AzureRmAppServicePlan

Para listar todos os planos de serviço de aplicação num grupo de recursos específica, utilize:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Para obter um plano de serviço de aplicação específica, utilize:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurar um plano de serviço de aplicação existente ###

Para alterar as definições para um plano de serviço de aplicação existente, utilize o cmdlet **Set-AzureRmAppServicePlan** . Pode alterar a camada, tamanho de trabalho e o número de trabalhadores 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Dimensionamento um plano de serviço de aplicação ####

Para dimensionar um plano de serviço de aplicação existente, utilize:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Alterar o tamanho de trabalho de um plano de serviço de aplicação ####

Para alterar o tamanho dos trabalhadores num plano de serviço de aplicação existente, utilize:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Alterar a camada de um plano de serviço de aplicação ####

Para alterar a camada de um plano de serviço de aplicação existente, utilize:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Eliminar um plano de serviço de aplicação existente ###

Para eliminar um plano de serviço de aplicação existente, todas as aplicações web atribuídas tem de ser movido ou eliminado pela primeira vez. Em seguida, utilizar o cmdlet **Remover AzureRmAppServicePlan** pode eliminar o plano de serviço de aplicação.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Gerir a aplicação de serviço Web Apps ##

### <a name="create-a-web-app"></a>Criar uma aplicação Web ###

Para criar uma aplicação web, utilize o cmdlet **AzureRmWebApp novo** .

Seguem-se descrições dos parâmetros diferentes:

- **Nome**: nome para a aplicação web.
- **AppServicePlan**: nome para o plano de serviço utilizado para a aplicação web do anfitrião.
- **ResourceGroupName**: grupo de recursos que aloja o plano de serviço de aplicação.
- **Localização**: a localização da aplicação web.

Exemplo para utilizar este cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Criar uma aplicação Web num ambiente de aplicação de serviço ###

Para criar uma aplicação web no ambiente de serviço de uma aplicação (Auxiliar). Utilize o mesmo comando **Novo AzureRmWebApp** com parâmetros adicionais para especificar o nome de Auxiliar e o nome do grupo de recursos que pertence a Auxiliar.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Para saber mais sobre o ambiente de serviço de aplicação, consulte [Introdução à aplicação de ambiente de serviço](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Eliminar uma aplicação Web existente ###

Para eliminar uma aplicação web existente, pode utilizar o cmdlet **Remover AzureRmWebApp** , precisa de especificar o nome do web app e o nome do grupo de recursos.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Lista existente Web Apps ###

A lista de aplicações web existente, utilize o cmdlet **Get-AzureRmWebApp** .

Para listar todas as aplicações web com a sua subscrição, utilize:

    Get-AzureRmWebApp

Para listar todas as aplicações web num grupo de recursos específica, utilize:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Para obter uma aplicação web específica, utilize:

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurar uma aplicação Web existente ###

Para alterar as definições e as configurações para uma aplicação web existente, utilize o cmdlet **Set-AzureRmWebApp** . Para obter uma lista completa dos parâmetros, verifique a [ligação de referência do Cmdlet](https://msdn.microsoft.com/library/mt652487.aspx)

Exemplo (1): Utilize este cmdlet para alterar as cadeias de ligação

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Exemplo (2): Adicionar ou alterar as definições da aplicação

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Exemplo (3): configurar a aplicação web para executar no modo de 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Alterar o estado de uma aplicação Web existente ###

#### <a name="restart-a-web-app"></a>Reiniciar uma aplicação web ####

Para reiniciar a uma aplicação web, tem de especificar o grupo de nome e recursos da aplicação web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Parar uma aplicação web ####

Para parar de uma aplicação web, tem de especificar o grupo de nome e recursos da aplicação web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar uma aplicação web ####

Para iniciar uma aplicação web, tem de especificar o grupo de nome e recursos da aplicação web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gerir perfis de publicação na Web App ###

Cada aplicação web tem um perfil de publicação que pode ser utilizado para publicar as suas aplicações, várias operações podem ser executadas em perfis de publicação.

#### <a name="get-publishing-profile"></a>Obter o perfil de publicação ####

Para obter o perfil de publicação para uma aplicação web, utilize:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Este comando ecos o perfil de publicação para a linha de comandos saída bem como o perfil de publicação a um ficheiro de texto.

#### <a name="reset-publishing-profile"></a>Repor o perfil de publicação ####

Para repor tanto a palavra-passe publicação para FTP e da web implementar para uma aplicação web, utilize:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Gerir os certificados de aplicação Web ###

Para saber mais sobre como gerir certificados da aplicação web, consulte o artigo [encadernação de certificados SSL através do PowerShell](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>Próximos passos ###
- Para saber mais sobre o suporte do Azure Gestor de recursos do PowerShell, consulte o artigo [utilizar o PowerShell Azure com o Gestor de recursos do Azure.](../powershell-azure-resource-manager.md)
- Para saber mais sobre ambientes de serviço de aplicação, consulte o artigo [Introdução à aplicação de ambiente de serviço.](app-service-app-service-environment-intro.md)
- Para obter informações sobre a gestão de certificados SSL de serviço de aplicação através do PowerShell, consulte o artigo [encadernação de certificados SSL através do PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Para obter informações sobre a lista completa dos cmdlets do PowerShell com base no Gestor de recursos do Azure para Azure Web Apps, consulte o artigo [Azure referência do Cmdlet do Web Apps Azure Gestor de recursos Cmdlets do PowerShell.](https://msdn.microsoft.com/library/mt619237.aspx)
- - Para obter informações sobre como gerir o serviço de aplicação utilizando o clip, consulte o artigo [Using Azure Resource Manager-Based XPlat clip para Azure Web App.](app-service-web-app-azure-resource-manager-xplat-cli.md)
