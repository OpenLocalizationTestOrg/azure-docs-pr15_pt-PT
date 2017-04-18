<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com livros Overdrive | Microsoft Azure" 
    description="Saiba como utilizar livros Overdrive com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Tutorial: Integração do Azure Active Directory com Overdrive livros
  
Este tutorial objectivo Mostrar a integração do Azure e OverDrive.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um OverDrive serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a OverDrive poderão para o início de sessão único para a aplicação no site da sua empresa OverDrive (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para OverDrive
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Cenário")
##<a name="enabling-the-application-integration-for-overdrive"></a>Ativar a integração da aplicação para OverDrive
  
Objectivo de nesta secção é como ativar a integração da aplicação para OverDrive de destaque.

###<a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>Para ativar a integração da aplicação para OverDrive, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **OverDrive**.

    ![Galeria de aplicação] (./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galeria de aplicação")

7.  No painel de resultados, selecione **OverDrive**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![OverDrive] (./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para OverDrive com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **OverDrive** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Ativar o início de sessão único] (./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Ativar o início de sessão único")

2.  Na página **como pretende que como os utilizadores para iniciar sessão OverDrive** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **OverDrive URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*http://mslibrarytest.libraryreserve.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurar o URL da aplicação")

4.  Na **Configurar serviço single sign-on na OverDrive** página, para transferir o ficheiro de metadados e, em seguida, envie-a para a equipa de suporte OverDrive.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configurar serviço single sign-on")

    >[AZURE.NOTE]A equipa de suporte OverDrive configura serviço single sign-on por si e envia uma notificação quando tiver sido concluída a configuração.

5.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item ação para configurar o aprovisionamento de utilizador para OverDrive.  
Quando um utilizador atribuído tenta que inicie sessão no OverDrive, uma conta de OverDrive é automaticamente criada se for necessário.

>[AZURE.NOTE]Pode utilizar quaisquer outros OverDrive utilizador conta ferramentas para a criação ou APIs fornecida pela OverDrive às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>Para atribuir utilizadores a OverDrive, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **OverDrive **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).