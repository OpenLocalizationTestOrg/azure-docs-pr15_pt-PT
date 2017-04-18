<properties
   pageTitle="Listar a aplicação na Galeria de aplicação do Azure Active Directory"
   description="Como uma aplicação que suporta o início de sessão único na galeria do Azure Active Directory da lista | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Listar a aplicação na Galeria de aplicação do Azure Active Directory

Para listar uma aplicação que suporta serviço single sign-on com o Azure Active Directory na [Galeria do Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), a aplicação pela primeira vez precisa implementar um dos seguintes modos de integração:

* **Ligar OpenID** - direta integração com o Azure AD utilizando OpenID ligar-se para a autenticação e a API do Azure AD consentimento para a configuração. Se apenas estão a ser iniciadas uma integração e a aplicação não suporta SAML, este é o modo de recomendar.

* **SAML** – a aplicação já tem a capacidade de configurar fornecedores de identidade de terceiros utilizando o protocolo SAML.

Requisitos de listagem para cada modo estão abaixo.

##<a name="openid-connect-integration"></a>OpenID ligar integração

Para integrar a aplicação com o Azure AD, seguindo as [instruções de programador](active-directory-authentication-scenarios.md). Em seguida, conclua as perguntas abaixo e enviar para waadpartners@microsoft.com.

* Fornece as credenciais para uma conta de inquilino de teste ou com a aplicação que pode ser utilizada pelo equipa do Azure AD para testar a integração.  

* Fornece instruções sobre como a equipa do Azure AD pode iniciar sessão e ligar uma instância do Azure AD a sua aplicação utilizando o [Azure AD consentimento framework](active-directory-integrating-applications.md#overview-of-the-consent-framework). 

* Fornece quaisquer instruções adicionais necessárias para a equipa do Azure AD testar serviço single sign-on com a aplicação. 

* Forneça as informações abaixo:

> Nome da empresa:
> 
> Web site da empresa:
> 
> Nome da aplicação:
> 
> Descrição da aplicação (limite de 256 carateres):
> 
> Site de aplicação (informativo):
> 
> Web site de suporte de técnica de aplicação ou informações de contacto:
> 
> ID de cliente da aplicação, conforme apresentado nos detalhes da aplicação na https://manage.windowsazure.com:
> 
> Onde os clientes Ir para se inscrever no URL da inscrição de aplicação e/ou compre a aplicação:
> 
> Selecione até três categorias para a sua aplicação para estará listado em (para categorias disponíveis, consulte o Azure Active Directory Marketplace):
> 
> Anexe ícone pequeno de aplicação (ficheiro PNG, 45px por 45px, cor de fundo sólida):
> 
> Anexe ícone grande da aplicação (ficheiro PNG, 215px por 215px, cor de fundo sólida):
> 
> Anexe o logótipo da aplicação (ficheiro PNG, 150px por 122px, cor de fundo transparente):

##<a name="saml-integration"></a>Integração do SAML

Qualquer aplicação compatível com SAML 2.0 pode ser integrada diretamente com um inquilino do Azure AD utilizando [estas instruções para adicionar uma aplicação personalizada](active-directory-saas-custom-apps.md). Assim que tiver testado que integração de aplicações funciona com o Azure AD, enviar as seguintes informações para <waadpartners@microsoft.com>.

* Fornece as credenciais para uma conta de inquilino de teste ou com a aplicação que pode ser utilizada pelo equipa do Azure AD para testar a integração.  

* Fornece os valores de (serviço do consumidor afirmação) SAML Sign-On URL, o URL de emissor (entidade ID) e o URL de resposta para a sua aplicação, como descrito [aqui](active-directory-saas-custom-apps.md). Se fornecer normalmente estes valores como parte de um ficheiro de metadados SAML, em seguida, envie que também.

* Forneça uma breve descrição de como configurar o Azure AD como um fornecedor de identidades na sua aplicação utilizando SAML 2.0. Se a sua aplicação suporta configurar Azure AD como um fornecedor de identidades através de um portal de administração de gestão personalizada, em seguida, certifique-se as credenciais fornecidas acima incluem a capacidade de configurar esta tarefa.

* Forneça as informações abaixo:

> Nome da empresa:
> 
> Web site da empresa:
> 
> Nome da aplicação:
> 
> Descrição da aplicação (limite de 256 carateres):
> 
> Site de aplicação (informativo):
> 
> Web site de suporte de técnica de aplicação ou informações de contacto:
> 
> Onde os clientes Ir para se inscrever no URL da inscrição de aplicação e/ou compre a aplicação:
> 
> Selecione até três categorias para a sua aplicação para estará listado em (para categorias disponíveis, consulte o [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Anexe ícone pequeno de aplicação (ficheiro PNG, 45px por 45px, cor de fundo sólida):
> 
> Anexe ícone grande da aplicação (ficheiro PNG, 215px por 215px, cor de fundo sólida):
> 
> Anexe o logótipo da aplicação (ficheiro PNG, 150px por 122px, cor de fundo transparente):
