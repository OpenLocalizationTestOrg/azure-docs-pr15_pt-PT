<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Veracode | Microsoft Azure" 
    description="Saiba como utilizar Veracode com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: Integração do Azure Active Directory com Veracode
  
Este tutorial objectivo Mostrar a integração do Azure e Veracode. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Veracode serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Veracode poderão para o início de sessão único para a aplicação utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Veracode
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-veracode-tutorial/IC802903.png "Cenário")

##<a name="enabling-the-application-integration-for-veracode"></a>Ativar a integração da aplicação para Veracode
  
Objectivo de nesta secção é como ativar a integração da aplicação para Veracode de destaque.

###<a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Para ativar a integração da aplicação para Veracode, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-veracode-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-veracode-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-veracode-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-veracode-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Veracode**.

    ![Galeria de aplicação] (./media/active-directory-saas-veracode-tutorial/IC802904.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Veracode**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Veracode] (./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Veracode com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
A aplicação Veracode espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** .  
A captura de ecrã seguinte mostra um exemplo para esta situação.

![Atributos] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Veracode** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Veracode** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurar o Single Sign-On")

3.  Na página **Configurar definições de aplicação** , clique em **seguinte**.

    ![Configurar definições de aplicação] (./media/active-directory-saas-veracode-tutorial/IC802909.png "Configurar definições de aplicação")

4.  Na página **Configurar serviço single sign-on na Veracode** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Veracode como administrador.

6.  No menu no canto superior, clique em **Definições**e, em seguida, clique em **Admin**.

    ![Administração] (./media/active-directory-saas-veracode-tutorial/IC802911.png "Administração")

7.  Clique no separador **SAML** .

8.  Na secção **Definições da organização SAML** , execute os seguintes passos:

    ![Administração] (./media/active-directory-saas-veracode-tutorial/IC802912.png "Administração")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Veracode** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor**
    2.  Para carregar o certificado transferido, clique em **Escolher ficheiro**.
    3.  Selecione **Ativar registo automático**.

9.  Na secção **Definições de registo de Self** , execute os seguintes passos e, em seguida, clique em **Guardar**:

    ![Administração] (./media/active-directory-saas-veracode-tutorial/IC802913.png "Administração")

    1.  Como **Nova de utilizador de ativação**, selecione **Sem ativação necessário**.
    2.  Como as **Atualizações de dados do utilizador**, selecione **Preferência Veracode User Data**.
    3.  Para **Detalhes do atributo SAML**, selecione o seguinte:
        -   **Funções de utilizador**
        -   **Administrador da política**
        -   **Revisor**
        -   **Oportunidade potencial de segurança**
        -   **Executivo**
        -   **Submissores**
        -   **Criador de blocos**
        -   **Pesquisar todos os tipos de**
        -   **Membros de equipa**
        -   **Equipa predefinida**

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurar o Single Sign-On")

11. No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-veracode-tutorial/IC795920.png "Atributos")

12. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    ![Atributos] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Atributos")

  	| Nome do atributo | Valor do atributo |
  	|:---------------|:----------------|
  	| NomePróprio      | User.givenName  |
  	| Apelido       | User.Surname    |
  	| mensagem de correio electrónico          | User.Mail       |

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar utilizador atributo**.
    
    2.  Na caixa de texto **Nome do atributo** , escreva o nome do atributo apresentado para nessa linha.

    3.  Na caixa de texto **Valor do atributo** , selecione o valor do atributo apresentado para nessa linha.

    4.  Clique em **concluído**.

13. Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Veracode, tem de ser aprovisionadas para Veracode.  
No caso de Veracode, aprovisionamento é uma tarefa automatizada.  
Não existe nenhum item ação por si...
  
Os utilizadores são criados automaticamente se for necessário durante a primeira única início de sessão tentativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Veracode utilizador conta ferramentas para a criação ou APIs fornecida pela Veracode às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Para atribuir utilizadores a Veracode, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Veracode **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-veracode-tutorial/IC802915.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-veracode-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).