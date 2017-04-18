<properties
   pageTitle="Alterações de atributo de objeto de utilizador externo para pré-visualização de colaboração do Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active B2B de diretório suporta as relações de publicação em empresa ao ativar parceiros de negócios aceder a suas aplicações empresariais seletivamente"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Pré-visualização de colaboração do Azure AD B2B: as alterações de atributos de objeto do utilizador externo

Cada utilizador num directório Azure AD for representada por um objeto de utilizador. O objeto do utilizador no Azure AD sofre alterações de atributo em várias fases da colaboração B2B convidar-resgatar fluxo. O objeto de utilizador que representa o utilizador de parceiro no diretório tem os atributos que alterar em resgatar tempo, quando o utilizador de parceiro clica na ligação no convite de e-mail. Especificamente:

- Os atributos **SignInName** e **AltSecId** são propagados
- O atributo **DisplayName** alterações do nome de utilizador Principal (user_fabrikam.com#EXT#@contoso.com) para o nome de início de sessão(user@fabrikam.com)

Controlar o estes atributos no Azure AD pode ajudar a resolver se ou não um utilizador de parceiro tem resgatar o seu convite de colaboração B2B.

## <a name="related-articles"></a>Artigos relacionados
Procure os nossos outros artigos no Azure AD B2B colaboração:

- [O que é o Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Obter instruções detalhadas sobre](active-directory-b2b-detailed-walkthrough.md)
- [Referência de formato de ficheiro CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de utilizadores externos](active-directory-b2b-references-external-user-token-format.md)
- [Limitações de pré-visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
