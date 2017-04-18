<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Brightspace por Desire2Learn | Microsoft Azure" 
    description="Saiba como utilizar Brightspace por Desire2Learn com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Tutorial: Integração do Azure Active Directory com Brightspace por Desire2Learn

Este tutorial objectivo Mostrar a integração do Azure e Brightspace por Desire2Learn.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Subscrição com capacidade de um Brightspace por Desire2Learn serviço single sign-on

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Brightspace por Desire2Learn poderão para o início de sessão único para a aplicação no seu Brightspace ao site de empresa Desire2Learn (sinal de fornecedor iniciados por serviço no), ou ao utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Brightspace por Desire2Learn
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Cenário")
##<a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>Ativar a integração da aplicação para Brightspace por Desire2Learn

Objectivo de nesta secção é como ativar a integração da aplicação para Brightspace por Desire2Learn de destaque.

###<a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Para ativar a integração da aplicação para Brightspace por Desire2Learn, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Brightspace por Desire2Learn**.

    ![Galeria de Apllication] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Galeria de Apllication")

7.  No painel de resultados, selecione **Brightspace por Desire2Learn**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Brightspace por Desire2Learn] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace por Desire2Learn")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Brightspace por Desire2Learn com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Brightspace por Desire2Learn** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Brightspace por Desire2Learn** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurar o URL da aplicação")

    1.  Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos utilizadores para iniciar sessão no seu **Brightspace por Desire2Learn** (por exemplo: *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
    2.  Clique em **seguinte**

4.  Na página **Configurar serviço single sign-on na Brightspace por Desire2Learn** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde os metadados no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurar o Single Sign-On")

5.  Envie o ficheiro de metadados transferido para o seu Brightspace pela equipa de suporte Desire2Learn.

    >[AZURE.NOTE] O Brightspace pela equipa de suporte Desire2Learn tem de fazer a configuração de SSO real.
Irá obter uma notificação quando SSO tenha sido ativado para a sua subscrição.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Brightspace por Desire2Learn, tem aprovisionadas para Brightspace por Desire2Learn.  
No caso de Brightspace por Desire2Learn, as contas de utilizador tem de ser criados pelo seu Brightspace pela equipa de suporte Desire2Learn.

>[AZURE.NOTE] Pode utilizar quaisquer outros Brightspace através de ferramentas de criação de conta de utilizador Desire2Learn ou APIs fornecida pela Brightspace por Desire2Learn aprovisionar o Azure Active Directory contas de utilizador.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Para atribuir utilizadores a Brightspace por Desire2Learn, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Brightspace por Desire2Learn **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
