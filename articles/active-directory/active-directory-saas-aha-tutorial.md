<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Aha! | Microsoft Azure" 
    description="Saiba como utilizar Aha! com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>Tutorial: Integração do Azure Active Directory com Aha!

É o objectivo deste tutorial mostrar a integração do Azure e Aha!  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Aha! o início de sessão único activado subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD ter atribuído a Aha! poderão único início de sessão para a aplicação no seu Aha! site de empresa (sinal de fornecedor iniciados por serviço no) ou utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Aha!
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-aha-tutorial/IC798944.png "Cenário")
##<a name="enabling-the-application-integration-for-aha"></a>Ativar a integração da aplicação para Aha!

O objectivo desta secção é destacar como ativar a integração da aplicação para Aha!.

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Para ativar a integração da aplicação para Aha!, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-aha-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-aha-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-aha-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-aha-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Aha!**.

    ![Galeria de aplicação] (./media/active-directory-saas-aha-tutorial/IC798945.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Aha!**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Aha!] (./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é destacar como permitir que os utilizadores autenticar Aha! com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal do Azure clássico, sobre a **Aha!** integração de aplicações da página, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798946.png "Configurar o Single Sign-On")

2.  No **como pretende utilizadores a iniciar sessão Aha!** página, selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798947.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na **Aha! Início de sessão no URL** caixa de texto, escreva o URL é utilizado pelos utilizadores para o início de sessão no seu Aha! Aplicação (por exemplo: "*https://company.aha.io/session/new*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-aha-tutorial/IC798948.png "Configurar o URL da aplicação")

4.  No **configurar o início de sessão único ao Aha!** página, transferir o seu ficheiro de metadados, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798949.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão na sua Aha! site de empresa como administrador.

6.  No menu no canto superior, clique em **Definições**.

    ![Definições] (./media/active-directory-saas-aha-tutorial/IC798950.png "Definições")

7.  Clique em **conta**.

    ![Perfil] (./media/active-directory-saas-aha-tutorial/IC798951.png "Perfil")

8.  Clique em **segurança e o início de sessão único**.

    ![Segurança e o início de sessão único] (./media/active-directory-saas-aha-tutorial/IC798952.png "Segurança e o início de sessão único")

9.  Na secção **Single Sign-On** , como **Fornecedor de identidades**, selecione **SAML2.0**.

    ![Segurança e o início de sessão único] (./media/active-directory-saas-aha-tutorial/IC798953.png "Segurança e o início de sessão único")

10. Na página de configuração **Single Sign-On** , execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")

    1.  Na caixa de texto **nome** , escreva um nome para a configuração.
    2.  Para **utilizar o configurar**, selecione **O ficheiro de metadados**.
    3.  Para carregar o ficheiro de metadados transferido, clique em **Procurar**.
    4.  Clique em **Atualizar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-aha-tutorial/IC798955.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que os utilizadores do Azure AD que inicie sessão no Aha!, tem de ser aprovisionadas para Aha!.  
No caso de Aha!, aprovisionamento é uma tarefa automatizada.  
Não existe nenhum item ação por si.
  
Os utilizadores são criados automaticamente se for necessário durante a primeira única início de sessão tentativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Aha! ferramentas de criação de conta de utilizador ou APIs fornecidos pela Aha! Aprovisionamento de contas de utilizador AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>Para atribuir utilizadores a Aha!, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  No **Aha!** integração de aplicações página, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-aha-tutorial/IC798956.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-aha-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
