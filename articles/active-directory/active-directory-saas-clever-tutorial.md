<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Clever | Microsoft Azure" 
    description="Saiba como utilizar Clever com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>Tutorial: Integração do Azure Active Directory com Clever

Este tutorial objectivo Mostrar a integração do Azure e Clever. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino inteligente

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Clever poderão para o início de sessão único para a aplicação no site da sua empresa inteligente (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Clever
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-clever-tutorial/IC798977.png "Cenário")
##<a name="enabling-the-application-integration-for-clever"></a>Ativar a integração da aplicação para Clever

Objectivo de nesta secção é como ativar a integração da aplicação para Clever de destaque.

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Para ativar a integração da aplicação para Clever, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-clever-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-clever-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-clever-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-clever-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Clever**.

    ![Galeria de aplicação] (./media/active-directory-saas-clever-tutorial/IC798978.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Clever**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Inteligente] (./media/active-directory-saas-clever-tutorial/IC798979.png "Inteligente")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Clever com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
A aplicação inteligente espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** .  
A captura de ecrã seguinte mostra um exemplo para esta situação.

![Atributos] (./media/active-directory-saas-clever-tutorial/IC798980.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Clever** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC784682.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Clever** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798981.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Inteligente início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação inteligente (por exemplo: *https://clever.com/in/azsandbox*) e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-clever-tutorial/IC798982.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Clever** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798983.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa inteligente como administrador.

6.  Na barra de ferramentas, clique em **Login instantâneas**.

    ![Início de sessão instantâneo] (./media/active-directory-saas-clever-tutorial/IC798984.png "Início de sessão instantâneo")

7.  Na página de **Início de sessão instantânea** , execute os seguintes passos:

    ![Início de sessão instantâneo] (./media/active-directory-saas-clever-tutorial/IC798985.png "Início de sessão instantâneo")

    1.  Escreva o **URL de início de sessão**.  

        >[AZURE.NOTE] O **URL de início de sessão** é um valor personalizado.
Pode obter o valor real a partir da sua equipa de suporte inteligente.

    2.  Como o **Sistema de identidade**, selecione **ADFS**.
    3.  Clique em **Guardar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-clever-tutorial/IC798986.png "Configurar o Single Sign-On")

9.  No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-clever-tutorial/IC795920.png "Atributos")

10. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    ![atributos token SAML] (./media/active-directory-saas-clever-tutorial/IC795921.png "atributos token SAML")

  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|clever.Student.credentials.District\_nome de utilizador|User.userPrincipalName|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar utilizador atributo**.
    2.  Na caixa de texto **Nome do atributo** , escreva o nome do atributo apresentado para nessa linha.
    3.  Na caixa de texto **Valor do atributo** , selecione o valor do atributo apresentado para nessa linha.
    4.  Clique em **concluído**.

11. Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Clever, tem de ser aprovisionadas para Clever.  
No caso de Clever, aprovisionamento é uma tarefa manual que precisa de ser executado pela equipa de suporte inteligente.

>[AZURE.NOTE] Pode utilizar quaisquer outras ferramentas do utilizador inteligente conta criação ou APIs fornecida pela Clever às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>Para atribuir utilizadores a Clever, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Clever **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-clever-tutorial/IC798987.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-clever-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
