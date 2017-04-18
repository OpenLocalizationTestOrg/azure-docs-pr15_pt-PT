<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com o construtor de "e" | Microsoft Azure" 
    description="Saiba como utilizar o construtor de "e" com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Tutorial: Integração do Azure Active Directory com o construtor de "e"
  
Este tutorial objectivo Mostrar a integração do Azure e construtor de "e".  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino do construtor de "e"
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a "e" construtor poderão para o início de sessão único para a aplicação no site da sua empresa e construtor (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o construtor de "e"
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-e-builder-tutorial/IC777378.png "Cenário")
##<a name="enabling-the-application-integration-for-e-builder"></a>Ativar a integração de aplicação para o construtor de "e"
  
O objectivo desta secção é destacar como ativar a integração de aplicação para o construtor de "e".

###<a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>Para ativar a integração de aplicação para o construtor de "e", execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-e-builder-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-e-builder-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-e-builder-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-e-builder-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva o **Construtor de "e"**.

    ![Galeria de aplicação] (./media/active-directory-saas-e-builder-tutorial/IC777379.png "Galeria de aplicação")

7.  No painel de resultados, selecione o **Construtor de "e"**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Construtor de "e"] (./media/active-directory-saas-e-builder-tutorial/IC777380.png "Construtor de "e"")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para "e"-Builder com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação do **Construtor de "e"** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão construtor de "e"** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **construtor e inicie sessão no URL** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. e Builder.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configurar o URL da aplicação")

4.  **Configurar serviço single sign-on no construtor de "e"** página, para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, o ficheiro de dados localmente como **c:\\"e" BuilderMetaData.xml**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configurar serviço single sign-on")

5.  Reencaminhar esse ficheiro de metadados ao construtor de "e" equipa de suporte. Configura as necessidades da equipa de suporte serviço single sign-on por si.

6.  Selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item ação para configurar o utilizador aprovisionamento ao construtor de "e".  
Quando um utilizador atribuído tenta que inicie sessão no construtor de "e" utilizando o painel de acesso, o construtor de "e" verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo construtor de "e".
##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Para atribuir utilizadores a construtor de "e", execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação do **Construtor de "e" **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-e-builder-tutorial/IC777386.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-e-builder-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
