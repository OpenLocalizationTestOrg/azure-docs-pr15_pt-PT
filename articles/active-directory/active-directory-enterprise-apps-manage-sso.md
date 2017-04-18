<properties
    pageTitle="Única gestão de início de sessão para aplicações empresariais na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Saiba como gerir início de sessão único para aplicações empresariais utilizando o Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Pré-visualização: Gerir single sign-on para aplicações empresariais no novo portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-enterprise-apps-manage-sso.md)
- [Azure portal clássico](active-directory-sso-integrate-saas-apps.md)

Este artigo descreve como utilizar o [Azure portal](https://portal.azure.com) para gerir definições de início de sessão únicos para as aplicações, especialmente fases, que foram adicionadas a partir da [Galeria de aplicação do Azure Active Directory (Azure AD)](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). A experiência de gestão do Azure AD para single sign-on está atualmente na pré-visualização público e este artigo descreve as novas funcionalidades, bem como algumas limitações temporárias que serão num local apenas durante o período de pré-visualização. [Qual é a pré-visualização?](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>Localizar as aplicações no novo portal

Partir Setembro de 2016, todas as aplicações que tenham sido configuradas para single sign-on num diretório, por um administrador do diretório através da [Galeria de aplicação do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) no interior do [Azure portal clássico](https://manage.windowsazure.com), podem agora ser visualizadas e geridas no portal do Azure.

Podem encontrar estas aplicações na secção **Aplicações empresariais** do portal do Azure, uma ligação para o qual pode ser encontrada na lista **Mais serviços** no [portal](https://portal.azure.com). Aplicações empresariais são as aplicações que foram implementadas e estão a ser utilizadas pelos utilizadores na sua organização.

![Pá aplicações empresariais][1]

Selecione **todas as aplicações** para ver uma lista de todas as aplicações que tenham sido configurados, incluindo as aplicações que tivessem sido adicionadas a partir da galeria. Selecionar uma aplicação carrega o pá recurso para essa aplicação, onde podem ser visualizados relatórios nessa aplicação e pode ser gerida uma variedade de definições.

Para gerir as definições de início de sessão únicos, selecione **o início de sessão único**.

![Pá de recursos de aplicação][2]


##<a name="single-sign-on-modes"></a>Modos de início de sessão únicos

O **serviço Single sign-on** pá começa com um menu de **modo** que permite que o modo único início de sessão de ser configurado. As opções disponíveis incluem:

* **Baseadas em SAML Sign-on** - esta opção está disponível se a aplicação suporta completo federado serviço single sign-on com o Azure Active Directory utilizando o protocolo SAML 2.0.

* **Com base em palavra-passe de início de sessão** - esta opção está disponível se Azure AD suporta o formulário de palavra-passe de preenchimento para esta aplicação.

* **Ligado ao iniciar sessão no** - anteriormente conhecido como "Existente single sign-on", esta opção permite aos administradores para colocar uma ligação para esta aplicação no iniciador de aplicações do utilizador painel Azure AD acesso ou do Office 365.

Para mais informações sobre estes modos, consulte o artigo [como único início de sessão com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).


##<a name="saml-based-sign-on"></a>Baseadas em SAML início de sessão

A opção **baseadas em SAML início de sessão** apresenta uma pá que é dividido em quatro secções:

###<a name="domains-and-urls"></a>Domínios e URLs
Este é onde todos os detalhes sobre a aplicação domínio e URLs são adicionados no diretório da sua Azure AD. Todas as entradas necessárias para tornar a aplicação de trabalho de início de sessão único são apresentadas diretamente no ecrã, Considerando que todas as entradas opcionais podem ser visualizadas ao selecionar a caixa de verificação **Mostrar avançadas definições de URL** . A lista completa de entradas do tipo suportadas inclui:

* **Início de sessão no URL** – onde o utilizador vai para o início de sessão para esta aplicação. Se a aplicação estiver configurada para executar o serviço iniciados por fornecedor início de sessão único no, em seguida, quando um utilizador navega para este URL, o fornecedor de serviços é que o redirecionamento necessário para Azure AD para autenticar e de início de sessão do utilizador. Se este campo é preenchido, em seguida, Azure AD irá utilizar este URL para iniciar a aplicação a partir do Office 365 e o painel do Azure AD acesso. Se este campo for omitido, em seguida, Azure AD em vez disso, executa o fornecedor de identidades-início de sessão iniciada quando a aplicação é iniciada a partir do Office 365, o painel de acesso do Azure AD ou a partir do Azure AD único início de sessão do URL.

* **Identificador** - URI este deverá identificar exclusivamente a aplicação para o qual único ao iniciar sessão no está configurado. Este é o valor que Azure AD envia novamente a aplicação como o parâmetro de audiência do SAML token e a aplicação é esperada para validá-lo. Este valor também é apresentado como o ID da entidade em qualquer metadados SAML fornecido pela aplicação.

* **URL de resposta** - o URL de resposta é onde a aplicação de espera receber o token SAML. Isto é também conhecido como o URL do serviço de consumidor afirmação (ACS). Depois de estes foram introduzidas, clique em seguinte para avançar para o ecrã seguinte. Este ecrã fornece informações sobre o que tem de ser configurado no lado aplicação para ativá-la aceitar um token SAML a partir do Azure AD.

* **Estado do reencaminhamento** - o estado do reencaminhamento é um parâmetro opcional que o pode ajudar a indicar a aplicação onde redirecionar o utilizador depois de concluída a autenticação. Normalmente, o valor é um URL válido na aplicação, no entanto, algumas aplicações utilizam este campo de forma diferente (Consulte início de sessão único a aplicação na documentação para obter detalhes). A capacidade de definir o estado do reencaminhamento é uma nova funcionalidade que seja exclusiva para o novo portal Azure.

###<a name="user-attributes"></a>Atributos do utilizador
Este é onde os administradores podem ver e editar os atributos que são enviados no token SAML problemas do Azure AD para a aplicação de cada vez que os utilizadores iniciar sessão.

Para a primeira versão de pré-visualização, o atributo só editável suportado é o atributo **Identificador de utilizador** . O valor deste atributo é o campo no Azure AD que identifica exclusivamente cada utilizador dentro da aplicação. Por exemplo, se a aplicação foi implementada utilizando o "endereço de E-Mail" como o nome de utilizador e o identificador exclusivo, em seguida, o valor seria definido para o campo "user.mail" no Azure AD.

Edição de atributos adicionais será suportada numa pré-visualização subsequente.

###<a name="saml-signing-certificate"></a>Certificado de assinatura SAML
Esta secção mostra os detalhes do certificado que utiliza o Azure AD para iniciar sessão os tokens SAML são enviados para a aplicação autentica o utilizador de cada vez. Permite que as propriedades do certificado atual para que seja inspecionado, incluindo a data de expiração.

A capacidade de sobreposição o certificado e gerir certificado adicional opções serão suportadas numa versão de pré-visualização subsequentes. Tenha em atenção que totalmente a gestão de certificados ainda pode ser executada no [Azure portal clássica](active-directory-sso-certs.md).

###<a name="application-configuration"></a>Configuração da aplicação
A secção final fornece a documentação e/ou controlos necessários para configurar a própria aplicação para utilizar o Azure Active Directory como um fornecedor de identidades.

No menu da lista de opções de saída de **Configurar a aplicação** fornece novas concisas incorporadas instruções para configurar a aplicação. Este é outra nova funcionalidade exclusiva para o novo portal Azure.

> [AZURE.NOTE] Para um exemplo completo da documentação incorporado, consulte a aplicação de Salesforce.com. Documentação para as aplicações adicionais está a ser adicionada continuamente durante a pré-visualização.

![Documentos incorporados][3]

##<a name="password-based-sign-on"></a>Com base em palavra-passe de início de sessão
Se for suportado para a aplicação, selecionando o modo SSO com base em palavra-passe e selecionando **Guardar** instantaneamente configura-o para fazer SSO com base em palavra-passe. Para obter mais informações sobre como implementar o SSO com base em palavra-passe, consulte o artigo [como único início de sessão com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Com base em palavra-passe de início de sessão][4]


##<a name="linked-sign-on"></a>Início de sessão ligados
Se for suportado para a aplicação, selecionar o modo SSO ligado permite-lhe introduzir o URL que pretende que o painel de acesso do Azure AD ou o Office 365 para redirecionar para quando os utilizadores clicam nesta aplicação. Para mais informações sobre SSO ligada (anteriormente conhecido como "SSO existente"), consulte o artigo [como único início de sessão com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![O início de sessão ligado][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
