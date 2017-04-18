<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Panorama9 | Microsoft Azure" 
    description="Saiba como utilizar Panorama9 com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integração do Azure Active Directory com Panorama9
  
Este tutorial objectivo Mostrar a integração do Azure e Panorama9.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Panorama9 serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Panorama9 poderão para o início de sessão único para a aplicação no site da sua empresa Panorama9 (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Panorama9
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-panorama9-tutorial/IC790016.png "Cenário")
##<a name="enabling-the-application-integration-for-panorama9"></a>Ativar a integração da aplicação para Panorama9
  
Objectivo de nesta secção é como ativar a integração da aplicação para Panorama9 de destaque.

###<a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Para ativar a integração da aplicação para Panorama9, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-panorama9-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-panorama9-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-panorama9-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-panorama9-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Panorama9**.

    ![Galeria de aplicação] (./media/active-directory-saas-panorama9-tutorial/IC790017.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Panorama9**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Panorama9] (./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Panorama9 com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Panorama9 requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Panorama9** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Panorama9** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Panorama9 início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão no Panorama9 (por exemplo: "*https://dashboard.panorama9.com/saml/access/3262*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Panorama9** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde-a localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Panorama9 como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Gerir**e, em seguida, clique em **extensões**.

    ![Extensões] (./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensões")

7.  Na caixa de diálogo **extensões** , clique em **Single Sign-On**.

    ![Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790024.png "Single Sign-On")

8.  Na secção **Definições** , execute os seguintes passos:

    ![Definições] (./media/active-directory-saas-panorama9-tutorial/IC790025.png "Definições")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Panorama9** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto do **URL do fornecedor de identidade** .
    2.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **identificação de certificado** .  

        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    3.  Clique em **Guardar**.

9.  No portal do Azure AD clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Panorama9, tem de ser aprovisionadas para Panorama9.  
No caso de Panorama9, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Panorama9** como administrador.

2.  No menu no canto superior, clique em **Gerir**e, em seguida, clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-panorama9-tutorial/IC790027.png "Utilizadores")

3.  Clique em **+**.

4.  Na secção de dados de utilizador, execute os seguintes passos:

    ![Utilizadores] (./media/active-directory-saas-panorama9-tutorial/IC790028.png "Utilizadores")

    1.  Na caixa de **correio eletrónico** do texto, escreva o endereço de e-mail de um utilizador do Azure Active Directory válido que pretende aprovisionar.
    2.  Clique em **Guardar**.

>[AZURE.NOTE]Pode utilizar quaisquer outros Panorama9 utilizador conta ferramentas para a criação ou APIs fornecida pela Panorama9 às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Para atribuir utilizadores a Panorama9, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Panorama9** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-panorama9-tutorial/IC790029.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-panorama9-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).