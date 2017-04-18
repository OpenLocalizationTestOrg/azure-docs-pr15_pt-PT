<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Pagerduty | Microsoft Azure" 
    description="Saiba como utilizar Pagerduty com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Tutorial: Integração com o Azure Active Directory com Pagerduty
  
Este tutorial objectivo Mostrar a integração do Azure e Pagerduty.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Pagerduty
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Pagerduty poderão para o início de sessão único para a aplicação no site da sua empresa Pagerduty (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Pagerduty
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Cenário")
##<a name="enabling-the-application-integration-for-pagerduty"></a>Ativar a integração da aplicação para Pagerduty
  
Objectivo de nesta secção é como ativar a integração da aplicação para Pagerduty de destaque.

###<a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Para ativar a integração da aplicação para Pagerduty, execute os seguintes passos:

1.  No Portal de gestão do Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-pagerduty-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Pagerduty**.

    ![Galeria de aplicação] (./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Pagerduty**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![PagerDuty] (./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Pagerduty com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Pagerduty** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Pagerduty** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Pagerduty URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. Pagerduty.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o url do app] (./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurar o url do app")

4.  Na página **Configurar serviço single sign-on na Pagerduty** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Pagerduty como administrador.

6.  No menu no canto superior, clique em **Definições da conta**.

    ![Definições da conta] (./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Definições da conta")

7.  Clique em **serviço single sign-on**.

    ![O início de sessão único] (./media/active-directory-saas-pagerduty-tutorial/IC778536.png "O início de sessão único")

8.  Na página ativar Single Sign-on (SSO), execute os seguintes passos:

    ![Ativar o início de sessão único] (./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Ativar o início de sessão único")

    1.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado x. 509**
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Pagerduty** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Pagerduty** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .
    5.  Selecione **Ativar o Single Sign-on**.
    6.  Clique em **Guardar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Pagerduty, tem de ser aprovisionadas para Pagerduty.  
No caso de Pagerduty, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Pagerduty** .

2.  No menu no canto superior, clique em **utilizadores**.

3.  Clique em **Adicionar utilizadores**.

    ![Adicionar utilizadores] (./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Adicionar utilizadores")

4.  Na caixa de diálogo **convidar a sua equipa** , escreva o **primeiro e último nome** e o endereço de **E-Mail** do utilizador do Azure AD que pretende aprovisionar, clique em **Adicionar**e, em seguida, clique em **Enviar convida**.

    ![Convidar a sua equipa] (./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Convidar a sua equipa")

    >[AZURE.NOTE] Todos os utilizadores adicionados irão receber um convite para criar uma conta de PagerDuty.

>[AZURE.NOTE] Pode utilizar quaisquer outros Pagerduty utilizador conta ferramentas para a criação ou APIs fornecida pela Pagerduty às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Para atribuir utilizadores a Pagerduty, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Pagerduty **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).