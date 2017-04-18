<properties
    pageTitle="Azure AD e aplicações: orientar os programadores | Microsoft Azure"
    description="Escrita para profissionais de TI, este artigo fornece diretrizes para integrar Azure aplicações com o Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Azure AD e aplicações: desenvolver aplicações de linha de negócio

Este guia fornece uma descrição geral do desenvolvimento de linha de negócio (LoB) aplicações para o Azure Active Directory (AD). O público-alvo é Active Directory/globais administradores do Office 365.

## <a name="overview"></a>Descrição geral

Criar aplicações de integrado com o Azure AD permite aos utilizadores na sua organização serviço single sign-on com o Office 365. Com a aplicação no oferece-lhe do Azure AD que lhe controlo sobre a política de autenticação para a aplicação. Para saber mais sobre condicional access e como pode proteger as aplicações com autenticação multifator (MFA) consulte o artigo [Configurar regras de acesso](active-directory-conditional-access-azuread-connected-apps.md).

Registe-se a sua aplicação para utilizar o Azure Active Directory. Registar a aplicação significa que os programadores podem a utilizar o Azure AD para autenticar os utilizadores e pedir acesso a recursos de utilizador, tais como correio eletrónico, calendário e documentos.

Qualquer membro do diretório da sua (não convidados) pode registar uma aplicação, caso contrário, conhecida como *criar um objeto de aplicação*.

Registar uma aplicação permite que qualquer utilizador fazer o seguinte:

- Obter uma identidade para a sua aplicação que reconhece do Azure AD
- Obter um ou mais segredos/teclas que pode utilizar a aplicação para se autenticar AD
- Criar marca para a aplicação no portal do Azure com um nome personalizado, o logótipo, etc.
- Aplique as funcionalidades de autorização do Azure AD a sua aplicação, incluindo:
  - Controlo de acesso baseado em funções RBCA)
  - Azure Active Directory que o servidor de autorização de oAuth (segura uma API exposta pela aplicação)

- Declarar as permissões necessárias necessárias para a aplicação para funcionar como esperado, incluindo:-permissões de aplicações (só administradores globais). Por exemplo: associação de função num outra Azure AD aplicação ou função associação relativo um recurso de Azure, grupo de recursos ou subscrição - permissões delegadas (qualquer utilizador). Por exemplo: Azure AD, iniciar sessão e perfil de leitura


> [AZURE.NOTE]Por predefinição, qualquer membro pode registar uma aplicação. Para saber como restringir permissões para o registo de aplicações a membros específicos, consulte o artigo [como as aplicações são adicionadas à Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Eis o que o administrador global, tem de fazer para ajudar os programadores tornar a sua aplicação está preparado para produção:

- Configurar regras de acesso (access política/MFA)
- Configurar a aplicação para necessitam de atribuição de utilizador e atribuir utilizadores
- Suprimir a experiência de consentimento predefinida do utilizador

## <a name="configure-access-rules"></a>Configurar regras de acesso

Configure regras de acesso por aplicação as suas aplicações SaaS. Por exemplo, pode exigir MFA ou só permitir o acesso aos utilizadores em redes fidedignas. Os detalhes para este estão disponíveis no documento [Configurar regras de acesso](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurar a aplicação para necessitam de atribuição de utilizador e atribuir utilizadores

Por predefinição, os utilizadores podem aceder aplicações sem a ser atribuída. No entanto, se a aplicação expõe funções ou se pretende que a aplicação seja apresentado no painel de acesso de um utilizador, deve exigir a atribuição de utilizador.

[Exigir a atribuição de utilizador](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Se for um subscritor do Azure AD Premium ou para o conjunto de aplicações de mobilidade da empresa (EMS), recomendamos a utilização de grupos. Atribuir grupos para a aplicação permite-lhe delegar a gestão de acesso contínuo ao proprietário do grupo. Pode criar o grupo ou peça a parte responsável na sua organização para criar o grupo utilizando a funcionalidade de gestão do grupo.

[Atribuir utilizadores a uma aplicação](active-directory-applications-guiding-developers-assigning-users.md)  
[Atribuir grupos à aplicação](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Suprimir consentimento de utilizador

Por predefinição, cada utilizador analise uma experiência de consentimento para iniciar sessão. A experiência de consentimento, lhes pedir para conceder permissões para uma aplicação, pode ser confuso para utilizadores que estão familiarizados com essas decisões.

Para as aplicações que confia, pode simplificar a experiência do utilizador por consentir para a aplicação em nome da sua organização.

Para mais informações sobre consentimento de utilizador e a experiência de consentimento no Azure, consulte [Aplicações de integração com o Azure Active Directory](active-directory-integrating-applications.md).

##<a name="related-articles"></a>Artigos relacionados

- [Activar o acesso remoto seguro para aplicações no local com o Proxy de aplicação do Azure AD](active-directory-application-proxy-get-started.md)
- [Pré-visualização do Azure acesso condicional para as aplicações de SaaS](active-directory-conditional-access-azuread-connected-apps.md)
- [Gerir o acesso às aplicações do Azure AD](active-directory-managing-access-to-apps.md)
- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
