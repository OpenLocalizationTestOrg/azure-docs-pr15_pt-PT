<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Projectplace | Microsoft Azure" 
    description="Saiba como utilizar Projectplace com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Tutorial: Integração do Azure Active Directory com Projectplace
  
Este tutorial objectivo Mostrar a integração do Azure e Projectplace.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Projectplace serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Projectplace poderão para o início de sessão único para a aplicação no site da sua empresa Projectplace (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Projectplace
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-projectplace-tutorial/IC790217.png "Cenário")
##<a name="enabling-the-application-integration-for-projectplace"></a>Ativar a integração da aplicação para Projectplace
  
Objectivo de nesta secção é como ativar a integração da aplicação para Projectplace de destaque.

###<a name="to-enable-the-application-integration-for-projectplace-perform-the-following-steps"></a>Para ativar a integração da aplicação para Projectplace, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-projectplace-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-projectplace-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-projectplace-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-projectplace-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Projectplace**.

    ![Galeria de aplicação] (./media/active-directory-saas-projectplace-tutorial/IC790218.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Projectplace**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ProjectPlace] (./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Projectplace com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Projectplace** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configurar o início único")

2.  Na página **como pretende utilizadores a iniciar sessão Projectplace** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o início único] (./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configurar o início único")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Projectplace início de sessão no URL** , escreva o seu URL do inquilino ProjectPlace (por exemplo: "*http://company.projectplace.com*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Projectplace** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início único] (./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configurar o início único")

5.  Envie o metadatafile para a equipa de suporte Projectplace.

    >[AZURE.NOTE] A configuração de início de sessão único tem de ser executado pela equipa de suporte Projectplace. Irá obter uma notificação assim que já foi concluída a configuração.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configurar o início único")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Projectplace, tem de ser aprovisionadas para Projectplace.  
No caso de Projectplace, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Projectplace** como administrador.

2.  Aceda a **pessoas**e, em seguida, clique em **Membros**.

    ![Pessoas] (./media/active-directory-saas-projectplace-tutorial/IC790228.png "Pessoas")

3.  Clique em **Adicionar membro**.

    ![Adicionar membros] (./media/active-directory-saas-projectplace-tutorial/IC790232.png "Adicionar membros")

4.  Na secção **Adicionar membro** , execute os seguintes passos:

    ![Novos membros] (./media/active-directory-saas-projectplace-tutorial/IC790233.png "Novos membros")

    1.  Na caixa de texto **Novos membros** , escreva o endereço de e-mail de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Enviar**

        >[AZURE.NOTE] Um e-mail que inclua uma ligação para confirmar a conta antes de se tornar ativa é enviado para o titular de conta Azure Active Directory.
    
>[AZURE.NOTE]Pode utilizar quaisquer outros Projectplace utilizador conta ferramentas para a criação ou APIs fornecida pela Projectplace às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-projectplace-perform-the-following-steps"></a>Para atribuir utilizadores a Projectplace, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Projectplace **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-projectplace-tutorial/IC790234.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-projectplace-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).