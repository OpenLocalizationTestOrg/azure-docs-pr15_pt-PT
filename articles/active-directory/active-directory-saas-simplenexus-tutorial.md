<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com SimpleNexus | Microsoft Azure" 
    description="Saiba como utilizar SimpleNexus com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Tutorial: Integração do Azure Active Directory com SimpleNexus
  
Este tutorial objectivo Mostrar a integração do Azure e SimpleNexus.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um SimpleNexus serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a SimpleNexus poderão para o início de sessão único para a aplicação no site da sua empresa SimpleNexus (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para SimpleNexus
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Cenário")
##<a name="enabling-the-application-integration-for-simplenexus"></a>Ativar a integração da aplicação para SimpleNexus
  
Objectivo de nesta secção é como ativar a integração da aplicação para SimpleNexus de destaque.

###<a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>Para ativar a integração da aplicação para SimpleNexus, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-simplenexus-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **nexus simples**.

    ![Galeria de aplicação] (./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Galeria de aplicação")

7.  No painel de resultados, selecione **SimpleNexus**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Nexus simples] (./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Nexus simples")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para SimpleNexus com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **SimpleNexus** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão SimpleNexus** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **SimpleNexus URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://simplenexus.com/CompanyName\_início de sessão*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na SimpleNexus** , clique em **transferir metadados**e, em seguida, reencaminhe o ficheiro de metadados para a equipa de suporte SimpleNexus.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurar o Single Sign-On")

    >[AZURE.NOTE] O início de sessão único tem de estar ativada pela equipa de suporte de SimpleNexus.

5.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no SimpleNexus, tem de ser aprovisionadas para SimpleNexus.  
No caso de SimpleNexus, aprovisionamento é uma tarefa manual realizada pelo administrador inquilino.

>[AZURE.NOTE] Pode utilizar quaisquer outros SimpleNexus utilizador conta ferramentas para a criação ou APIs fornecida pela SimpleNexus às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>Para atribuir utilizadores a SimpleNexus, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **SimpleNexus **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).