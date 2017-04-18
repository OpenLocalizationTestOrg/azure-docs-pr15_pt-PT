<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com TeamSeer | Microsoft Azure" 
    description="Saiba como utilizar TeamSeer com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: Integração do Azure Active Directory com TeamSeer
  
Este tutorial objectivo Mostrar a integração do Azure e TeamSeer.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino TeamSeer
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a TeamSeer poderão para o início de sessão único para a aplicação no site da sua empresa TeamSeer (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para TeamSeer
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-teamseer-tutorial/IC789618.png "Cenário")

##<a name="enabling-the-application-integration-for-teamseer"></a>Ativar a integração da aplicação para TeamSeer
  
Objectivo de nesta secção é como ativar a integração da aplicação para TeamSeer de destaque.

###<a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Para ativar a integração da aplicação para TeamSeer, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-teamseer-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-teamseer-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-teamseer-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-teamseer-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **TeamSeer**.

    ![Galeria de aplicação] (./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galeria de aplicação")

7.  No painel de resultados, selecione **TeamSeer**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![TeamSeer] (./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para TeamSeer com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **TeamSeer** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão TeamSeer** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **TeamSeer URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*http://www.teamseer.com/companyid*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na TeamSeer** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa TeamSeer como administrador.

6.  Aceda a **HR Admin**.

    ![Administrador de recursos humanos] (./media/active-directory-saas-teamseer-tutorial/IC789634.png "Administrador de recursos humanos")

7.  Clique em **configuração**.

    ![Programa de configuração] (./media/active-directory-saas-teamseer-tutorial/IC789635.png "Programa de configuração")

8.  Clique em **Configurar SAML fornecedor detalhes**.

    ![Definições de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789636.png "Definições de SAML")

9.  Na secção de detalhes do fornecedor de SAML, execute os seguintes passos:

    ![Definições de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789637.png "Definições de SAML")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na TeamSeer** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto do **URL** .
    2.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado pública IdP** .

10. Para concluir a configuração de fornecedor SAML, execute os seguintes passos:

    ![Definições de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789638.png "Definições de SAML")

    1.  Em **Testar endereços de E-Mail**, escreva o endereço de e-mail do utilizador de teste.
    2.  Na caixa de texto **emissor** , escreva o URL de emissor do fornecedor de serviços.
    3.  Clique em **Guardar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no TeamSeer, tem de ser aprovisionadas para ShiftPlanning.  
No caso de TeamSeer, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **TeamSeer** como administrador.

2.  Execute os seguintes passos:

    ![Administrador de recursos humanos] (./media/active-directory-saas-teamseer-tutorial/IC789640.png "Administrador de recursos humanos")

    1.  Aceda a **HR administração \> utilizadores**.
    2.  Clique em **executar o Assistente de novo utilizador**.

3.  Na secção de **Detalhes do utilizador** , execute os seguintes passos:

    ![Detalhes do utilizador] (./media/active-directory-saas-teamseer-tutorial/IC789641.png "Detalhes do utilizador")

    1.  Escreva o **nome próprio**, **Apelido**, **nome de utilizador (endereço de E-Mail)** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **seguinte**.

4.  Siga as instruções de ecrã para adicionar um novo utilizador e clique em **Concluir**.

>[AZURE.NOTE] Pode utilizar quaisquer outros TeamSeer utilizador conta ferramentas para a criação ou APIs fornecida pela TeamSeer aprovisionamento de contas de utilizador do Azure AD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Para atribuir utilizadores a TeamSeer, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **TeamSeer **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-teamseer-tutorial/IC789642.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-teamseer-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).