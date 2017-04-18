<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com NetDocuments | Microsoft Azure" 
    description="Saiba como utilizar NetDocuments com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Tutorial: Integração do Azure Active Directory com NetDocuments
  
Este tutorial objectivo Mostrar a integração do Azure e NetDocuments.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino NetDocuments
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a NetDocuments poderão para o início de sessão único para a aplicação no site da sua empresa NetDocuments (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para NetDocuments
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Cenário")
##<a name="enabling-the-application-integration-for-netdocuments"></a>Ativar a integração da aplicação para NetDocuments
  
Objectivo de nesta secção é como ativar a integração da aplicação para NetDocuments de destaque.

###<a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>Para ativar a integração da aplicação para NetDocuments, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-netdocuments-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **NetDocuments**.

    ![Galeria de aplicação] (./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galeria de aplicação")

7.  No painel de resultados, selecione **NetDocuments**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![NetDocuments] (./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para NetDocuments com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para NetDocuments requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **NetDocuments** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão NetDocuments** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurar o URL da aplicação")

    1.  Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação NetDocuments (por exemplo: "*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*").
    2.  Na caixa de texto **NetDocuments resposta URL** , escreva o mesmo valor que escreveu para a caixa de texto de **Início de sessão no URL** .  

        >[AZURE.NOTE]Pode encontrar o valor correto no final da caixa de diálogo de **Identidade federada** (consulte a captura de ecrã do passo 9).

    3.  Clique em **seguinte**

4.  Na página **Configurar serviço single sign-on na NetDocuments** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa NetDocuments como administrador.

6.  Aceda a **Admin**.

7.  Clique em **Adicionar e remover utilizadores e grupos**.

    ![Repositório] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositório")

8.  Clique em **Configurar avançadas opções de autenticação**.

    ![Configurar opções de autenticação de avançadas] (./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configurar opções de autenticação de avançadas")

9.  Na caixa de diálogo **A identidade federado** , execute os seguintes passos:

    ![Federado Identitty] (./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Federado Identitty")

    1.  Com o **tipo de servidor de identidade federados**, selecione **Os serviços de Federação do Active Directory**.
    2.  Clique em **Escolher ficheiro**para carregar o ficheiro transferido metadados.
    3.  Clique em **OK**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no NetDocuments, tem de ser aprovisionadas para NetDocuments.  
No caso de NetDocuments, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  De início de sessão no site da sua empresa **NetDocuments** como administrador.

2.  No menu no canto superior, clique em **Admin**.

    ![Admin] (./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Admin")

3.  Clique em **Adicionar e remover utilizadores e grupos**.

    ![Repositório] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Repositório")

4.  Na caixa de texto **Endereço de correio eletrónico** , escreva o endereço de e-mail de uma conta do Azure Active Directory válida que pretende aprovisionar o e, em seguida, clique em **Adicionar utilizador**.

    ![Endereço de e-mail] (./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Endereço de e-mail")

    >[AZURE.NOTE]Titular da conta Azure Active Directory receberão uma mensagem de e-mail que inclui uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE]Pode utilizar quaisquer outros NetDocuments utilizador conta ferramentas para a criação ou APIs fornecida pela NetDocuments às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>Para atribuir utilizadores a NetDocuments, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **NetDocuments **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).