<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com o correio eletrónico de Zoho | Microsoft Azure" 
    description="Saiba como utilizar o correio Zoho com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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
    ms.author="markvi" />

#<a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Tutorial: Integração do Azure Active Directory com o correio eletrónico de Zoho
  
Este tutorial objectivo Mostrar a integração do Azure e Zoho correio.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Zoho correio
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Zoho correio poderão para o início de sessão único para a aplicação no site da sua empresa Zoho Mail (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o correio Zoho
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Cenário")

##<a name="enabling-the-application-integration-for-zoho-mail"></a>Ativar a integração de aplicação para o correio Zoho
  
Objectivo de nesta secção é como ativar a integração da aplicação de correio de Zoho de destaque.

###<a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Para ativar a integração da aplicação de correio de Zoho, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Zoho correio**.

    ![Galeria de aplicação] (./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Correio Zoho**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Zoho correio] (./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho correio")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados Mail Zoho com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **Correio de Zoho** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Zoho correio** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurar o URL da aplicação")

    um. Na caixa de texto **Zoho correio início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte:`http://<company name>.ZohoMail.com`

    b. Clique em **seguinte**.


4.  Na página **Configurar serviço single sign-on na Zoho correio** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Zoho correio como administrador.

6.  Vá para o **Painel de controlo**.

    ![Painel de controlo] (./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Painel de controlo")

7.  Clique no separador **SAML autenticação** .

    ![Autenticação do SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Autenticação do SAML")

8.  Na secção de **Detalhes de autenticação SAML** , execute os seguintes passos:

    ![Detalhes de autenticação SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Detalhes de autenticação SAML")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Zoho correio** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Zoho correio** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Zoho correio** , copie o valor de **Alterar URL de palavra-passe** e, em seguida, colá-la a caixa de texto **Alterar URL de palavra-passe** .
    4.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto **PublicKey** .
    6.  Como **algoritmo de**, selecione **RSA**.
    7.  Clique em **OK**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD iniciar sessão numa mensagem de E-Mail Zoho, tem de ser aprovisionadas numa mensagem de E-Mail Zoho.  
No caso de correio de Zoho, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Zoho correio** como administrador.

2.  Aceda a **Painel de controlo \> correio e documentos**.

3.  Aceda a **Detalhes do utilizador \> adicionar utilizador**.

    ![Adicionar utilizador] (./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Adicionar utilizador")

4.  Na caixa de diálogo **Adicionar utilizadores** , execute os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Adicionar utilizador")

    1.  Escreva o **nome próprio**, **Apelido**, **ID de E-Mail**, **palavra-passe** de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **OK**.  

        >[AZURE.NOTE] Titular da conta Azure Active Directory receberão um e-mail com uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar qualquer outras correio Zoho utilizador conta ferramentas para a criação ou APIs fornecida pela Zoho correio às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Para atribuir aos utilizadores para Zoho Mail, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação de **Correio de Zoho **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).