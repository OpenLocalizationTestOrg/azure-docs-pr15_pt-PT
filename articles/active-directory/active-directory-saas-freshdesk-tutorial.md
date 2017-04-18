<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Freshdesk | Microsoft Azure" 
    description="Saiba como utilizar Freshdesk com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integração com o Azure Active Directory com Freshdesk
  
Este tutorial objectivo Mostrar a integração do Azure e Freshdesk.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Freshdesk
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Freshdesk poderão para o início de sessão único para a aplicação no site da sua empresa Freshdesk (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Freshdesk
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Cenário")
##<a name="enabling-the-application-integration-for-freshdesk"></a>Ativar a integração da aplicação para Freshdesk
  
Objectivo de nesta secção é como ativar a integração da aplicação para Freshdesk de destaque.

###<a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Para ativar a integração da aplicação para Freshdesk, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-freshdesk-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Freshdesk**.

    ![Galeria de aplicação] (./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Freshdesk**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Freshdesk] (./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Freshdesk com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Freshdesk requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Freshdesk** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Freshdesk** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Freshdesk URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. Freshdesk.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Freshdesk** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\Freshdesk.cer**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Freshdesk como administrador.

6.  No menu no canto superior, clique em **Admin**.

    ![Admin] (./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

7.  No separador **Definições gerais** , clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Segurança")

8.  Na secção **segurança** , execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign-On")

    1.  **Para **Único início de sessão no (SSO)**, selecione.**
    2.  Selecione **SAML SSO**.
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Freshdesk** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do SAML** .
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Freshdesk** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .
    5.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado de segurança** .  

        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Freshdesk, tem de ser aprovisionadas para Freshdesk.  
No caso de Freshdesk, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Freshdesk** .

2.  No menu no canto superior, clique em **Admin**.

    ![Admin] (./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3.  No separador **Definições gerais** , clique em **agentes**.

    ![Agentes] (./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agentes")

4.  Clique em **novo agente**.

    ![Novo agente] (./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Novo agente")

5.  Na caixa de diálogo informações do agente, execute os seguintes passos:

    ![Informações do agente] (./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Informações do agente")

    1.  Na caixa de texto **Nome completo** , escreva o nome da conta Azure AD que pretende aprovisionar.
    2.  Na caixa de **correio eletrónico** do texto, escreva o endereço de e-mail do Azure AD da conta Azure AD que pretende aprovisionar.
    3.  Na caixa de texto **título** , escreva o título da conta Azure AD que pretende aprovisionar.
    4.  Selecione a **função agentes**e, em seguida, clique em **atribuir**.
    5.  Clique em **Guardar**.
    
        >[AZURE.NOTE] Titular da conta Azure AD receberão uma mensagem de e-mail que inclui uma ligação para confirmar a conta antes de está ativado.

>[AZURE.NOTE] Pode utilizar quaisquer outros Freshdesk utilizador conta ferramentas para a criação ou APIs fornecida pela Freshdesk às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Para atribuir utilizadores a Freshdesk, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Freshdesk **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).