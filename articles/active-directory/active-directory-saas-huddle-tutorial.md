<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Huddle | Microsoft Azure" 
    description="Saiba como utilizar Huddle com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>Tutorial: Integração do Azure Active Directory com Huddle
  
Este tutorial objectivo Mostrar a integração do Azure e Huddle.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Huddle serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Huddle poderão para o início de sessão único para a aplicação no site da sua empresa Huddle (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Huddle
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Configurar o Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurar o Single Sign-On")
##<a name="enabling-the-application-integration-for-huddle"></a>Ativar a integração da aplicação para Huddle
  
Objectivo de nesta secção é como ativar a integração da aplicação para Huddle de destaque.

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Para ativar a integração da aplicação para Huddle, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-huddle-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-huddle-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-huddle-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-huddle-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Huddle**.

    ![Galeria de aplicação] (./media/active-directory-saas-huddle-tutorial/IC787831.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Huddle**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Huddle] (./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Huddle com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Huddle** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurar o Single Sign-On")

2.  Na página **como pretende que como os utilizadores para iniciar sessão Huddle** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Huddle início de sessão no URL** , escreva o URL do seu inquilino do Huddle utilizando o padrão seguinte "*http://company.huddle.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Huddle** , execute os seguintes passos:

    ![Configurar o Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurar o Single Sign-On")

    1.  Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.
    2.  Copie o valor de **Emissor URL** , o valor de **SAML SSO URL** e o certificado transferido e, em seguida, envie-lhes para a equipa de suporte Huddle.

    >[AZURE.NOTE] O início de sessão único tem de estar ativada pela equipa de suporte de Huddle.
Irá obter uma notificação quando tiver sido concluída a configuração.

5.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Huddle, tem de ser aprovisionadas para Huddle.  
No caso de Huddle, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu site de empresa **Huddle** como administrador.

2.  Clique em **área de trabalho**.

3.  Clique em **pessoas \> convidar pessoas**.

    ![Pessoas] (./media/active-directory-saas-huddle-tutorial/IC787838.png "Pessoas")

4.  Na secção **criar um novo convite** , execute os seguintes passos:

    ![Novo convite] (./media/active-directory-saas-huddle-tutorial/IC787839.png "Novo convite")

    1.  Na lista **Escolher uma equipa para convidar pessoas para participar** , selecione **equipa**.
    2.  Escreva o **Endereço de E-Mail** de uma conta AAD válida que pretende aprovisionar para a caixa de texto relacionada.
    3.  Clique em **Convidar**.

    >[AZURE.NOTE] Titular da conta Azure AD irão receber um e-mail que inclua uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Huddle utilizador conta ferramentas para a criação ou APIs fornecida pela Huddle aprovisionar AAD contas de utilizador.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Para atribuir utilizadores a Huddle, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Huddle **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-huddle-tutorial/IC787840.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-huddle-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).