<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com uservoice do | Microsoft Azure" 
    description="Saiba como utilizar uservoice com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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

#<a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integração com o Azure Active Directory com uservoice do
  
Este tutorial objectivo Mostrar a integração do Azure e uservoice do.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino do site
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a uservoice do poderão para o início de sessão único para a aplicação no site da sua empresa site (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para uservoice do
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-uservoice-tutorial/IC777514.png "Cenário")

##<a name="enabling-the-application-integration-for-uservoice"></a>Ativar a integração da aplicação para uservoice do
  
Objectivo desta secção é como ativar a integração da aplicação para uservoice de destaque.

###<a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Para ativar a integração da aplicação para uservoice do, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-uservoice-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-uservoice-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-uservoice-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-uservoice-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **uservoice do**.

    ![Galeria de aplicação] (./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galeria de aplicação")

7.  No painel de resultados, selecione **uservoice do**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Uservoice do] (./media/active-directory-saas-uservoice-tutorial/IC777810.png "Uservoice do")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para uservoice com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para uservoice do requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração do **uservoice do** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão site** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Uservoice do URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. UserVoice.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na uservoice do** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\UserVoice.cer**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa site como administrador.

6.  Na barra de ferramentas no canto superior, clique em definições e, em seguida, selecione Web portal a partir do menu.

    ![Definições] (./media/active-directory-saas-uservoice-tutorial/IC777519.png "Definições")

7.  No separador **Web portal** , na secção **autenticação de utilizador** , clique em **Editar** para abrir a página de diálogo **Editar autenticação de utilizador**

    ![Portal da Web] (./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portal da Web")

8.  Na página de diálogo **Editar autenticação de utilizador** , execute os seguintes passos:

    ![Editar a autenticação de utilizador] (./media/active-directory-saas-uservoice-tutorial/IC777521.png "Editar a autenticação de utilizador")

    1.  Clique em **Single Sign-On (SSO)**.
    2.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na uservoice do** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la **SSO remoto início de sessão na** caixa de texto.
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na uservoice do** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a **caixa de texto Sign-Out remoto SSO**.
    4.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **identificação de SHA1 certificado atual** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Clique em **Guardar definições de autenticação**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no site, tem de ser aprovisionadas para uservoice do.  
No caso do uservoice do, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino do **uservoice do** .

2.  Aceda a **Definições**.

    ![Definições] (./media/active-directory-saas-uservoice-tutorial/IC777811.png "Definições")

3.  Clique em **Geral**.

4.  Clique em **permissões e agentes**.

    ![Agentes e permissões] (./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agentes e permissões")

5.  Clique em **Adicionar administradores**.

    ![Adicionar administradores] (./media/active-directory-saas-uservoice-tutorial/IC777813.png "Adicionar administradores")

6.  Na caixa de diálogo **Convidar administradores** , execute os seguintes passos:

    ![Convidar administradores] (./media/active-directory-saas-uservoice-tutorial/IC777814.png "Convidar administradores")

    1.  Na texbox mensagens de correio eletrónico, escreva o endereço de e-mail da conta que pretende aprovisionar o e, em seguida, clique em **Adicionar**.
    2.  Clique em **Convidar**.

>[AZURE.NOTE] Pode utilizar qualquer outras site utilizador conta ferramentas para a criação ou APIs fornecida pela uservoice às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Para atribuir utilizadores a uservoice do, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do **uservoice do **aplicação, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-uservoice-tutorial/IC777523.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-uservoice-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).