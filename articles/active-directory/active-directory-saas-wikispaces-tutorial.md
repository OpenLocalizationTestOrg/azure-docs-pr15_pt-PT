<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Wikispaces | Microsoft Azure" 
    description="Saiba como utilizar Wikispaces com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Tutorial: Integração do Azure Active Directory com Wikispaces
  
Este tutorial objectivo Mostrar a integração do Azure e Wikispaces.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Wikispaces serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Wikispaces poderão para o início de sessão único para a aplicação no site da sua empresa Wikispaces (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Wikispaces
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Sceanrio] (./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##<a name="enabling-the-application-integration-for-wikispaces"></a>Ativar a integração da aplicação para Wikispaces
  
Objectivo de nesta secção é como ativar a integração da aplicação para Wikispaces de destaque.

###<a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Para ativar a integração da aplicação para Wikispaces, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-wikispaces-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Wikispaces**.

    ![Galeria de aplicação] (./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Wikispaces**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Wikispaces] (./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Wikispaces com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Wikispaces** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Wikispaces** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Wikispaces início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*http://company.wikispaces.net*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Wikispaces** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurar o Single Sign-On")

5.  Envie o metadatafile para a equipa de suporte Wikispaces.

    >[AZURE.NOTE] A configuração de início de sessão único tem de ser executado pela equipa de suporte Wikispaces. Irá obter uma notificação assim que já foi concluída a configuração.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Wikispaces, tem de ser aprovisionadas para Wikispaces.  
No caso de Wikispaces, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Wikispaces** como administrador.

2.  Aceda aos **Membros**.

    ![Membros] (./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Membros")

3.  Clique em **Convidar pessoas**.

    ![Convidar pessoas] (./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Convidar pessoas")

4.  Na secção de **Convidar pessoas** , execute os seguintes passos:

    ![Convidar pessoas] (./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Convidar pessoas")

    1.  Escreva os **nomes de utilizador ou o endereço de E-Mail** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Enviar**.  

        >[AZURE.NOTE] Titular da conta Azure Active Directory recebe uma mensagem de e-mail incluindo uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Wikispaces utilizador conta ferramentas para a criação ou APIs fornecida pela Wikispaces às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Para atribuir utilizadores a Wikispaces, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Wikispaces **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).