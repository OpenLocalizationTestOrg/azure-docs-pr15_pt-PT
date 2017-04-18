<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com InsideView | Microsoft Azure" 
    description="Saiba como utilizar InsideView com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Integração do Azure Active Directory com InsideView
  
Este tutorial objectivo Mostrar a integração do Azure e InsideView.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino InsideView
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a InsideView poderão para o início de sessão único para a aplicação no site da sua empresa InsideView (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para InsideView
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-insideview-tutorial/IC794128.png "Cenário")
##<a name="enabling-the-application-integration-for-insideview"></a>Ativar a integração da aplicação para InsideView
  
Objectivo de nesta secção é como ativar a integração da aplicação para InsideView de destaque.

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Para ativar a integração da aplicação para InsideView, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-insideview-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-insideview-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-insideview-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-insideview-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **InsideView**.

    ![Galeria de aplicação] (./media/active-directory-saas-insideview-tutorial/IC794129.png "Galeria de aplicação")

7.  No painel de resultados, selecione **InsideView**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![InsideView] (./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para InsideView com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **InsideView** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurar o início único")

2.  Na página **como pretende utilizadores a iniciar sessão InsideView** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o início único] (./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurar o início único")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **InsideView resposta URL** , escreva o URL da sua InsideView SSO (por exemplo: `https://my.insideview.com/iv/<STS Name>/login.iv`) e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na InsideView** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início único] (./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurar o início único")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa InsideView como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Admin**, **SingleSignOn definições**e, em seguida, clique em **Adicionar SAML**.

    ![SAML Single Sign-On definições] (./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign-On definições")

7.  Na secção de **Adicionar um novo SAML** , execute os seguintes passos:

    ![Adicionar um novo SAML] (./media/active-directory-saas-insideview-tutorial/IC794136.png "Adicionar um novo SAML")

    1.  Na caixa de texto **STS nome** , escreva um nome para a configuração.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na InsideView** , copie o valor de **Ponto final de iniciados por do fornecedor de serviço (SP)** e, em seguida, colá-la a caixa de texto de **Ponto final de Unsolicated WS/SamlP-Fed** .
    3.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado STS**
    5.  Na caixa de texto **Crm mapeamento de Id de utilizador** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    6.  Na caixa de texto **Crm mapeamento de correio eletrónico** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Na caixa de texto **Crm mapeamento de nome próprio** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    8.  Na caixa de texto **Crm apelido mapeamento** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9.  Clique em **Guardar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurar o início único")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no InsideView, tem de ser aprovisionadas para InsideView.  
No caso de InsideView, aprovisionamento é uma tarefa manual.
  
Para obter os utilizadores ou contactos criados no InsideView, contacte o seu Gestor de sucesso do cliente ou enviar e-mail**support@insideview.com**

>[AZURE.NOTE] Pode utilizar quaisquer outros InsideView utilizador conta ferramentas para a criação ou APIs fornecida pela InsideView aprovisionamento de contas de utilizador do Azure AD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Para atribuir utilizadores a InsideView, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **InsideView **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-insideview-tutorial/IC794138.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-insideview-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).