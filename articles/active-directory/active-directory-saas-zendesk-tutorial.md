<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Zendesk | Microsoft Azure" 
    description="Saiba como utilizar Zendesk com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integração do Azure Active Directory com Zendesk
  
Este tutorial objectivo Mostrar a integração do Azure e Zendesk.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Zendesk
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Zendesk poderão para o início de sessão único para a aplicação no site da sua empresa Zendesk (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Zendesk
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-zendesk-tutorial/IC773083.png "Cenário")

##<a name="enabling-the-application-integration-for-zendesk"></a>Ativar a integração da aplicação para Zendesk
  
Objectivo de nesta secção é como ativar a integração da aplicação para Zendesk de destaque.

###<a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Para ativar a integração da aplicação para Zendesk, execute os seguintes passos:

1.  No Portal de gestão do Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-zendesk-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-zendesk-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-zendesk-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-zendesk-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Zendesk**.

    ![Galeria de aplicação] (./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Zendesk**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Zendesk] (./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Zendesk com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Zendesk requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal do Azure AD, na página de integração de aplicação **Zendesk** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![O início de sessão único] (./media/active-directory-saas-zendesk-tutorial/IC773086.png "O início de sessão único")

2.  Na página **como pretende utilizadores a iniciar sessão Zendesk** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurar o URL da aplicação")
  
    um. Na caixa de texto **Zendesk URL início de sessão** , escreva o seu URL utilizando o padrão seguinte:`https://<tenant-name>.zendesk.com`

    b. Clique em **seguinte**.



4.  Na página **Configurar serviço single sign-on na Zendesk** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu compiter.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Zendesk como administrador.

6.  Clique em **Admin**.

7.  No painel de navegação esquerdo, clique em **Definições**e, em seguida, clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-zendesk-tutorial/IC773089.png "Segurança")

8.  Na página de **segurança** , clique no separador **administrador e agentes** .

9.  Selecione **sessão único (SSO) e SAML**e, em seguida, selecione **SAML**.

10. No portal do Azure AD, na página **Configurar serviço single sign-on na Zendesk** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **SAML SSO URL** .

11. No portal do Azure AD, na página **Configurar serviço single sign-on na Zendesk** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **Termine a sessão de URL remoto** .

    ![O início de sessão único] (./media/active-directory-saas-zendesk-tutorial/IC773090.png "O início de sessão único")

12. Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado** .

    >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

13. Clique em **Guardar**.

14. No portal do Azure AD, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no **Zendesk**, tem de ser aprovisionadas para **Zendesk**.  
No caso de **Zendesk**, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Aprovisionamento de uma conta de utilizador para Zendesk, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Zendesk** .

2.  Selecione o separador **Lista de clientes** .

3.  Selecione o separador de **utilizador** e clique em **Adicionar**.

    ![Adicionar utilizador] (./media/active-directory-saas-zendesk-tutorial/IC773632.png "Adicionar utilizador")

4.  Escreva o endereço de e-mail de uma conta existente do Azure AD que pretende aprovisionar o e, em seguida, clique em **Guardar**.

    ![Novo utilizador] (./media/active-directory-saas-zendesk-tutorial/IC773633.png "Novo utilizador")

>[AZURE.NOTE] Pode utilizar quaisquer outros Zendesk utilizador conta ferramentas para a criação ou APIs fornecida pela Zendesk às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Para atribuir utilizadores a Zendesk, execute os seguintes passos:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicação **Zendesk **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-zendesk-tutorial/IC773094.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-zendesk-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
