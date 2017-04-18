<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com indicações no Microsoft | Microsoft Azure" 
    description="Saiba como utilizar indicações no Microsoft com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Tutorial: Integração do Azure Active Directory com indicações no Microsoft

Este tutorial objectivo Mostrar a integração do Azure Active Directory e indicações no Microsoft.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Obter as instruções uma subscrição do Microsoft

Se não tiver um indicações federados numa subscrição do Microsoft ainda, mensagem de correio electrónico um pedido para "*service@DirectionsOnMicrosoft.com*".

Depois de concluir este tutorial, os utilizadores do Azure Active Directory que tiver atribuído a obter as instruções Microsoft poderão para o início de sessão único para a aplicação utilizando o início de sessão único.

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para obter as instruções Microsoft
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Cenário")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>Ativar a integração de aplicação para obter as instruções Microsoft

O objectivo desta secção é destacar como ativar a integração de aplicação para obter as instruções Microsoft.

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>Para ativar a integração de aplicação para obter as instruções Microsoft, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **indicações no Microsoft**.

    ![Galeria de aplicação] (./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galeria de aplicação")

7.  No painel de resultados, selecione **indicações no Microsoft**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Cenário] (./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Cenário")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para obter as instruções Microsoft com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página **indicações no Microsoft** aplicação integração, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Ativar Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Ativar Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão indicações no Microsoft** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Microsoft Azure AD Singel início de sessão] (./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel início de sessão")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto URL no início de sessão, escreva **https://www.directionsonmicrosoft.com/user/login**e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na indicações no Microsoft** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurar o Single Sign-On")

5.  Enviar o ficheiro de metadados para obter as instruções a equipa de suporte da Microsoft (*service@DirectionsOnMicrosoft.com*). Para permitir que as instruções no equipa de suporte da Microsoft localizar a associação de site federados, inclua as informações da empresa no seu correio eletrónico.

    >[AZURE.NOTE] Serviço Single sign-on para obter as instruções Microsoft tem de ser ativado por indicações equipa de suporte da Microsoft.
Receberá uma notificação quando o serviço single sign-on foi ativado.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Não existe nenhum item ação para configurar o utilizador aprovisionamento para obter as instruções Microsoft.  
Quando um utilizador atribuído tenta iniciar sessão para obter as instruções Microsoft utilizando o painel de acesso, indicações no Microsoft verifica se o utilizador existir. Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo indicações no Microsoft.
##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>Para atribuir utilizadores a obter as instruções Microsoft, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página **indicações no Microsoft **aplicação integração, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Sim")
