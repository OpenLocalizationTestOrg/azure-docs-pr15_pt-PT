<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com o Adobe EchoSign | Microsoft Azure" 
    description="Saiba como utilizar o Adobe EchoSign com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Tutorial: Integração do Azure Active Directory com o Adobe EchoSign

Este tutorial objectivo Mostrar a integração do Azure e Adobe EchoSign.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um EchoSign Adobe única ao iniciar sessão no subscrição activada

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Adobe EchoSign poderão para o início de sessão único para a aplicação no site da sua empresa Adobe EchoSign (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Adobe EchoSign
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Cenário")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>Ativar a integração da aplicação para Adobe EchoSign

Objectivo de nesta secção é como ativar a integração da aplicação para Adobe EchoSign de destaque.

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>Para ativar a integração da aplicação para Adobe EchoSign, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Adobe EchoSign**.

    ![Galeria de aplicação] (./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Adobe EchoSign**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Adobe EchoSign] (./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Adobe EchoSign com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração do **Adobe EchoSign** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Adobe EchoSign** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Adobe EchoSign início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*https://company.echosign.com/*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Adobe EchoSign** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Adobe EchoSign como administrador.

6.  No menu no canto superior, clique em **conta**e, em seguida, no painel de navegação o dado para a esquerda, clique em **Definições de SAML** em **Definições da conta**.

    ![Conta] (./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Conta")

7.  Na secção definições SAML, execute os seguintes passos:

    ![Definições de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Definições de SAML")

    1.  Como **SAML modo**, selecione **SAML obrigatório**.
    2.  Selecione **Permitir EchoSign conta aos administradores inicie sessão com as respetivas credenciais EchoSign**.
    3.  Como a **Criação de utilizador**, selecione **Adicionar automaticamente os utilizadores autenticados através do SAML**.

8.  Mova, efetuar os seguintes passos:

    ![Definições de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Definições de SAML")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Adobe EchoSign** , copie o valor de **ID da entidade** e, em seguida, colá-la a caixa de texto **IdP o ID da entidade** .
    2.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na Adobe EchoSign** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão IdP** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Adobe EchoSign** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **IdP termine a sessão URL** .
    4.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado para um ficheiro de texto binário](http://youtu.be/PlgrzUZ-Y1o) 
 5.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado de IdP** 6.  Clique em **Guardar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Adobe EchoSign, tem de ser aprovisionadas para Adobe EchoSign.  
No caso de Adobe EchoSign, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Adobe EchoSign** como administrador.

2.  No menu no canto superior, clique em **conta**e, em seguida, no painel de navegação o dado para a esquerda, clique em **utilizadores e grupos**e, em seguida, clique em **criar um novo utilizador**.

    ![Conta] (./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Conta")

3.  Na secção **Criar novo utilizador** , execute os seguintes passos:

    ![Criar utilizador] (./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Criar utilizador")

    1.  Escreva o **Endereço de E-Mail**, **nome próprio** e **Apelido** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Criar utilizador**.

        >[AZURE.NOTE] Titular da conta Azure Active Directory receberão um e-mail que inclui uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Adobe EchoSign utilizador conta ferramentas para a criação ou APIs fornecida pela Adobe EchoSign às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Para atribuir utilizadores a Adobe EchoSign, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do **Adobe EchoSign **aplicação, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
