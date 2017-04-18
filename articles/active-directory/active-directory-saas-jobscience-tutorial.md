<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Jobscience | Microsoft Azure" 
    description="Saiba como utilizar Jobscience com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração do Azure Active Directory com Jobscience
  
Este tutorial objectivo Mostrar a integração do Azure e Jobscience.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Uma subscrição Jobscience Single Sign-On activado
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Jobscience poderão para o início de sessão único para a aplicação no site da sua empresa Jobscience (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Jobscience
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-jobscience-tutorial/IC784341.png "Cenário")
##<a name="enabling-the-application-integration-for-jobscience"></a>Ativar a integração da aplicação para Jobscience
  
Objectivo de nesta secção é como ativar a integração da aplicação para Jobscience de destaque.

###<a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Para ativar a integração da aplicação para Jobscience, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-jobscience-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-jobscience-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-jobscience-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-jobscience-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **jobscience**.

    ![Galeria de aplicação] (./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Jobscience**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Jobscience] (./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Jobscience com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Jobscience requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa Jobscience como administrador.

2.  Aceda a **configuração**.

    ![Programa de configuração] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programa de configuração")

3.  No painel de navegação à esquerda, na secção **administrar** , clique em **Gestão de domínios** para expandir a secção relacionada e, em seguida, clique no **Meu domínio** para abrir a página do **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meu domínio")

4.  Para verificar que o domínio foi configuração corretamente, certifique-se de que é "o**Passo 4 implementado para os utilizadores**" e reveja as "**Definições do meu domínio**".

    ![Domínio implementado utilizador] (./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domínio implementado utilizador")

5.  Numa janela do browser web diferente, inicie sessão no seu portal clássica Azure.

6.  Na página de integração de aplicação **Jobscience** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurar o Single Sign-On")

7.  Na página **como pretende utilizadores a iniciar sessão Jobscience** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurar o Single Sign-On")

8.  Na página **Configurar o URL da aplicação** , na caixa de texto **Jobscience URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*http://company.my.salesforce.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurar o URL da aplicação")

9.  Na página **Configurar serviço single sign-on na Jobscience** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurar o Single Sign-On")

10. No site de empresa Jobscience, clique em **Controlos de segurança**e, em seguida, clique em **Definições de Single Sign-On**.

    ![Controlos de segurança] (./media/active-directory-saas-jobscience-tutorial/IC784364.png "Controlos de segurança")

11. Na secção **Definições de Single Sign-On** , execute os seguintes passos:

    ![Definições de início de sessão únicos] (./media/active-directory-saas-jobscience-tutorial/IC781026.png "Definições de início de sessão únicos")

    1.  Selecione **SAML ativado**.
    2.  Clique em **Novo**.

12. Na caixa de diálogo **SAML único início de sessão definição Editar** , execute os seguintes passos:

    ![SAML única início de sessão definição] (./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML única início de sessão definição")

    1.  Na caixa de texto **nome** , escreva um nome para a configuração.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Jobscience** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor**
    3.  Na caixa de texto **Entidade Id** , escreva **https://salesforce-jobscience.com**
    4.  Clique em **Procurar** para carregar o certificado do Azure AD.
    5.  Como **O tipo de identidade do SAML**, selecione **afirmação contém o ID de Federação do objeto do utilizador**.
    6.  Como **Localização de identidade do SAML**, selecione a **identidade está no elemento de NameIdentfier da instrução assunto**.
    7.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Jobscience** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de identidade**
    8.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Jobscience** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de termine a sessão do fornecedor de identidade**
    9.  Clique em **Guardar**.

13. No painel de navegação à esquerda, na secção **administrar** , clique em **Gestão de domínios** para expandir a secção relacionada e, em seguida, clique no **Meu domínio** para abrir a página do **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Meu domínio")

14. Na página **Meu domínio** , na secção de **Personalização da página de início de sessão** , clique em **Editar**.

    ![Página de início de sessão de imagem corporativa] (./media/active-directory-saas-jobscience-tutorial/IC767826.png "Página de início de sessão de imagem corporativa")

15. Na página **Imagem corporativa página de início de sessão** , na secção de **Serviço de autenticação** , o nome das suas **Definições de SSO SAML** é apresentado. Selecione-o e, em seguida, clique em **Guardar**.

    ![Página de início de sessão de imagem corporativa] (./media/active-directory-saas-jobscience-tutorial/IC784366.png "Página de início de sessão de imagem corporativa")

16. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurar o Single Sign-On")
  
Para obter o SP iniciadas Single Sign-on URL de início de sessão, clique nas **definições de início de sessão** , na secção de menu de **Controlos de segurança** .

![Controlos de segurança] (./media/active-directory-saas-jobscience-tutorial/IC784368.png "Controlos de segurança")
  
Clique no perfil SSO que criou no passo anterior.  
Esta página mostra o início de sessão único no URL para a sua empresa (por exemplo, *https://companyname.my.salesforce.com?so=companyid*).
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Jobscience, tem de ser aprovisionadas para Jobscience.  
No caso de Jobscience, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Jobscience** como administrador.

2.  Aceda a configuração

    ![Programa de configuração] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programa de configuração")

3.  Aceda a **Gerir utilizadores \> utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-jobscience-tutorial/IC784369.png "Utilizadores")

4.  Clique em **novo utilizador**.

    ![Todos os utilizadores] (./media/active-directory-saas-jobscience-tutorial/IC784370.png "Todos os utilizadores")

5.  Na caixa de diálogo **Editar utilizador** , efetue os seguintes passos:

    ![O utilizador editar] (./media/active-directory-saas-jobscience-tutorial/IC784371.png "O utilizador editar")

    1.  Escreva o nome próprio, apelido, alias, e-mail, propriedades de nome e alcunhas de utilizador do utilizador do Azure AD que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Guardar**.

    >[AZURE.NOTE] Titular da conta Azure AD receberão uma mensagem de e-mail que inclui uma ligação para confirmar a conta antes de está ativado.

>[AZURE.NOTE] Pode utilizar quaisquer outros Jobscience utilizador conta ferramentas para a criação ou APIs fornecida pela Jobscience às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Para atribuir utilizadores a Jobscience, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Jobscience **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-jobscience-tutorial/IC784372.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-jobscience-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).