<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com o Zoom | Microsoft Azure" 
    description="Saiba como utilizar Zoom com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zoom"></a>Tutorial: Integração do Azure Active Directory com o Zoom
  
Este tutorial objectivo Mostrar a integração do Azure e Zoom.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino do Zoom
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Zoom poderão único início de sessão para a aplicação no site da sua empresa Zoom (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação de Zoom
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-zoom-tutorial/IC784693.png "Cenário")

##<a name="enabling-the-application-integration-for-zoom"></a>Ativar a integração da aplicação de Zoom
  
Objectivo de nesta secção é como ativar a integração da aplicação para Zoom de destaque.

###<a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Para ativar a integração da aplicação de Zoom, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-zoom-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-zoom-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-zoom-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-zoom-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva o **Zoom**.

    ![Galeria de aplicação] (./media/active-directory-saas-zoom-tutorial/IC784694.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Aplicar Zoom**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Ampliar] (./media/active-directory-saas-zoom-tutorial/IC784695.png "Ampliar")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para aplicar Zoom com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Zoom** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784696.png "Configurar serviço single sign-on")

2.  Na página **como pretende que como os utilizadores para iniciar sessão Zoom** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784697.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Zoom URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*http://company.zoom.us*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-zoom-tutorial/IC784698.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on em Zoom** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784699.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Zoom como administrador.

6.  Clique no separador **Single Sign-On** .

    ![O início de sessão único] (./media/active-directory-saas-zoom-tutorial/IC784700.png "O início de sessão único")

7.  Clique no separador **Controlo de segurança** e, em seguida, aceda às definições do **Single Sign-On** .

8.  Na secção Single Sign-On, execute os seguintes passos:

    ![O início de sessão único] (./media/active-directory-saas-zoom-tutorial/IC784701.png "O início de sessão único")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Zoom** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto de **início de sessão no URL da página** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Zoom** , copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto do **URL da página terminar sessão** .
    3.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **certificado de fornecedor de identidade**
    5.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Zoom** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor** .
    6.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zoom-tutorial/IC784702.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Zoom, tem de ser aprovisionadas para o Zoom.  
No caso de Zoom, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu site de empresa **Zoom** como administrador.

2.  Clique no separador **Gestão de contas** e, em seguida, clique em **Gestão de utilizadores**.

3.  Na secção Gestão de utilizadores, clique em **Adicionar utilizadores**.

    ![Gestão de utilizadores] (./media/active-directory-saas-zoom-tutorial/IC784703.png "Gestão de utilizadores")

4.  Na página **Adicionar utilizadores** , execute os seguintes passos:

    ![Adicionar utilizadores] (./media/active-directory-saas-zoom-tutorial/IC784704.png "Adicionar utilizadores")

    1.  Com o **Tipo de utilizador**, selecione **básica**.
    2.  Na caixa de texto **mensagens de correio eletrónico** , escreva o endereço de e-mail de uma conta AAD válida que pretende aprovisionar.
    3.  Clique em **Adicionar**.

>[AZURE.NOTE] Pode utilizar qualquer outras utilizador conta criação ferramentas de Zoom ou APIs fornecida pela Zoom para aprovisionar AAD contas de utilizador.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Para atribuir utilizadores a Zoom, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Zoom **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-zoom-tutorial/IC784705.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-zoom-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).