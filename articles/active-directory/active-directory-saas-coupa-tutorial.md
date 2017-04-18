<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Coupa | Microsoft Azure" 
    description="Saiba como utilizar Coupa com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Integração do Azure Active Directory com Coupa

Este tutorial objectivo Mostrar a integração do Azure e Coupa.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Coupa serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Coupa poderão para o início de sessão único para a aplicação utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Coupa
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-coupa-tutorial/IC791897.png "Cenário")
##<a name="enabling-the-application-integration-for-coupa"></a>Ativar a integração da aplicação para Coupa

Objectivo de nesta secção é como ativar a integração da aplicação para Coupa de destaque.

###<a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>Para ativar a integração da aplicação para Coupa, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-coupa-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-coupa-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-coupa-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-coupa-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Coupa**.

    ![Galeria de aplicação] (./media/active-directory-saas-coupa-tutorial/IC791898.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Coupa**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Coupa] (./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Coupa com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Coupa requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa Coupa como administrador.

2.  Aceda a **configuração \> controlo de segurança**.

    ![Controlos de segurança] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Controlos de segurança")

3.  Para transferir o ficheiro de metadados Coupa para o seu computador, clique em **Transferir e importar SP metadados**.

    ![Metadados do Coupa SP] (./media/active-directory-saas-coupa-tutorial/IC791901.png "Metadados do Coupa SP")

4.  Numa janela do browser diferente, inicie sessão no portal do Azure clássica.

5.  Na página de integração de aplicação **Coupa** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurar o Single Sign-On")

6.  Na página **como pretende utilizadores a iniciar sessão Coupa** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurar o Single Sign-On")

7.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurar o URL da aplicação")

    1.  Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação Coupa (por exemplo: "*http://company.Coupa.com*").
    2.  Abra o seu ficheiro de metadados Coupa transferido e, em seguida, copie o **AssertionConsumerService índice/URL**.
    3.  Na caixa de texto **Coupa resposta URL** , cole o valor de **AssertionConsumerService índice/URL** .
    4.  Clique em **seguinte**.

8.  Na página **Configurar serviço single sign-on na Coupa** , para transferir o ficheiro de metadados, clique em **transferir metadados**e, em seguida, guarde o ficheiro localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurar o Single Sign-On")

9.  No site de empresa Coupa, aceda a **configuração \> controlo de segurança**.

    ![Controlos de segurança] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Controlos de segurança")

10. Na secção **Inicie sessão com as credenciais de Coupa** , execute os seguintes passos:

    ![Inicie sessão com as credenciais de Coupa] (./media/active-directory-saas-coupa-tutorial/IC791906.png "Inicie sessão com as credenciais de Coupa")

    1.  Selecione **Inicie sessão com SAML**.
    2.  Clique em **Procurar** para carregar o seu ficheiro de metadados Azure Active transferido.
    3.  Clique em **Guardar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Coupa, tem de ser aprovisionadas para Coupa.  
No caso de Coupa, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Coupa** como administrador.

2.  No menu no canto superior, clique em **configuração**e, em seguida, clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-coupa-tutorial/IC791908.png "Utilizadores")

3.  Clique em **Criar**.

    ![Criar utilizadores] (./media/active-directory-saas-coupa-tutorial/IC791909.png "Criar utilizadores")

4.  Na secção **Criar do utilizador** , execute os seguintes passos:

    ![Detalhes do utilizador] (./media/active-directory-saas-coupa-tutorial/IC791910.png "Detalhes do utilizador")

    1.  Escreva o **início de sessão**, **nome próprio**, **Apelido**, **ID de início de sessão único**, atributos de **correio eletrónico** de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Criar**.

    >[AZURE.NOTE] Titular da conta Azure Active Directory receberão uma mensagem de e-mail com uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Coupa utilizador conta ferramentas para a criação ou APIs fornecida pela Coupa às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-coupa-perform-the-following-steps"></a>Para atribuir utilizadores a Coupa, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Coupa **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-coupa-tutorial/IC791911.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-coupa-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
