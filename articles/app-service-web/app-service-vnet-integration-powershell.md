<properties
    pageTitle="Ligar a sua aplicação à sua rede virtual utilizando o PowerShell"
    description="Obter instruções sobre como ligar ao e trabalhar com redes virtuais através do PowerShell"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="wpickett"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="ccompy"/>

# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>Ligar a sua aplicação à sua rede virtual utilizando o PowerShell #

## <a name="overview"></a>Descrição geral ##

No serviço de aplicação do Azure, pode ligar a sua aplicação (web, telemóvel ou API) a uma rede virtual Azure (VNet) na sua subscrição. Esta funcionalidade é designado por VNet integração. A funcionalidade de integração de VNet não deve ser confundida com a funcionalidade de ambiente de serviço de aplicação, que permite-lhe executar uma instância da aplicação de serviço de Azure na sua rede virtual.

A funcionalidade de integração de VNet tem uma interface de utilizador (IU) no novo portal que pode utilizar a integração com redes virtuais que são implementadas utilizando o modelo de implementação clássico ou o modelo de implementação do Azure o Gestor de recursos. Se pretender obter mais informações sobre a funcionalidade, consulte o artigo [integrar a aplicação com uma rede virtual Azure](web-sites-integrate-with-vnet.md).

Este artigo é não sobre como utilizar a IU mas preferir sobre como ativar a integração do utilizando o PowerShell. Porque os comandos para cada modelo de implementação são diferentes, este artigo tem uma secção para cada modelo de implementação.  

Antes de continuar com este artigo, certifique-se de que tem:

- O SDK do PowerShell Azure mais recente instalada. Pode instalar esta com o instalador do plataforma Web.
- Uma aplicação do serviço de aplicação do Azure em execução num padrão ou Premium SKU.

## <a name="classic-virtual-networks"></a>Redes virtuais clássicos ##

Esta secção explica três tarefas para redes virtuais que utilizam o modelo clássico de implementação:

1. Ligar a sua aplicação a uma rede virtual pré-existentes que tem um gateway e está configurada para o site do ponto de conectividade.
1. Atualize a informação de integração de rede virtual para a sua aplicação.
1. Desliga a sua aplicação da sua rede virtual.

### <a name="connect-an-app-to-a-classic-vnet"></a>Ligar uma aplicação a um VNet clássico ###

Para ligar uma aplicação a uma rede virtual, siga estes três passos:

1. Declare para a aplicação web que irão participar uma rede virtual em particular. A aplicação irá gerar um certificado que será dada à rede virtual para o site do ponto de conectividade.
1. Carregar o certificado de aplicação web para a rede virtual e, em seguida, obter o pacote VPN ponto-para-site URI.
1. Atualize ligação de rede virtual do web app com o pacote de ponto-para-site URI.

Os passos primeiros e terceiros são totalmente scripts, mas o segundo passo exige uma ação única e manual através do portal ou access para executar ações de **colocar** ou **correcção** no ponto final de Gestor de recursos do Azure de rede virtual. Contacte o suporte do Azure para que esta opção activada. Antes de começar, certifique-se de que tem uma rede virtual clássica que tenha conectividade de ponto-para-site já foi ativado e um gateway implementado. Para criar o gateway e activar a conectividade de ponto-para-site, tem de utilizar o portal conforme descrito na [criação de um gateway VPN][createvpngateway].

A rede virtual clássica tem de estar na mesma subscrição do seu plano de serviço de aplicação que detém a aplicação que são integrar com.

##### <a name="set-up-azure-powershell-sdk"></a>Configurar o Azure PowerShell SDK #####

Abra uma janela do PowerShell e configurar a conta do Azure e subscrição utilizando:

    Login-AzureRmAccount

Esse comando será aberto um pedido para obter as suas credenciais Azure. Após iniciar sessão, utilize um dos seguintes comandos para selecionar a subscrição que pretende utilizar. Certifique-se de que está a utilizar a subscrição à rede virtual e o seu plano de serviço de aplicação estão no.

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

ou

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>Variáveis utilizadas neste artigo #####

Para simplificar a comandos, podemos vai definir uma variável de PowerShell **$Configuration** com a configuração específica.

Defina uma variável, da seguinte forma no PowerShell com os seguintes parâmetros:

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

A localização da aplicação deverá a localização sem espaços. Por exemplo, EUA Ocidental é westus.

    $Configuration.WebAppLocation = "[Your web app Location]"

O item seguinte é onde deve ser escrito o certificado. Deverá ser um caminho de escrita no seu computador local. Certifique-se incluir. cer no final.

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Para ver que definir, escreva **$Configuration**.

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

Os restantes nesta secção assume que tem uma variável criada como tal descritos.

##### <a name="declare-the-virtual-network-to-the-app"></a>Declarar a rede virtual da aplicação #####

Utilize o seguinte comando para indicar à aplicação que irá utilizar este rede virtual específico. Isto vai fazer com que a aplicação gerar certificados necessários:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Se este comando ser bem sucedida, **$vnet** deve ter uma variável de **Propriedades** na mesma. Deverá conter a variável de **Propriedades de** uma impressão digital do certificado e os dados de certificados.

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>Carregar o certificado de aplicação web para a rede virtual #####

Um manual, único passo é necessário para cada subscrição e uma combinação de rede virtual. Isto é, se estiver a ligar aplicações na subscrição A para A rede Virtual, terá de efetuar este passo apenas uma vez, independentemente de aplicações quantos configurar. Se estiver a adicionar uma nova aplicação a outra rede virtual, terá de fazê-lo novamente. O motivo para isto é que um conjunto de certificados é gerado um nível de subscrição na aplicação de serviço de Azure, e o conjunto é gerado uma vez para cada rede virtual que as aplicações ligar-se-irão ao.

Os certificados irão já foram definidos se seguiu os passos seguintes ou se integrada com a mesma rede virtual utilizando o portal.

O primeiro passo é gerar o ficheiro. cer. É o segundo passo carregar o ficheiro. cer à sua rede virtual. Para gerar o ficheiro. cer da chamada API no passo anterior, execute os seguintes comandos.

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

O certificado será localizado na localização que **$Configuration.GeneratedCertificatePath** especifica.

Para carregar o certificado manualmente, utilizar o [Azure portal] [ azureportal] e **Navegar Virtual rede (clássico)** > **ligações VPN** > **ponto-para-site** > **Gerir certificados**. A partir daqui, carregue o seu certificado.

##### <a name="get-the-point-to-site-package"></a>Obter o pacote de ponto-para-site #####

O próximo passo na configuração de uma ligação de rede virtual uma aplicação web é obter o pacote de ponto-para-site e forneça-lo para a sua aplicação web.

Guarde o modelo seguinte num ficheiro denominado GetNetworkPackageUri.json num local no seu computador, por exemplo, C:\Azure\Templates\GetNetworkPackageUri.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


Definir parâmetros de entrada:

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

O script de chamada:

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


A variável de **$output. Outputs.packageUri** agora irá conter o pacote de URI para ser fornecidas para a sua aplicação web.

##### <a name="upload-the-point-to-site-package-to-your-app"></a>Carregar o pacote de ponto-para-site para a sua aplicação #####

O passo final é fornecer a aplicação com este pacote. Basta executar o comando seguinte:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Se uma mensagem pedir para confirmar que está a substituir um recurso existente, certifique-se para permiti-lo.

Após este comando ser bem sucedida, a aplicação deverá agora estar ligada à rede virtual. Para confirmar o sucesso, aceda à sua consola de aplicação e escreva o seguinte:

    SET WEBSITE_

Se existir uma variável de ambiente denominada WEBSITE_VNETNAME que tenha um valor que corresponda ao nome da rede virtual destino, todas as configurações de tem foi bem sucedida.

### <a name="update-classic-vnet-integration-information"></a>Atualizar informações de integração de VNet clássicas ###

Para atualizar ou voltar a sincronizar as suas informações, basta repetir os passos que seguiu quando criou a integração em primeiro lugar. Sejam esses passos:

1. Defina as informações de configuração.
1. Declare a rede virtual da aplicação.
1. Obter o pacote de ponto-para-site.
1. Carregar o pacote de ponto-para-site para a sua aplicação.

### <a name="disconnect-your-app-from-a-classic-vnet"></a>Desligar a sua aplicação de um VNet clássico ###

Para desligar a aplicação, tem as informações de configuração que foram definidas durante a integração de rede virtual. Utilizar essas informações, existe, em seguida, um comando para desligar a sua aplicação da sua rede virtual.

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Redes virtuais do Gestor de recursos ##

Redes virtuais do Gestor de recursos tem API do Gestor de recursos do Azure, simplificar a alguns processos quando comparado com redes virtuais clássicas. Temos um script que irá ajudá-lo concluir as seguintes tarefas:

- Criar uma rede virtual do Gestor de recursos e integrar a sua aplicação com o mesmo.
- Criar um gateway, configurar a conectividade de ponto-para-site numa rede de virtual pré-existentes no Gestor de recursos e, em seguida, integrar a sua aplicação com o mesmo.
- Integre a aplicação uma rede de virtual do Gestor de recursos pré-existentes que tem um gateway e um ponto-para-site com capacidade de conectividade.
- Desliga a sua aplicação da sua rede virtual.

### <a name="resource-manager-vnet-app-service-integration-script"></a>Script de integração de serviço de aplicação VNet Gestor de recursos ###

Copie o seguinte script e guarde-o para um ficheiro. Se não quiser utilizar o script, à vontade obter informações sobre a partir dos mesmos para saber como configurar tudo com uma rede virtual do Gestor de recursos.


    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
          else
        {
          Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

Guarde uma cópia do script. Neste artigo, denomina-se V2VnetAllinOne.ps1, mas pode utilizar outro nome. Não existem sem argumentos para este script. Pode simplesmente executá-la. A primeira coisa que o script irá fazer é pedir-lhe iniciar sessão. Após iniciar sessão, o script obtém detalhes sobre a sua conta e devolve uma lista de subscrições. Não, contando o pedido para as suas credenciais, a execução do script inicial este aspeto:

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Demonstração de subscrição (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) Teste de MS (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Demonstração púrpura subscrição (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Escolha uma opção: 3

    Conta: ccompy@microsoft.com ambiente: AzureCloud subscrição: 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d inquilino: 722278f-fef1-499f-91ab-2323d011db47

    Introduza o grupo de recursos da sua aplicação: hcdemo rg introduza o nome da sua aplicação: v2vnetpowershell o que pretende fazer?

    1) Adicionar uma nova rede Virtual a uma aplicação
    2) Adicionar uma rede Virtual existente a uma aplicação
    3) Remover uma rede Virtual a partir de uma aplicação

Os restantes esta secção explica cada uma das três essas opções.

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Criar um VNet Gestor de recursos e integrar com o mesmo ###

Para criar uma nova rede virtual que utiliza o modelo de implementação do Gestor de recursos e integrá-lo com a sua aplicação, selecione **1) adicionar uma nova rede Virtual a uma aplicação**. Isto irá pedir-lhe o nome da rede virtual. No meu caso, como pode ver nas seguintes definições, posso utilizado o nome, v2pshell.

O script dá os detalhes sobre a rede virtual que está a ser criado. Se pretendo, pode alterar qualquer um dos valores Em execução neste exemplo, que criei uma rede virtual que tem as seguintes definições:

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

Se quiser alterar qualquer um dos valores, escreva **Y** e efetue as alterações. Quando estiver satisfeito com as definições de rede virtual, escreva **N** ou prima simplesmente Enter quando lhe for pedido sobre como alterar as definições. A partir daí no até à conclusão, o script irá indicar-lhe algumas do que ' i's efetuar até-lo a começar a criar o gateway de rede virtual. Esse passo pode demorar uma hora. Não existe nenhum indicador de progresso nesta fase, mas o script permitirá que saber quando o gateway foi criado.

Quando tiver terminado o script, dirá **terminado**. Neste momento, terá uma rede virtual do Gestor de recursos que tem o nome e definições que selecionou. Esta nova rede virtual também será integrada com a sua aplicação.

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>Integrar a aplicação com um pré-existentes VNet de Gestor de recursos ###

Quando estiver a integração com uma rede virtual pré-existentes, se fornecer uma rede virtual do Gestor de recursos que não tenha um gateway ou conectividade de ponto-para-site, o script irá configurar que. Se o VNET já tiver esses aspetos configurar, o script vai diretamente para a integração de aplicação. Para iniciar este processo, basta selecionar **2) adicionar uma rede Virtual existente a uma aplicação**.

Esta opção só funciona se tiver uma rede de virtual do Gestor de recursos pré-existentes que está na mesma subscrição como a sua aplicação. Depois de selecionar a opção, será apresentada uma lista das suas redes virtual do Gestor de recursos.   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Escolha uma opção: 5

Basta selecione a rede virtual ao qual pretende integrar com. Se já tiver um gateway que tenha conectividade de ponto-para-site ativada, o script integrar simplesmente a aplicação com a sua rede virtual. Se não tiver um gateway, terá de especificar sub-rede o gateway. Sua sub-rede aceda gateway tem de estar no seu espaço de endereços de rede virtual e não pode estar em qualquer outra sub-rede. Se tiver uma rede virtual sem um gateway e executar este passo, coisas o seguinte aspeto:

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

Neste exemplo, que criei um gateway de rede virtual que tem as seguintes definições:

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

Se quiser alterar qualquer uma destas definições, pode fazê-lo. Caso contrário, prima Enter e o script irá criar o gateway e anexar a sua aplicação à sua rede virtual. A hora de criação de gateway ainda é uma hora, no entanto, por isso, certifique-se de que que tenha em atenção. Quando tudo estiver concluído, o script dirá **terminado**.

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>Desligar a sua aplicação do VNet Gestor de recursos ###

Desligar a sua aplicação da sua rede virtual não efetuou o gateway ou desativar a conectividade de ponto-para-site. Que poderá, afinal, utilizá-lo de algo. -Lo também não desligá-lo a partir de outras aplicações que não seja aquele que forneceu. Para efetuar esta ação, selecione **3) remover uma rede Virtual a partir de uma aplicação**. Ao fazê-lo, irá ver algo parecido com:

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Apesar do script diz eliminar, não elimina a rede virtual. -La apenas está a remover a integração. Depois de confirmar que este é o pretende fazer, o comando é processado bastante rapidamente e mostra-lhe **True** quando estiver concluída.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com
