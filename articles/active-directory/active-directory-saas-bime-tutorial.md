<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Bime | Microsoft Azure" 
    description="Saiba como utilizar Bime com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bime"></a>Tutorial: Integração com o Azure Active Directory com Bime

Este tutorial objectivo Mostrar a integração do Azure e Bime.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Bime

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Bime poderão para o início de sessão único para a aplicação no site da sua empresa Bime (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Bime
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-bime-tutorial/IC775552.png "Cenário")
##<a name="enabling-the-application-integration-for-bime"></a>Ativar a integração da aplicação para Bime

Objectivo de nesta secção é como ativar a integração da aplicação para Bime de destaque.

###<a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Para ativar a integração da aplicação para Bime, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-bime-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-bime-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-bime-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-bime-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Bime**.

    ![Galeria de aplicação] (./media/active-directory-saas-bime-tutorial/IC775553.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Bime**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Bime] (./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Bime com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Bime requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Bime** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bime-tutorial/IC771709.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Bime** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775555.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Bime URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. Bimeapp.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-bime-tutorial/IC775556.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Bime** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\Bime.cer**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775557.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Bime como administrador.

6.  Na barra de ferramentas, clique em **Admin**e, em seguida, **conta**.

    ![Admin] (./media/active-directory-saas-bime-tutorial/IC775558.png "Admin")

7.  Na página de configuração da conta, efetue os seguintes passos:

    ![Configurar o Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775559.png "Configurar o Single Sign-On")

    1.  Selecione **Ativar SAML autenticação**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Bime** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto de **Início de sessão de URL remoto** .
    3.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  Clique em **Guardar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-bime-tutorial/IC775560.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Bime, tem de ser aprovisionadas para Bime.  
No caso de Bime, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Bime** .

2.  Na barra de ferramentas, clique em **Admin**e, em seguida, **os utilizadores**.

    ![Admin] (./media/active-directory-saas-bime-tutorial/IC775561.png "Admin")

3.  Na **Lista de utilizadores**, clique em **Adicionar novo utilizador** ("+").

    ![Utilizadores] (./media/active-directory-saas-bime-tutorial/IC775562.png "Utilizadores")

4.  Na página de diálogo de **Detalhes do utilizador** , efetue os seguintes passos:

    ![Detalhes do utilizador] (./media/active-directory-saas-bime-tutorial/IC775563.png "Detalhes do utilizador")

    1.  Introduza o nome próprio, apelido, início de sessão, E-Mail de uma conta AAD válida que pretende aprovisionar.
    2.  Clique em Guardar.

>[AZURE.NOTE] Pode utilizar quaisquer outros Bime utilizador conta ferramentas para a criação ou APIs fornecida pela Bime às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-bime-perform-the-following-steps"></a>Para atribuir utilizadores a Bime, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Bime **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-bime-tutorial/IC775564.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-bime-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
