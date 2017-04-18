<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com 15Five | Microsoft Azure" 
    description="Saiba como utilizar 15Five com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-15five"></a>Tutorial: Integração do Azure Active Directory com 15Five

Este tutorial objectivo Mostrar a integração do Azure e 15Five. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um 15Five serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a 15Five poderão para o início de sessão único para a aplicação no site da sua empresa 15Five (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para 15Five
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-15five-tutorial/IC784667.png "Cenário")
##<a name="enabling-the-application-integration-for-15five"></a>Ativar a integração da aplicação para 15Five

Objectivo de nesta secção é como ativar a integração da aplicação para 15Five de destaque.

###<a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Para ativar a integração da aplicação para 15Five, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-15five-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-15five-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-15five-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-15five-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **15Five**.

    ![Galeria de aplicação] (./media/active-directory-saas-15five-tutorial/IC784668.png "Galeria de aplicação")

7.  No painel de resultados, selecione **15Five**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![15Five] (./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para 15Five com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **15Five** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-15five-tutorial/IC784670.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão 15Five** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-15five-tutorial/IC784671.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **15Five URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*https://company.15Five.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-15five-tutorial/IC784672.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na 15Five** , clique em **transferir metadados**e, em seguida, reencaminhe o ficheiro de metadados para a equipa de suporte 15Five.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-15five-tutorial/IC784673.png "Configurar serviço single sign-on")

    >[AZURE.NOTE] O início de sessão único tem de estar ativada pela equipa de suporte de 15Five.

5.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-15five-tutorial/IC784674.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no 15Five, tem de ser aprovisionadas para 15Five.  
No caso de 15Five, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **15Five** como administrador.

2.  Aceda a **Gerir da empresa**.

    ![Gerir da empresa] (./media/active-directory-saas-15five-tutorial/IC784675.png "Gerir da empresa")

3.  Aceda a **pessoas \> adicionar pessoas**.

    ![Pessoas] (./media/active-directory-saas-15five-tutorial/IC784676.png "Pessoas")

4.  Na secção Adicionar novo de uma pessoa, execute os seguintes passos:

    ![Adicionar novo de uma pessoa] (./media/active-directory-saas-15five-tutorial/IC784677.png "Adicionar novo de uma pessoa")

    1.  Escreva o **nome próprio**, **Apelido**, **título**, o **endereço de E-Mail** de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **concluído**.

    >[AZURE.NOTE] Titular da conta Azure AD irão receber um e-mail que inclua uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros 15Five utilizador conta ferramentas para a criação ou APIs fornecida pela 15Five às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-15five-perform-the-following-steps"></a>Para atribuir utilizadores a 15Five, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **15Five **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-15five-tutorial/IC784678.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-15five-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
