<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com LogicMonitor | Microsoft Azure" 
    description="Saiba como utilizar LogicMonitor com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integração do Azure Active Directory com LogicMonitor
  
Este tutorial objectivo Mostrar a integração do Azure e LogicMonitor.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino LogicMonitor
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para LogicMonitor
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Cenário")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>Ativar a integração da aplicação para LogicMonitor
  
Objectivo de nesta secção é como ativar a integração da aplicação para LogicMonitor de destaque.

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Para ativar a integração da aplicação para LogicMonitor, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **logicmonitor**.

    ![Galeria de aplicação] (./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Galeria de aplicação")

7.  No painel de resultados, selecione **LogicMonitor**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![LogicMonitor] (./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para LogicMonitor com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **LogicMonitor **, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão LogicMonitor** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão LogicMonitor \("e", g,: "*http://company.logicmonitor.com*"\)e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na LogicMonitor** , clique em **transferir metadados**e, em seguida, guarde-o no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configurar o Single Sign-On")

5.  Inicie sessão no site da sua empresa **LogicMonitor** como administrador.

6.  No menu no canto superior, clique em **Definições**.

    ![Definições] (./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Definições")

7.  No vampiro a navegação no lado esquerdo, clique em **Início de sessão**

    ![Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")

8.  Na secção **definições de início de sessão único (SSO)** , execute os seguintes passos:

    ![Definições de início de sessão únicos] (./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Definições de início de sessão únicos")

    1.  Selecione **Ativar Single Sign-on**.
    2.  Como **Predefinido atribuição de funções**, selecione **só de leitura**.
    3.  Abra o ficheiro transferido metadados no bloco de notas e, em seguida, cole o conteúdo do ficheiro na caixa de texto de **Metadados do fornecedor de identidade** .
    4.  Clique em **Guardar alterações**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para AAD os utilizadores possam iniciar sessão, tem de ser aprovisionadas para a aplicação de LogicMonitor utilizando os respetivos nomes de utilizador do Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa LogicMonitor como administrador.

2.  No menu no canto superior, clique em **Definições**e, em seguida, clique em **utilizadores e funções**.

    ![Utilizadores e funções] (./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Utilizadores e funções")

3.  Clique em **Adicionar**.

4.  Na secção **Adicionar uma conta** , execute os seguintes passos:

    ![Adicionar uma conta] (./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Adicionar uma conta")

    1.  Escreva os valores de **nome de utilizador**, **E-Mail**, **palavra-passe** e **Escreva novamente a palavra-passe** do utilizador do Azure Active Directory que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Selecione **funções**, **Ver permissões** e o **Estado**.
    3.  Clique em **Submeter**.

>[AZURE.NOTE]Pode utilizar quaisquer outros LogicMonitor utilizador conta ferramentas para a criação ou APIs fornecida pela LogicMonitor aprovisionar o Azure Active Directory contas de utilizador.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Para atribuir utilizadores a LogicMonitor, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **LogicMonitor** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).




