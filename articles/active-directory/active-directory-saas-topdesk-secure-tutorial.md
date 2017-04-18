<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com TOPdesk - seguro | Microsoft Azure"
    description="Saiba como utilizar TOPdesk - seguro com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!." 
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

#<a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Azure Active Directory com TOPdesk - seguro
  
Este tutorial objectivo Mostrar a integração do Azure e TOPdesk - seguro.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um TOPdesk - seguro serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a TOPdesk - seguro poderão para o início de sessão único para a aplicação no seu TOPdesk - site seguro da empresa (sinal de fornecedor iniciados por serviço no) ou utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para TOPdesk - seguro
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Cenário")

##<a name="enabling-the-application-integration-for-topdesk---secure"></a>Ativar a integração da aplicação para TOPdesk - seguro
  
O objectivo desta secção é destacar como ativar a integração da aplicação para TOPdesk - seguro.

###<a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Para ativar a integração da aplicação para TOPdesk - seguro, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **TOPdesk - seguro**.

    ![Galeria de aplicação] (./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galeria de aplicação")

7.  No painel de resultados, selecione **TOPdesk - seguro**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![TOPdesk - seguro] (./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - seguro")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
O objectivo nesta secção é destacar como permitir que os utilizadores autenticar TOPdesk - seguro com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML.  
Configurar single sign-on para TOPdesk - seguro requer que carregar um ficheiro do ícone de logótipo. Para obter o ficheiro do ícone, contacte a equipa de suporte TOPdesk.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **TOPdesk - seguro** como administrador.

2.  No **TOPdesk** menu, clique em **Definições**.

    ![Definições] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Definições")

3.  Clique em **definições de início de sessão**.

    ![Definições de início de sessão] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Definições de início de sessão")

4.  Expandir o menu de **Definições de início de sessão** e, em seguida, clique em **Geral**.

    ![Geral] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Geral")

5.  Na secção de **seguro** da secção de configuração de **início de sessão do SAML** , execute os seguintes passos:

    ![Technical Settings] (./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Technical Settings")

    1.  Clique em **Transferir** para transferir o ficheiro de metadados público e, em seguida, guarde-a localmente no seu computador.
    2.  Abra o ficheiro de metadados e, em seguida, localize o nó **AssertionConsumerService** .
        ![Serviço do consumidor de declaração] (./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Serviço do consumidor de declaração")
    3.  Copie o valor de **AssertionConsumerService** .  

        >[AZURE.NOTE] Terá do valor na secção **Configurar o URL da aplicação** mais tarde neste tutorial.

6.  Numa janela do browser web diferente, inicie sessão no seu **portal clássica Azure** como administrador.

7.  Na página de integração de aplicação **TOPdesk - seguro** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurar o Single Sign-On")

8.  Na página **como pretende utilizadores a iniciar sessão TOPdesk - seguro** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurar o Single Sign-On")

9.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurar o URL da aplicação")

    1.  Na caixa de texto **TOPdesk - início de sessão seguro no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão no seu TOPdesk - aplicação segura (por exemplo: "*https://qssolutions.topdesk.net*").
    2.  Na caixa de texto **TOPdesk – URL público de resposta** , cole o **TOPdesk - seguro URL AssertionConsumerService** (por exemplo: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  Clique em **seguinte**.

10. Na página **Configurar serviço single sign-on na TOPdesk - seguro** , para transferir o ficheiro de metadados, clique em **transferir metadados**e, em seguida, guarde o ficheiro localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurar o Single Sign-On")

11. Para criar um ficheiro de certificado, execute os seguintes passos:

    ![Certificado] (./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificado")

    1.  Abra o ficheiro transferido metadados.
    2.  Expanda o nó **RoleDescriptor** que tem um **xsi: type** da **Moro: ApplicationServiceType**.
    3.  Copie o valor do **certificado X509** nó.
    4.  Guarde o valor de **certificado X509** copiado localmente no seu computador num ficheiro.

12. No seu TOPdesk - site seguro da empresa, no **TOPdesk** menu, clique em **Definições**.

    ![Definições] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Definições")

13. Clique em **definições de início de sessão**.

    ![Definições de início de sessão] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Definições de início de sessão")

14. Expandir o menu de **Definições de início de sessão** e, em seguida, clique em **Geral**.

    ![Geral] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Geral")

15. Na secção **público** , clique em **Adicionar**.

    ![Adicionar] (./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Adicionar")

16. Na página de diálogo do **Assistente de configuração do SAML** , execute os seguintes passos:

    ![Assistente de configuração do SAML] (./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Assistente de configuração do SAML")

    1.  Para carregar o ficheiro transferido metadados, em **Metadados de Federação**, clique em **Procurar**.
    2.  Para carregar o ficheiro de certificado em **Certificado (RSA)**, clique em **Procurar**.
    3.  Para carregar o ficheiro do logótipo que recebeu da equipa de suporte TOPdesk, em **ícone logótipo**, clique em **Procurar**.
    4.  Na caixa de texto **atributo de nome de utilizador** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  Na caixa de texto **nome a apresentar** , escreva um nome para a configuração.
    6.  Clique em **Guardar**.

17. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que os utilizadores do Azure AD que inicie sessão no TOPdesk - seguro, eles tem de ser aprovisionados para TOPdesk - seguro.  
No caso de TOPdesk - seguro, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **TOPdesk - seguro** como administrador.

2.  No menu no canto superior, clique em **TOPdesk \> novo \> ficheiros de suporte \> operador**.

    ![Operador] (./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operador")

3.  Na caixa de diálogo **Novo operador** , execute os seguintes passos:

    ![Novo operador] (./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Novo operador")

    1.  Clique no separador Geral.
    2.  Na caixa de texto **Apelido** da secção **Geral** , escreva o apelido de uma conta do Azure Active Directory válida que pretende aprovisionar.
    3.  Selecione um **Site** para a conta na secção **localização** .
    4.  Na caixa de texto **Nome de início de sessão** da secção **TOPdesk início de sessão** , escreva um nome de início de sessão para o utilizador.
    5.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros TOPdesk - ferramentas de criação de conta de utilizador seguro ou APIs fornecidos pela TOPdesk - seguro aprovisionamento de contas de utilizador AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Para atribuir utilizadores a TOPdesk - seguro, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **TOPdesk - seguro **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).