<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com RightAnswers | Microsoft Azure" 
    description="Saiba como utilizar RightAnswers com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>Tutorial: Integração do Azure Active Directory com RightAnswers
  
Este tutorial objectivo Mostrar a integração do Azure e RightAnswers. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um RightAnswers serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a RightAnswers poderão para o início de sessão único para a aplicação utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para RightAnswers
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Cenário")
##<a name="enabling-the-application-integration-for-rightanswers"></a>Ativar a integração da aplicação para RightAnswers
  
Objectivo de nesta secção é como ativar a integração da aplicação para RightAnswers de destaque.

###<a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>Para ativar a integração da aplicação para RightAnswers, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-rightanswers-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **RightAnswers**.

    ![Galeria de aplicação] (./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Galeria de aplicação")

7.  No painel de resultados, selecione **RightAnswers**e, em seguida, clique em **Concluir** para adicionar a aplicação.
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para RightAnswers com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **RightAnswers** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão RightAnswers** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configurar o Single Sign-On")

3.  Na página **Configurar definições de aplicação** , na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão à aplicação RightAnswers (por exemplo: *https://fortify.rightanswers.com/portal/ss/*) e, em seguida, clique em **seguinte**.

    ![Configurar definições de aplicação] (./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configurar definições de aplicação")

4.  Na página **Configurar serviço single sign-on na RightAnswers** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configurar o Single Sign-On")

5.  Envie o ficheiro de metadados transferido para a equipa de suporte RightAnswers.

    >[AZURE.NOTE] A equipa de suporte RightAnswers tem de fazer a configuração de SSO real.
Irá obter uma notificação quando SSO tenha sido ativado para a sua subscrição.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no RightAnswers, tem de ser aprovisionadas para RightAnswers.  
No caso de RightAnswers, aprovisionamento é uma tarefa automatizada.  
Não existe nenhum item ação por si.
  
Os utilizadores são criados automaticamente se for necessário durante a primeira única início de sessão tentativa.

>[AZURE.NOTE]Pode utilizar quaisquer outros RightAnswers utilizador conta ferramentas para a criação ou APIs fornecida pela RightAnswers às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>Para atribuir utilizadores a RightAnswers, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **RightAnswers **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).