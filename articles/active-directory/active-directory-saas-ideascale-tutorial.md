<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com IdeaScale | Microsoft Azure" 
    description="Saiba como utilizar IdeaScale com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: Integração do Azure Active Directory com IdeaScale
  
Este tutorial objectivo Mostrar a integração do Azure e IdeaScale.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um IdeaScale serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a IdeaScale poderão para o início de sessão único para a aplicação utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para IdeaScale
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-ideascale-tutorial/IC790838.png "Cenário")
##<a name="enabling-the-application-integration-for-ideascale"></a>Ativar a integração da aplicação para IdeaScale
  
Objectivo de nesta secção é como ativar a integração da aplicação para IdeaScale de destaque.

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>Para ativar a integração da aplicação para IdeaScale, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-ideascale-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-ideascale-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-ideascale-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-ideascale-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **IdeaScale**.

    ![Galeria de aplicação] (./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galeria de aplicação")

7.  No painel de resultados, selecione **IdeaScale**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![IdeaScale] (./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para IdeaScale com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para IdeaScale requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **IdeaScale** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão IdeaScale** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **IdeaScale início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão sua aplicação IdeaScale (por exemplo: "*https://company.IdeaScale.com*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na IdeaScale** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa IdeaScale como administrador.

6.  Aceda a **definições da Comunidade**.

    ![Definições da Comunidade] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Definições da Comunidade")

7.  Aceda a **segurança \> único início definições**.

    ![Definições de início único] (./media/active-directory-saas-ideascale-tutorial/IC790848.png "Definições de início único")

8.  Como **Início único tipo**, selecione **SAML 2.0**.

    ![Único tipo de início] (./media/active-directory-saas-ideascale-tutorial/IC790849.png "Único tipo de início")

9.  Na caixa de diálogo **Definições de início Single** , execute os seguintes passos:

    ![Definições de início único] (./media/active-directory-saas-ideascale-tutorial/IC790850.png "Definições de início único")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na IdeaScale** , copie o valor de **ID da entidade** e, em seguida, colá-la a caixa de texto do **SAML IdP o ID da entidade** .
    2.  Copie o conteúdo do seu ficheiro de metadados transferido e, em seguida, colá-la a caixa de texto do **SAML IdP metadados** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na IdeaScale** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de sucesso termine a sessão** .
    4.  Clique em **Guardar alterações**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no IdeaScale, tem de ser aprovisionadas para IdeaScale.  
No caso de IdeaScale, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **IdeaScale** como administrador.

2.  Aceda a **definições da Comunidade**.

    ![Definições da Comunidade] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Definições da Comunidade")

3.  Aceda a **definições básicas de \> gestão de membros**.

4.  Clique em **Adicionar membro**.

    ![Gestão de membros] (./media/active-directory-saas-ideascale-tutorial/IC790852.png "Gestão de membros")

5.  Na secção Adicionar novo membro, execute os seguintes passos:

    ![Adicionar novo membro] (./media/active-directory-saas-ideascale-tutorial/IC790853.png "Adicionar novo membro")

    1.  Na caixa de texto **Endereços de correio eletrónico** , escreva o endereço de e-mail de uma conta AAD válida que pretende aprovisionar.
    2.  Clique em **Guardar alterações**.

    >[AZURE.NOTE] Titular da conta Azure Active Directory receberão uma mensagem de e-mail com uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros IdeaScale utilizador conta ferramentas para a criação ou APIs fornecida pela IdeaScale às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Para atribuir utilizadores a IdeaScale, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **IdeaScale **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-ideascale-tutorial/IC790854.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).