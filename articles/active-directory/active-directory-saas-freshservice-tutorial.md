<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com FreshService | Microsoft Azure" 
    description="Saiba como utilizar FreshService com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Tutorial: Integração do Azure Active Directory com FreshService
  
Este tutorial objectivo Mostrar a integração do Azure e FreshService.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um FreshService serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a FreshService poderão para o início de sessão único para a aplicação utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para FreshService
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-freshservice-tutorial/IC790807.png "Cenário")
##<a name="enabling-the-application-integration-for-freshservice"></a>Ativar a integração da aplicação para FreshService
  
Objectivo de nesta secção é como ativar a integração da aplicação para FreshService de destaque.

###<a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Para ativar a integração da aplicação para FreshService, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-freshservice-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-freshservice-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-freshservice-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-freshservice-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **FreshService**.

    ![Galeria de aplicação] (./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galeria de aplicação")

7.  No painel de resultados, selecione **FreshService**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Freshservice] (./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para FreshService com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para FreshService requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **FreshService** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão FreshService** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **FreshService início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação Freshdesk (por exemplo: "*http://democompany.freshservice.com/*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na FreshService** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa FreshService como administrador.

6.  No menu no canto superior, clique em **Admin**.

    ![Admin] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

7.  No **Portal de cliente**, clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-freshservice-tutorial/IC790815.png "Segurança")

8.  Na secção **segurança** , execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign-On")

    1.  Mudar **OnON de início de sessão único**.
    2.  Selecione **SAML SSO**.
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na FreshService** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do SAML** .
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na FreshService** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .
    5.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado de segurança** .
    
        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no FreshService, tem de ser aprovisionadas para FreshService.  
No caso de FreshService, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **FreshService** como administrador.

2.  No menu no canto superior, clique em **Admin**.

    ![Admin] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

3.  Na secção **Gestão de utilizadores** , clique em **solicitadores**.

    ![Solicitadores] (./media/active-directory-saas-freshservice-tutorial/IC790818.png "Solicitadores")

4.  Clique em **novo autor da requisição**.

    ![Novos solicitadores] (./media/active-directory-saas-freshservice-tutorial/IC790819.png "Novos solicitadores")

5.  Na secção do **Autor da requisição novo** , execute os seguintes passos:

    ![Autor da requisição novo] (./media/active-directory-saas-freshservice-tutorial/IC790820.png "Autor da requisição novo")

    1.  Introduza os atributos de **nome próprio** e **E-Mail** de uma conta do Azure Active Directory válida que pretende à disposição as caixas de texto relacionadas.
    2.  Clique em **Guardar**.

    >[AZURE.NOTE] Titular da conta Azure Active Directory receberão um e-mail que inclua uma ligação para confirmar a conta antes de se tornar ativa

>[AZURE.NOTE] Pode utilizar quaisquer outros FreshService utilizador conta ferramentas para a criação ou APIs fornecida pela FreshService às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Para atribuir utilizadores a FreshService, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **FreshService **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-freshservice-tutorial/IC790821.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-freshservice-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).