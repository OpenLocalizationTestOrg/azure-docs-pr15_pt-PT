<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Mindflash | Microsoft Azure" 
    description="Saiba como utilizar Mindflash com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Tutorial: Integração do Azure Active Directory com Mindflash
  
Este tutorial objectivo Mostrar a integração do Azure e Mindflash.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Mindflash serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Mindflash poderão para o início de sessão único para a aplicação no site da sua empresa Mindflash (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Mindflash
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-mindflash-tutorial/IC787132.png "Cenário")
##<a name="enabling-the-application-integration-for-mindflash"></a>Ativar a integração da aplicação para Mindflash
  
Objectivo de nesta secção é como ativar a integração da aplicação para Mindflash de destaque.

###<a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Para ativar a integração da aplicação para Mindflash, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-mindflash-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-mindflash-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-mindflash-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-mindflash-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Mindflash**.

    ![Galeria de aplicação] (./media/active-directory-saas-mindflash-tutorial/IC787133.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Mindflash**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Mindflash] (./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Mindflash com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Mindflash** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Mindflash** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **URL no início de sessão** , escreva o seu URL utilizando o padrão seguinte "*http://company.mindflash.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Mindflash** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configurar o Single Sign-On")

5.  Envie o metadatafile para a equipa de suporte Mindflash.

    >[AZURE.NOTE] A configuração de início de sessão único tem de ser executado pela equipa de suporte Mindflash. Irá obter uma notificação assim que já foi concluída a configuração.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Mindflash, tem de ser aprovisionadas para Mindflash.  
No caso de Mindflash, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Mindflash** como administrador.

2.  Aceda a **Gerir utilizadores**.

    ![Gerir utilizadores] (./media/active-directory-saas-mindflash-tutorial/IC787140.png "Gerir utilizadores")

3.  Clique em **Adicionar utilizadores**e, em seguida, clique em **Novo**.

4.  Na secção **Adicionar novos utilizadores** , execute os seguintes passos:

    ![Adicionar novos utilizadores] (./media/active-directory-saas-mindflash-tutorial/IC787141.png "Adicionar novos utilizadores")

    1.  Escreva o **nome próprio**, **Apelido** e **E-Mail** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Adicionar**.

>[AZURE.NOTE]Pode utilizar quaisquer outros Mindflash utilizador conta ferramentas para a criação ou APIs fornecida pela Mindflash às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Para atribuir utilizadores a Mindflash, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Mindflash **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-mindflash-tutorial/IC787142.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-mindflash-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).