<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Thoughtworks Mingle | Microsoft Azure" 
    description="Saiba como utilizar Thoughtworks Mingle com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integração do Azure Active Directory com Thoughtworks Mingle
  
Este tutorial objectivo Mostrar a integração do Azure e Thoughtworks Mingle.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Thoughtworks Mingle
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Thoughtworks Mingle
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Cenário")

##<a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Ativar a integração da aplicação para Thoughtworks Mingle
  
Objectivo de nesta secção é como ativar a integração da aplicação para Thoughtworks Mingle de destaque.

###<a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Para ativar a integração da aplicação para Thoughtworks Mingle, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **thoughtworks mingle**.

    ![Galeria de aplicação] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Thoughtworks Mingle**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Thoughtworks Mingle] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticar Thoughtworks Mingle com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado para Thoughtworks Mingle.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Thoughtworks Mingle **, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Thoughtworks Mingle** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Thoughtworks Mingle inquilino URL** , escreva o seu URL utilizando o padrão seguinte "*http://company.mingle.thoughtworks.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Thoughtworks Mingle** , clique em transferir metadados e, em seguida, guarde-o no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configurar serviço single sign-on")

5.  Inicie sessão no seu site de empresa **Thoughtworks Mingle** como administrador.

6.  Clique no separador **administrador** e, em seguida, clique em **SSO Config**.

    ![Configuração SSO] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Configuração SSO")

7.  Na secção **SSO Config** , execute os seguintes passos:

    ![Configuração SSO] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Configuração SSO")

    1.  Para carregar o ficheiro de metadados, clique em **Escolher ficheiro**.
    2.  Clique em **Guardar alterações**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para AAD os utilizadores possam iniciar sessão, tem de ser aprovisionadas para a aplicação Thoughtworks Mingle utilizar os seus nomes de utilizador do Azure Active Directory.  
No caso de Thoughtworks Mingle, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu site de empresa Thoughtworks Mingle como administrador.

2.  Clique no **perfil**.

    ![O primeiro projecto] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "O primeiro projecto")

3.  Clique no separador **administrador** e, em seguida, clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Utilizadores")

4.  Clique em **novo utilizador**.

    ![Novo utilizador] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Novo utilizador")

5.  Na página de diálogo **Novo utilizador** , execute os seguintes passos:

    ![Novo utilizador] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Novo utilizador")

    1.  Escreva o **nome de início de sessão**, **nome a apresentar**, **palavra-passe de escolher**, **Confirmar palavra-passe** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Com o **tipo de utilizador**, selecione o **utilizador completo**.
    3.  Clique em **criar este perfil**.

>[AZURE.NOTE] Pode utilizar quaisquer outros Thoughtworks Mingle utilizador conta ferramentas para a criação ou APIs fornecida pela Thoughtworks Mingle às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Para atribuir utilizadores a Thoughtworks Mingle, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Thoughtworks Mingle** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
