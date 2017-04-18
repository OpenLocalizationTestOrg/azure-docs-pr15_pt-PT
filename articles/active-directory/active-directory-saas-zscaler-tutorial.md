<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Zscaler | Microsoft Azure" 
    description="Saiba como utilizar Zscaler com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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

#<a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Integração do Azure Active Directory com Zscaler
  
Este tutorial objectivo Mostrar a integração do Azure e Zscaler. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino no Zscaler
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Zscaler
2.  Configurar o início de sessão único
3.  Configurar as definições de proxy
4.  Configurar o aprovisionamento de utilizador
5.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-zscaler-tutorial/IC769226.png "Cenário")

##<a name="enabling-the-application-integration-for-zscaler"></a>Ativar a integração da aplicação para Zscaler
  
Objectivo de nesta secção é como ativar a integração da aplicação para Zscaler de destaque.

###<a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Para ativar a integração da aplicação para Zscaler, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-zscaler-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-zscaler-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-zscaler-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-zscaler-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Zscaler**.

    ![Galeria de aplicação] (./media/active-directory-saas-zscaler-tutorial/IC769227.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Zscaler**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Zscaler] (./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Zscaler com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado para Zscaler.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Zscaler** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Ativar o início de sessão único] (./media/active-directory-saas-zscaler-tutorial/IC769229.png "Ativar o início de sessão único")

2.  Na página **como pretende utilizadores a iniciar sessão Zscaler** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar única ao iniciar sessão no] (./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configurar única ao iniciar sessão no")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Zscaler URL início de sessão** , escreva o sinal de URL que recebeu do Zscaler e, em seguida, clique em **seguinte**: 

    >[AZURE.NOTE] Contacte a equipa de suporte Zscaler se não sabe o que é o seu URL de início de sessão.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Zscaler** , execute os seguintes passos:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configurar serviço single sign-on")

    1.  Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\Zscaler.cer**.
    2.  Copie o **URL do pedido de autenticação** para sua área de transferência.

5.  Inicie sessão no seu inquilino Zscaler.

6.  No menu no canto superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administração")

7.  Em **Gerir administradores e funções**, clique em **Gerir utilizadores e de autenticação**.

    ![Gerir administradores e funções] (./media/active-directory-saas-zscaler-tutorial/IC769487.png "Gerir administradores e funções")

8.  Na secção **Selecione a opção de autenticação para a sua organização** , execute os seguintes passos:

    ![Escolher opções de autenticação] (./media/active-directory-saas-zscaler-tutorial/IC769488.png "Escolher opções de autenticação")

    1.  Selecione **autenticar com SAML Single Sign-on**.
    2.  Clique em **Configurar SAML únicos início de sessão parâmetros**.

9.  Na página de diálogo **Configurar SAML único início de sessão parâmetros** , execute os seguintes passos e, em seguida, clique em **concluído**:

    ![Carregar certificado] (./media/active-directory-saas-zscaler-tutorial/IC769489.png "Carregar certificado")

    1.  Na caixa de texto **URL do Portal de SAML para onde os utilizadores são enviados para autenticação** , cole o valor do campo **URL do pedido de autenticação** a partir do Azure portal clássico.
    2.  Na caixa de texto **atributo que contém o nome de início de sessão** , escreva **NameID**.
    3.  No campo **Carregar certificado SSL de público** , carregue o certificado que transferiu a partir do Azure portal clássico.
    4.  Selecione **Ativar o aprovisionamento de automática SAML**.

10. Na página de diálogo **Configurar autenticação de utilizador** , execute os seguintes passos:

    ![Configurar autenticação de utilizador] (./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configurar autenticação de utilizador")

    1.  Clique em **Guardar**.
    2.  Clique em **Ativar agora**.

11. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configurar serviço single sign-on")

##<a name="configuring-proxy-settings"></a>Configurar as definições de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de proxy no Internet Explorer

1.  Inicie o **Internet Explorer**.

2.  Selecione **Opções da Internet** no menu de **Ferramentas** para abrir a caixa de diálogo **Opções da Internet** .

    ![Opções da Internet] (./media/active-directory-saas-zscaler-tutorial/IC769492.png "Opções da Internet")

3.  Clique no separador **ligações** .

    ![Ligações] (./media/active-directory-saas-zscaler-tutorial/IC769493.png "Ligações")

4.  Clique em **definições de LAN** para abrir a caixa de diálogo **Definições de LAN** .

5.  Na secção de servidor Proxy, execute os seguintes passos:

    ![Servidor proxy] (./media/active-directory-saas-zscaler-tutorial/IC769494.png "Servidor proxy")

    1.  Selecione utilizar um servidor proxy para a sua LAN.
    2.  Na caixa de texto endereço, escreva **gateway.zscalertwo.net**.
    3.  Na caixa de texto porta, escreva **80**.
    4.  Selecione **Ignorar o servidor proxy para endereços locais**.
    5.  Clique em **OK** para fechar a caixa de diálogo **definições de rede Local (LAN)** .

6.  Clique em **OK** para fechar a caixa de diálogo **Opções da Internet** .

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Zscaler, tem de ser aprovisionadas para Zscaler.  
No caso de Zscaler, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Zscaler** .

2.  Clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administração")

3.  Clique em **Gestão de utilizadores**.

    ![Gestão de utilizadores] (./media/active-directory-saas-zscaler-tutorial/IC781036.png "Gestão de utilizadores")

4.  No separador de **utilizadores** , clique em **Adicionar**.

    ![Adicionar] (./media/active-directory-saas-zscaler-tutorial/IC781037.png "Adicionar")

5.  Na secção Adicionar utilizador, execute os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-zscaler-tutorial/IC781038.png "Adicionar utilizador")

    1.  Escreva o **ID de utilizador**, **Nome de utilizador**, **palavra-passe**, **Confirme a palavra-passe**e, em seguida, selecione **grupos** e o **departamento** de uma conta AAD válida que pretende aprovisionar.
    2.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros Zscaler utilizador conta ferramenta de criação ou APIs fornecida pela Zscaler às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Para atribuir utilizadores a Zscaler, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Zscaler** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-zscaler-tutorial/IC769495.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-zscaler-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
