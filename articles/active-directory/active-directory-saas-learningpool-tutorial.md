<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Learningpool | Microsoft Azure" 
    description="Saiba como utilizar Learningpool com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Tutorial: Integração do Azure Active Directory com Learningpool
  
Este tutorial objectivo Mostrar a integração do Azure e Learningpool.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Learningpool serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Learningpool poderão para o início de sessão único para a aplicação no site da sua empresa Learningpool (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Learningpool
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-learningpool-tutorial/IC791166.png "Cenário")
##<a name="enabling-the-application-integration-for-learningpool"></a>Ativar a integração da aplicação para Learningpool
  
Objectivo de nesta secção é como ativar a integração da aplicação para Learningpool de destaque.

###<a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Para ativar a integração da aplicação para Learningpool, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-learningpool-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-learningpool-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-learningpool-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-learningpool-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Learningpool**.

    ![Galeria de aplicação] (./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Learningpool**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Learningpool] (./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Learningpool com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.
  
A aplicação Learningpool espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** .  
A captura de ecrã seguinte mostra um exemplo para esta situação.

![Atributos Token SAML] (./media/active-directory-saas-learningpool-tutorial/IC795074.png "Atributos Token SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **Learningpool** , no menu na parte superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-learningpool-tutorial/IC795075.png "Atributos")

2.  Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    ###  

  	|Nome do atributo                |Valor do atributo            |
  	|------------------------------|---------------------------|

     urn: oid:1.2.840.113556.1.4.221 | User.userPrincipalName
  	|-------------------------------|--------------------------|  
     urn: oid:2.5.4.42|User.givenName   
  	|urn: oid:0.9.2342.19200300.100.1.3|User.Mail
  	|urn: oid:2.5.4.4|User.Surname

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar utilizador atributo**.
    2.  Na caixa de texto **Nome do atributo** , escreva o nome do atributo apresentado para nessa linha.
    3.  A partir da lista de **Valor do atributo** , selecione o valor do atributo apresentado para nessa linha.
    4.  Clique em **concluído**.

3.  Clique em **Aplicar alterações**.

4.  No seu browser, clique em **voltar** a caixa de diálogo de **Início rápido** para voltar a abrir.

5.  Clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar Singel Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurar Singel Sign-On")

6.  Na página **como pretende utilizadores a iniciar sessão Learningpool** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurar o Single Sign-On")

7.  Na página **Configurar o URL da aplicação** , na caixa de texto **Learningpool início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão sua aplicação Learningpool (por exemplo: https://parliament.preview.learningpool.com/auth/shibboleth/index.php) e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurar o URL da aplicação")

8.  Na página **Configurar serviço single sign-on na Learningpool** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurar o Single Sign-On")

9.  Reencaminhe esse ficheiro de metadados para a equipa de suporte Learningpool.

    >[AZURE.NOTE]O início de sessão único tem de estar ativada pela equipa de suporte de Learningpool.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Learningpool, tem de ser aprovisionadas para Learningpool.
  
Não existe nenhum item de ação que pode configurar Learningpool do aprovisionamento de utilizador.  
Os utilizadores têm de ser criada pela sua equipa de suporte Learningpool.

>[AZURE.NOTE]Pode utilizar quaisquer outros Learningpool utilizador conta ferramentas para a criação ou APIs fornecida pela Learningpool às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Para atribuir utilizadores a Learningpool, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Learningpool **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-learningpool-tutorial/IC795081.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-learningpool-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).