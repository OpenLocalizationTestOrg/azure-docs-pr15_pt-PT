<properties
    pageTitle="Web App clonar através do PowerShell"
    description="Saiba como clonar suas aplicações Web do novo Web Apps através do PowerShell."
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
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplicação de serviço de aplicação Azure clonar através do PowerShell#

Com a versão do Microsoft Azure PowerShell versão 1.1.0 uma nova opção foi adicionada ao novo AzureRMWebApp que iria fornecem o utilizador a capacidade de clonar uma aplicação Web existentes para uma aplicação recentemente criada numa região diferente ou na mesma região. Isto irá permitir que os clientes implementar um número de aplicações através de diferentes regiões de forma rápida e fácil.

Aplicação clonar está atualmente suportados apenas para planos de serviço de aplicação de camada premium. A nova funcionalidade utiliza as mesmas limitações como a funcionalidade de cópia de segurança do Web Apps, consulte o artigo [Criar cópia de segurança de uma aplicação web na aplicação de serviço de Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Para saber como utilizar o Gestor de recursos do Azure com base no Azure PowerShell cmdlets para gerir as suas aplicações Web selecione [Gestor de recursos do Azure com base comandos do PowerShell para Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>Clonar uma aplicação existente ##

Cenário: Uma aplicação web existente na região Sul Central-nos, o utilizador optar por clonar o conteúdo para uma nova aplicação web na América do Norte Central-nos região. Pode fazê-lo ao utilizar a versão do Gestor de recursos do Azure do cmdlet do PowerShell para criar uma nova aplicação web com a opção - SourceWebApp.

Saber o nome do grupo de recursos que contém o origem web app, pode utilizamos o seguinte comando do PowerShell para obter informações sobre a aplicação de web de origem (neste caso denominado origem-Web App):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Para criar um novo plano de serviço de aplicação, pode utilizamos AzureRmAppServicePlan novo comando tal como no exemplo seguinte

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Utilizar o comando novo AzureRmWebApp, que possamos criar a nova aplicação web na região América do Norte Central-nos e associam-lo a uma camada premium existente plano de serviço de aplicação, além disso podemos pode utilizar o mesmo grupo de recurso como o origem web app ou definir um novo grupo de recursos, demonstra o seguinte:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Para uma aplicação web existente, incluindo todos os associado implementação de clonar faixas, o utilizador terá de utilizar o parâmetro IncludeSourceWebAppSlots, o comando PowerShell seguinte demonstra a utilização desse parâmetro com o comando novo AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Para clonar uma aplicação web existente dentro da mesma região, o utilizador terá de criar um novo grupo de recursos e um novo serviço de aplicação planear na mesma região e, em seguida, utilizando o seguinte comando do PowerShell para clonar do web app

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonar uma aplicação existente para um ambiente do serviço de aplicação ##

Cenário: Uma aplicação web existente na região Sul Central-nos, o utilizador optar por clonar o conteúdo para uma nova aplicação web para um ambiente de serviço do existente aplicação (Auxiliar).

Saber o nome do grupo de recursos que contém o origem web app, pode utilizamos o seguinte comando do PowerShell para obter informações sobre a aplicação de web de origem (neste caso denominado origem-Web App):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Saber o nome a Auxiliar e o nome do grupo de recursos que pertence a Auxiliar, o utilizador pode utilizar o comando AzureRmWebApp novo para criar a nova aplicação web na Auxiliar existente, demonstra o seguinte procedimento:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

O parâmetro de localização é necessário devido a razão legado, mas no caso de criar uma aplicação num Auxiliar será ignorada. 

## <a name="cloning-an-existing-app-slot"></a>Clonar uma ranhura de aplicação existente ##

Cenário: O utilizador optar por clonar uma ranhura de aplicação Web existente para quer uma nova aplicação Web ou de uma nova ranhura Web App. A nova aplicação Web pode ser na mesma região como a ranhura original do Web App ou numa região diferente.

Saber o nome do grupo de recursos que contém o origem web app, pode utilizamos o seguinte comando do PowerShell para obter informações da origem web app ranhura (neste caso denominadas origem webappslot) associadas à origem de Web App-Web App:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

A seguinte demonstra a criação de um clonar da aplicação web origem a uma nova aplicação web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configurar o Gestor de tráfego enquanto clonar uma aplicação ##

Criar aplicações web da região com várias e configurar o Gestor de tráfego de Azure para encaminhar o tráfego para todos os estas aplicações web, é um cenário importante n para garantir que as aplicações dos clientes estão altamente disponíveis, quando clonar uma aplicação web existente tem a opção de ligar ambas as aplicações web para um novo perfil de Gestor de tráfego ou existente - Repare que apenas Gestor de recursos do Azure versão do Gestor de tráfego é suportada.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Criar um novo perfil de Gestor de tráfego enquanto clonar uma aplicação ###

Cenário: O utilizador optar por clonar uma aplicação web para outra região, durante a configuração de um perfil de Gestor de tráfego de Gestor de recursos do Azure que incluam ambas as aplicações web. A seguinte demonstra a criação de um clonar da aplicação web origem a uma nova aplicação web ao configurar um novo perfil de Gestor de tráfego:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Adicionar novo clonar Web App a um perfil de Gestor de tráfego existente ###

Cenário: O utilizador já têm um perfil de Gestor de tráfego de Gestor de recursos do Azure que ele gostaria de adicionar ambas as aplicações web como pontos finais. Para fazê-lo, podemos primeiro necessita de reunir o id de perfil de Gestor de tráfego existente, irá precisamos o id da subscrição, nome do grupo de recursos e o nome de perfil de Gestor de tráfego existente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Depois de ter o id do Gestor de tráfego, a seguinte demonstra a criação de um clonar da aplicação web origem a uma nova aplicação web ao adicioná-los a um perfil de Gestor de tráfego existente:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrições atuais ##

Esta funcionalidade está atualmente na pré-visualização, estamos a trabalhar para adicionar novas capacidades ao longo do tempo, a lista seguinte são as restrições conhecidas na versão atual do clonar aplicação:

- As definições de escala automática não são clonar
- Definições da agenda de cópia de segurança não são clonar
- Definições de VNET não são clonar
- Informações de aplicação não são automaticamente configuradas da aplicação web do destino
- Fácil Auth definições não são clonar
- Extensão de kudu não são clonar
- Sugestão regras não são clonar
- Conteúdo da base de dados não são clonar


### <a name="references"></a>Referências ###
- [Gestor de recursos do Azure comandos do PowerShell com base para o Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Web App clonar através do Portal do Azure](app-service-web-app-cloning-portal.md)
- [Agregar uma aplicação web na aplicação de serviço do Azure](web-sites-backup.md)
- [Azure suporte de Gestor de recursos para pré-visualização do Gestor de tráfego Azure](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introdução à aplicação de serviço ambiente](app-service-app-service-environment-intro.md)
- [Utilizar o Azure PowerShell com o Gestor de recursos Azure](../powershell-azure-resource-manager.md)
