<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com folga | Microsoft Azure" 
    description="Saiba como utilizar a folga com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>Tutorial: Integração do Azure Active Directory com margem
  
Este tutorial objectivo Mostrar a integração do Azure e margem.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Uma margem serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a margem poderão para o início de sessão único para a aplicação no site da sua empresa folga (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para a margem
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-slack-tutorial/IC794980.png "Cenário")

##<a name="enabling-the-application-integration-for-slack"></a>Ativar a integração da aplicação para a margem
  
Objectivo de nesta secção é como ativar a integração da aplicação para a folga de destaque.

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>Para ativar a integração da aplicação para a margem, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-slack-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-slack-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-slack-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-slack-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **margem**.

    ![Galeria de aplicação] (./media/active-directory-saas-slack-tutorial/IC794981.png "Galeria de aplicação")

7.  No painel de resultados, selecione a **margem**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Cenário] (./media/active-directory-saas-slack-tutorial/IC796925.png "Cenário")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para folga com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **margem** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794982.png "Configurar o Single Sign-On")

2.  Na página **como pretende que como os utilizadores para iniciar sessão margem** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794983.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Folga de início de sessão no URL** , escreva o URL do seu inquilino folga (por exemplo: "*https://azuread.slack.com*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-slack-tutorial/IC794984.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na margem** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794985.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa folga como administrador.

6.  Aceda a **para a Microsoft Azure AD \> definições de equipa**.

    ![Definições de equipa] (./media/active-directory-saas-slack-tutorial/IC794986.png "Definições de equipa")

7.  Na secção **Definições de equipa** , clique no separador de **autenticação** e, em seguida, clique em **Alterar definições**.

    ![Definições de equipa] (./media/active-directory-saas-slack-tutorial/IC794987.png "Definições de equipa")

8.  Na caixa de diálogo **Definições de autenticação SAML** , execute os seguintes passos:

    ![Definições de SAML] (./media/active-directory-saas-slack-tutorial/IC794988.png "Definições de SAML")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na margem** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **SAML 2.0 ponto final de protocolo HTTP ()** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na margem** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **Emissor do fornecedor de identidade** .
    3.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.
    
        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado pública** .
    5.  Desmarque a opção **Permitir que os utilizadores para alterar o respetivo endereço de e-mail**.
    6.  Selecione **Permitir que os utilizadores para escolher o seu próprio nome de utilizador**.
    7.  Como a **autenticação para a sua equipa deve ser utilizada por**, selecione **é opcional**.
    8.  Clique em **Guardar a configuração**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-slack-tutorial/IC794989.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no margem, tem de ser aprovisionadas na margem.
  
Não existe nenhum item ação para configurar o aprovisionamento de utilizador para a margem.  
Quando um utilizador atribuído tenta que inicie sessão no margem, uma conta de folga é automaticamente criada se for necessário.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>Para atribuir utilizadores a margem, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação de **margem **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-slack-tutorial/IC794990.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-slack-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).