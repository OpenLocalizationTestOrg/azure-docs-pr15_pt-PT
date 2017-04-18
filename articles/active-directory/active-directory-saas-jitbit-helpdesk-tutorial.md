<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com o suporte técnico de Jitbit | Microsoft Azure" 
    description="Saiba como utilizar o suporte técnico de Jitbit com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Integração do Azure Active Directory com o suporte técnico de Jitbit
  
Este tutorial objectivo Mostrar a integração do Azure e suporte técnico de Jitbit.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino do suporte técnico de Jitbit
  
Depois de concluir este tutorial, os utilizadores do Azure AD que tiver atribuído para o suporte técnico de Jitbit poderão para o início de sessão único para a aplicação no site da sua empresa suporte técnico de Jitbit (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o suporte técnico de Jitbit
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Cenário")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Ativar a integração de aplicação para o suporte técnico de Jitbit
  
Objectivo de nesta secção é como ativar a integração de aplicação para o suporte técnico de Jitbit de destaque.

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Para ativar a integração de aplicação para o suporte técnico de Jitbit, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva o **Suporte técnico de Jitbit**.

    ![Galeria de aplicação] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Suporte técnico de Jitbit**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![JitBit] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para suporte técnico de Jitbit com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque. .  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Para poder configurar o início de sessão único no seu inquilino do suporte técnico de Jitbit, tem de contactar o suporte técnico de Jitbit de suporte técnico para obter esta funcionalidade activada de pela primeira vez.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de **Suporte técnico de Jitbit** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão suporte técnico de Jitbit** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Jitbit suporte técnico URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. Jitbit.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on ao suporte técnico de Jitbit** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\Jitbit Helpdesk.cer**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa suporte técnico de Jitbit como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administração")

7.  Clique em **Definições gerais**.

    ![Utilizadores, empresas e permissões] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Utilizadores, empresas e permissões")

8.  Na secção de configuração de **definições de autenticação** , execute os seguintes passos:

    ![Definições de autenticação] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Definições de autenticação")

    1.  Selecione **Ativar SAML 2.0 única ao iniciar sessão no** iniciar sessão utilizando o único início de sessão (SSO) com **OneLogin**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on ao suporte técnico de Jitbit** , copie o valor de **fornecedor de serviço (SP) iniciados por ponto final** e, em seguida, colá-la a caixa de texto do **URL de ponto final** .
    3.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra o seu certificado codificado base-64, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado x. 509**
    5.  Clique em **Guardar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no suporte técnico de Jitbit, tem de ser aprovisionadas para o suporte técnico de Jitbit.  
No caso de suporte técnico do Jitbit, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino do **Suporte técnico de Jitbit** .

2.  No menu no canto superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administração")

3.  Clique em **utilizadores, empresas e permissões**.

    ![Utilizadores, empresas e permissões] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Utilizadores, empresas e permissões")

4.  Clique em **Adicionar utilizador**.

    ![Adicionar utilizador] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Adicionar utilizador")

5.  Na secção criar, escreva os dados da conta Azure AD pretende aprovisionar para as seguintes caixas de texto: **nome de utilizador**, **E-Mail**, **nome próprio**, **Apelido**

    ![Criar] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Criar")

6.  Clique em **Criar**.

>[AZURE.NOTE] Pode utilizar qualquer outras suporte técnico de Jitbit utilizador conta ferramentas para a criação ou APIs fornecida pela suporte técnico de Jitbit às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Para atribuir utilizadores ao suporte técnico de Jitbit, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de **Suporte técnico de Jitbit **aplicação, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).