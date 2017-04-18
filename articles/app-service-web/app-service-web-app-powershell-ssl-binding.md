<properties
    pageTitle="Encadernação de certificados SSL através do PowerShell"
    description="Saiba como vincular certificados SSL para a sua aplicação web através do PowerShell."
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

# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>Azure aplicação SSL certificado enlace do serviço através do PowerShell #

Com a versão do Microsoft Azure PowerShell versão 1.1.0 um cmdlet novo foi adicionado que pretende atribuir ao utilizador a capacidade de vincular existentes ou novos certificados SSL para uma aplicação Web existente.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Para saber como utilizar o Gestor de recursos do Azure com base no Azure PowerShell cmdlets para gerir as suas aplicações Web selecione [Gestor de recursos do Azure com base comandos do PowerShell para Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>Carregar e vincular um certificado SSL de novo ##

Cenário: O utilizador optar por vincular um certificado SSL para uma das suas aplicações web.

Saber o nome do grupo de recursos que contém a aplicação web, no nome da aplicação web, o caminho do ficheiro. pfx certificado no computador do utilizador, a palavra-passe para o certificado e o nome do anfitrião personalizado, podemos pode utilizar o seguinte comando do PowerShell para criar nessa encadernação SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Tenha em atenção que antes de adicionar uma ligação SSL para uma aplicação web, tem de ter um nome de anfitrião (domínio personalizado) já tenha configurado. Se o nome do anfitrião não está configurado, em seguida, obterá um erro 'hostname' não existir ao executar o novo AzureRmWebAppSSLBinding. Pode adicionar um nome de anfitrião diretamente a partir do portal do ou através do Azure PowerShell. Pode ser o fragmento de PowerShell que se segue para configurar o nome do anfitrião antes de executar AzureRmWebAppSSLBinding novo.   
  
    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   
  
É importante compreender que o cmdlet Set-AzureRmWebApp substitui os nomes de anfitriões para a aplicação web. Por conseguinte, o fragmento de PowerShell acima é acrescentar à lista existente dos nomes de anfitrião para a aplicação web.  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>Carregar e vincular um certificado SSL existente ##

Cenário: O utilizador optar por vincular um certificado SSL anteriormente carregado para uma das suas aplicações web.

Poderemos obter a lista de certificados já carregou para um grupo de recursos específico, utilizando o comando seguinte

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

Tenha em atenção que os certificados são locais para uma localização específica e o grupo de recursos, o utilizador precisar de carregar novamente o certificado se a aplicação web configurado estiver numa localização diferente e recursos de grupo outros que que o certificado conforme seja necessário 

Saber o nome do grupo de recursos que contém a aplicação web, no nome da aplicação web, a impressão digital do certificado e o nome do anfitrião personalizado, podemos pode utilizar o seguinte comando do PowerShell para criar nessa encadernação SSL:

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>Eliminar uma ligação SSL existente  ##

Cenário: O utilizador optar por eliminar uma ligação SSL existente.

Saber o nome do grupo de recursos que contém a aplicação web, no nome da aplicação web e o nome do anfitrião personalizado, podemos pode utilizar o seguinte comando do PowerShell para remover nessa encadernação SSL:

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

Tenha em atenção que, se o enlace SSL removido foi o último enlace utilizar esse certificado nessa localização, por predefinição, o certificado será eliminada, se o utilizador pretende manter o certificado que ele pode utilizar a opção de DeleteCertificate para manter o certificado

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>Referências ###
- [Gestor de recursos do Azure comandos do PowerShell com base para o Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Introdução à aplicação de serviço ambiente](app-service-app-service-environment-intro.md)
- [Utilizar o Azure PowerShell com o Gestor de recursos Azure](../powershell-azure-resource-manager.md)
