<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com TalentLMS | Microsoft Azure" 
    description="Saiba como utilizar TalentLMS com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração com o Azure Active Directory com TalentLMS
  
Este tutorial objectivo Mostrar a integração do Azure e TalentLMS.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino TalentLMS
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a TalentLMS poderão para o início de sessão único para a aplicação no site da sua empresa TalentLMS (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para TalentLMS
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-talentlms-tutorial/IC777289.png "Cenário")

##<a name="enabling-the-application-integration-for-talentlms"></a>Ativar a integração da aplicação para TalentLMS
  
Objectivo de nesta secção é como ativar a integração da aplicação para TalentLMS de destaque.

###<a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>Para ativar a integração da aplicação para TalentLMS, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-talentlms-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-talentlms-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-talentlms-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-talentlms-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **TalentLMS**.

    ![Galeria de aplicação] (./media/active-directory-saas-talentlms-tutorial/IC777290.png "Galeria de aplicação")

7.  No painel de resultados, selecione **TalentLMS**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![TalentLMS] (./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para TalentLMS com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque. .  
Configurar o serviço single sign-on para TalentLMS requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **TalentLMS** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão TalentLMS** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **TalentLMS URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. TalentLMSapp.com*"e, em seguida, clique em **seguinte**.

    ![Ao iniciar sessão no URL] (./media/active-directory-saas-talentlms-tutorial/IC777294.png "Ao iniciar sessão no URL")

4.  Na página **Configurar serviço single sign-on na TalentLMS** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\TalentLMS.cer**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa TalentLMS como administrador.

6.  Na secção **& definições da conta** , clique no separador de **utilizadores** .

    ![& Definições da conta] (./media/active-directory-saas-talentlms-tutorial/IC777296.png "& Definições da conta")

7.  Clique **Single Sign-On (SSO)**,

8.  Na secção Single Sign-On, execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")

    1.  Na lista **tipo de integração de SSO** , selecione **SAML 2.0**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na TalentLMS** , copie o valor de **ID do fornecedor de identidade** e, em seguida, colá-la a caixa de texto do **fornecedor de identidades (IdP)** .
    3.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado** .

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na TalentLMS** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto de **início de sessão de URL remoto** .
    5.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na TalentLMS** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **de URL remoto terminar sessão** .
    6.  Na caixa de texto **TargetedID** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
    7.  Na caixa de texto **nome próprio** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    8.  Na caixa de texto **Apelido** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    9.  Na caixa de **correio eletrónico** do texto, escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
    10. Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no TalentLMS, tem de ser aprovisionadas para TalentLMS.  
No caso de TalentLMS, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **TalentLMS** .

2.  Clique em **utilizadores**e, em seguida, clique em **Adicionar utilizador**.

3.  Na página de diálogo **Adicionar utilizador** , execute os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-talentlms-tutorial/IC777299.png "Adicionar utilizador")

    1.  Escreva os valores de atributo relacionado da conta de utilizador do Azure AD as seguintes caixas de texto: **nome próprio**, **Apelido**, **endereço de E-Mail**.
    2.  Clique em **Adicionar utilizador**.

>[AZURE.NOTE] Pode utilizar quaisquer outros TalentLMS utilizador conta ferramentas para a criação ou APIs fornecida pela TalentLMS às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Para atribuir utilizadores a TalentLMS, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **TalentLMS **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-talentlms-tutorial/IC777300.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-talentlms-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).