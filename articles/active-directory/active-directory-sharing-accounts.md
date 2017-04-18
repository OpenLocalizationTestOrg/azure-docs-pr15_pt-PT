<properties
    pageTitle="Partilha de contas utilizando o Azure AD |  Microsoft Azure"
    description="Descreve como Azure Active Directory permitem às organizações partilhar contas para no local aplicações e serviços em nuvem consumidor."
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Partilha de contas com Azure AD

## <a name="overview"></a>Descrição geral
Por vezes, as organizações devem utilizar um nome de utilizador e palavra-passe para várias pessoas. Normalmente, isto acontece em duas situações:

- Ao aceder a aplicações que necessitam de um único início de sessão e a palavra-passe para cada utilizador, se aplicações no local ou consumidor cloud services (por exemplo, contas de redes sociais empresarial).
- Quando criar ambientes de vários utilizadores. Poderá ter uma conta local, única que tem privilégios elevados e pode ser utilizada para principais atividades de configuração, administração e recuperação (por exemplo, "global" conta de administrador local para o Office 365) ou a conta de raiz no Salesforce.

Normalmente, nestas contas seriam ser partilhadas por distribuir as credenciais (nome de utilizador/palavra-passe) a indivíduos direito ou armazená-las numa localização partilhada onde vários agentes fidedignos podem aceder aos mesmos.

O modelo de partilha tradicional tem várias desvantagens:

- Ativar o acesso a novas aplicações requer que a distribuição das credenciais para todas as pessoas que precisa de aceder.
- Cada aplicação partilhada pode exigir o seu próprio conjunto único de credenciais partilhadas, pedir aos utilizadores que não se esqueça de vários conjuntos de credenciais. Quando os utilizadores têm de se lembrar muitos credenciais, o risco aumenta que irá recorrer às práticas de risco. (por exemplo, escrever para baixo de palavras-passe).
- Não consegue indicar quem tem acesso a uma aplicação.
- Não consegue indicar quem tem *acedidos* uma aplicação.
- Quando precisar de remover o acesso a uma aplicação, tem de atualizar as credenciais e voltar a distribuí-los a todos os participantes que precisa de aceder a essa aplicação.

## <a name="azure-active-directory-account-sharing"></a>Partilha do Azure Active Directory conta

Azure AD fornece uma nova abordagem utilizando contas partilhadas que elimina estes desvantagens.

O administrador do Azure AD configura as aplicações que um utilizador pode aceder ao utilizar o painel de acesso e escolher o tipo de melhor de início de sessão único adequada para a aplicação. Uma desses tipos, *com base em palavra-passe de sessão único no*, permite Azure AD funcionar como um tipo de "corretor" durante o processo de início de sessão para essa aplicação.

Os utilizadores iniciam sessão numa vez com a sua conta institucional. Esta é a mesma conta que utilizam regularmente para aceder ao seu correio eletrónico ou ambiente de trabalho. Podem descobrir e aceder a apenas as aplicações que estão atribuídos. Com contas partilhadas, esta lista de aplicações pode incluir qualquer número de credenciais partilhadas. O utilizador final não é necessário para não se esqueça de escrever ou apontar para baixo várias contas que possam estar a utilizar.

Contas partilhadas não só aumentar supervisão e melhorar da eficiência de utilização, estes também melhoram a segurança. Os utilizadores com permissões para utilizar as credenciais não ver a palavra-passe partilhada, mas preferir obterem permissões para utilizar a palavra-passe como parte de um fluxo de autenticação orquestrada. Além disso, com algumas aplicações de SSO de palavra-passe, tem a opção ter Azure AD periodicamente passagem com o rato (update) a palavra-passe que utiliza grandes e complexas palavras-passe, aumentar a segurança da conta. O administrador pode facilmente conceder ou revogar o acesso a uma aplicação e também saber quem tem acesso à conta e quem pode ser consultada-lo no passado.

Azure AD suporta a contas partilhadas para qualquer Enterprise mobilidade conjunto de aplicações (EMS), Premium ou do utilizadores licenciados básicos, através de todos os tipos de palavra-passe única ao iniciar sessão no aplicações. Pode partilhar contas para qualquer uma das milhares de aplicações integradas previamente na Galeria de aplicação e pode adicionar a sua própria aplicação autenticação por palavra-passe com [aplicações SSO personalizadas](active-directory-sso-integrate-saas-apps.md).

Azure AD que ativar a partilha de conta incluem:

- [Palavra-passe serviço single sign-on](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- Palavra-passe único início de sessão agente
- [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
- Aplicações de palavra-passe personalizada
- [Dashboard/relatórios de utilização da aplicação](active-directory-passwords-get-insights.md)
- Portais de acesso do utilizador final
- [Proxy de aplicação](active-directory-application-proxy-get-started.md)
- [O Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Partilha de uma conta
Para utilizar o Azure AD para partilhar uma conta que é necessário:

- Adicionar uma aplicação [Galeria de aplicações](https://azure.microsoft.com/marketplace/active-directory/) ou uma [aplicação personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Configurar a aplicação de palavra-passe único início de sessão (SSO)
- Utilizar [grupo baseada atribuição](active-directory-accessmanagement-group-saasapps.md) e selecione a opção para introduzir uma credencial partilhada
- Opcional: em algumas aplicações, como o Facebook, Twitter ou LinkedIn, pode ativar a opção para [recuperar-através de palavra-passe de automatizado do Azure AD](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Também pode tornar a conta partilhada mais segura com autenticação Multifator (MFA) (Saiba mais sobre como [proteger aplicações com o Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) e pode delegar a capacidade de gerir a quem tem acesso à aplicação utilizando a gestão de grupo [Self-Service do Azure AD](active-directory-accessmanagement-self-service-group-management.md) .

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Proteger as aplicações com o access condicional](active-directory-conditional-access.md)
- [Grupo de gestão personalizada gestão/SSAA](active-directory-accessmanagement-self-service-group-management.md)
