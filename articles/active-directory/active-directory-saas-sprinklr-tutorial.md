<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Sprinklr | Microsoft Azure" 
    description="Saiba como utilizar Sprinklr com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração do Azure Active Directory com Sprinklr
  
Este tutorial objectivo Mostrar a integração do Azure e Sprinklr.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Sprinklr
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Sprinklr poderão para o início de sessão único para a aplicação no site da sua empresa Sprinklr (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Sprinklr
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Cenário")

##<a name="enabling-the-application-integration-for-sprinklr"></a>Ativar a integração da aplicação para Sprinklr
  
Objectivo de nesta secção é como ativar a integração da aplicação para Sprinklr de destaque.

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>Para ativar a integração da aplicação para Sprinklr, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-sprinklr-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Sprinklr**.

    ![Galeria de aplicação] (./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Sprinklr**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Sprinklr] (./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Sprinklr com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Sprinklr** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Sprinklr** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Sprinklr URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. sprinklr.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Sprinklr** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Sprinklr como administrador.

6.  Aceda a **Administração \> definições**.

    ![Administração] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administração")

7.  Aceda a **Gerir parceiro \> início de sessão único** na partir do painel esquerdo.

    ![Gerir parceiro] (./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gerir parceiro")

8.  Clique em **+ Add inícios de início de sessão único**.

    ![Inícios de início de sessão únicos] (./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Inícios de início de sessão únicos")

9.  Na página **Single Sign-on** , execute os seguintes passos:

    ![Inícios de início de sessão únicos] (./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Inícios de início de sessão únicos")

    1.  Na caixa de texto **nome** , escreva um nome para a sua configuração (por exemplo: *WAADSSOTest*).
    2.  Selecione **ativado**.
    3.  Selecione **Utilizar novo SSO certificado**.
    4.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado de fornecedor de identidade**
    6.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Sprinklr** , copie o valor de **ID do fornecedor de identidade** e, em seguida, colá-la a caixa de texto de **Id da entidade** .
    7.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Sprinklr** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de identidade** .
    8.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Sprinklr** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de termine a sessão do fornecedor de identidade** .
    9.  Com o **ID de utilizador do SAML tipo**, selecione **afirmação contém utilizador "nome de utilizador do s sprinklr.com**.
    10. Como **SAML localização do ID de utilizador**, selecione o **ID de utilizador é no elemento de nome identificador da instrução assunto**.
    11. Feche a **Guardar**.

        ![SAML] (./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para AAD os utilizadores possam iniciar sessão, tem de ser aprovisionadas para acesso dentro da aplicação Sprinklr.  
Esta secção descreve como criar contas de utilizador AAD dentro Sprinklr.

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Aprovisionamento de uma conta de utilizador no Sprinklr, execute os seguintes passos:

1.  Inicie a sessão no site da sua empresa Sprinklr como administrador.

2.  Aceda a **Administração \> definições**.

    ![Administração] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administração")

3.  Aceda a **Gerir cliente \> utilizadores** a partir do painel esquerdo.

    ![Definições] (./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Definições")

4.  Clique em **Adicionar utilizador**.

    ![Definições] (./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Definições")

5.  Na caixa de diálogo **Editar utilizador** , execute os seguintes passos:

    ![Editar utilizador] (./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Editar utilizador")

    1.  Na **mensagem de correio electrónico**, caixas de texto **nome próprio** e **Apelido** , tipo de informações de uma conta de utilizador do Azure AD que pretende aprovisionar.
    2.  Selecione a **palavra-passe desativado**.
    3.  Selecione um **idioma**.
    4.  Selecione um **tipo de utilizador**.
    5.  Clique em **Atualizar**.

    >[AZURE.IMPORTANT] **Palavra-passe desativado** tem de estar selecionada para permitir que um utilizador iniciar sessão através de um fornecedor de identidades.

6.  Aceda a **função**e, em seguida, execute os seguintes passos:

    ![Funções de parceiro] (./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Funções de parceiro")

    1.  Na lista **Global** , selecione **todos os\_permissões**.
    2.  Clique em **Atualizar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros Sprinklr utilizador conta ferramentas para a criação ou APIs fornecida pela Sprinklr aprovisionamento de contas de utilizador do Azure AD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Para atribuir utilizadores a Sprinklr, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Sprinklr **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).