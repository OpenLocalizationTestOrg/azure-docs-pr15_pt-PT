<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Relic novo | Microsoft Azure" 
    description="Saiba como utilizar Relic nova com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integração do Azure Active Directory com Relic novo
  
Este tutorial objectivo Mostrar como configurar o serviço single sign-on entre o Azure Active Directory e Relic novo.
  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um novo Relic serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure Active Directory que tiver atribuído a nova Relic poderão para serviço single sign-on utilizando o painel de acesso AAD.

1.  Ativar a integração de aplicação para o novo Relic
2.  Configurar o Single Sign-On
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-new-relic-tutorial/IC797030.png "Cenário")
##<a name="enabling-the-application-integration-for-new-relic"></a>Ativar a integração de aplicação para o novo Relic
  
Objectivo de nesta secção é como ativar a integração de aplicação para o novo Relic de destaque.

###<a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Para ativar a integração de aplicação para o novo Relic, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-new-relic-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-new-relic-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-new-relic-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-new-relic-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva o **Novo Relic**.

    ![Galeria de aplicação] (./media/active-directory-saas-new-relic-tutorial/IC797031.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Relic novo**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Novo Relic] (./media/active-directory-saas-new-relic-tutorial/IC797032.png "Novo Relic")
##<a name="configuring-single-sign-on"></a>Configurar o Single Sign-On
  
Esta secção descreve como permitir que os utilizadores autenticar Relic novo com a sua conta no Azure Active Directory, utilizando a Federação com base no protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Relic novo** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão novo Relic** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Novo Relic início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão aplicação Relic novo e, em seguida, clique em **seguinte**. 

    O URL da aplicação é o URL da sua Relic novo inquilino (por exemplo: *https://rpm.newrelic.com*):

    ![Configurar o URL da aplicação] (./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na nova Relic** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente para o seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão site da sua empresa **Relic novo** como administrador.

6.  No menu no canto superior, clique em **Definições da conta**.

    ![Definições da conta] (./media/active-directory-saas-new-relic-tutorial/IC797036.png "Definições da conta")

7.  Clique no separador **segurança e de autenticação** e, em seguida, clique no separador **Single Sign-on** .

    ![Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")

8.  Na página de diálogo SAML, execute os seguintes passos:

    ![SAML] (./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Clique em **Selecionar ficheiro** para carregar o seu certificado transferido do Azure Active Directory.
    2.  No Azure clássico portal do, na página **Configurar serviço single sign-on na nova Relic** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão remoto** .
    3.  No portal clássico Azure, na página **Configurar serviço single sign-on na nova Relic** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **termine a sessão URL de destino** .
    4.  Clique em **Guardar as minhas alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure Active Directory que inicie sessão no novo Relic, tem de ser aprovisionadas para Relic novo.  
No caso de novo Relic, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Aprovisionamento de uma conta de utilizador para o novo Relic, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Relic novo** como administrador.

2.  No menu no canto superior, clique em **Definições da conta**.

    ![Definições da conta] (./media/active-directory-saas-new-relic-tutorial/IC797040.png "Definições da conta")

3.  No painel de **conta** no lado esquerdo, clique em **Resumo**e, em seguida, clique em **Adicionar utilizador**.

    ![Definições da conta] (./media/active-directory-saas-new-relic-tutorial/IC797041.png "Definições da conta")

4.  Na caixa de diálogo **utilizadores ativos** , efetue os seguintes passos:

    ![Utilizadores ativos] (./media/active-directory-saas-new-relic-tutorial/IC797042.png "Utilizadores ativos")

    1.  Na caixa de **correio eletrónico** do texto, escreva o endereço de e-mail de um utilizador do Azure Active Directory válido que pretende aprovisionar.
    2.  Que **função** selecione **utilizador**.
    3.  Clique em **Adicionar este utilizador**.

>[AZURE.NOTE]Pode utilizar quaisquer outros Relic novo utilizador conta ferramentas para a criação ou APIs fornecida pela nova Relic às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Para atribuir utilizadores a nova Relic, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Relic novo** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-new-relic-tutorial/IC797043.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-new-relic-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).




