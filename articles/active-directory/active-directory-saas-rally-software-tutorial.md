<properties 
    pageTitle="Tutorial: Azure Active Directory integração com o Software está | Microsoft Azure" 
    description="Saiba como utilizar o Rally Software com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Azure Active Directory integração com o Software está
  
Este tutorial objectivo Mostrar a integração do Azure e Rally Software.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Rally Software
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o Rally Software
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-rally-software-tutorial/IC769525.png "Cenário")
##<a name="enabling-the-application-integration-for-rally-software"></a>Ativar a integração de aplicação para o Rally Software
  
Objectivo de nesta secção é como ativar a integração da aplicação para Rally Software de destaque.

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Para ativar a integração de aplicação para o Rally Software, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-rally-software-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-rally-software-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-rally-software-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-rally-software-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **está**.

    ![Galeria de aplicação] (./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Rally Software**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Rally Software] (./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados ao Rally Software com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado para Rally Software.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **Rally Software **, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configurar serviço single sign-on")

2.  Na página **como pretende que como os utilizadores para iniciar sessão está** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Rally URL do inquilino de Software** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. rally.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na está** , clique em transferir metadados e, em seguida, guarde-o no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configurar serviço single sign-on")

5.  Inicie sessão no seu inquilino do **Rally Software** .

6.  Na barra de ferramentas no canto superior, clique em **configuração**e, em seguida, selecione a **subscrição**.

    ![Subscrição] (./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscrição")

7.  Clique no botão de **ação** na barra de ferramentas visível no lado direito e, em seguida, selecione **Editar subscrição**.

8.  Na página de diálogo da **subscrição** , execute os seguintes passos e, em seguida, clique em **Guardar e fechar**:

    ![Autenticação] (./media/active-directory-saas-rally-software-tutorial/IC769542.png "Autenticação")

    1.  Selecione **está ou SSO autenticação** a partir da lista pendente de autenticação
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Rally Software** , copie o valor de **ID do fornecedor de identidade** e, em seguida, colá-la a caixa de texto do **URL do fornecedor de identidade**
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Rally Software** , copie o valor **De URL remoto termine a sessão** .

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para AAD os utilizadores possam iniciar sessão, tem de ser aprovisionadas para a aplicação de Rally Software utilizar os seus nomes de utilizador do Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino do Rally Software.

2.  Aceda a **configuração \> utilizadores**e, em seguida, clique em **+ Add New**.

    ![Utilizadores] (./media/active-directory-saas-rally-software-tutorial/IC781039.png "Utilizadores")

3.  Escreva o nome na caixa de texto novo utilizador e, em seguida, clique em **Adicionar com detalhes**.

4.  Na secção **Criar utilizador** , execute os seguintes passos:

    ![Criar utilizador] (./media/active-directory-saas-rally-software-tutorial/IC781040.png "Criar utilizador")

    1.  Na caixa de texto **Nome de utilizador** , escreva o nome do utilizador que pretende aprovisionar Azure AD.
    2.  Na caixa de texto **Endereço de correio eletrónico** , escreva o endereço de e-mail do utilizador que pretende aprovisionar Azure AD.
    3.  Clique em **Guardar e fechar**.

>[AZURE.NOTE]Pode utilizar qualquer outras Rally Software utilizador conta ferramentas para a criação ou APIs fornecido pelo Rally Software às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Para atribuir utilizadores a Rally Software, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação de **Rally Software** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-rally-software-tutorial/IC769548.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-rally-software-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).




