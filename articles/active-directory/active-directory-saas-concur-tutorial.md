<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Concur | Microsoft Azure" 
    description="Saiba como utilizar Concur com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>Tutorial: Integração do Azure Active Directory com Concur  


Este tutorial objectivo Mostrar a integração do Azure e Concur.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino no Concur

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Concur
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-concur-tutorial/IC769766.png "Cenário")

>[AZURE.NOTE] A configuração da sua subscrição Concur para federados SSO através do SAML é uma tarefa em separado, que terá de contactar o Concur para efetuar.

##<a name="enabling-the-application-integration-for-concur"></a>Ativar a integração da aplicação para Concur

Objectivo de nesta secção é como ativar a integração da aplicação para Concur de destaque.

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>Para ativar a integração da aplicação para Concur, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-concur-tutorial/IC700993.png "O Active Directory")

2.  A partir do **diretório** lista, selecione o diretório para o qual pretende ativar a integração do diretório.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-concur-tutorial/IC700994.png "Aplicações")

4.  Para abrir a **Galeria de aplicação**, clique em **Adicionar uma aplicação**e, em seguida, clique em **Adicionar uma aplicação para a minha organização utilizar**.

    ![o que pretende fazer?] (./media/active-directory-saas-concur-tutorial/IC700995.png "o que pretende fazer?")

5.  Na **caixa de pesquisa**, escreva **Concur**.

    ![Galeria de aplicação] (./media/active-directory-saas-concur-tutorial/IC721727.png "Galeria de aplicação")

6.  No painel de resultados, selecione **Concur**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Coincidirem] (./media/active-directory-saas-concur-tutorial/IC721728.png "Coincidirem")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Concur com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

>[AZURE.NOTE] A configuração da sua subscrição Concur para federados SSO através do SAML é uma tarefa em separado, que terá de contactar o Concur para efetuar.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Concur **, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-concur-tutorial/IC769767.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Concur** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-concur-tutorial/IC769768.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Coincidirem URL início de sessão** , escreva o seu inquilino concur início de sessão no URL e, em seguida, clique em **seguinte**: 

    ![Configurar iniciar sessão no URL] (./media/active-directory-saas-concur-tutorial/IC769769.png "Configurar iniciar sessão no URL")

4.  Na página **Configurar serviço single sign-on na Concur** , execute os passos seguintes.

    ![Configurar iniciar sessão no URL] (./media/active-directory-saas-concur-tutorial/IC769770.png "Configurar iniciar sessão no URL")

    1.  Clique em transferir os metadados e seguro, em seguida, o ficheiro de dados para o seu computador.
    2.  Contacte a equipa de suporte Concur para configurar o SSO do seu inquilino.
    3.  Selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .  

    >[AZURE.NOTE] A configuração da sua subscrição Concur para federados SSO através do SAML é uma tarefa em separado, que terá de contactar o Concur para efetuar.

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Objectivo de nesta secção é como ativar o aprovisionamento de contas de utilizador do Active Directory para Concur de destaque.

Para ativar as aplicações no serviço de despesas, existem tem de ser a configuração inicial e a utilização de um perfil de administrador de serviços Web. Basta não adicione a função de administrador WS ao seu perfil de administrador existente que utilizar para as funções administrativas T & R.

Coincidirem consultores ou o administrador do cliente tem de criar um perfil de administrador do serviço Web distintos e o administrador do cliente tem de utilizar este perfil para as funções de administrador de serviços Web (por exemplo, permitindo-aplicações). Estes perfis devem ser mantidos separadas do diário T & "e" administrador perfil o administrador de cliente (o perfil de administrador T & "e" não deve ter a função WSAdmin atribuída).

Quando cria o perfil a ser utilizado para ativar a aplicação, introduza o nome do administrador de cliente para os campos de perfil de utilizador. Isto vai atribuir propriedade ao perfil. Quando estiver criado o perfil (is), o cliente tem de iniciar sessão com a este perfil para clique no botão "*Ativar*" para uma aplicação do parceiro no menu de serviços Web.

Pelos seguintes motivos, esta ação não deve ser efectuada com o perfil que utilizam para administração T & r normal.

1.  O cliente tem de ser que clica em "*Sim*" na janela de diálogo que é apresentada depois de uma aplicação está ativada. Esta, clique em reconhece que o cliente é disposto para a aplicação de parceiro aceder aos respetivos dados, para que o utilizador ou o parceiro não é possível clicar no botão Sim.
2.  Se um administrador de cliente tiver ativado a uma aplicação utilizando o administrador T & "e" perfil deixa a empresa (que resulta no perfil que está a ser inactivado), de aplicações activadas utilizando a que o perfil não irá funcionar até que a aplicação esteja ativada com outro perfil de administrador WS ativo. Este é o motivo pelo qual são suposto para criar distintos WS administração perfis.
3.  Se um administrador sair da empresa, o nome associado ao perfil WS administração pode ser alterado para o administrador de substituição, se pretender sem que afetam que a aplicação ativada, pois não necessita desse perfil inactivados

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Concur** .

2.  No menu de **Administração** , selecione **Serviços Web**.

    ![Concur inquilino] (./media/active-directory-saas-concur-tutorial/IC721729.png "Concur inquilino")

3.  No lado esquerdo, a partir do painel de **Serviços Web** , selecione **Ativar a aplicação de parceiro**.

    ![Ativar a aplicação de parceiro] (./media/active-directory-saas-concur-tutorial/IC721730.png "Ativar a aplicação de parceiro")

4.  A partir da lista de **Ativar a aplicação** , selecione o **Azure Active Directory**e, em seguida, clique em **Ativar**.

    ![Microsoft Azure Active Directory] (./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Clique em **Sim** para fechar a caixa de diálogo **Confirmar a ação** .

    ![Confirmar a ação] (./media/active-directory-saas-concur-tutorial/IC721732.png "Confirmar a ação")

6.  No portal do Azure clássico, selecione **Concur** a partir da lista de aplicações para abrir a página de diálogo **Concur** .

7.  Para abrir a página de diálogo **Configurar aprovisionamento de utilizador** , clique em **Configurar aprovisionamento de utilizador**.

8.  Introduza o nome de utilizador e a palavra-passe do administrador do Concur e, em seguida, clique em **seguinte**.

9.  Para concluir a configuração, na página de **confirmação** , clique no botão **concluída** .

Agora pode criar uma conta de teste, espere 10 minutos e certifique-se de que a conta foi sincronizada para Concur.
##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>Para atribuir utilizadores a Concur, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Concur **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-concur-tutorial/IC769771.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-concur-tutorial/IC767830.png "Sim")

Agora que deve esperar 10 minutos e certifique-se de que a conta foi sincronizada para Concur.

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
