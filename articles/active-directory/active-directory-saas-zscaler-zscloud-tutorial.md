<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Zscaler ZSCloud | Microsoft Azure"
    description="Saiba como utilizar Zscaler ZSCloud com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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


#<a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Tutorial: Integração com o Azure Active Directory com Zscaler ZSCloud
  
Este tutorial objectivo Mostrar a integração do Azure e ZScaler ZSCloud.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um ZScaler ZSCloud serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a ZScaler ZSCloud poderão único início de sessão para a aplicação no site da sua empresa ZScaler ZSCloud (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md)
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para ZScaler ZSCloud
2.  Configurar o início de sessão único
3.  Configurar as definições de proxy
4.  Configurar o aprovisionamento de utilizador
5.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800275.png "Cenário")

##<a name="enabling-the-application-integration-for-zscaler-zscloud"></a>Ativar a integração da aplicação para ZScaler ZSCloud
  
Objectivo de nesta secção é como ativar a integração da aplicação para ZScaler ZSCloud de destaque.

###<a name="to-enable-the-application-integration-for-zscaler-zscloud-perform-the-following-steps"></a>Para ativar a integração da aplicação para ZScaler ZSCloud, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **ZScaler ZSCloud**.

    ![Galeria de aplicação] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800276.png "Galeria de aplicação")

7.  No painel de resultados, selecione **ZScaler ZSCloud**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ZScaler ZSCloud] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800277.png "ZScaler ZSCloud")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para ZScaler ZSCloud com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base-64 ao seu inquilino ZScaler ZSCloud.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **ZScaler ZSCloud** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800278.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão ZScaler ZSCloud** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800279.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **ZScaler ZSCloud início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para o início de sessão à aplicação ZScaler ZSCloud e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800280.png "Configurar o URL da aplicação")

    >[AZURE.NOTE] Pode obter o valor real para o ambiente da sua equipa de suporte ZScaler ZSCloud caso precise da mesma.

4.  Na página **Configurar serviço single sign-on na ZScaler ZSCloud** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800281.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa ZScaler ZSCloud como administrador.

6.  No menu no canto superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800206.png "Administração")

7.  Em **Gerir administradores e funções**, clique em **Gerir utilizadores e de autenticação**.

    ![Gerir utilizadores e autenticação] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800207.png "Gerir utilizadores e autenticação")

8.  Na secção **Escolher opções de autenticação para a sua organização** , execute os seguintes passos:

    ![Autenticação] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800208.png "Autenticação")

    1.  Selecione **autenticar com SAML Single Sign-On**.
    2.  Clique em **Configurar SAML únicos início de sessão parâmetros**.

9.  Na página de diálogo **Configurar SAML único início de sessão parâmetros** , execute os seguintes passos e, em seguida, clique em **concluído**:

    ![Single Sign-On] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800209.png "Single Sign-On")

    1.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na ZScaler ZSCloud** , copie o valor de **URL de pedido de autenticação** e, em seguida, colá-la a caixa de texto do **URL do Portal de SAML para onde os utilizadores são enviados para a autenticação** .
    2.  Na caixa de texto **atributo que contém o nome de início de sessão** , escreva **NameID**.
    3.  Para carregar o certificado transferido, clique em **Zscaler pem**.
    4.  Selecione **Ativar o aprovisionamento de automática SAML**.

10. Na página de diálogo **Configurar autenticação de utilizador** , execute os seguintes passos:

    ![Administração] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800210.png "Administração")

    1.  Clique em **Guardar**.
    2.  Clique em **Ativar agora**.

11. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na ZScaler ZSCloud** , selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800282.png "Configurar o Single Sign-On")

##<a name="configuring-proxy-settings"></a>Configurar as definições de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de proxy no Internet Explorer

1.  Inicie o **Internet Explorer**.

2.  Selecione **Opções da Internet** no menu de **Ferramentas** para abrir a caixa de diálogo **Opções da Internet** .

    ![Opções da Internet] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC769492.png "Opções da Internet")

3.  Clique no separador **ligações** .

    ![Ligações] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC769493.png "Ligações")

4.  Clique em **definições de LAN** para abrir a caixa de diálogo **Definições de LAN** .

5.  Na secção de servidor Proxy, execute os seguintes passos:

    ![Servidor proxy] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC769494.png "Servidor proxy")

    1.  Selecione utilizar um servidor proxy para a sua LAN.
    2.  Na caixa de texto endereço, escreva **gateway.zscalerone.net**.
    3.  Na caixa de texto porta, escreva **80**.
    4.  Selecione **Ignorar o servidor proxy para endereços locais**.
    5.  Clique em **OK** para fechar a caixa de diálogo **definições de rede Local (LAN)** .

6.  Clique em **OK** para fechar a caixa de diálogo **Opções da Internet** .

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no ZScaler ZSCloud, tem de ser aprovisionadas para ZScaler ZSCloud.  
No caso de ZScaler ZSCloud, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Zscaler** .

2.  Clique em **Administração**.

    ![Administração] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC781035.png "Administração")

3.  Clique em **Gestão de utilizadores**.

    ![Adicionar] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Adicionar")

4.  No separador de **utilizadores** , clique em **Adicionar**.

    ![Adicionar] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Adicionar")

5.  Na secção Adicionar utilizador, execute os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC781038.png "Adicionar utilizador")

    1.  Escreva o **ID de utilizador**, **Nome de utilizador**, **palavra-passe**, **Confirme a palavra-passe**e, em seguida, selecione **grupos** e o **departamento** de uma conta AAD válida que pretende aprovisionar.
    2.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros ZScaler ZSCloud utilizador conta ferramentas para a criação ou APIs fornecida pela ZScaler ZSCloud às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-zscaler-zscloud-perform-the-following-steps"></a>Para atribuir utilizadores a ZScaler ZSCloud, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **ZScaler ZSCloud** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC800283.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-zscaler-zscloud-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).