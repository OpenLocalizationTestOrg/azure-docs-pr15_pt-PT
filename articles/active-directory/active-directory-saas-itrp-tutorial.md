<properties
    pageTitle="Tutorial: Integração com o Azure Active Directory com ITRP | Microsoft Azure" 
    description="Saiba como utilizar ITRP com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-itrp"></a>Tutorial: Integração com o Azure Active Directory com ITRP
  
Este tutorial objectivo Mostrar a integração do Azure e ITRP.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino ITRP
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a ITRP poderão para o início de sessão único para a aplicação no site da sua empresa ITRP (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para ITRP
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-itrp-tutorial/IC775551.png "Cenário")
##<a name="enabling-the-application-integration-for-itrp"></a>Ativar a integração da aplicação para ITRP
  
Objectivo de nesta secção é como ativar a integração da aplicação para ITRP de destaque.

###<a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>Para ativar a integração da aplicação para ITRP, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-itrp-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-itrp-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-itrp-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-itrp-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **ITRP**.

    ![Galeria de aplicação] (./media/active-directory-saas-itrp-tutorial/IC775565.png "Galeria de aplicação")

7.  No painel de resultados, selecione **ITRP**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para ITRP com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para ITRP requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **ITRP** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-itrp-tutorial/IC771709.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão ITRP** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775567.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **ITRP URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. ITRP.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-itrp-tutorial/IC775568.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na ITRP** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\ITRP.cer**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775569.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa ITRP como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Definições**.

    ![ITRP] (./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  No painel de navegação esquerdo, selecione **Single Sign-On**.

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8.  Na secção de configuração Single Sign-On, execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")

    ![Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")

    1.  Clique em **Ativar**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na ITRP** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **Termine a sessão de URL remoto** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na ITRP** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **SAML SSO URL** .
    4.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado** .
        
        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-itrp-tutorial/IC775574.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no ITRP, tem de ser aprovisionadas para ITRP.  
No caso de ITRP, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **ITRP** .

2.  Na barra de ferramentas no canto superior, clique em **registos**.

    ![Admin] (./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")

3.  No menu de pop-up, selecione **pessoas**.

    ![Pessoas] (./media/active-directory-saas-itrp-tutorial/IC775587.png "Pessoas")

4.  Clique em **Adicionar novo de uma pessoa** ("+").

    ![Admin] (./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")

5.  Na caixa de diálogo Adicionar novo de uma pessoa, execute os seguintes passos:

    ![Utilizador] (./media/active-directory-saas-itrp-tutorial/IC775577.png "Utilizador")

    1.  Escreva o **nome**, **E-Mail** de uma conta AAD válida que pretende aprovisionar.
    2.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros ITRP utilizador conta ferramentas para a criação ou APIs fornecida pela ITRP às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-itrp-perform-the-following-steps"></a>Para atribuir utilizadores a ITRP, execute os seguintes passos:

1.  No portal do Azure AD, crie uma conta de teste.

2.  Na página de integração de aplicação **ITRP **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-itrp-tutorial/IC775588.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-itrp-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).