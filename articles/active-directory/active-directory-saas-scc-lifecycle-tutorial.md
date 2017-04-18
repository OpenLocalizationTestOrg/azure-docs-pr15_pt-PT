<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com SCC ciclo de vida | Microsoft Azure" 
    description="Saiba como utilizar SCC ciclo de vida com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integração do Azure Active Directory com SCC ciclo de vida
  
Este tutorial objectivo Mostrar a integração do Azure e SCC ciclo de vida.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um ciclo de vida SCC serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a SCC ciclo de vida poderão para o início de sessão único para a aplicação no site da sua empresa SCC ciclo de vida (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para SCC ciclo de vida
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Cenário")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>Ativar a integração da aplicação para SCC ciclo de vida
  
Objectivo de nesta secção é como ativar a integração da aplicação para SCC ciclo de vida de destaque.

###<a name="to-enable-the-application-integration-for-scc-lifecycle-perform-the-following-steps"></a>Para ativar a integração da aplicação para SCC ciclo de vida, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **SCC ciclo de vida**.

    ![Galeria de aplicação] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Galeria de aplicação")

7.  No painel de resultados, selecione **SCC ciclo de vida**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Ciclo de vida SCC] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "Ciclo de vida SCC")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para SCC ciclo de vida com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração do **Ciclo de vida SCC** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão ciclo de vida SCC** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação de ciclo de vida SCC utilizando o padrão de seguinte "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on no ciclo de vida SCC** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configurar o Single Sign-On")

5.  Reencaminhe esse ficheiro de metadados para a equipa de suporte do ciclo de vida de SCC.

    >[AZURE.NOTE]O início de sessão único tem de estar ativada pela equipa de suporte do ciclo de vida SCC.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no ciclo de vida SCC, tem de ser aprovisionadas para SCC ciclo de vida.
  
Não existe nenhum item ação para configurar o aprovisionamento de utilizador para SCC ciclo de vida.  
Quando um utilizador atribuído tenta que inicie sessão no ciclo de vida SCC, uma conta do ciclo de vida SCC é automaticamente criada se for necessário.

>[AZURE.NOTE]Pode utilizar quaisquer outros SCC ciclo de vida utilizador conta ferramentas para a criação ou APIs fornecidos pelo SCC ciclo de vida para contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-scc-lifecycle-perform-the-following-steps"></a>Para atribuir utilizadores a SCC ciclo de vida, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do **Ciclo de vida SCC **aplicação, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).