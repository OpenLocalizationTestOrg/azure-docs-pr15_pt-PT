<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com SumoLogic | Microsoft Azure" 
    description="Saiba como utilizar SumoLogic com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: Integração com o Azure Active Directory com SumoLogic
  
Este tutorial objectivo Mostrar a integração do Azure e SumoLogic.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino SumoLogic
  
Depois de concluir este tutorial, os utilizadores do Azure AD ter atribuído a SumoLogicwill ser conseguir único início de sessão para a aplicação no seu SumoLogic da empresa site (sinal de fornecedor iniciados por serviço no) ou utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para SumoLogic
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-sumologic-tutorial/IC778549.png "Cenário")

##<a name="enabling-the-application-integration-for-sumologic"></a>Ativar a integração da aplicação para SumoLogic
  
Objectivo de nesta secção é como ativar a integração da aplicação para SumoLogic de destaque.

###<a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Para ativar a integração da aplicação para SumoLogic, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-sumologic-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-sumologic-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-sumologic-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-sumologic-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **sumologic**.

    ![Galeria de aplicação] (./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galeria de aplicação")

7.  No painel de resultados, selecione **SumoLogic**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![SumoLogic] (./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para SumoLogic com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 para o seu SumoLogictenant.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **SumoLogic** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778552.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão SumoLogic** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778553.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **SumoLogic URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. SumoLogic.com*"e, em seguida, clique em **seguinte**.

    ![Configurar aoo URL] (./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurar aoo URL")

4.  Na página **Configurar serviço single sign-on na SumoLogic** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778555.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa SumoLogic como administrador.

6.  Aceda a **Gerir \> segurança**.

    ![Gerir] (./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gerir")

7.  Clique em **SAML**.

    ![Definições de segurança global] (./media/active-directory-saas-sumologic-tutorial/IC778557.png "Definições de segurança global")

8.  Na lista **Selecione uma configuração ou crie um novo** , selecione **Azure AD**e, em seguida, clique em **Configurar**.

    ![Configurar SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurar SAML 2.0")

9.  Na caixa de diálogo **Configurar SAML 2.0** , execute os seguintes passos:

    ![Configurar SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurar SAML 2.0")

    1.  Na caixa de texto **Nome de configuração** , escreva **Azure AD**.
    2.  Selecione o **modo de depuração**.
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na SumoLogic** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor** .
    4.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na SumoLogic** , copie o valor de **URL de pedido de autenticação** e, em seguida, colá-la a caixa de texto **Authn pedidos URL** .
    5.  Crie um ficheiro de **Base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole o certificado de todo a caixa de texto de **Certificado x. 509** .
    7.  Como **Atributo de correio eletrónico**, selecione **utilizar SAML assunto**.
    8.  Selecione **SP iniciados por configuração de início de sessão**.
    9.  Na caixa de texto **Caminho de início de sessão** , escreva **Azure**.
    10. Clique em **Guardar**.

10. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na SumoLogic** , selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sumologic-tutorial/IC778560.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no SumoLogic, tem de ser aprovisionadas para SumoLogic.  
No caso de SumoLogic, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **SumoLogic** .

2.  Aceda a **Gerir \> utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-sumologic-tutorial/IC778561.png "Utilizadores")

3.  Clique em **Adicionar**.

    ![Utilizadores] (./media/active-directory-saas-sumologic-tutorial/IC778562.png "Utilizadores")

4.  Na caixa de diálogo **Novo utilizador** , efetue os seguintes passos:

    ![Novo utilizador] (./media/active-directory-saas-sumologic-tutorial/IC778563.png "Novo utilizador")

    1.  Escreva as informações relacionadas da conta Azure AD que pretende aprovisionar para caixas de texto **nome próprio**, **Apelido** e **E-Mail** .
    2.  Selecione uma função.
    3.  Como **Estado**, selecione **ativo**.
    4.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros SumoLogic utilizador conta ferramentas para a criação ou APIs fornecida pela SumoLogic às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Para atribuir utilizadores a SumoLogic, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **SumoLogic** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-sumologic-tutorial/IC778564.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-sumologic-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).