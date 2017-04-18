<properties 
    pageTitle="Tutorial: Integração de integração Azure Active Directory com Druva | Microsoft Azure" 
    description="Saiba como utilizar Druva com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Tutorial: Integração de integração Azure Active Directory com Druva

Este tutorial objectivo Mostrar a integração do Azure e Druva.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Druva serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Druva poderão para o início de sessão único para a aplicação no site da sua empresa Druva (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Druva
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-druva-tutorial/IC795084.png "Cenário")
##<a name="enabling-the-application-integration-for-druva"></a>Ativar a integração da aplicação para Druva

Objectivo de nesta secção é como ativar a integração da aplicação para Druva de destaque.

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Para ativar a integração da aplicação para Druva, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-druva-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-druva-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-druva-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-druva-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Druva**.

    ![Galeria de aplicação] (./media/active-directory-saas-druva-tutorial/IC795085.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Druva**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Druva] (./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Druva com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

A aplicação Druva espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** .  
A captura de ecrã seguinte mostra um exemplo para esta situação.

![Atributos Token SAML] (./media/active-directory-saas-druva-tutorial/IC795087.png "Atributos Token SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Druva** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795027.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Druva** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795088.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Druva início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão sua aplicação Druva (por exemplo: "*https://cloud.druva.com/home/*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-druva-tutorial/IC795089.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Druva** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795090.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Druva como administrador.

6.  Aceda a **Gerir \> definições**.

    ![Definições] (./media/active-directory-saas-druva-tutorial/IC795091.png "Definições")

7.  Na caixa de diálogo Definições de Single Sign-On, execute os seguintes passos:

    ![Definições Simpl Sign-On] (./media/active-directory-saas-druva-tutorial/IC795092.png "Definições Simpl Sign-On")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Druva** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de ID** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Druva** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL do ID fornecedor termine a sessão** .
    3.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto de **ID do fornecedor de certificado**
    5.  Para abrir a página de **Definições** , clique em **Guardar**.

8.  Na página **Definições** , clique em **Gerar SSO Token**.

    ![Definições] (./media/active-directory-saas-druva-tutorial/IC795093.png "Definições")

9.  Na caixa de diálogo **Único início de sessão autenticação Token** , execute os seguintes passos:

    ![Token SSO] (./media/active-directory-saas-druva-tutorial/IC795094.png "Token SSO")

    1.  Clique em **Copiar**.
    2.  Clique em **Fechar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-druva-tutorial/IC795095.png "Configurar o Single Sign-On")

11. No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-druva-tutorial/IC795096.png "Atributos")

12. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|em sincronização\_auth\_token|<*valor da área de transferência*>|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar utilizador atributo**.
    2.  Na caixa de texto **Nome do atributo** , escreva o nome do atributo apresentado para nessa linha.
    3.  Na caixa de texto **Valor do atributo** , escreva o valor do atributo apresentado para nessa linha.
    4.  Clique em **concluído**.

13. Clique em **Aplicar alterações**.
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Druva, tem de ser aprovisionadas para Druva.  
No caso de Druva, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Druva** como administrador.

2.  Aceda a **Gerir \> utilizadores**.

    ![Gerir utilizadores] (./media/active-directory-saas-druva-tutorial/IC795097.png "Gerir utilizadores")

3.  Clique em **Criar novo**.

    ![Gerir utilizadores] (./media/active-directory-saas-druva-tutorial/IC795098.png "Gerir utilizadores")

4.  Na caixa de diálogo Criar novo utilizador, efetue os seguintes passos:

    ![Criar Novo_utilizador] (./media/active-directory-saas-druva-tutorial/IC795099.png "Criar Novo_utilizador")

    1.  Escreva o endereço de e-mail e o nome de uma conta de utilizador do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Criar utilizador**.

>[AZURE.NOTE] Pode utilizar quaisquer outros Druva utilizador conta ferramentas para a criação ou APIs fornecida pela Druva às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>Para atribuir utilizadores a Druva, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Druva **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-druva-tutorial/IC795100.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-druva-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
