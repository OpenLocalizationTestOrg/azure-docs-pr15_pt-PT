<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Gigya | Microsoft Azure" 
    description="Saiba como utilizar Gigya com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: Integração do Azure Active Directory com Gigya
  
Este tutorial objectivo Mostrar a integração do Azure e Gigya.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Subscrição com capacidade de um Gigya single sign-on
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Gigya poderão para o início de sessão único para a aplicação no site da sua empresa Gigya (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Gigya
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Configurar o Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurar o Single Sign-On")
##<a name="enabling-the-application-integration-for-gigya"></a>Ativar a integração da aplicação para Gigya
  
Objectivo de nesta secção é como ativar a integração da aplicação para Gigya de destaque.

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Para ativar a integração da aplicação para Gigya, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-gigya-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-gigya-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-gigya-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-gigya-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Gigya**.

    ![Galeria de aplicação] (./media/active-directory-saas-gigya-tutorial/IC789513.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Gigya**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Gigya] (./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Gigya com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Gigya** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Gigya** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Gigya início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*http://company.gigya.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Gigya** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Gigya como administrador.

6.  Aceda a **definições \> SAML início de sessão**e, em seguida, clique no botão **Adicionar** .

    ![Início de sessão do SAML] (./media/active-directory-saas-gigya-tutorial/IC789532.png "Início de sessão do SAML")

7.  Na secção **SAML início de sessão** , execute os seguintes passos:

    ![Configuração do SAML] (./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuração do SAML")

    1.  Na caixa de texto **nome** , escreva um nome para a configuração.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Gigya** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Gigya** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto **Único início de sessão URL do serviço de** .
    4.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na Gigya** , copie o valor de **Formato de nome do identificador** e, em seguida, colá-la a caixa de texto de **Formato de nome do ID** .
    5.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado x. 509** .
    7.  Clique em **Guardar definições**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Gigya, tem de ser aprovisionadas para Gigya.  
No caso de Gigya, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Gigya** como administrador.

2.  Aceda a **Administração \> gerir utilizadores**e, em seguida, clique em **Convidar utilizadores**.

    ![Gerir utilizadores] (./media/active-directory-saas-gigya-tutorial/IC789535.png "Gerir utilizadores")

3.  Na caixa de diálogo Convidar utilizadores, execute os seguintes passos:

    ![Convidar utilizadores] (./media/active-directory-saas-gigya-tutorial/IC789536.png "Convidar utilizadores")

    1.  Na caixa de **correio eletrónico** do texto, escreva o alias de e-mail de uma conta do Azure Active Directory válida que pretende aprovisionar.
    2.  Clique em **Convidar utilizador**.
    
        >[AZURE.NOTE] Titular da conta Azure Active Directory receberão um e-mail que inclui uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Gigya utilizador conta ferramentas para a criação ou APIs fornecida pela Gigya às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Para atribuir utilizadores a Gigya, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Gigya **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-gigya-tutorial/IC789537.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-gigya-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).