<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Benefitsolver | Microsoft Azure"
    description="Saiba como utilizar Benefitsolver com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Tutorial: Integração do Azure Active Directory com Benefitsolver

Este tutorial objectivo Mostrar a integração do Azure e Benefitsolver.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Benefitsolver serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Benefitsolver poderão para o início de sessão único para a aplicação utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Benefitsolver
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Cenário")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>Ativar a integração da aplicação para Benefitsolver

Objectivo de nesta secção é como ativar a integração da aplicação para Benefitsolver de destaque.

###<a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Para ativar a integração da aplicação para Benefitsolver, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Benefitsolver**.

    ![Galeria de aplicação] (./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Benefitsolver**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Benefitssolver] (./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Benefitsolver com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
A aplicação Benefitsolver espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** .  
A captura de ecrã seguinte mostra um exemplo para esta situação.

![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Benefitsolver** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Benefitsolver** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurar o Single Sign-On")

3.  Na página **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar definições de aplicação] (./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configurar definições de aplicação")

    1.  Na caixa de texto **URL no início de sessão** , escreva **http://azure.benefitsolver.com**.
    2.  Na caixa de texto **Resposta URL** , escreva **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  


    3.  Clique em **seguinte**.

4.  Na página **Configurar serviço single sign-on na Benefitsolver** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurar o Single Sign-On")

5.  Envie o ficheiro de metadados transferido para a equipa de suporte Benefitsolver.

    >[AZURE.NOTE] A equipa de suporte Benefitsolver tem de fazer a configuração de SSO real.
Irá obter uma notificação quando SSO tenha sido ativado para a sua subscrição.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurar o Single Sign-On")

7.  No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Atributos")

8.  Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    ![Atributos] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Atributos")

  	|Nome do atributo|Valor do atributo|
  	|---|---|
  	|ClientID|Que precisa para obter este valor a partir da sua equipa de suporte Benefitsolver.|
  	|ClientKey|Que precisa para obter este valor a partir da sua equipa de suporte Benefitsolver.|
  	|LogoutURL|Que precisa para obter este valor a partir da sua equipa de suporte Benefitsolver.|
  	|O campo IDdeEmpregado|Que precisa para obter este valor a partir da sua equipa de suporte Benefitsolver.|

    1.  Para cada linha de dados na tabela acima, clique em **Adicionar utilizador atributo**.
    2.  Na caixa de texto **Nome do atributo** , escreva o nome do atributo apresentado para nessa linha.
    3.  Na caixa de texto **Valor do atributo** , selecione o valor do atributo apresentado para nessa linha.
    4.  Clique em **concluído**.

9.  Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Benefitsolver, tem de ser aprovisionadas para Benefitsolver.  
No caso de Benefitsolver, dados de funcionários estão na sua aplicação povoada através de um ficheiro de contagem do seu sistema HRIS (normalmente durante a noite).  

>[AZURE.NOTE] Pode utilizar quaisquer outros Benefitsolver utilizador conta ferramentas para a criação ou APIs fornecida pela Benefitsolver às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Para atribuir utilizadores a Benefitsolver, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Benefitsolver **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
