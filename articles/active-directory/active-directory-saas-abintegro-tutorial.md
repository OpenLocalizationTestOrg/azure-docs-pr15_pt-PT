<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Abintegro | Microsoft Azure" 
    description="Saiba como utilizar Abintegro com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Tutorial: Integração do Azure Active Directory com Abintegro

Este tutorial objectivo Mostrar a integração do Azure e Abintegro.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Abintegro serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Abintegro poderão para o início de sessão único para a aplicação no site da sua empresa Abintegro (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Abintegro
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-abintegro-tutorial/IC790076.png "Cenário")
##<a name="enabling-the-application-integration-for-abintegro"></a>Ativar a integração da aplicação para Abintegro

Objectivo de nesta secção é como ativar a integração da aplicação para Abintegro de destaque.

###<a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>Para ativar a integração da aplicação para Abintegro, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-abintegro-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-abintegro-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-abintegro-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-abintegro-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **abintegro**.

    ![Galeria de aplicação] (./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Abintegro**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Abintegro] (./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Abintegro com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Abintegro** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurar o início único")

2.  Na página **como pretende utilizadores a iniciar sessão Abintegro** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o início único] (./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurar o início único")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Abintegro início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão Abintegro (por exemplo: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`) e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Abintegro** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início único] (./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurar o início único")

5.  Envie o metadatafile para a equipa de suporte Abintegro.

    >[AZURE.NOTE] A configuração de início de sessão único tem de ser executado pela equipa de suporte Abintegro. Irá obter uma notificação assim que já foi concluída a configuração.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurar o início único")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Não existe nenhum item de ação que pode configurar Abintegro do aprovisionamento de utilizador.  
Quando um utilizador atribuído tenta que inicie sessão no Abintegro utilizando o painel de acesso, Abintegro verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo Abintegro.
##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Para atribuir utilizadores a Abintegro, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Abintegro **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-abintegro-tutorial/IC790084.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-abintegro-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
