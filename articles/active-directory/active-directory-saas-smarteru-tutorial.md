<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com SmarterU | Microsoft Azure" 
    description="Saiba como utilizar SmarterU com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutorial: Integração com o Azure Active Directory com SmarterU
  
Este tutorial objectivo Mostrar a integração do Azure e SmarterU.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino SmarterU
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a SmarterU poderão para o início de sessão único para a aplicação no site da sua empresa SmarterU (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para SmarterU
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-smarteru-tutorial/IC777320.png "Cenário")

##<a name="enabling-the-application-integration-for-smarteru"></a>Ativar a integração da aplicação para SmarterU
  
Objectivo de nesta secção é como ativar a integração da aplicação para SmarterU de destaque.

###<a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Para ativar a integração da aplicação para SmarterU, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-smarteru-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-smarteru-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-smarteru-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-smarteru-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **SmarterU**.

    ![Fallery de aplicação] (./media/active-directory-saas-smarteru-tutorial/IC777321.png "Fallery de aplicação")

7.  No painel de resultados, selecione **SmarterU**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![SmarterU] (./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para SmarterU com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **SmarterU** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão SmarterU** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurar o Single Sign-On")

3.  **Configurar serviço single sign-on na SmarterU** página, para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, o ficheiro de dados localmente como **c:\\SmarterUMetaData.cer**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurar o Single Sign-On")

4.  Numa janela do browser web diferente, inicie sessão no site da sua empresa SmarterU como administrador.

5.  Na barra de ferramentas no canto superior, clique em **Definições da conta**.

    ![Definições da conta] (./media/active-directory-saas-smarteru-tutorial/IC777326.png "Definições da conta")

6.  Na página de configuração da conta, efetue os seguintes passos:

    ![Autorização externa] (./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorização externa")

    1.  Selecione **Ativar autorização externa**.
    2.  Na secção de **Controlo do modelo global de início de sessão** , selecione o separador **SmarterU** .
    3.  Na secção de **Início de sessão do utilizador predefinido** , selecione o separador **SmarterU** .
    4.  Selecione **Ativar Okta**.
    5.  Copie o conteúdo do ficheiro transferido metadados e, em seguida, colá-la a caixa de texto **Okta metadados** .
    6.  Clique em **Guardar**.

7.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no SmarterU, tem de ser aprovisionadas para SmarterU.  
No caso de SmarterU, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **SmarterU** .

2.  Aceda a **utilizadores**.

3.  Na secção de utilizador, execute os seguintes passos:

    ![Novo utilizador] (./media/active-directory-saas-smarteru-tutorial/IC777329.png "Novo utilizador")

    1.  Clique em **+ utilizador**.
    2.  Escreva os valores de atributo relacionado da conta de utilizador do Azure AD as seguintes caixas de texto: **de E-Mail principal**, **ID do funcionário**, **palavra-passe**, **Verifique se a palavra-passe**, **dado nome**, **Apelido**.
    3.  Clique em **ativo**.
    4.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros SmarterU utilizador conta ferramentas para a criação ou APIs fornecida pela SmarterU às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Para atribuir utilizadores a SmarterU, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **SmarterU **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-smarteru-tutorial/IC777330.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-smarteru-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).