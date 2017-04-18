<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Clarizen | Microsoft Azure" 
    description="Saiba como utilizar Clarizen com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: Integração do Azure Active Directory com Clarizen

Este tutorial objectivo Mostrar a integração do Azure e Clarizen.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Clarizen serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Clarizen poderão para o início de sessão único para a aplicação no site da sua empresa Clarizen (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Clarizen
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-clarizen-tutorial/IC784679.png "Cenário")
##<a name="enabling-the-application-integration-for-clarizen"></a>Ativar a integração da aplicação para Clarizen

Objectivo de nesta secção é como ativar a integração da aplicação para Clarizen de destaque.

###<a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Para ativar a integração da aplicação para Clarizen, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-clarizen-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-clarizen-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-clarizen-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-clarizen-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Clarizen**.

    ![Galeria de aplicação] (./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Clarizen**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Clarizen] (./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Clarizen com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Clarizen** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Clarizen** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurar o Single Sign-On")

3.  Na página **Configurar serviço single sign-on na Clarizen** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurar o Single Sign-On")

4.  Numa janela do browser web diferente, inicie sessão no site da sua empresa **Clarizen** como administrador (por exemplo: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Clique em seu nome de utilizador e, em seguida, clique em **Definições**.

    ![Definições] (./media/active-directory-saas-clarizen-tutorial/IC784685.png "Definições")

6.  Clique no separador **Definições globais** e, em seguida, junto a **Autenticação federado**, clique em **Editar**.

    ![Definições globais] (./media/active-directory-saas-clarizen-tutorial/IC786906.png "Definições globais")

7.  Na caixa de diálogo **Federado autenticação** , execute os seguintes passos:

    ![Federado autenticação] (./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federado autenticação")

    1.  Clique em **carregar** para carregar o seu certificado transferido.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Clarizen** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto de **início de sessão no URL** .
    3.  No portal clássico Azure, na página de diálogo **Configurar único terminar sessão na Clarizen** , copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto **Sign-out URL** .
    4.  Selecione **utilizar mensagem**.
    5.  Clique em **Guardar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Clarizen, tem de ser aprovisionadas para Clarizen.  
No caso de Clarizen, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Clarizen** como administrador.

2.  Clique em **pessoas**.

    ![Pessoas] (./media/active-directory-saas-clarizen-tutorial/IC784689.png "Pessoas")

3.  Clique em **Convidar utilizador**.

    ![Convidar utilizadores] (./media/active-directory-saas-clarizen-tutorial/IC784690.png "Convidar utilizadores")

4.  Na página de diálogo convidar pessoas, execute os seguintes passos:

    ![Convidar pessoas] (./media/active-directory-saas-clarizen-tutorial/IC784691.png "Convidar pessoas")

    1.  Na caixa de **correio eletrónico** do texto, escreva o endereço de e-mail de uma conta do Azure Active Directory válida que pretende aprovisionar.
    2.  Clique em **Convidar**.

    >[AZURE.NOTE] Titular da conta Azure Active Directory irá receber uma mensagem de e-mail e seguir uma ligação para confirmar a respetiva conta antes que se torne ativo.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Para atribuir utilizadores a Clarizen, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Clarizen **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-clarizen-tutorial/IC784692.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-clarizen-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
