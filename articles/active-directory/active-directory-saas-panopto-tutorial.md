<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Panopto | Microsoft Azure" 
    description="Saiba como utilizar Panopto com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Integração do Azure Active Directory com Panopto
  
Este tutorial objectivo Mostrar a integração do Azure e Panopto.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Panopto
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Panopto poderão para o início de sessão único para a aplicação no site da sua empresa Panopto (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Panopto
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-panopto-tutorial/IC777665.png "Cenário")
##<a name="enabling-the-application-integration-for-panopto"></a>Ativar a integração da aplicação para Panopto
  
Objectivo de nesta secção é como ativar a integração da aplicação para Panopto de destaque.

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Para ativar a integração da aplicação para Panopto, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-panopto-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-panopto-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-panopto-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-panopto-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Panopto**.

    ![Galeria de Appkication] (./media/active-directory-saas-panopto-tutorial/IC777666.png "Galeria de Appkication")

7.  No painel de resultados, selecione **Panopto**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Panopto] (./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Panopto com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Panopto** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Panopto** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Panopto URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. Panopto.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Panopto** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Panopto como administrador.

6.  Na barra de ferramentas no lado esquerdo, clique em **sistema**e, em seguida, clique em **Fornecedores de identidade**.

    ![Sistema] (./media/active-directory-saas-panopto-tutorial/IC777670.png "Sistema")

7.  Clique em **Adicionar um fornecedor**.

    ![Fornecedores de identidade] (./media/active-directory-saas-panopto-tutorial/IC777671.png "Fornecedores de identidade")

8.  Na secção de fornecedor SAML, execute os seguintes passos:

    ![Configuração de SaaS] (./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuração de SaaS")

    1.  Na lista **Tipo de fornecedor** , selecione **SAML20**
    2.  Na caixa de **Nome da instância de** texto, escreva um nome para a instância.
    3.  Na caixa de texto **Descrição amigável** , escreva uma descrição amigável.
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Panopto** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor** .
    5.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Panopto** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto **Dar toques o Url da página** .
    6.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    7.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto de **Chave pública**
    8.  Clique em **Guardar**.
        ![Guardar] (./media/active-directory-saas-panopto-tutorial/IC777673.png "Guardar")

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item de ação que pode configurar Panopto do aprovisionamento de utilizador.  
Quando um utilizador atribuído tenta que inicie sessão no Panopto utilizando o painel de acesso, Panopto verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo Panopto.

>[AZURE.NOTE]Pode utilizar quaisquer outros Panopto utilizador conta ferramentas para a criação ou APIs fornecida pela Panopto aprovisionamento de contas de utilizador do Azure AD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Para atribuir utilizadores a Panopto, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Panopto **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-panopto-tutorial/IC777675.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-panopto-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).