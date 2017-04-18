<properties
   pageTitle="Limitações de pré-visualização atual de colaboração Azure Active Directory B2B | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Pré-visualização de colaboração do Azure AD B2B: atual limitações de pré-visualizar

- Autenticação multifator (MFA) não é suportada no utilizadores externos. Por exemplo, se Contoso tem MFA, mas parceiro organograma não, em seguida, parceiro organograma utilizadores não não possível conceder MFA através de colaboração B2B.
- Convites são possíveis apenas através de CSV; convites individuais e acesso à API não são suportadas.
- Apenas administradores globais do Azure AD pode carregar ficheiros. csv.
- Atualmente, convites para endereços de e-mail do consumidor (como hotmail.com, Gmail.com ou comcast.net) não são suportadas.
- Acesso de utilizadores externos para aplicações não testados no local.
- Os utilizadores externos não são automaticamente limpar quando o utilizador real é eliminado a partir do seu diretório.
- Convites para listas de distribuição não são suportadas.
- Máximo de 2000 registos pode ser carregado através de CSV.

## <a name="related-articles"></a>Artigos relacionados
Procure os nossos outros artigos no Azure AD B2B colaboração:

- [O que é o Azure AD B2B colaboração?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como funciona](active-directory-b2b-how-it-works.md)
- [Obter instruções detalhadas sobre](active-directory-b2b-detailed-walkthrough.md)
- [Referência de formato de ficheiro CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de utilizadores externos](active-directory-b2b-references-external-user-token-format.md)
- [Alterações de atributo de objeto do utilizador externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
