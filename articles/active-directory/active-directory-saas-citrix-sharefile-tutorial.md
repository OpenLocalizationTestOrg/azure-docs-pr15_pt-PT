<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Citrix ShareFile | Microsoft Azure" 
    description="Saiba como utilizar Citrix ShareFile com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integração com o Azure Active Directory com Citrix ShareFile

Este tutorial objectivo Mostrar a integração do Azure e Citrix ShareFile.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Citrix ShareFile

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Citrix ShareFile poderão para o início de sessão único para a aplicação no site da sua empresa Citrix ShareFile (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Citrix ShareFile
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Cenário")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>Ativar a integração da aplicação para Citrix ShareFile

Objectivo de nesta secção é como ativar a integração da aplicação para Citrix ShareFile de destaque.

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Para ativar a integração da aplicação para Citrix ShareFile, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Citrix ShareFile**.

    ![Galeria de aplicação] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Citrix ShareFile**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Citrix ShareFile] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Citrix ShareFile com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Citrix ShareFile** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Ativar o início de sessão único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Ativar o início de sessão único")

2.  Na página **como pretende utilizadores a iniciar sessão Citrix ShareFile** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Citrix ShareFile início de sessão no URL** , escreva o URL da sua utilizando o padrão seguinte `https://<tenant-name>.shareFile.com`e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Citrix ShareFile** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![ConfigureSingle Sign-On] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa **Citrix ShareFile** como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Admin**.

7.  No painel de navegação esquerdo, selecione **Configurar Single Sign-On**.

    ![Administração da conta] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Administração da conta")

8.  No **Single Sign-On / configuração do SAML 2.0** página de diálogo em **Definições básicas**, execute os seguintes passos:

    ![O início de sessão único] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "O início de sessão único")

    1.  Clique em **Ativar SAML**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Citrix ShareFile** , copie o valor de **ID da entidade** e, em seguida, colá-lo à **Your IDP emissor / ID da entidade** caixa de texto.
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Citrix ShareFile** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    4.  No portal clássico Azure, **a configurar serviço single sign-on na Citrix ShareFile** página de diálogo, copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .
    5.  Clique em **Alterar** junto ao campo **Certificado x. 509** e, em seguida, carregue o certificado que transferiu a partir do portal de clássico do Azure AD.
        ![Definições básicas] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Definições básicas")

9.  Clique em **Guardar** no portal de gestão de Citrix ShareFile.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Citrix ShareFile, tem de ser aprovisionadas para Citrix ShareFile.  
No caso de Citrix ShareFile, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Citrix ShareFile** .

2.  Clique em **Gerir utilizadores \> gerir utilizadores base \> + criar empregado**.

    ![Criar colaborador] (./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Criar de funcionários")

3.  Introduza o **E-Mail**, **nome próprio** e **Apelido** de uma Azure AD válido de conta pretende aprovisionar.

    ![Informações básicas] (./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Informações básicas")

4.  Clique em **Adicionar utilizador**.

    >[AZURE.NOTE] Titular da conta AAD irá receber uma mensagem de e-mail e seguir uma ligação para confirmar a respetiva conta antes que se torne ativo.

>[AZURE.NOTE] Pode utilizar quaisquer outros Citrix ShareFile utilizador conta ferramentas para a criação ou APIs fornecida pela Citrix ShareFile às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Para atribuir utilizadores a Citrix ShareFile, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Citrix ShareFile **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
