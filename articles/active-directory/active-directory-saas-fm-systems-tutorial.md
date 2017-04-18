<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com FM: sistemas | Microsoft Azure" 
    description="Saiba como utilizar FM: sistemas com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-fm-systems"></a>Tutorial: Integração do Azure Active Directory com FM: sistemas
  
Este tutorial objectivo Mostrar a integração do Azure e FM:Systems.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um FM:Systems serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a FM:Systems poderão para o início de sessão único para a aplicação no site da sua empresa FM:Systems (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para FM:Systems
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Cenário")
##<a name="enabling-the-application-integration-for-fmsystems"></a>Ativar a integração da aplicação para FM:Systems
  
Objectivo de nesta secção é como ativar a integração da aplicação para FM:Systems de destaque.

###<a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Para ativar a integração da aplicação para FM:Systems, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-fm-systems-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **FM:Systems**.

    ![Galeria de aplicação] (./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Galeria de aplicação")

7.  No painel de resultados, selecione **FM:Systems**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![FM: sistemas] (./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: sistemas")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para FM:Systems com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **FM:Systems** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão FM:Systems** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurar o URL da aplicação")

    1.  Na caixa de texto **FM:Systems início de sessão no URL** , escreva o seu FM:Systems **URL de responder** (por exemplo: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  

        >[AZURE.WARNING] Pode obter este valor a partir do seu FM: sistemas equipa de suporte.

    2.  Clique em **seguinte**

4.  Na página **Configurar serviço single sign-on na FM:Systems** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde os metadados no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurar o Single Sign-On")

5.  Enviar o ficheiro transferido metadados à sua FM: sistemas equipa de suporte.

    >[AZURE.NOTE] O FM: Equipa de suporte de sistemas tem de fazer a configuração de SSO real.
Irá obter uma notificação quando SSO tenha sido ativado para a sua subscrição.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no FM:Systems, tem de ser aprovisionadas para FM:Systems.  
No caso de FM:Systems, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Numa janela do browser web, inicie sessão no site da sua empresa FM:Systems como administrador.

2.  Aceda a **Administração do sistema \> Gerir segurança \> utilizadores \> lista de utilizadores**.

    ![Administração do sistema] (./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Administração do sistema")

3.  Clique em **Criar novo utilizador**.

    ![Criar novo utilizador] (./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Criar novo utilizador")

4.  Na secção **Criar utilizador** , execute os seguintes passos:

    ![Criar utilizador] (./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Criar utilizador")

    1.  Escreva o nome de utilizador, a palavra-passe e sua confirmação, o endereço de e-mail e o ID de empregado de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **seguinte**.

>[AZURE.NOTE] Pode utilizar quaisquer outros FM:Systems utilizador conta ferramentas para a criação ou APIs fornecida pela FM:Systems às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Para atribuir utilizadores a FM:Systems, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **FM:Systems **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).