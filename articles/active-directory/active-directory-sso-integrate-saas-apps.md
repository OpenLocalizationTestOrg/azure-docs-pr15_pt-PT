<properties
    pageTitle="Integrar o Azure Active Directory serviço single sign-on com aplicações SaaS |  Microsoft Azure"
    description="Ative a autenticação de início de sessão única e de aprovisionamento do access centralizada gestão de aplicações SaaS no Azure Active Directory do utilizador. Uma descrição geral de como integrar o Azure Active Directory para SaaS aplicações."
    services="active-directory"
      keywords="integrar o Azure AD SaaS aplicações"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrar o Azure Active Directory serviço single sign-on SaaS aplicações  

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-enterprise-apps-manage-sso.md)
- [Azure portal clássico](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Para começar a configurar o serviço single sign-on para uma aplicação que está a trazer para a sua organização, utilizar um directório existente no Azure Active Directory (Azure AD). Pode utilizar num diretório do Azure AD que obtêm através do Microsoft Azure, Office 365 ou do Windows Intune. Se tiver duas ou mais dos seguintes procedimentos, consulte o artigo [administrar no diretório da sua Azure AD](active-directory-administer.md) para determinar qual deles pretende utilizar.

## <a name="authentication"></a>Autenticação

Para as aplicações que suportam o SAML 2.0, WS-Federation, ou OpenID ligar protocolos, utilizações do Azure Active Directory certificados para estabelecer relações de confiança de assinatura. Para mais informações sobre isto, consulte o artigo [Gerir certificados de federados single sign-on](active-directory-sso-certs.md).

Para as aplicações que suportam apenas HTML baseada em formulários iniciar sessão, Azure Active Directory utiliza 'vaulting de palavra-passe' para estabelecer relações de fidedignidade. Isto permite que os utilizadores na sua organização para iniciada automaticamente para uma aplicação de SaaS por Azure AD utilizando as informações de conta de utilizador a partir da aplicação SaaS. Azure AD recolhe e segura armazena as informações da conta de utilizador e a palavra-passe relacionada. Para mais informações, consulte o artigo [com base em palavra-passe serviço single sign-on](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorização

Uma conta aprovisionada permite que um utilizador autorizado para utilizar uma aplicação depois de que foram autenticados através do serviço single sign-on. Aprovisionamento de utilizador puder ser processado manualmente ou em alguns casos, pode adicionar e remover as informações de utilizador a partir da aplicação de SaaS com base nas alterações efetuadas no Azure Active Directory. Para mais informações sobre como utilizar conectores existentes do Azure AD para aprovisionamento automatizado, consulte o artigo [utilizador automatizadas de aprovisionamento e anular aprovisionamento para SaaS aplicações](active-directory-saas-app-provisioning.md).

Caso contrário, pode manualmente adicionar informações de utilizador para uma aplicação, ou utilize outras soluções de aprovisionamento que estão disponíveis no mercado.

## <a name="access"></a>Access

Azure AD fornece várias formas personalizáveis para implementar aplicações aos utilizadores finais na sua organização. Não estão bloqueadas para qualquer implementação específica ou solução de acesso. Pode utilizar [a solução que melhor se adequa às suas necessidades](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Considerações adicionais para as aplicações já em utilização

Configurar o início de sessão único para uma aplicação que utiliza a sua organização já é um processo diferente do processo de criação de novas contas para uma nova aplicação. Existem apenas alguns passos introdutórios, incluindo: Noções sobre como os utilizadores experiência do início de sessão à aplicação após está integrado e mapeamento identidades dos utilizadores na aplicação para identidades do Azure AD.

> [AZURE.NOTE] Para configurar o SSO para uma aplicação existente, tem de ter os direitos de administrador global em ambos os Azure AD e a aplicação SaaS.

### <a name="mapping-user-accounts"></a>Mapeamento de contas de utilizador

Identidade de um utilizador normalmente tem um identificador exclusivo que pode ser um endereço de e-mail ou nome principal de utilizador (UPN). Precisar de ligação (mapa) identidade de aplicação de cada utilizador para as respectivas identidade do Azure AD. Existem algumas formas para realizar esta tarefa dependendo de como o requisito da autenticação de aplicação.

Para mais informações sobre mapeamento de identidades de aplicações com identidades do Azure AD, consulte o artigo [Personalizar afirmações emitidas no token de SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e os [mapeamentos de atributo personalizar para aprovisionar](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Noções sobre o registo do utilizador na experiência

Ao integrar SSO para uma aplicação que já está a ser utilizado, é importante que compreenda que a experiência do utilizador, será afectada. Para todas as aplicações, os utilizadores irão começar a utilizar as respetivas credenciais do Azure AD para iniciar sessão. Também pode dever-tem de utilizar um portal de diferentes para aceder as aplicações.

SSO para algumas aplicações pode ser efetuado num início de sessão da aplicação na interface, mas para outras aplicações, o utilizador terá que ir através de um portal central como ([As minhas aplicações](http://myapps.microsoft.com) ou [Office 365](http://portal.office.com/myapps)) para iniciar sessão. Saiba mais sobre os diferentes tipos de SSO e a sua experiência utilizador [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

Outro recurso útil é o *utilizador Suppressing consentimento* no artigo [directrizes programadores](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Próximos passos


SaaS das aplicações que pode encontra na Galeria de aplicação, Azure AD fornece um número de [tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md).

Se a aplicação não estiver na Galeria de aplicação, pode [adicioná-lo para a Galeria de aplicação do Azure AD como uma aplicação personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Existe mais detalhes sobre todos estes problemas na biblioteca de Azure.com, começando [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory.](active-directory-appssoaccess-whatis.md).

## <a name="see-also"></a>Consulte também

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
