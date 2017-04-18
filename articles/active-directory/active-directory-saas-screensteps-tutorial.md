<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com ScreenSteps | Microsoft Azure" 
    description="Saiba como utilizar ScreenSteps com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integração do Azure Active Directory com ScreenSteps
  
Este tutorial objectivo Mostrar a integração do Azure e ScreenSteps.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino ScreenSteps
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a ScreenSteps poderão para o início de sessão único para a aplicação no site da sua empresa ScreenSteps (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para ScreenSteps
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-screensteps-tutorial/IC778516.png "Cenário")
##<a name="enabling-the-application-integration-for-screensteps"></a>Ativar a integração da aplicação para ScreenSteps
  
Objectivo de nesta secção é como ativar a integração da aplicação para ScreenSteps de destaque.

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>Para ativar a integração da aplicação para ScreenSteps, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-screensteps-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-screensteps-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-screensteps-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-screensteps-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **ScreenSteps**.

    ![Galeria de aplicação] (./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galeria de aplicação")

7.  No painel de resultados, selecione **ScreenSteps**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ScreenSteps] (./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para ScreenSteps com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **ScreenSteps** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão ScreenSteps** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **ScreenSteps URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. ScreenSteps.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na ScreenSteps** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa ScreenSteps como administrador.

6.  Clique em **Gestão de conta**.

    ![Gestão de contas] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestão de contas")

7.  Clique em **autenticação remota**.

    ![Autenticação remota] (./media/active-directory-saas-screensteps-tutorial/IC778524.png "Autenticação remota")

8.  Clique em **ponto final de autenticação de criar**.

    ![Autenticação remota] (./media/active-directory-saas-screensteps-tutorial/IC778525.png "Autenticação remota")

9.  Na secção **criar um ponto final de autenticação** , execute os seguintes passos:

    ![Criar um ponto final de autenticação] (./media/active-directory-saas-screensteps-tutorial/IC778526.png "Criar um ponto final de autenticação")

    1.  Na caixa de texto **título** , escreva um título.
    2.  A partir da lista de **modo** , selecione **SAML**.
    3.  Clique em **Criar**.

10. Na secção de **Ponto final de autenticação remoto** , execute os seguintes passos:

    ![Ponto final de autenticação remota] (./media/active-directory-saas-screensteps-tutorial/IC778527.png "Ponto final de autenticação remota")

    1.  No portal clássico Azure, na página **Configurar serviço single sign-on na ScreenSteps** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto de **Início de sessão de URL remoto** .
    2.  No portal clássico Azure, na página **Configurar serviço single sign-on na ScreenSteps** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **termine a sessão URL** .
    3.  Clique em **Escolher um ficheiro**e, em seguida, carregue o certificado transferido.
    4.  Clique em **Atualizar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no **ScreenSteps**, tem de ser aprovisionadas para **ScreenSteps**.  
No caso de **ScreenSteps**, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Aprovisionamento de uma conta de utilizador para ScreenSteps, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **ScreenSteps** .

2.  Clique em **Gestão de conta**.

    ![Gestão de contas] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestão de contas")

3.  Clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-screensteps-tutorial/IC778544.png "Utilizadores")

4.  Clique em **criar um utilizador**.

    ![Todos os utilizadores] (./media/active-directory-saas-screensteps-tutorial/IC778545.png "Todos os utilizadores")

5.  A partir da lista de **Função de utilizador** , selecione uma função para o utilizador.

6.  Na secção de função de utilizador, escreva o**"nome próprio**, **Apelido**, **E-Mail**, **início de sessão**, **palavra-passe** e **Palavra-passe confirmação"**de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.

    ![Novo utilizador] (./media/active-directory-saas-screensteps-tutorial/IC778546.png "Novo utilizador")

7.  Na secção grupos, selecione o **Grupo de autenticação (SAML)**e, em seguida, clique em **Criar utilizador**.

    ![Grupos] (./media/active-directory-saas-screensteps-tutorial/IC778547.png "Grupos")

>[AZURE.NOTE]Pode utilizar quaisquer outros ScreenSteps utilizador conta ferramentas para a criação ou APIs fornecida pela ScreenSteps às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Para atribuir utilizadores a ScreenSteps, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **ScreenSteps **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-screensteps-tutorial/IC773094.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-screensteps-tutorial/IC778548.png "Atribuir aos utilizadores")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).