<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Kontiki | Microsoft Azure" 
    description="Saiba como utilizar Kontiki com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Integração do Azure Active Directory com Kontiki
  
Este tutorial objectivo Mostrar a integração do Azure e Kontiki.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Kontiki serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Kontiki poderão para o início de sessão único para a aplicação no site da sua empresa Kontiki (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Kontiki
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-kontiki-tutorial/IC790235.png "Cenário")
##<a name="enabling-the-application-integration-for-kontiki"></a>Ativar a integração da aplicação para Kontiki
  
Objectivo de nesta secção é como ativar a integração da aplicação para Kontiki de destaque.

###<a name="to-enable-the-application-integration-for-kontiki-perform-the-following-steps"></a>Para ativar a integração da aplicação para Kontiki, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-kontiki-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-kontiki-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-kontiki-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-kontiki-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Kontiki**.

    ![Galeria de aplicação] (./media/active-directory-saas-kontiki-tutorial/IC790236.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Kontiki**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Kontiki] (./media/active-directory-saas-kontiki-tutorial/IC790237.png "Kontiki")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Kontiki com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Kontiki** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-kontiki-tutorial/IC790238.png "Configurar o início único")

2.  Na página **como pretende utilizadores a iniciar sessão Kontiki** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o início único] (./media/active-directory-saas-kontiki-tutorial/IC790239.png "Configurar o início único")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Kontiki início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão Kontiki (por exemplo: "*https://company.mc.eval.kontiki.com/*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-kontiki-tutorial/IC790240.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Kontiki** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início único] (./media/active-directory-saas-kontiki-tutorial/IC790241.png "Configurar o início único")

5.  Envie o metadatafile para a equipa de suporte Kontiki.

    >[AZURE.NOTE] A configuração de início de sessão único tem de ser executado pela equipa de suporte Kontiki. Irá obter uma notificação assim que já foi concluída a configuração.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-kontiki-tutorial/IC790242.png "Configurar o início único")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item de ação que pode configurar Kontiki do aprovisionamento de utilizador.  
Quando um utilizador atribuído tenta que inicie sessão no Kontiki utilizando o painel de acesso, Kontiki verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo Kontiki.
##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-kontiki-perform-the-following-steps"></a>Para atribuir utilizadores a Kontiki, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Kontiki **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-kontiki-tutorial/IC790243.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-kontiki-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).