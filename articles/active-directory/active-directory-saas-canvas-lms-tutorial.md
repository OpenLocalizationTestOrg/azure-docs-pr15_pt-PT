<properties
    pageTitle="Tutorial: Integração com o Azure Active Directory com tela LMS | Microsoft Azure" 
    description="Saiba como utilizar tela LMS com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Tutorial: Integração com o Azure Active Directory com tela LMS

Este tutorial objectivo Mostrar a integração do Azure e a tela.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino tela

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a tela poderão para o início de sessão único para a aplicação no site da sua empresa tela (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para tela
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Cenário")
##<a name="enabling-the-application-integration-for-canvas"></a>Ativar a integração da aplicação para tela

Objectivo de nesta secção é como ativar a integração da aplicação para tela de destaque.

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Para ativar a integração da aplicação para tela, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **tela**.

    ![Galeria de aplicação] (./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galeria de aplicação")

7.  No painel de resultados, selecione a **tela**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Tela] (./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Tela")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados à tela com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para tela requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação a **tela** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão tela** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Tela URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte `https://<tenant-name>.instructure.com`e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na tela** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa tela como administrador.

6.  Aceda a **cursos \> gerido contas \> Microsoft**.

    ![Tela] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Tela")

7.  No painel de navegação à esquerda, selecione a **autenticação**e, em seguida, clique em **Adicionar novo Config de SAML**.

    ![Autenticação] (./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Autenticação")

8.  Na página atual integração, execute os seguintes passos:

    ![Integração do atual] (./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Integração do atual")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na tela** , copie o valor de **ID da entidade** e, em seguida, colá-la a caixa de texto **IdP o ID da entidade** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na tela** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **Registo no URL** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na tela** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **Registo de saída de URL** .
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na tela** , copie o valor de **Alterar URL de palavra-passe** e, em seguida, colá-la a caixa de texto de **Ligação de palavra-passe de alterar** .
    5.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  A partir da lista de **Atributo de início de sessão** , selecione **NameID**.
    7.  Na lista **Formato do identificador** , selecione **emailAddress**.
    8.  Clique em **Guardar definições de autenticação**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no tela, tem de ser aprovisionadas para tela.  
No caso de tela, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **tela** .

2.  Aceda a **cursos \> gerido contas \> Microsoft**.

    ![Tela] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Tela")

3.  Clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Utilizadores")

4.  Clique em **Adicionar novo utilizador**.

    ![Utilizadores] (./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Utilizadores")

5.  Na página Adicionar um novo utilizador caixa de diálogo, efetue os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Adicionar utilizador")

    1.  Na caixa de texto **Nome completo** , escreva o nome do utilizador.
    2.  Na caixa de **correio eletrónico** do texto, escreva o endereço de e-mail do utilizador.
    3.  Na caixa de texto de **início de sessão** , escreva o endereço de e-mail do Azure AD do utilizador.
    4.  Selecione **o utilizador acerca da criação esta conta de E-Mail**.
    5.  Clique em **Adicionar utilizador**.

>[AZURE.NOTE] Pode utilizar quaisquer outros tela utilizador conta ferramentas para a criação ou APIs fornecida pela tela às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Para atribuir utilizadores a tela, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação a **tela **, clique em **atribuir aos utilizadores**.

    ![Atribuir utilizadores] (./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Atribuir utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
