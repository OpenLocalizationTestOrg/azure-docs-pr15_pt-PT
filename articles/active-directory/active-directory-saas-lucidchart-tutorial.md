<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Lucidchart | Microsoft Azure" 
    description="Saiba como utilizar Lucidchart com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Tutorial: Integração do Azure Active Directory com Lucidchart
  
Este tutorial objectivo Mostrar a integração do Azure e Lucidchart.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Lucidchart serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Lucidchart poderão para o início de sessão único para a aplicação no site da sua empresa Lucidchart (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Lucidchart
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Cenário")
##<a name="enabling-the-application-integration-for-lucidchart"></a>Ativar a integração da aplicação para Lucidchart
  
Objectivo de nesta secção é como ativar a integração da aplicação para Lucidchart de destaque.

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Para ativar a integração da aplicação para Lucidchart, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-lucidchart-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Lucidchart**.

    ![Galeria de aplicação] (./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Lucidchart**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Lucidchart] (./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Lucidchart com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Lucidchart** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Lucidchart** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Lucidchart início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão sua aplicação Lucidchart (por exemplo: "*https://chart2.office.lucidchart.com/saml/sso/azure*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Lucidchart** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de dados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Lucidchart como administrador.

6.  No menu no canto superior, clique em **equipa**.

    ![Equipa] (./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Equipa")

7.  Clique em **aplicação \> gerir SAML**.

    ![Gerir SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gerir SAML")

8.  Na página de diálogo **Definições de autenticação SAML** , execute os seguintes passos:

    1.  Selecione **Ativar a autenticação de SAML**e, em seguida, clique em **opcional**.
        ![Definições de autenticação SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Definições de autenticação SAML")
    2.  Na caixa de texto **domínio** , escreva o seu domínio e, em seguida, clique em **Alterar certificado**.
        ![Certificado de alteração] (./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Certificado de alteração")
    3.  Abra o seu ficheiro transferido metadados, copie o conteúdo e, em seguida, colá-la a caixa de texto de **Metadados carregar** .
        ![Carregar metadados] (./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Carregar metadados")
    4.  Selecione **Adicionar automaticamente um novo utilizador para a equipa**e, em seguida, clique em **Guardar alterações**.
        ![Guardar alterações] (./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Guardar alterações")

9.  Selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item de ação que pode configurar Lucidchart do aprovisionamento de utilizador.  
Quando um utilizador atribuído tenta que inicie sessão no Lucidchart utilizando o painel de acesso, Lucidchart verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo Lucidchart.
##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Para atribuir utilizadores a Lucidchart, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Lucidchart **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).