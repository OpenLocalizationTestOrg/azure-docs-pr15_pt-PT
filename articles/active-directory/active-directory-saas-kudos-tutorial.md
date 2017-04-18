<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Kudos | Microsoft Azure" 
    description="Saiba como utilizar Kudos com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-kudos"></a>Tutorial: Integração do Azure Active Directory com Kudos
  
Este tutorial objectivo Mostrar a integração do Azure e Kudos.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Kudos
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Kudos poderão para o início de sessão único para a aplicação no site da sua empresa Kudos (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Kudos
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-kudos-tutorial/IC787799.png "Cenário")
##<a name="enabling-the-application-integration-for-kudos"></a>Ativar a integração da aplicação para Kudos
  
Objectivo de nesta secção é como ativar a integração da aplicação para Kudos de destaque.

###<a name="to-enable-the-application-integration-for-kudos-perform-the-following-steps"></a>Para ativar a integração da aplicação para Kudos, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-kudos-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-kudos-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-kudos-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-kudos-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Kudos**.

    ![Galeria de aplicação] (./media/active-directory-saas-kudos-tutorial/IC787800.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Kudos**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Kudos] (./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Kudos com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Kudos** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787802.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Kudos** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787803.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Kudos início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*https://company.kudosnow.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Kudos** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787805.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Kudos como administrador.

6.  No menu no canto superior, clique em **Definições**.

    ![Definições] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Definições")

7.  Clique em **integrações \> SSO**.

8.  Na secção **SSO** , execute os seguintes passos:

    ![SSO] (./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Kudos** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto **ao iniciar sessão no URL** .
    2.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP]
        Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **certificado x. 509**
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Kudos** , copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto **Termine a sessão para URL** .
    5.  Na caixa de texto **Your Kudos URL** , escreva o nome da sua empresa.
    6.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-kudos-tutorial/IC787808.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Kudos, tem de ser aprovisionadas para Kudos.  
No caso de Kudos, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Kudos** como administrador.

2.  No menu no canto superior, clique em **Definições**.

    ![Definições] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Definições")

3.  Clique em **Admin do utilizador**.

4.  Clique no separador de **utilizadores** e, em seguida, clique em **Adicionar um utilizador**.

    ![Administrador de utilizadores] (./media/active-directory-saas-kudos-tutorial/IC787809.png "Administrador de utilizadores")

5.  Na secção de **Adicionar um utilizador** , execute os seguintes passos:

    ![Adicionar um utilizador] (./media/active-directory-saas-kudos-tutorial/IC787810.png "Adicionar um utilizador")

    1.  Escreva o **nome próprio**, **Apelido**, **E-Mail** e outros detalhes de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Criar utilizador**.

>[AZURE.NOTE]Pode utilizar quaisquer outros Kudos utilizador conta ferramentas para a criação ou APIs fornecida pela Kudos às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-kudos-perform-the-following-steps"></a>Para atribuir utilizadores a Kudos, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Kudos **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-kudos-tutorial/IC787811.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-kudos-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).