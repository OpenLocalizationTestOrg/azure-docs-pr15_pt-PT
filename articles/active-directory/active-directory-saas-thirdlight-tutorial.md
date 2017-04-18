<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Thirdlight | Microsoft Azure" 
    description="Saiba como utilizar Thirdlight com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: Integração com o Azure Active Directory com Thirdlight
  
Este tutorial objectivo Mostrar a integração do Azure e Thirdlight.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Thirdlight serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Thirdlight poderão para o início de sessão único para a aplicação no site da sua empresa Thirdlight (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Thirdlight
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Cenário")

##<a name="enabling-the-application-integration-for-thirdlight"></a>Ativar a integração da aplicação para Thirdlight
  
Objectivo de nesta secção é como ativar a integração da aplicação para Thirdlight de destaque.

###<a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>Para ativar a integração da aplicação para Thirdlight, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-thirdlight-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Thirdlight**.

    ![Galeria de aplicação] (./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Thirdlight**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ThirdLight] (./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Thirdlight com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Thirdlight requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Thirdlight** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Thirdlight** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Thirdlight URL início de sessão** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação Thirdlight (por exemplo: "*http://azuresso2.thirdlight.com/*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Thirdlight** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Thirdlight como administrador.

6.  Aceda a **configuração \> administração do sistema**e, em seguida, clique em **SAML2**.

    ![Administração do sistema] (./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administração do sistema")

7.  Na secção de configuração SAML2, execute os seguintes passos:

    ![SAML Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")

    1.  Selecione **Ativar SAML2 Single Sign-On**.
    2.  Como **origem para IdP metadados**, selecione **Carregar IdP metadados de XML**.
    3.  Abra o ficheiro transferido metadados, copie o conteúdo e, em seguida, colá-la a caixa de texto **IdP metadados XML** .
    4.  Clique em **definições de SAML2 guardar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Thirdlight, tem de ser aprovisionadas para Thirdlight.  
No caso de Thirdlight, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Thirdlight** como administrador.

2.  Aceda ao separador **utilizadores** .

3.  Selecione **utilizadores e grupos**.

4.  Clique em botão **Adicionar novo utilizador** .

5.  Introduza **o nome de utilizador, nome ou descrição, E-Mail, escolher uma predefinição ou grupo novos membros** de uma conta AAD válida que pretende aprovisionar.

6.  Clique em **Criar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros Thirdlight utilizador conta ferramentas para a criação ou APIs fornecida pela Thirdlight às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Para atribuir utilizadores a Thirdlight, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Thirdlight **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).