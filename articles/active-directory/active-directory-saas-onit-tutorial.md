<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Onit | Microsoft Azure" 
    description="Saiba como utilizar Onit com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-onit"></a>Tutorial: Integração do Azure Active Directory com Onit
  
Este tutorial objectivo Mostrar a integração do Azure e Onit.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Onit serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Onit poderão para o início de sessão único para a aplicação no site da sua empresa Onit (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Onit
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-onit-tutorial/IC791166.png "Cenário")
##<a name="enabling-the-application-integration-for-onit"></a>Ativar a integração da aplicação para Onit
  
Objectivo de nesta secção é como ativar a integração da aplicação para Onit de destaque.

###<a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Para ativar a integração da aplicação para Onit, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-onit-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-onit-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-onit-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-onit-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Onit**.

    ![Galeria de aplicação] (./media/active-directory-saas-onit-tutorial/IC791167.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Onit**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Onit] (./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Onit com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Onit requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).
  
A aplicação Onit espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** .  
A captura de ecrã seguinte mostra um exemplo para esta situação.

![Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **Onit** , no menu na parte superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-onit-tutorial/IC791169.png "Atributos")

2.  Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    
  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|nome|User.userPrincipalName|
  	|mensagem de correio electrónico|User.Mail|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar utilizador atributo**.
    2.  Na caixa de texto **Nome do atributo** , escreva o nome do atributo apresentado para nessa linha.
    3.  A partir da lista de **Valor do atributo** , selecione o valor do atributo apresentado para nessa linha.
    4.  Clique em **concluído**.

3.  Clique em **Aplicar alterações**.

4.  No seu browser, clique em **voltar** a caixa de diálogo de **Início rápido** para voltar a abrir.

5.  Clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791170.png "Configurar o Single Sign-On")

6.  Na página **como pretende utilizadores a iniciar sessão Onit** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791171.png "Configurar o Single Sign-On")

7.  Na página **Configurar o URL da aplicação** , na caixa de texto **Onit início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão sua aplicação Onit (por exemplo: "*https://ms-sso-test.onit.com*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-onit-tutorial/IC791172.png "Configurar o URL da aplicação")

8.  Na página **Configurar serviço single sign-on na Onit** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791173.png "Configurar o Single Sign-On")

9.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Onit como administrador.

10. No menu no canto superior, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-onit-tutorial/IC791174.png "Administração")

11. Clique em **Editar Corporation**.

    ![Editar Corporation] (./media/active-directory-saas-onit-tutorial/IC791175.png "Editar Corporation")

12. Clique no separador **segurança** .

    ![Informações da empresa de editar] (./media/active-directory-saas-onit-tutorial/IC791176.png "Informações da empresa de editar")

13. No separador **segurança** , execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")

    1.  Como **Estratégia de autenticação**, selecione **início de sessão e palavra-passe**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Onit** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de destino Idp** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Onit** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **Idp termine a sessão URL** .
    4.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de orientação do diapositivo notas Idp (SHA1)** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Como **SSO tipo**, selecione **SAML**.
    6.  Na caixa de **texto do botão de início de sessão SSO** do texto, escreva um texto do botão que gostar.
    7.  Selecione **início de sessão com SSO: obrigatório para domínios/utilizadores que se seguem**, escreva o endereço de e-mail de um utilizador de teste para a caixa de texto relacionada e, em seguida, clique em **Atualizar**.
        ![Editar Corporation] (./media/active-directory-saas-onit-tutorial/IC791178.png "Editar Corporation")

14. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-onit-tutorial/IC791179.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Onit, tem de ser aprovisionadas para Onit.  
No caso de Onit, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Onit** como administrador.

2.  Clique em **Adicionar utilizador**.

    ![Administração] (./media/active-directory-saas-onit-tutorial/IC791180.png "Administração")

3.  Na página de diálogo **Adicionar utilizador** , execute os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-onit-tutorial/IC791181.png "Adicionar utilizador")

    1.  Escreva o **nome** e o **Endereço de E-Mail** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Criar**.  

        >[AZURE.NOTE] O proprietário da conta receberão um e-mail que inclua uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Onit utilizador conta ferramentas para a criação ou APIs fornecida pela Onit às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-onit-perform-the-following-steps"></a>Para atribuir utilizadores a Onit, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Onit **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-onit-tutorial/IC791182.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-onit-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).