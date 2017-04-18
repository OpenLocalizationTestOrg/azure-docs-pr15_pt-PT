<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Primavera CM | Microsoft Azure" 
    description="Saiba como utilizar Primavera CM com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>Tutorial: Integração do Azure Active Directory com Primavera CM
  
Este tutorial objectivo Mostrar como configurar o serviço single sign-on entre o Azure Active Directory e SpringCM.
  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um SpringCM serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure Active Directory que tiver atribuído a SpringCM poderão para serviço single sign-on utilizando o painel de acesso AAD.

1.  Ativar a integração da aplicação para SpringCM
2.  Configurar o Single Sign-On
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Cenário")

##<a name="enabling-the-application-integration-for-springcm"></a>Ativar a integração da aplicação para SpringCM
  
Objectivo de nesta secção é como ativar a integração da aplicação para SpringCM de destaque.

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>Para ativar a integração da aplicação para SpringCM, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-spring-cm-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **SpringCM**.

    ![Galeria de aplicação] (./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galeria de aplicação")

7.  No painel de resultados, selecione **SpringCM**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![SpringCM] (./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>Configurar o Single Sign-On
  
Esta secção descreve como permitir que os utilizadores autenticar SpringCM com a sua conta no Azure Active Directory, utilizando a Federação com base no protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **SpringCM** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar single Sign-On] (./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configurar single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão SpringCM** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar single Sign-On] (./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configurar single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **SpringCM início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação SpringCM e, em seguida, clique em **seguinte**. 

    O URL de aplicação é o URL de inquilino SpringCM (por exemplo: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![Configurar o URL da aplicação] (./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na SpringCM** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente para o seu computador.

    ![Configurar o início único] (./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurar o início único")

5.  Numa janela do browser web diferente, inicie sessão site da sua empresa **SpringCM** como administrador.

6.  No menu no canto superior, clique em **Ir para**, clique em **Preferências**e, em seguida, na secção **Preferências da conta** , clique em **SAML SSO**.

    ![SAML SSO] (./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  Na secção de configuração de fornecedor de identidade, execute os seguintes passos:

    ![Configuração de fornecedor de identidade] (./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configuração de fornecedor de identidade")

    1.  Para carregar o certificado do Azure Active Directory transferido, clique em **Selecionar certificado emissor** ou **Alterar emissor certificado**.
    2.  No portal clássico Azure, na página **Configurar serviço single sign-on na SpringCM** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor** .
    3.  No portal clássico Azure, na página **Configurar serviço single sign-on na SpringCM** , copie o valor de **Singel Sign-On URL do serviço** e, em seguida, colá-la a caixa de texto do **Fornecedor de serviço (SP) iniciados por ponto final** .
    4.  Como **SAML com permissão para**, selecione **Ativar**.
    5.  Clique em **Guardar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurar o início único")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure Active Directory que inicie sessão no SpringCM, tem de ser aprovisionadas para SpringCM.  
No caso de SpringCM, aprovisionamento é uma tarefa manual.

>[AZURE.NOTE] Para obter mais detalhes, consulte o artigo [criar e editar um utilizador SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Aprovisionamento de uma conta de utilizador para SpringCM, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **SpringCM** como administrador.

2.  Clique em **Ir para**e, em seguida, clique em **Livro de endereços**.

    ![Criar utilizador] (./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Criar utilizador")

3.  Clique em **Criar utilizador**.

4.  Selecione uma **função de utilizador**.

5.  Selecione **Enviar E-mails de ativação**.

6.  Escreva o nome próprio, apelido e endereço de e-mail de uma conta de utilizador do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.

7.  Adicione o utilizador a um **grupo de segurança**.

8.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros SpringCM utilizador conta ferramentas para a criação ou APIs fornecida pela SpringCM às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Para atribuir utilizadores a SpringCM, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **SpringCM** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).




