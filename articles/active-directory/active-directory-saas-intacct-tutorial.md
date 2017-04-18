<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Intacct | Microsoft Azure" 
    description="Saiba como utilizar Intacct com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: Integração do Azure Active Directory com Intacct
  
Este tutorial objectivo Mostrar a integração do Azure e Intacct.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Intacct
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Intacct poderão para o início de sessão único para a aplicação no site da sua empresa Intacct (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Intacct
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-intacct-tutorial/IC790030.png "Cenário")
##<a name="enabling-the-application-integration-for-intacct"></a>Ativar a integração da aplicação para Intacct
  
Objectivo de nesta secção é como ativar a integração da aplicação para Intacct de destaque.

###<a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>Para ativar a integração da aplicação para Intacct, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-intacct-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-intacct-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-intacct-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-intacct-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Intacct**.

    ![Galeria de aplicação] (./media/active-directory-saas-intacct-tutorial/IC790031.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Intacct**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Intacct] (./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Intacct com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Intacct** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Intacct** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Intacct início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*https://Intacct.com/company*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Intacct** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Intacct como administrador.

6.  Clique no separador **empresa** e, em seguida, clique em **Informações da empresa**.

    ![Empresa] (./media/active-directory-saas-intacct-tutorial/IC790037.png "Empresa")

7.  Clique no separador **segurança** e, em seguida, clique em **Editar**.

    ![Segurança] (./media/active-directory-saas-intacct-tutorial/IC790038.png "Segurança")

8.  Na secção de **início de sessão único (SSO)** , execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign-On")

    1.  Selecione **Ativar o início de sessão único**.
    2.  Com o **tipo de fornecedor de identidade**, selecione **SAML 2.0**.
    3.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na Intacct** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **Emissor URL** .
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Intacct** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    5.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.
        
        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **certificado**
    7.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Intacct, tem de ser aprovisionadas para Intacct.  
No caso de Intacct, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Intacct** .

2.  Clique no separador **empresa** e, em seguida, clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-intacct-tutorial/IC790041.png "Utilizadores")

3.  Clique no separador de **Adicionar**

    ![Adicionar] (./media/active-directory-saas-intacct-tutorial/IC790042.png "Adicionar")

4.  Na secção **Informações do utilizador** , execute os seguintes passos:

    ![Informações do utilizador] (./media/active-directory-saas-intacct-tutorial/IC790043.png "Informações do utilizador")

    1.  Escreva o **ID de utilizador**, o **Apelido**, **nome próprio**, o **endereço de E-Mail**, o **título** e o **telefone** de uma conta do Azure AD que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Selecione os **privilégios de administrador** de uma conta do Azure AD que pretende aprovisionar.
    3.  Clique em **Guardar**.
        
        >[AZURE.NOTE] Titular da conta AAD irá receber uma mensagem de e-mail e seguir uma ligação para confirmar a respetiva conta antes que se torne ativo.

>[AZURE.NOTE] Pode utilizar quaisquer outros Intacct utilizador conta ferramentas para a criação ou APIs fornecida pela Intacct às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-intacct-perform-the-following-steps"></a>Para atribuir utilizadores a Intacct, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Intacct **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-intacct-tutorial/IC790044.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-intacct-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).