<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Qualtrics | Microsoft Azure" 
    description="Saiba como utilizar Qualtrics com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Tutorial: Integração do Azure Active Directory com Qualtrics
  
Este tutorial objectivo Mostrar a integração do Azure e Qualtrics.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Qualtrics serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Qualtrics poderão para o início de sessão único para a aplicação no site da sua empresa Qualtrics (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Qualtrics
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Cenário")
##<a name="enabling-the-application-integration-for-qualtrics"></a>Ativar a integração da aplicação para Qualtrics
  
Objectivo de nesta secção é como ativar a integração da aplicação para Qualtrics de destaque.

###<a name="to-enable-the-application-integration-for-qualtrics-perform-the-following-steps"></a>Para ativar a integração da aplicação para Qualtrics, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-qualtrics-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Qualtrics**.

    ![Galeria de aplicação] (./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Qualtrics**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Qualtrics] (./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Qualtrics com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Qualtrics** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Qualtrics** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Qualtrics início de sessão no URL** , escreva o seu URL (por exemplo: "*https://ssotest2ut1.qualtrics.com*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Qualtrics** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configurar o Single Sign-On")

5.  Envie o metadatafile para a equipa de suporte Qualtrics.

    >[AZURE.NOTE]A configuração de início de sessão único tem de ser executado pela equipa de suporte Qualtrics. Irá obter uma notificação assim que já foi concluída a configuração.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item de ação que pode configurar Qualtrics do aprovisionamento de utilizador.  
Quando um utilizador atribuído tenta que inicie sessão no Qualtrics utilizando o painel de acesso, Qualtrics verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo Qualtrics.
##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-qualtrics-perform-the-following-steps"></a>Para atribuir utilizadores a Qualtrics, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Qualtrics **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).