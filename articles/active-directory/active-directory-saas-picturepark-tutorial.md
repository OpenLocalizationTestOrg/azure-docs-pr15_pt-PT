<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Picturepark | Microsoft Azure" 
    description="Saiba como utilizar Picturepark com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração com o Azure Active Directory com Picturepark
  
Este tutorial objectivo Mostrar a integração do Azure e Picturepark.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Picturepark
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Picturepark poderão para o início de sessão único para a aplicação no site da sua empresa Picturepark (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Picturepark
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-picturepark-tutorial/IC795055.png "Cenário")

##<a name="enabling-the-application-integration-for-picturepark"></a>Ativar a integração da aplicação para Picturepark
  
Objectivo de nesta secção é como ativar a integração da aplicação para Picturepark de destaque.

###<a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>Para ativar a integração da aplicação para Picturepark, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-picturepark-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-picturepark-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-picturepark-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-picturepark-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Picturepark**.

    ![Galeria de aplicação] (./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Picturepark**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Picturepark] (./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Picturepark com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Picturepark requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)...

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Picturepark** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Picturepark** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Picturepark início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*http://company.picturepark.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Picturepark** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Picturepark como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Ferramentas administrativas**e, em seguida, clique em **Management Console**.

    ![Consola de gestão] (./media/active-directory-saas-picturepark-tutorial/IC795062.png "Consola de gestão")

7.  Clique em **autenticação**e, em seguida, clique em **fornecedores de identidade**.

    ![Autenticação] (./media/active-directory-saas-picturepark-tutorial/IC795063.png "Autenticação")

8.  Na secção de **configuração de fornecedor de identidade** , execute os seguintes passos:

    ![Configuração de fornecedor de identidade] (./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configuração de fornecedor de identidade")

    1.  Clique em **Adicionar**.
    2.  Escreva um nome para a configuração.
    3.  Selecione **Definir como predefinido**.
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Picturepark** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto **Emissor URI** .
    5.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Impressão de recorrer emissor fidedignos** .  

        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    6.  Clique em **JoinDefaultUsersGroup**.
    7.  Para definir o atributo **Emailaddress** na caixa de texto **afirmação** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
        ![Configuração] (./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuração")
    8.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Picturepark, tem de ser aprovisionadas para Picturepark.  
No caso de Picturepark, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **Picturepark** .

2.  Na barra de ferramentas no canto superior, clique em **Ferramentas administrativas**e, em seguida, clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-picturepark-tutorial/IC795067.png "Utilizadores")

3.  No separador **Descrição geral de utilizadores** , clique em **Novo**.

    ![Gestão de utilizadores] (./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gestão de utilizadores")

4.  Na caixa de diálogo **Criar utilizador** , efetue os seguintes passos:

    ![Criar utilizador] (./media/active-directory-saas-picturepark-tutorial/IC795069.png "Criar utilizador")

    1.  Tipo de: **endereço de E-Mail**, **palavra-passe**, **Confirme a palavra-passe**, **nome próprio**, **Apelido**, **empresa**, **país**, **ZIP**, **Cidade** de um utilizador válido para o Azure Active Directory que pretende aprovisionar to para as caixas de texto relacionadas.
    2.  Selecione um **idioma**.
    3.  Clique em **Criar**.

>[AZURE.NOTE]Pode utilizar quaisquer outros Picturepark utilizador conta ferramentas para a criação ou APIs fornecida pela Picturepark às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Para atribuir utilizadores a Picturepark, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Picturepark **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-picturepark-tutorial/IC795070.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-picturepark-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).