<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com pedra angular sob | Microsoft Azure" 
    description="Saiba como utilizar o pedido de pedra angular com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Tutorial: Integração do Azure Active Directory com pedra angular pedido

Este tutorial objectivo Mostrar a integração do Azure e pedra angular pedido.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um pedido de pedra angular inquilino

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a pedra angular sob poderão para o início de sessão único para a aplicação no site da sua empresa pedra angular sob (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para pedra angular pedido
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Cenário")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Ativar a integração da aplicação para pedra angular pedido

O objectivo desta secção é destacar como ativar a integração da aplicação para pedra angular pedido.

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Para ativar a integração da aplicação para pedra angular pedido, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **pedra angular pedido**.

    ![Galeria de aplicação] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galeria de aplicação")

7.  No painel de resultados, selecione a **Pedra angular pedido**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Pedido de pedra angular] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Pedido de pedra angular")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados a pedra angular sob com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Pedra angular pedido** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Ativar Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Ativar Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão pedido de pedra angular** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Pedra angular sob URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*http://company.csod.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on no pedido de pedra angular** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurar o Single Sign-On")

5.  Envie a que equipa de suporte nos seguintes itens para o pedido de pedra angular:

    1.  O certificado transferido
    2.  O valor de **Início de sessão de URL remoto**
    3.  O valor **De URL remoto termine a sessão** .

    >[AZURE.NOTE] Single Sign-On tem de ser configurado pela equipa de suporte a pedra angular pedido.
Irá obter uma notificação a partir da equipa de suporte quando tiver sido concluída a configuração.

6.  Selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no pedido de pedra angular, tem de ser aprovisionadas para pedra angular pedido.  
No caso de pedido de pedra angular, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Enviar informações da (por exemplo: nome, Emial) sobre o utilizador do Azure AD pretende aprovisionar para sob a pedra angular equipa de suporte.

>[AZURE.NOTE] Pode utilizar quaisquer outros pedra angular sob utilizador conta ferramentas para a criação ou APIs fornecida pela pedra angular sob às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Para atribuir utilizadores a pedra angular pedido, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Pedra angular pedido **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Atribuir aos utilizadores")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
