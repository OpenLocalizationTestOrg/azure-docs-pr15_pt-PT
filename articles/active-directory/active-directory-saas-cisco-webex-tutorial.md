<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Cisco Webex | Microsoft Azure" 
    description="Saiba como utilizar Cisco Webex com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: Integração com o Azure Active Directory com Cisco Webex

Este tutorial objectivo Mostrar a integração do Azure e Cisco Webex.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Cisco Webex

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Cisco Webex poderão para o início de sessão único para a aplicação no site da sua empresa Cisco Webex (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Cisco Webex
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Cenário")
##<a name="enabling-the-application-integration-for-cisco-webex"></a>Ativar a integração da aplicação para Cisco Webex

Objectivo de nesta secção é como ativar a integração da aplicação para Cisco Webex de destaque.

###<a name="to-enable-the-application-integration-for-cisco-webex-perform-the-following-steps"></a>Para ativar a integração da aplicação para Cisco Webex, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Cisco Webex**.

    ![Galeria de aplicação] (./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Cisco Webex**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Cisco Webex] (./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Cisco Webex com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Cisco Webex** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Cisco Webex** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurar o URL da aplicação")

    1.  Na caixa de texto **De início de sessão no URL** , escreva o seu URL do inquilino Cisco Webex (por exemplo: *http://contoso.webex.com*).
    2.  Na caixa de texto **Cisco Webex resposta URL** , escreva o **URL do Cisco Webex AssertionConsumerService** (por exemplo: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  Na página **Configurar serviço single sign-on na Cisco Webex** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Cisco Webex como administrador.

6.  No menu no canto superior, clique em **Administração de sites**.

    ![Administração de sites] (./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Administração de sites")

7.  Na secção **Gerir Site** , clique em **Configuração de SSO**.

    ![Configuração de SSO] (./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configuração de SSO")

8.  Na secção de configuração de SSO Web Federado, execute os seguintes passos:

    ![Federado SSO configuração] (./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federado SSO configuração")

    1.  A partir da lista de **Federação protocolo** , selecione **SAML 2.0**.
    2.  Crie um ficheiro de **Base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abra o seu certificado codificado base-64 no bloco de notas e, em seguida, copie o conteúdo da mesma.
    4.  Clique em **Importar SAML metadados**e, em seguida, cole o seu certificado codificado base-64.
    5.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na Cisco Webex** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor para SAML (IdP ID)** .
    6.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na Cisco Webex** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do cliente SSO serviço** .
    7.  Na lista **Formato NameID** , selecione **endereço de E-Mail**.
    8.  Na caixa de texto **AuthnContextClassRef** , escreva **Urn: organização de normalização: nomes: tc: SAML:2.0:ac:classes:Password**.
    9.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Cisco Webex** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL do cliente SSO serviço termine a sessão** .
    10. Clique em **Atualizar**.

9.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Cisco Webex** , selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Cisco Webex, tem de ser aprovisionadas para Cisco Webex.  
No caso de Cisco Webex, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Cisco Webex** .

2.  Aceda a **Gerir utilizadores \> adicionar utilizador**.

    ![Adicionar utilizadores] (./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Adicionar utilizadores")

3.  Na secção Adicionar utilizador, efetue os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Adicionar utilizador")

    1.  Com o **Tipo de conta**, selecione **Host**.
    2.  Escreva as informações de um utilizador existente do Azure AD para as seguintes caixas de texto: **nome próprio, apelido**, **nome de utilizador**, **E-Mail**, **palavra-passe**, **Confirme a palavra-passe**.
    3.  Clique em **Adicionar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros Cisco Webex utilizador conta ferramentas para a criação ou APIs fornecida pela Cisco Webex às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-cisco-webex-perform-the-following-steps"></a>Para atribuir utilizadores a Cisco Webex, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Cisco Webex **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
