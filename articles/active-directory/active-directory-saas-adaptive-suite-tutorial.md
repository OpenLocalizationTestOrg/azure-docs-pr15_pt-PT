<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Suite ajustável | Microsoft Azure"
    description="Saiba como utilizar o conjunto de aplicações ajustável com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Tutorial: Integração do Azure Active Directory com ajustável conjunto de aplicações

Este tutorial objectivo Mostrar a integração do Azure e Suite ajustável.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino do conjunto de aplicações ajustável

Depois de concluir este tutorial, os utilizadores do Azure AD que tiver atribuído ao conjunto de aplicações ajustável poderão para o início de sessão único para a aplicação no site da sua empresa Suite ajustável (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para obter o conjunto ajustável
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Cenário")
##<a name="enabling-the-application-integration-for-adaptive-suite"></a>Ativar a integração de aplicação para obter o conjunto ajustável

Objectivo de nesta secção é como ativar a integração de aplicação para obter o conjunto ajustável de destaque.

###<a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Para ativar a integração de aplicação para obter o conjunto ajustável, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Ajustável conjunto de aplicações**.

    ![Galeria de aplicação] (./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Ajustável conjunto de aplicações**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Conjunto de aplicações ajustável] (./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Conjunto de aplicações ajustável")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados ao conjunto de aplicações ajustável com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para obter o conjunto ajustável requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Ajustável conjunto de aplicações** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão conjunto de aplicações ajustável** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurar o Single Sign-On")

3.  Na página **Configurar definições de aplicação** , na caixa de texto **Resposta URL** , escreva o URL da sua utilizando o padrão seguinte "*samlsso/https://login.adaptiveinsights.com:443/RlJFRVRSSUFMMTI3MTE =*" e, em seguida, clique em **seguinte**.

    >[AZURE.NOTE] Pode obter este valor a partir da página de **Definições de SSO SAML** o conjunto ajustável.

    ![Configurar definições de aplicação] (./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configurar definições de aplicação")

4.  Na página **Configurar serviço single sign-on no conjunto de aplicações ajustável** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Suite ajustável como administrador.

6.  Aceda a **Admin**.

    ![Admin] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

7.  Na secção **utilizadores e funções** , clique em **Gerir definições de SSO SAML**.

    ![Gerir definições de SAML SSO] (./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Gerir definições de SAML SSO")

8.  Na página **Definições de SSO SAML** , execute os seguintes passos:

    ![Definições de SAML SSO] (./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Definições de SAML SSO")

    1.  Na caixa de texto **nome do fornecedor de identidade** , escreva um nome para a configuração.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on no conjunto de aplicações ajustável** , copie o valor de **ID da entidade de** e, em seguida, colá-la a caixa de texto do **fornecedor de identidades ID da entidade** .
    3.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on no conjunto de aplicações ajustável** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **fornecedor de identidades SSO URL** .
    4.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on no conjunto de aplicações ajustável** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto **personalizado termine a sessão URL** .
    5.  Para carregar o certificado transferido, clique em **Escolher ficheiro**.
    6.  Como o **id de utilizador do SAML**, selecione o **nome de utilizador ajustável informações do utilizador**.
    7.  Como **localização de id de utilizador do SAML**, selecione o **id de utilizador no NameID do assunto**.
    8.  Como **Formatar SAML NameID**, selecione **endereço de E-Mail**.
    9.  Como **Ativar SAML**, selecione **Permitir SSO SAML e direta ajustável informações de início de sessão**.
    10. Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no conjunto de aplicações ajustável, tem de ser aprovisionadas para o conjunto de aplicações ajustável.  
No caso de um conjunto de aplicações ajustável, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Suite ajustável** como administrador.

2.  Aceda a **Admin**.

    ![Admin] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

3.  Na secção **utilizadores e funções** , clique em **Adicionar utilizador**.

    ![Adicionar utilizador] (./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Adicionar utilizador")

4.  Na secção **Novo utilizador** , execute os seguintes passos:

    ![Submeter] (./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Submeter")

    1.  Escreva o **nome**, **início de sessão**, **E-Mail**, **palavra-passe** do utilizador Azure Active Directory válido que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Selecione uma **função**.
    3.  Clique em **Submeter**.

>[AZURE.NOTE] Pode utilizar qualquer outras conjunto de aplicações ajustável utilizador conta ferramentas para a criação ou APIs fornecida pela Suite ajustável às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Para atribuir utilizadores ao conjunto de aplicações ajustável, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Ajustável conjunto de aplicações **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
