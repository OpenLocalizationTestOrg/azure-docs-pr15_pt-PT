<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com BlueJeans | Microsoft Azure" 
    description="Saiba como utilizar BlueJeans com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>Tutorial: Azure AD integração com BlueJeans

Este tutorial objectivo Mostrar a integração do Azure e BlueJeans.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um BlueJeans serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a BlueJeans poderão para o início de sessão único para a aplicação no site da sua empresa BlueJeans (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para BlueJeans
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Cenário")
##<a name="enabling-the-application-integration-for-bluejeans"></a>Ativar a integração da aplicação para BlueJeans

Objectivo de nesta secção é como ativar a integração da aplicação para BlueJeans de destaque.

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Para ativar a integração da aplicação para BlueJeans, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-bluejeans-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **BlueJeans**.

    ![Galeria de aplicação] (./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galeria de aplicação")

7.  No painel de resultados, selecione **BlueJeans**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![BlueJeans] (./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para BlueJeans com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **BlueJeans** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão BlueJeans** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **BlueJeans início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*https://company.BlueJeans.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na BlueJeans** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa **BlueJeans** como administrador.

6.  Aceda a **Administração \> as definições dos grupos \> segurança**.

    ![Admin] (./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

7.  Na secção **segurança** , execute os seguintes passos:

    ![SAML Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign-On")

    1.  Selecione **SAML Single Sign-On**.
    2.  Selecione **Ativar aprovisionamento automático**.

8.  Deslocar-se com os seguintes passos:

    ![Caminho de certificados] (./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Caminho de certificados")

    1.  Clique em **Selecionar ficheiro**e, em seguida, carregue o certificado transferido.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na BlueJeans** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na BlueJeans** , copie o valor de **Alterar URL de palavra-passe** e, em seguida, colá-la a caixa de texto **Palavra-passe alterar URL** .
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na BlueJeans** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .

9.  Deslocar-se com os seguintes passos:

    ![Guardar alterações] (./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Guardar alterações")

    1.  Na caixa de texto **id de utilizador** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  Na caixa de **correio eletrónico** do texto, escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Clique em **Guardar alterações**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no BlueJeans, tem de ser aprovisionadas para BlueJeans.  
No caso de BlueJeans, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **BlueJeans** como administrador.

2.  Aceda a **Administração \> gerir utilizadores \> adicionar utilizador**.

    ![Admin] (./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")

    >[AZURE.IMPORTANT] No separador **Adicionar utilizador** só está disponível se, no **separador Segurança**, **Ativar aprovisionamento automático** está desmarcada.

3.  Na secção **Adicionar utilizador** , execute os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Adicionar utilizador")

    1.  Escreva um **Nome de utilizador BlueJeans**, um **endereço de correio eletrónico**, um **BlueJeans ID da reunião**, um **Código de acesso do moderador**, um **Nome completo**, a **empresa** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Adicionar utilizador**.

>[AZURE.NOTE] Pode utilizar quaisquer outros BlueJeans utilizador conta ferramentas para a criação ou APIs fornecida pela BlueJeans às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Para atribuir utilizadores a BlueJeans, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **BlueJeans **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
