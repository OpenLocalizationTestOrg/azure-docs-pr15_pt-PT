<properties
    pageTitle="Ferramentas de Azure linha de comandos em diferentes plataformas com base no Gestor de recursos para Azure da aplicação Web | Microsoft Azure"
    description="Saiba como utilizar as novas ferramentas de linha de comandos de em diferentes plataformas com base no Gestor de recursos do Azure para gerir as suas aplicações Web do Azure."
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

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Utilizar o Azure recursos com base no Gestor XPlat clip do Azure Web App#

> [AZURE.SELECTOR]
- [Clip Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Com a versão da versão de ferramentas de linha de comandos do Microsoft Azure em diferentes plataformas 0.10.5, foram adicionados novos comandos. Estes comandos dar o utilizador a capacidade de utilizar os comandos do PowerShell com base no Gestor de recursos do Azure para gerir aplicações Web.

Para obter informações sobre a gestão de grupos de recursos, consulte o artigo [utilizar o clip do Azure para gerir grupos de recursos e recursos Azure](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Gerir a aplicação de serviço de planos ##

### <a name="create-an-app-service-plan"></a>Criar um plano de serviço de aplicação ###
Para criar um plano de serviço de aplicação, utilize o comando **Criar azure appserviceplan** .

Seguem-se descrições dos parâmetros diferentes:

-   **– grupo de recursos**: grupo de recursos que inclui o plano de serviço de aplicação recentemente criado.
-   **– nome**: nome do plano de serviço de aplicação.
-   **– localização**: localização do plano de serviço de aplicação.
-   **– camada**: o sku comparar pretendido (as opções são: F1 (gratuito). D1 (partilhada). B1 (básico pequeno), B2 (básico médio) e B3 (Basic grande). S1 (padrão pequeno), S2 (padrão médio) e S3 (padrão grande). P1 (Premium pequeno), P2 (Premium médio) e P3 (Premium grande).)
-   **– instâncias**: o número de trabalhadores na aplicação de serviço plano (valor predefinido é 1).

Exemplo para utilizar este cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>Planos do serviço de lista existente de aplicação ###

Para listar os planos do serviço de aplicação existente, utilize o comando **azure appserviceplan lista** .

Para listar todos os planos de serviço de aplicação num grupo de recursos específica, utilize:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Para obter um plano de serviço de aplicação específica, utilize o comando **Mostrar azure appserviceplan** :

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurar um plano de serviço de aplicação existente ###

Para alterar as definições para um plano de serviço de aplicação existente, utilize o comando **azure appserviceplan config** . Pode alterar o sku e o número de trabalhadores 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Dimensionamento um plano de serviço de aplicação ####

Para dimensionar um plano de serviço de aplicação existente, utilize:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Alterar o SKU de um plano de serviço de aplicação ####

Para alterar o sku de um plano de serviço de aplicação existente, utilize:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Eliminar um plano de serviço de aplicação existente ###

Para eliminar um plano de serviço de aplicação existente, todas as aplicações web atribuídas tem de ser movido ou eliminado pela primeira vez. Em seguida, utilizando o comando **Eliminar azure Web App** , que pode eliminar o plano de serviço de aplicação.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Gerir a aplicação de serviço Web Apps ##

### <a name="create-a-web-app"></a>Criar uma aplicação Web ###

Para criar uma aplicação web, utilize o comando **Criar azure Web App** .

Seguem-se descrições dos parâmetros diferentes:

- **– nome**: nome para a aplicação web.
- **– plano**: nome para o plano de serviço utilizado para a aplicação web do anfitrião.
- **– grupo de recursos**: grupo de recursos que aloja o plano de serviço de aplicação.
- **– localização**: a localização da aplicação web.

Exemplo para utilizar este cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Eliminar uma aplicação Web existente ###

Para eliminar uma aplicação web existentes pode utilizar o comando **Eliminar azure Web App** , precisa de especificar o nome do web app e o nome do grupo de recursos.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Lista existente Web Apps ###

A lista de aplicações web existente, utilize o comando **lista azure Web App** .

Para listar todas as aplicações web num grupo de recursos específica, utilize:

    azure webapp list --resource-group ContosoAzureResourceGroup

Para obter uma aplicação web específica, utilize o comando **Mostrar azure Web App** .

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurar uma aplicação Web existente ###

Para alterar as definições e as configurações para uma aplicação web existente, utilize o comando **do conjunto de configuração do azure Web App** .

Exemplo (1): alterar a versão de php de uma aplicação web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Exemplo (2): Adicionar ou alterar as definições da aplicação

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Para saber o que pode ser outra configuração alterados, utilize o comando **Web App azure config set -h** .

### <a name="change-the-state-of-an-existing-web-app"></a>Alterar o estado de uma aplicação Web existente ###

#### <a name="restart-a-web-app"></a>Reiniciar uma aplicação web ####

Para reiniciar a uma aplicação web, tem de especificar o grupo de nome e recursos da aplicação web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Parar uma aplicação web ####

Para parar de uma aplicação web, tem de especificar o grupo de nome e recursos da aplicação web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar uma aplicação web ####

Para iniciar uma aplicação web, tem de especificar o grupo de nome e recursos da aplicação web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Gerir perfis de publicação na Web App ###

Cada aplicação web tem um perfil de publicação que pode ser utilizado para publicar as suas aplicações.

#### <a name="get-publishing-profile"></a>Obter o perfil de publicação ####

Para obter o perfil de publicação para uma aplicação web, utilize:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Este comando ecos o nome de publicação de perfil de utilizador e palavra-passe para a linha de comandos.

### <a name="manage-web-app-hostnames"></a>Gerir nomes de anfitriões do Web App ###

Para gerir hostname enlaces para a sua aplicação web, utilize o comando de **nomes de config anfitriões azure Web App**  

#### <a name="list-hostname-bindings"></a>Lista hostname enlaces ####

Para obter as ligações de hostname atual de uma aplicação web, utilize:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Adicionar hostname enlaces ####

Para adicionar o nome do anfitrião enlaces para uma aplicação web, utilize:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Eliminar o nome do anfitrião enlaces ####

Para eliminar o nome do anfitrião enlaces, utilize:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Próximos passos ###
- Para saber mais sobre o suporte do Azure Gestor de recursos do clip, consulte o artigo [Utilize o clip do Azure para gerir grupos de recursos e recursos Azure.](../xplat-cli-azure-resource-manager.md)
- Para obter informações sobre a gestão de aplicação de serviço através do PowerShell, consulte o artigo [Using Azure Resource Manager-Based o PowerShell para gerir aplicações Web do Azure.](app-service-web-app-azure-resource-manager-powershell.md)
