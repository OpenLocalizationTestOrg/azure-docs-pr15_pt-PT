<properties
   pageTitle="Guia de introdução integrar o Azure Active Directory com aplicações introdução |  Microsoft Azure"
   description="Este artigo é um guia de introdução ao obter para integrar o Azure Active Directory (AD) com aplicações no local e na nuvem aplicações."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrar o Azure Active Directory com aplicações introdução guia de introdução
## <a name="overview"></a>Descrição geral
Este tópico destina-se para lhe dar um quadro de referência para integrar aplicações com o Azure Active Directory (AD). Cada uma das secções abaixo contêm um breve resumo de um tópico mais detalhado, para que possa identificar quais as partes deste guia de introdução ao obter são relevantes para si.  Siga as hiperligações para um vôo picado mais aprofundada sobre cada assunto.

## <a name="before-you-begin-take-inventory"></a>Antes de começar, realizar um inventário
Antes de saltar para integrar aplicações com o Azure AD, é importante saber onde estão e em que pretende aceder.  As seguintes questões destinam-se para o ajudar a pensar sobre o projeto de integração de aplicação do Azure AD.

### <a name="application-inventory"></a>Inventário de aplicações
- Onde estão todas as aplicações? Quem é o proprietário-los?
- Que tipo de autenticação de as aplicações necessitam de?
- Quem precisa de acesso aos quais as aplicações?
- Pretende implementar uma nova aplicação?
  - Irá criar-sessões e implementá-lo numa instância cluster Azure?
  - Irá utilizar outro que esteja disponível na Galeria de aplicação de Azure?

### <a name="user-and-group-inventory"></a>Inventário de utilizador e grupo
- Onde residem as suas contas de utilizador?
 - Active Directory no local
 - Azure AD
 - Dentro de uma base de dados de aplicação separada que é o proprietário
 - Em aplicações unsanctioned
 - Todas as operações anteriores
- Que permissões e atribuições de funções é que os utilizadores individuais atualmente tenho? Precisa de rever o seu acesso ou tem a certeza de que as atribuições de acesso e a função de utilizador são adequadas agora?
- São grupos já foram estabelecidos no Active Directory no local?
 - Como os seus grupos estão organizados?
 - Quem são os membros do grupo?
 - O que atribuições de funções/permissões os grupos atualmente possuo?
- Irá precisar de limpar o bases de dados do utilizador/grupo antes de integrar?  (Isto é uma pergunta muito importante. Lixo no lixo saída).

### <a name="access-management-inventory"></a>Inventário de gestão do Access
- Como pode gerir atualmente acesso do utilizador para as aplicações? É necessário alterar?  Considerado outras formas de gerir o acesso, tal como com [RBAC](role-based-access-control-configure.md) por exemplo?
- Quem precisa de aceder ao que?

Talvez não tiver a resposta para todas estas questões adiantado mas não há.  Este guia pode ajudar a responder a algumas destas perguntas e tomar decisões algumas informados.

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição do Azure e um Azure Active directory.  Se ainda não tiver uma subscrição do Azure, pode experimentar Azure gratuitamente para 30 dias. [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integração de aplicações com o Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Simplificar a localização unsanctioned aplicações na nuvem com a deteção de aplicação na nuvem
Tal como mencionado acima, poderão existir aplicações que ainda não foi geridas pela sua organização até ao momento.  Como parte do processo de inventário, é possível localizar as aplicações do unsanctioned na nuvem. Consulte o artigo [localizar aplicações unsanctioned nuvem com a deteção de aplicação na nuvem](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Tipos de autenticação
Cada uma das suas aplicações poderá ter os requisitos de autenticação diferente. Azure AD, certificados de assinatura podem ser utilizado com aplicações que utilizam SAML 2.0, WS-Federation ou OpenID ligar protocolos, bem como palavra-passe de início de sessão único no. Para mais informações sobre a aplicação de tipos de autenticação para utilização com o Azure AD consulte o artigo [Gerir certificados para federada Single Sign-On no Azure Active Directory](active-directory-sso-certs.md) e a [palavra-passe de início de sessão único com base em](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Ativar o SSO com Proxy de aplicação do Azure AD
Com o Proxy de aplicação do Microsoft Azure AD, pode fornecer acesso às aplicações localizados dentro da sua rede privada de forma segura, a partir de qualquer lugar e em qualquer dispositivo. Depois de ter instalado um conector de proxy de aplicação no seu ambiente, podem ser configurada de facilmente com Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integrar o aplicações com o Azure AD
Os artigos seguintes abordam as diferentes maneiras aplicações integram com o Azure AD e fornecem algumas orientações.

- [Para determinar qual do Active Directory para utilizar](active-directory-administer.md)
- [Utilizar aplicações na Galeria de aplicação Azure](active-directory-appssoaccess-whatis.md)
- [Integração de lista de tutoriais de aplicações SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Gerir o acesso às aplicações
Os artigos seguintes descrevem formas como pode gerir o acesso às aplicações assim que tiver sido integrados com o Azure AD utilizando Azure AD conexões e Azure AD.

- [Gerir o acesso às aplicações com o Azure AD](active-directory-managing-access-to-apps.md)
- [Automatizar com conexões Azure AD](active-directory-saas-app-provisioning.md)
- [Atribuir utilizadores a uma aplicação](active-directory-applications-guiding-developers-assigning-users.md)
- [Atribuir grupos à aplicação](active-directory-applications-guiding-developers-assigning-groups.md)
- [Partilha de contas](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integração de aplicações personalizadas
Se estiver a escrever uma nova aplicação e pretender que o ajudarão os programadores nas potencialidades Azure AD, consulte o artigo [directrizes programadores](active-directory-applications-guiding-developers-for-lob-applications.md).

Se pretender adicionar a sua aplicação personalizada para a Galeria de aplicação do Azure, consulte o artigo ["Trazer a sua própria aplicação" com a configuração de SAML Self-Service do Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Consulte também

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
