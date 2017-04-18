<properties 
    pageTitle="Autenticar com o Active Directory no local na sua aplicação Azure | Microsoft Azure" 
    description="Saiba mais sobre as diferentes opções para as aplicações de linha de negócio na aplicação de serviço de Azure para autenticar com do Active Directory no local" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="web" 
    ms.date="08/31/2016" 
    ms.author="cephalin"/>

# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Autenticar com o Active Directory no local na sua aplicação do Azure #

Este artigo mostra-lhe como autenticar com no local Active Directory (AD) na [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md). Uma aplicação do Azure está alojada na nuvem, mas existem formas para autenticar no local utilizadores AD em segurança. 

## <a name="authenticate-through-azure-active-directory"></a>Autenticar através do Azure Active Directory
Um inquilino do Azure Active Directory pode ser sincronizado diretório com no local AD. Esta abordagem permite aos utilizadores de AD aceder a sua aplicação a partir da internet e autenticar utilizando as respetivas credenciais no local. Além disso, a aplicação de serviço de Azure fornece uma [Ativar chave solução para este método](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Com poucos cliques de um botão, pode ativar a autenticação com um inquilino sincronizado diretório para a sua aplicação Azure. Esta abordagem tem as seguintes vantagens:

-   Não requer qualquer código de autenticação na sua aplicação. Permitir que a aplicação de serviço de fazer: autenticação por si e dedica o tempo que fornece uma funcionalidade na sua aplicação.
-   [API do Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx) ativa o acesso a dados do diretório da sua aplicação do Azure.
-   Fornece SSO para [todas as aplicações suportadas pelo Azure Active Directory](/marketplace/active-directory/), incluindo o Office 365, Dynamics CRM Online, Microsoft Intune e milhares de aplicações não Microsoft cloud. 
-   Azure Active Directory suporta o controlo de acesso baseado em funções. Pode utilizar o padrão de [Authorize(Roles="X")] com alterações mínimas para o seu código.

Para ver como escrever uma aplicação Azure de linha de negócio que autentica com o Azure Active Directory, consulte o artigo [criar uma aplicação do Azure de linha de negócio com a autenticação do Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>Autenticar-se através de um STS no local
Se tiver no local seguro token serviço (STS) como o serviços de Federação do Active Directory (AD FS), pode utilizá-la para federar autenticação para a sua aplicação Azure. Esta abordagem é melhor quando a política da empresa impede dados AD a serem armazenados no Azure. No entanto, repare o seguinte procedimento:

-   STS topologia tem de ser implementado no local, com sobrecarga gestão e de custo.
-   Apenas administradores do AD FS podem configurar [dependente de festa de fidedignidades e afirmação regras](http://technet.microsoft.com/library/dd807108.aspx), que podem limitar as opções de programador. Por outro lado, é possível gerir e personalizar [em afirmações](http://technet.microsoft.com/library/ee913571.aspx) numa base por aplicação.
-   Access para o local dados AD requerem uma solução separada através da firewall da empresa.

Para ver como escrever uma aplicação Azure de linha de negócio que autentica com um STS no local, consulte o artigo [criar uma aplicação do Azure de linha de negócio com a autenticação do AD FS](web-sites-dotnet-lob-application-adfs.md).
 
