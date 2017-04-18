<properties
   pageTitle="Formato de token de utilizadores externos para pré-visualização de colaboração do Azure Active Directory B2B | Microsoft Azure"
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

# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Pré-visualização de colaboração do Azure AD B2B: Formatar token de utilizadores externos

As afirmações para um padrão Azure AD token são descritos no artigo [Token suportados e os tipos de afirmações](active-directory-token-and-claims.md) no azure.microsoft.com.

As afirmações que são diferentes para um utilizador externo da colaboração do B2B autenticado são os seguintes:<br/>
- **OID:** o ID do objeto do inquilino de recursos<br/>
- **TID**: ID de inquilinos do inquilino de recursos<br/>
- **Emissor**: Este é o inquilino do recurso<br/>
- **IDP**: Este é o inquilino principal do utilizador<br/>
- **AltSecId**: Este é o ID de segurança alternativo, que é opaco para si<br/>

## <a name="related-articles"></a>Artigos relacionados
Procure os nossos outros artigos no Azure AD B2B colaboração:

- [O que é o Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Obter instruções detalhadas sobre](active-directory-b2b-detailed-walkthrough.md)
- [Referência de formato de ficheiro CSV](active-directory-b2b-references-csv-file-format.md)
- [Alterações de atributo de objeto do utilizador externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitações de pré-visualização atual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
