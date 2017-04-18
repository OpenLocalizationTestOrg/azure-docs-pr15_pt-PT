<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com plataforma do SAP HANA nuvem | Microsoft Azure" 
    description="Saiba como utilizar SAP HANA nuvem plataforma com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Tutorial: Integração do Azure Active Directory com plataforma do SAP HANA na nuvem
  
Este tutorial objectivo Mostrar a integração do Azure e SAP HANA nuvem plataforma.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Uma conta de plataforma do SAP HANA na nuvem
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a plataforma do SAP HANA nuvem poderão para o início de sessão único para a aplicação utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]Tem de implementar a sua própria aplicação ou subscrever uma aplicação na sua conta do SAP HANA nuvem plataforma para testar o início de sessão único no. Neste tutorial, uma aplicação é implementada na conta.
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para a plataforma do SAP HANA na nuvem
2.  Configurar o início de sessão único
3.  Atribuir uma função a um utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Cenário")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>Ativar a integração de aplicação para a plataforma do SAP HANA na nuvem
  
O objectivo desta secção é destacar como ativar a integração de aplicação para a plataforma do SAP HANA na nuvem.

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>Para ativar a integração de aplicação para a plataforma do SAP HANA na nuvem, execute os seguintes passos:

1.  No Portal de gestão do Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **SAP HANA nuvem plataforma**.

    ![Galeria de aplicação] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Galeria de aplicação")

7.  No painel de resultados, selecione a **Plataforma do SAP HANA na nuvem**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Hana do SAP] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "Hana do SAP")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados a plataforma do SAP HANA nuvem com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 para o seu inquilino do SAP HANA nuvem plataforma.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação do **SAP HANA nuvem plataforma** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão SAP HANA nuvem plataforma** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configurar o Single Sign-On")

3.  Numa janela do browser web diferente, inicie sessão SAP HANA nuvem plataforma voo na https://account. \<anfitrião horizontal\>.ondemand.com/cockpit (por exemplo: *https://account.hanatrial.ondemand.com/cockpit*).

4.  Clique no separador **de fidedignidade** .

    ![Confiar] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Confiar")

5.  Na secção de gestão de fidedignidade, execute os seguintes passos:

    ![Obter metadados] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Obter metadados")

    1.  Clique no separador do **Fornecedor de serviços Local** .
    2.  Para transferir o ficheiro de metadados da plataforma do SAP HANA na nuvem, clique em **Obter metadados**.

6.  No portal do Azure Active clássico, na página **Configurar o URL da aplicação** , execute os seguintes passos e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configurar o URL da aplicação")

    1.  Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos utilizadores para inicie sessão na sua aplicação de **Plataforma do SAP HANA na nuvem** . Este é o URL de conta específicas de um recurso protegido na sua aplicação de plataforma do SAP HANA na nuvem. O URL é baseado no seguinte padrão: *https://\<applicationName\>\<accountName\>.\< anfitrião horizontal\>.ondemand.com/\<caminho\_para\_protegida\_recurso\> * (por exemplo: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Este é o URL na sua aplicação de plataforma do SAP HANA na nuvem que requer que o utilizador autenticar.

    2.  Abrir o ficheiro transferido de metadados da plataforma do SAP HANA na nuvem e, em seguida, localize a etiqueta **ns3:AssertionConsumerService** .
    3.  Copie o valor do atributo **localização** e, em seguida, colá-la a caixa de texto do **SAP HANA nuvem plataforma resposta URL** .

7.  Na página **Configurar serviço single sign-on na plataforma do SAP HANA na nuvem** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configurar o Single Sign-On")

8.  No SAP HANA nuvem plataforma voo, na secção **Locais fornecedor de serviços** , execute os seguintes passos:

    ![Gestão de fidedignidade] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Gestão de fidedignidade")

    1.  Clique em **Editar**.
    2.  Com o **Tipo de configuração**, selecione **personalizada**.
    3.  Como **Nome do fornecedor de Local**, deixe o valor predefinido.
    4.  Para gerar uma **Chave de assinatura** e um par de chave do **Certificado de assinatura** , clique em **Gerar par chaves**.
    5.  Como **Propagação principais**, selecione **desativado**.
    6.  Como **Forçar autenticação**, selecione **desativado**.
    7.  Clique em **Guardar**.

9.  Clique no separador **Fidedignos fornecedor de identidade** e, em seguida, clique em **Adicionar fornecedor de identidade fidedigno**.

    ![Gestão de fidedignidade] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Gestão de fidedignidade")

    >[AZURE.NOTE]Para gerir a lista de fornecedores de identidade fidedignos, tem de ter escolhido o tipo de configuração personalizado na secção locais fornecedor de serviços. Para tipo de configuração do predefinido, tem uma fidedignidade não editável e implícita para o serviço de ID do SAP. Para nenhuma, não precisa de quaisquer definições de fidedignidade.

10. Clique no separador **Geral** e, em seguida, clique em **Procurar** para carregar o ficheiro transferido metadados.

    ![Gestão de fidedignidade] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Gestão de fidedignidade")

    >[AZURE.NOTE] Após carregar o ficheiro de metadados, os valores para **URL de início de sessão único**, a **Única termine a sessão URL** e **Certificado de assinatura** são automaticamente preenchidos.

11. Clique no separador de **atributos** .

12. No separador **atributos** , execute os seguintes passos:

    ![Atributos] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Atributos")

    1.  Ao clicar em **Add Assertion-Based atributo**, adicione os seguintes atributos com base em declaração:

        |Atributo de declaração| Atributo de capital|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/givenName|   NomePróprio|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Surname|        Apelido|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/emailaddress|mensagem de correio electrónico|

    >[AZURE.NOTE]A configuração dos atributos depende como as aplicações no HCP sejam desenvolvidos, ou seja, que atributo (s) devem esperar na resposta SAML e em cujo nome (capital atributo) aceder a este atributo no código.
    >  
    >um.  O **Atributo predefinido** na captura de ecrã é apenas para fins de ilustração. Não é necessário para tornar o cenário trabalhar.  
    >
    >b.  Os nomes e os valores de **Capital atributo** apresentado na captura de ecrã dependem como a aplicação é desenvolvida. É possível que a sua aplicação requer mapeamentos diferentes.

13. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na plataforma do SAP HANA na nuvem** , selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configurar o Single Sign-On")
  
Como um passo opcional, pode configurar com base em declaração grupos para o seu fornecedor de identidade de diretório ativo do Azure

>[AZURE.NOTE]Utilizar grupos SAP HANA nuvem plataforma permite-lhe atribuir dinamicamente um ou mais utilizadores a um ou mais funções nas aplicações SAP HANA nuvem plataforma, determinadas pelo valores dos atributos na declaração de SAML 2.0. Por exemplo, se a declaração contém o atributo "*contrato = temporário*", poderá querer afetados todos os utilizadores a serem adicionados ao grupo "*temporário*". O grupo "*temporário*" pode conter um ou mais funções a partir de uma ou mais aplicações implementadas na sua conta de plataforma do SAP HANA na nuvem.
>  
>Utilize grupos com base em declaração se pretender atribuir massa muitos utilizadores a um ou mais funções de aplicações na sua conta de plataforma do SAP HANA na nuvem. Se apenas pretender atribuir um único ou pequeno número de utilizadores a (a) funções específicas Recomendamos atribuí-las diretamente no separador "**autorizações**" do voo a plataforma do SAP HANA na nuvem.

##<a name="assigning-a-role-to-a-user"></a>Atribuir uma função a um utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no plataforma do SAP HANA na nuvem, tem de atribuir funções da plataforma do SAP HANA nuvem às mesmas.

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Para atribuir uma função a um utilizador, execute os seguintes passos:

1.  Inicie sessão na sua voo **SAP HANA nuvem plataforma** .

2.  Execute os seguintes passos:

    ![Autorizações] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorizações")

    1.  Clique em **autorização**.
    2.  Clique no separador de **utilizadores** .
    3.  Na caixa de texto **utilizador** , escreva o endereço de e-mail do utilizador.
    4.  Clique em **atribuir** para atribuir ao utilizador para uma função.
    5.  Clique em **Guardar**.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Para atribuir utilizadores a plataforma do SAP HANA na nuvem, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação do **SAP HANA nuvem plataforma** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).