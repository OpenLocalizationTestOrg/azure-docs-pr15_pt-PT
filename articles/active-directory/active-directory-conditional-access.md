<Properties
    pageTitle="Acesso ao condicional Azure Active Directory | Microsoft Azure"  
    description="Utilize o controlo de acesso condicional Azure Active Directory para verificar a existência condições específicas ao autenticar para aceder às aplicações."  
    services="active-directory"
    keywords="acesso condicional para aplicações, acesso condicional com Azure AD, acesso seguro a recursos da empresa, as políticas de acesso condicional"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Acesso condicional no Azure Active Directory   

As capacidades de controlo no access condicional do Azure Active Directory (Azure AD) oferecem formas simples para ajudar a recursos seguros na nuvem e no local. Políticas de acesso condicional como autenticação multifator pode ajudar a proteger contra o risco de roubado e phished credenciais. Outras políticas de acesso condicional podem ajudar a manter os dados da sua organização. Por exemplo, para além de requerer credenciais, poderá tem uma política que apenas os dispositivos que são inscrito num sistema de gestão de dispositivos móveis como Microsoft Intune pode aceder a serviços de confidenciais da sua organização.


## <a name="prerequisites"></a>Pré-requisitos

Azure AD o acesso condicional é uma funcionalidade do [Azure Active Directory Premium](http://www.microsoft.com/identity). Cada utilizador que acede a uma aplicação que tenha políticas de acesso condicional aplicadas tem de ter uma licença do Azure AD Premium. Pode obter mais informações sobre os requisitos de licença num [relatório de utilizadores não licenciados](https://aka.ms/utc5ix).


## <a name="how-is-conditional-access-control-enforced"></a>Como é que o controlo de acesso condicional é aplicado?  

Com o controlo de acesso condicional num local, Azure AD verifica se existem condições específicas definidas para um utilizador para aceder a uma aplicação. Depois de acesso requisitos são preenchidos, o utilizador é autenticado e pode aceder à aplicação.  

![Descrição geral de acesso condicional](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Condições

Estas são as condições que pode incluir uma política de acesso condicional:

- **Membros do grupo**. Controlar o acesso de um utilizador com base na associação num grupo.

- **Localização**. Utilizar a localização do utilizador para acionar a autenticação multifator e utilize os controlos de bloco quando um utilizador não está numa rede fidedignos.

- **Dispositivo plataforma**. Utilize a plataforma de dispositivo, como iOS, Android, Windows Mobile ou Windows, como uma condição para a aplicação de política.

- **Com capacidade de dispositivo**. Estado do dispositivo, se ativado ou desativado, é validado durante a avaliação de política de dispositivo. Se desativar um dispositivo perdido ou roubado no diretório, já não pode satisfazer as requisitos da política.

- **Iniciar sessão e utilizador risco**. Pode utilizar a [proteção de identidade do Azure AD](active-directory-identityprotection.md) para políticas de risco de acesso condicional. Políticas de risco de acesso condicional ajudam a dar a sua protecção de avançar organização com base em eventos de risco e as atividades de início de sessão no invulgares.


## <a name="controls"></a>Controlos

Estes são os controlos que pode utilizar para impor a uma política de acesso condicional:

- **Autenticação multifator**. Pode exigir autenticação forte através de autenticação multifator. Pode utilizar a autenticação multifator com Azure a autenticação Multifator ou ao utilizar um fornecedor de autenticação multifator no local, combinado com os serviços de Federação do Active Directory (AD FS). Utilizando a autenticação multifator ajuda a proteger recursos de que está a ser acedido por um utilizador não autorizado que poderá ter adquirida com acesso às credenciais de um utilizador válido.

- **Bloco**. Pode aplicar condições, como localização do utilizador para bloquear o acesso do utilizador. Por exemplo, pode bloquear o acesso quando um utilizador não está numa rede fidedignos.

- **Compatível com dispositivos**. Pode configurar as políticas de acesso condicional ao nível do dispositivo. Pode configurar uma política para que apenas os computadores que estão associados ao domínio ou os dispositivos móveis que são inscrito na aplicação de gestão de dispositivos móveis, podem aceder a recursos da sua organização. Por exemplo, pode utilizar Intune para verificar a conformidade de dispositivo e, em seguida, comunicar a situação ao Azure AD para imposição quando os utilizadores tentarem aceder a uma aplicação. Para uma orientação detalhada sobre como utilizar o Intune para proteger as aplicações e dados, consulte o artigo [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). Também pode utilizar Intune para impor proteção de dados para dispositivos perdidos ou roubados. Para mais informações, consulte a [ajuda a proteger os seus dados com limpeza completa ou selective com o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Aplicações

Pode impor a uma política de acesso condicional ao nível da aplicação. Definir níveis de acesso para aplicações e serviços na nuvem ou no local. A política é aplicada diretamente para o Web site ou serviço. A política é aplicada de acesso para o browser e para as aplicações que aceder ao serviço.


## <a name="device-based-conditional-access"></a>Acesso condicional com base no dispositivo

Pode restringir o acesso às aplicações a partir de dispositivos que estão registados com o Azure AD e que satisfazem condições específicas. Acesso condicional com base no dispositivo protege recursos de uma organização de utilizadores que tentarem aceder aos recursos a partir do:

- Dispositivos desconhecidos ou que não é geridos.
- Dispositivos que não cumprem as políticas de segurança da organização configurados.

Pode definir políticas com base nestes requisitos:

- **Dispositivos façam parte de um domínio**. Defina uma política para restringir o acesso a dispositivos que estão associadas a um domínio do Active Directory no local, e que também estão registados com Azure AD. Esta política se aplica ao ambientes de trabalho do Windows, computadores portáteis e enterprise tablets.
Para mais informações sobre como configurar o registo automático dos dispositivos associado ao domínio com o Azure AD, consulte o artigo [configurar o registo automático do Windows dispositivos associado ao domínio com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Compatível com dispositivos**. Defina uma política para restringir o acesso a dispositivos que são marcados **em conformidade** no directório do sistema de gestão. Esta política assegura que apenas os dispositivos que cumprem as políticas de segurança como impor encriptação de ficheiros num dispositivo são permitidos acesso. Pode utilizar esta política para restringir o acesso a partir do seguintes dispositivos:

    - **Dispositivos com Windows façam parte de um domínio**. Geridas pelo centro de configuração do Gestor do sistema (no atual ramo) implementado numa configuração híbrida.
    - **Trabalho do Windows 10 Mobile ou dispositivos pessoais**. Gerido por Intune ou por um sistema de gestão de dispositivos móveis suportadas do terceiros.
    - **dispositivos iOS e Android**. Gerido pelo Intune.


Os utilizadores que acedam a aplicações que estejam protegidas por um dispositivo com base no, política de autoridade de certificação tem de aceder a aplicação a partir de um dispositivo que cumpra os requisitos desta política. Acesso negado se tentada num dispositivo que não cumpre os requisitos da política.

Para obter informações sobre como configurar uma política de autoridade de certificação baseada no dispositivo, no Azure AD, consulte o artigo [definir a política de acesso condicional com base no dispositivo ligado Azure Active Directory aplicações](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Recursos

Consulte os seguintes categorias de recursos e artigos para saber mais sobre a definição de acesso condicional para a sua organização.


### <a name="multi-factor-authentication-and-location-policies"></a>Políticas de autenticação e a localização de multifator

- [Introdução ao access condicional às aplicações Azure AD-ligada com base no grupo, localização e políticas de autenticação multifator](active-directory-conditional-access-azuread-connected-apps.md)
- [Aplicações que são suportadas](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>Acesso condicional com base no dispositivo

- [Definir a política de acesso condicional com base no dispositivo para o controlo de acesso a aplicações ligado Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
- [Configurar o registo automático do Windows dispositivos façam parte de um domínio com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Resolução de problemas de acesso do Azure Active Directory](active-directory-conditional-access-device-remediation.md)
- [Ajudar a proteger dados em dispositivos perdidos ou roubados utilizando o Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>Proteger os recursos com base no início de sessão no risco

-   [Proteção de identidade do Azure AD](active-directory-identityprotection.md)

### <a name="next-steps"></a>Próximos passos

- [Acesso condicional perguntas mais frequentes](active-directory-conditional-faqs.md)
- [Referência técnica](active-directory-conditional-access-technical-reference.md)
