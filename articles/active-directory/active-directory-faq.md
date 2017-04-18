<properties
    pageTitle="Perguntas mais frequentes do Azure Active Directory | Microsoft Azure"
    description="Azure Active Directory perguntas mais frequentes que fornece respostas a perguntas em conjunto com aceder a Azure e Azure Active Directory, acesso de gestão e de aplicação de palavra-passe."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Perguntas mais frequentes do Azure Active Directory

Azure Active Directory é uma identidade abrangente como uma solução de serviço (IDaaS) que abrange todos os aspetos da identidade, gestão de acesso e segurança.


Para obter mais detalhes, consulte o artigo [o que é o Azure Active Directory?](active-directory-whatis.md).



## <a name="accessing-azure-and-azure-active-directory"></a>Aceder a Azure e Azure Active Directory


**P: por que motivo recebo "sem subscrições encontrada" quando tento aceder ao Azure AD no portal do clássico Azure (https://manage.windowsazure.com)?**

**A:** Aceder ao portal clássico Azure requer a cada utilizador tenha permissões numa subscrição do Azure. Se tiver uma subscrição paga do Office 365 ou Azure AD, navegue para [http://aka.ms/accessAAD](http://aka.ms/accessAAD) de um passo de ativação únicas, caso contrário, terá de ativar um total [Azure avaliação](https://azure.microsoft.com/pricing/free-trial/) ou uma subscrição paga. 

Para obter mais detalhes, consulte:

- [Como subscrições Azure estão associadas com o Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Gerir o diretório para a sua subscrição do Office 365 no Azure](active-directory-manage-o365-subscription.md)

---

**P: qual é a relação entre o Azure AD, Office 365 e Azure?**

**A:** Azure Active Directory fornece capacidades de identidade e de acesso comuns para todos os serviços online da Microsoft. Se estiver a utilizar o Office 365, Microsoft Azure, Intune ou outras pessoas, já está a utilizar um Azure AD para ativar o início de sessão e aceder a gestão de todos estes serviços. 

Na verdade, todos os utilizadores que ativou para a Microsoft Online services são definidos como contas de utilizador numa ou mais ocorrências do Azure AD. Pode ativar a estas capacidades de contas gratuitamente Azure AD, tais como o acesso à nuvem aplicação.
 
Para além disso, o Azure AD pago serviços (por exemplo: Azure AD basic, Premium, EMS, etc.) complementar os outros Online serviços como o Office 365 e o Microsoft Azure com abrangente soluções empresariais da escala gestão e segurança.


---



## <a name="getting-started-with-hybrid-azure-ad"></a>Introdução ao híbrido Azure AD


**P: como me posso ligar meu diretório no local para Azure AD?**

**A:** Pode ligar o seu diretório no local ao Azure AD utilizando a **ligação do Azure AD**. 

Para obter mais detalhes, consulte o artigo [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).


---

**P: como posso configurar o SSO entre o meu diretório no local e as minhas aplicações na nuvem?**

**A:** Só tem de configurar o SSO entre o seu diretório no local e o Azure AD. Como aceder ao seu aplicações na nuvem através do Azure AD, o serviço de unidades automaticamente os seus utilizadores para autenticar corretamente com as respetivas credenciais no local.

Implementar a SSO nos locais pode ser facilmente atingido com soluções de Federação, por exemplo, o ADFS ou através da configuração de sincronizar hash de palavra-passe. Pode implementar facilmente ambas as opções utilizando o Assistente de configuração de ligação do Azure AD.
  

Para obter mais detalhes, consulte o artigo [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
  

---

**P: o Azure Active Directory fornecer um portal de gestão personalizada para os utilizadores da minha organização?**

**A:** Sim, Azure Active Directory fornece o [Painel de acesso do Azure AD](http://myapps.microsoft.com) para o utilizador personalizada e de acesso de aplicação. Se for um cliente do Office 365, pode encontrar muitas das mesmas capacidades no portal do Office 365. 

Para mais informações, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md). 



---

**P: o Azure AD ajudar-me a gerir a infraestrutura meu no local?**

**A:** Sim, que faz. A edição do Azure AD Premium fornece-lhe **Ligar-se do Estado de funcionamento**. Azure AD ligar estado de funcionamento do ajuda-o a monitorizar e obtenha visão sua infraestrutura de identidade no local e os serviços de sincronização.  

Para obter mais detalhes, consulte o artigo [Monitor suas no local identidade infraestrutura e sincronização dos serviços na nuvem](active-directory-aadconnect-health.md).  

---

## <a name="password-management"></a>Gestão de palavra-passe

**P: Posso utilizar a palavra-passe de Azure AD escrita-back sem sincronização de palavra-passe? (Também conhecidos por, gostaria de utilizar personalizada SSPR do Azure AD com palavra-passe escrita-back mas não quero minhas palavras-passe armazenadas na cloud?)**

**A:** Não necessita de sincronizar a sua AD as palavras-passe Azure AD para poder ativar escrita anterior. Num ambiente federado, Azure AD SSO baseia-se no diretório no local para autenticar o utilizador. Este cenário não requer a palavra-passe no local registadas no Azure AD.

---

**P: quanto tempo demora uma palavra-passe sejam escritos nos novamente AD no local?**

**A:** Palavra-passe escrita-back funciona em tempo real. 

Para obter mais detalhes, consulte o artigo [Introdução à gestão de palavra-passe](active-directory-passwords-getting-started.md) 


---

**P: pode utilizar palavras-passe escrita-back com palavras-passe que são geridas por um administrador?**

**A:** Sim, se tiver uma palavra-passe de escrita anterior ativado, as operações de palavra-passe executadas por um administrador são escritas novamente para o seu ambiente no local.  

Para obter mais respostas a palavra-passe perguntas relacionadas, consulte o artigo [Palavra-passe de gestão de perguntas mais frequentes](active-directory-passwords-faq.md).

---

## <a name="application-access"></a>Acesso de aplicação


**P: onde pode encontrar uma lista de aplicações que são previamente integrado com o Azure AD e das suas capacidades?**

**A:** Azure AD tem mais 2600 aplicações previamente integradas da Microsoft, fornecedores de serviços de aplicação ou parceiros. Todas as aplicações integradas previamente suportam SSO. SSO permite-lhe utilizar as suas credenciais organizacionais para aceder a suas aplicações. Algumas das aplicações também suportam aprovisionamento e anular aprovisionamento automatizado

Para obter uma lista completa das aplicações do previamente integradas, consulte o [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).


---

**P: o que acontece se a aplicação que é necessário não está no mercado Azure AD?**

**A:** Com o Azure AD Premium, pode adicionar e configurar uma aplicação que pretende. Dependendo das capacidades da aplicação e as suas preferências, pode configurar SSO e aprovisionamento automatizado.  

Para obter mais detalhes, consulte:

- [Configurar o serviço single sign-on para as aplicações que não estão na Galeria de aplicação do Azure Active Directory](active-directory-saas-custom-apps.md)
- [Utilizar SCIM para activar automáticas de aprovisionamento de utilizadores e grupos a partir do Azure Active Directory para aplicações](active-directory-scim-provisioning.md) 


---

**P: como os utilizadores sessão aplicações utilizando o Azure Active Directory?**
 
**A:** Azure Active directory fornece várias formas para que os utilizadores ver e aceder aos seus pedidos tais como:

- O painel de acesso do Azure AD

- Iniciador de aplicações do Office 365

- Direta início de sessão às aplicações federadas

- Ligações abrangente federados, com base em palavra-passe ou aplicações existentes

Para mais informações, consulte o artigo [Implementar Azure AD integrada as aplicações aos utilizadores](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**P: quais são as várias formas Azure Active Directory permite a autenticação e serviço single sign-on para aplicações?**
 
**A:** Azure Active Directory suporta muitos protocolos padronizados para autenticação e autorização como SAML 2.0, OpenID ligar, OAuth 2.0 e WS Federação. Azure AD também suporta vaulting de palavra-passe e capacidades de início de sessão automatizadas para as aplicações que apenas suportam uma autenticação baseada em formulários.  

Para obter mais informações, consulte:

- [Cenários de autenticação de Azure AD](active-directory-authentication-scenarios.md)

- [Protocolos de autenticação do Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [Como único início de sessão com o trabalho do Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**P: Posso adicionar aplicações estou a executar no local?**

**A:** Proxy de aplicação do Azure AD fornece-lhe um acesso fácil e seguro para aplicações web no local que escolher. Pode aceder a estas aplicações da mesma forma que estão a aceder às suas aplicações SaaS no Azure Active Directory. Não é necessário para está numa VPN ou alterar a sua infraestrutura de rede.  

Para obter mais detalhes, consulte o artigo [como fornecer acesso remoto seguro para aplicações no local](active-directory-application-proxy-get-started.md).


--- 

**P: como posso exigir MFA para utilizadores que acederem a uma aplicação específica?**

**A:** Com o access condicional do Azure AD, pode atribuir uma política de acesso exclusivo para cada aplicação. Na sua política, pode requerer MFA sempre, ou quando os utilizadores não estiver ligados à rede local.  

Para obter mais detalhes, consulte [proteger o acesso ao Office 365 e outras aplicações ligadas ao Azure Active Directory](active-directory-conditional-access.md).


---

**P: o que é automatizado aprovisionamento de utilizador para as aplicações de SaaS?**

**A:** Azure Active Directory permite-lhe automatizar a criação, manutenção e remoção da identidades de utilizador em muitas aplicações de nuvem populares (SaaS). 

Para mais informações, consulte o artigo [automatizar aprovisionamento de utilizador e Deprovisioning SaaS aplicações com o Azure Active Directory](active-directory-saas-app-provisioning.md)

---



