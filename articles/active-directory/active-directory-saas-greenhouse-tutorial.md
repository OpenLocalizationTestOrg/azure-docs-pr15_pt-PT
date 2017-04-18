<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com estufa | Microsoft Azure" 
    description="Saiba como utilizar estufa com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Tutorial: Integração do Azure Active Directory com estufa
  
Este tutorial objectivo Mostrar a integração do Azure e estufa.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Uma estufa única início de sessão subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a estufa poderão para o início de sessão único para a aplicação no site da sua empresa estufa (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para estufa
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Cenário")
##<a name="enabling-the-application-integration-for-greenhouse"></a>Ativar a integração da aplicação para estufa
  
Objectivo de nesta secção é como ativar a integração da aplicação para estufa de destaque.

###<a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Para ativar a integração da aplicação para estufa, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-greenhouse-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **estufa**.

    ![Galeria de aplicação] (./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Galeria de aplicação")

7.  No painel de resultados, selecione **estufa**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Estufa] (./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Estufa")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para estufa com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **estufa** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão estufa** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **URL no início de sessão** , escreva o seu URL utilizando o padrão seguinte "*https://company.greenhouse.io*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na estufa** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configurar serviço single sign-on")

5.  Reencaminhe esse ficheiro de metadados para a equipa de suporte estufa.

    >[AZURE.NOTE] O início de sessão único tem de estar ativada pela equipa de suporte de estufa.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no estufa, tem de ser aprovisionadas para estufa.  
No caso de estufa, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **estufa** como administrador.

2.  No menu no canto superior, clique em **Configurar**e, em seguida, clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Utilizadores")

3.  Clique em **novos utilizadores**.

    ![Novo utilizador] (./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Novo utilizador")

4.  Na secção **Adicionar novo utilizador** , execute os seguintes passos:

    ![Adicionar novo utilizador] (./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Adicionar novo utilizador")

    1.  Na caixa de texto **e-mails dos utilizadores Enter** , escreva o endereço de e-mail de uma conta do Azure Active Directory válida que pretende aprovisionar.
    2.  Clique em **Guardar**.
        
        >[AZURE.NOTE] Marcadores de posição de conta Azure Active Directory irão receber um e-mail que inclua uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros estufa utilizador conta ferramentas para a criação ou APIs fornecida pela estufa às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Para atribuir utilizadores a estufa, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **estufa **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).