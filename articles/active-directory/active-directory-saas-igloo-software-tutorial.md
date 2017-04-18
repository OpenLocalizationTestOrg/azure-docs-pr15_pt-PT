<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com um Software de Igloo | Microsoft Azure" 
    description="Saiba como utilizar o Software de Igloo com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integração do Azure Active Directory com um Software de Igloo
  
Este tutorial objectivo Mostrar a integração do Azure e Igloo Software.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um [Software de Igloo](http://www.igloosoftware.com/) Single Sign-on subscrição activada
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Igloo Software poderão para o início de sessão único para a aplicação no site da sua empresa Igloo Software (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o Software de Igloo
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Cenário")
##<a name="enabling-the-application-integration-for-igloo-software"></a>Ativar a integração de aplicação para o Software de Igloo
  
Objectivo de nesta secção é como ativar a integração de aplicação para o Software de Igloo de destaque.

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Para ativar a integração de aplicação para o Software de Igloo, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-igloo-software-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Igloo Software**.

    ![Galeria de aplicação] (./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Igloo Software**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Igloo] (./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados ao Igloo Software com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 para o seu inquilino do ambiente de trabalho Central.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **Igloo Software** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Igloo Software** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Igloo Software URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*https://company.igloocommunities.com/?signin*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on em Igloo Software** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Igloo Software como administrador.

6.  Vá para o **Painel de controlo**.

    ![Painel de controlo] (./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Painel de controlo")

7.  No separador **associação** , clique em **Definições de início de sessão**.

    ![Definições de início de sessão] (./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Definições de início de sessão")

8.  Na secção SAML configuração, clique em **Configurar autenticação de SAML**.

    ![Configuração do SAML] (./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configuração do SAML")

9.  Na secção **Configuração geral** , execute os seguintes passos:

    ![Configuração geral] (./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configuração geral")

    1.  Na caixa de texto **Nome da ligação** , escreva um nome personalizado para a configuração.
    2.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on em Igloo Software** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão IdP** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Igloo Software** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **IdP termine a sessão URL** .
    4.  Como **resposta termine a sessão e tipo de pedido de HTTP**, selecione **Publicar**.
    5.  Crie um ficheiro de texto a partir do certificado transferido.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Remover a primeira linha e a última linha da versão de ficheiro de texto do seu certificado, copie o texto de certificado restante e, em seguida, colá-la a caixa de texto do **Certificado pública** .

10. Na **resposta e configuração de autenticação**, execute os seguintes passos:

    ![Configuração de autenticação e de resposta] (./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuração de autenticação e de resposta")

    1.  Como **Fornecedor de identidades**, selecione **Microsoft ADFS**.
    2.  Como **Identificador tipo**, selecione **Endereço de E-Mail**.
    3.  Na caixa de **Correio eletrónico atributo** texto, escreva **emailaddress**.
    4.  Na caixa de texto **Nome próprio atributo** , escreva **givenname**.
    5.  Na caixa de texto **Último nome atributo** , escreva **Apelido**.

11. Faz os seguintes passos para concluir a configuração:

    ![Criação de utilizador no início de sessão] (./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Criação de utilizador no início de sessão")

    1.  Como a **criação de utilizador no início de sessão**, selecione **criar um novo utilizador no seu site quando iniciar sessão**.
    2.  Como **Iniciar sessão nas definições**, selecione o **botão de utilização SAML no ecrã "Início de sessão"**.
    3.  Clique em **Guardar**.

12. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item ação para configurar o aprovisionamento de utilizador para Igloo Software.  
Quando um utilizador atribuído tenta que inicie sessão no Software Igloo utilizando o painel de acesso, o Igloo Software verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é automaticamente criada por Igloo Software.
##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Para atribuir utilizadores a Igloo Software, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação de **Igloo Software **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).