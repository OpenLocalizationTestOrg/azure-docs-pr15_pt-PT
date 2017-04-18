<properties 
    pageTitle="Tutorial: Azure integração do Active Directory com xMatters sob | Microsoft Azure"
    description="Saiba como utilizar xMatters sob com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Azure integração do Active Directory com xMatters pedido
  
Este tutorial objectivo Mostrar a integração do Azure e xMatters pedido. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino do pedido de xMatters
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a xMatters sob poderão para o início de sessão único para a aplicação em xMatters sob site da sua empresa (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para xMatters pedido
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Cenário")

##<a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Ativar a integração da aplicação para xMatters pedido
  
O objectivo desta secção é destacar como ativar a integração da aplicação para xMatters pedido.

###<a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Para ativar a integração da aplicação para xMatters pedido, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **xMatters pedido**.

    ![Galeria de aplicação] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galeria de aplicação")

7.  No painel de resultados, selecione **XMatters pedido**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![xMatters pedido] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters pedido")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para pedido de XMatters com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **XMatters pedido** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão pedido de XMatters** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurar o URL da aplicação")

    um. Na caixa de texto **XMatters sob URL início de sessão** , escreva o seu URL utilizando o padrão seguinte:`https://<tenant-name>.XMattersOnDemandapp.com`

    b. Clique em **seguinte**.


4.  Na página **Configurar serviço single sign-on no pedido de XMatters** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Tem de reencaminhar o certificado para a equipa de suporte xMatters. O certificado tem de ser carregado pela equipa de suporte de xMatters antes de a pode finalizar a configuração de início de sessão único.

    ![Configurar única ao iniciar sessão no] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configurar única ao iniciar sessão no")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa XMatters sob como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Admin**e, em seguida, clique em **Detalhes da empresa** na barra de navegação no lado esquerdo.

    ![Admin] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7.  Na página **Configuração de SAML** , execute os seguintes passos:

    ![Configuração do SAML] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configuração do SAML")

    1.  Selecione **Ativar SAML**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on no pedido de XMatters** , copie o valor de **ID do fornecedor de identidade** e, em seguida, colá-la a caixa de texto de **ID do fornecedor de identidade** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on no pedido de XMatters** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto de **Início de sessão único no URL** .
    4.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on no pedido de XMatters** , copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto do **URL de termine a sessão único** .
    5.  Na página Detalhes da empresa, na parte superior, clique em **Guardar alterações**.
        ![Detalhes da empresa] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Detalhes da empresa")

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar única ao iniciar sessão no] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configurar única ao iniciar sessão no")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no pedido de XMatters, tem de ser aprovisionadas para XMatters pedido.  
No caso de pedido de XMatters, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino do **Pedido de XMatters** .

2.  Clique no separador de **utilizadores** .

3.  Clique em **Adicionar utilizador**.

    ![Utilizadores] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Utilizadores")

4.  Selecione **ativo**.

5.  Na secção de **Adicionar um utilizador** , execute os seguintes passos:

    ![Adicionar um utilizador] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Adicionar um utilizador")

    1.  Introduza o **ID de utilizador**, **nome próprio**, **Apelido**, **Site** de uma conta AAD válida que pretende aprovisionar.
    2.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros XMatters sob utilizador conta ferramentas para a criação ou APIs fornecida pela XMatters sob às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Para atribuir utilizadores a pedido XMatters, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **XMatters pedido **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).