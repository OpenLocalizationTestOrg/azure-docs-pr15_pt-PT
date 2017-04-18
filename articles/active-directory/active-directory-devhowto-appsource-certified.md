<properties
   pageTitle="Como obter AppSource certificado para o Azure Active Directory | Microsoft Azure"
   description="Mais informações sobre como obter a aplicação AppSource certificado para o Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/28/2016"
   ms.author="skwan;bryanla"/>

#<a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Como obter AppSource certificados para o Azure Active Directory (AD) 

Para receber AppSource certificação do Azure AD, a aplicação tem de implementar o início de sessão do inquilino com várias no padrão com Azure AD utilizando os protocolos OpenID ligar, OAuth 2.0 ou SAML 2.0. 

Se não estiver familiarizado com o início de sessão no Azure AD ou desenvolvimento de aplicações do inquilino com várias:

1. Comece por ler sobre o [Browser para cenários Web App em cenários de autenticação para Azure AD][AAD-Auth-Scenarios-Browser-To-WebApp].  
2. Em seguida, consulte o artigo o Azure AD, [guias de introdução do guia web aplicação][AAD-QuickStart-Web-Apps], que demonstram como implementar o início de sessão no e incluir exemplos de código do assistente. 

    > [AZURE.TIP] Experimente a pré-visualização do nosso novo [developer portal para](https://identity.microsoft.com/Docs/Web) irá ajudá-lo a começar a trabalhar rapidamente com o Azure Active Directory em apenas alguns minutos!  Portal do programador irá guiá-lo durante o processo de registo de uma aplicação e integrar o seu código Azure AD.  Quando tiver terminado, terá uma aplicação simple que pode autenticar os utilizadores no seu inquilino e um back-end que podem aceitar tokens e efetua validação.

3. Para saber como implementar o padrão de início de sessão do inquilino com várias com Azure AD, consulte o artigo [como iniciar sessão no qualquer utilizador do Azure Active Directory (AD) utilizando o padrão de aplicação do inquilino com várias][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>Conteúdo relacionado
Para obter mais informações sobre a criação de aplicações que suportam o início de sessão no Azure AD ou, para obter ajuda e suporte, consulte o [Guia do programador do Azure AD][AAD-Dev-Guide].

Utilize a secção de comentários Disqus seguir este artigo para fornecer comentários e ajude-nos refinar e dar forma a nossa conteúdo.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->










