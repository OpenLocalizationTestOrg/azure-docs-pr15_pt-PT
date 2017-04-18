<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com AppDynamics | Microsoft Azure" 
    description="Saiba como utilizar AppDynamics com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integração do Azure Active Directory com AppDynamics

Este tutorial objectivo Mostrar a integração do Azure e AppDynamics. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um AppDynamics serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a AppDynamics poderão para o início de sessão único para a aplicação no site da sua empresa AppDynamics (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para AppDynamics
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Cenário")
##<a name="enabling-the-application-integration-for-appdynamics"></a>Ativar a integração da aplicação para AppDynamics

Objectivo de nesta secção é como ativar a integração da aplicação para AppDynamics de destaque.

###<a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Para ativar a integração da aplicação para AppDynamics, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-appdynamics-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **AppDynamics**.

    ![Galeria de aplicação] (./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galeria de aplicação")

7.  No painel de resultados, selecione **AppDynamics**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![AppDynamics] (./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para AppDynamics com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **AppDynamics** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurar o início único")

2.  Na página **como pretende utilizadores a iniciar sessão AppDynamics** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o início único] (./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurar o início único")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **AppDynamics início de sessão no URL** , escreva o seu URL utilizado pelos seus utilizadores para o início de sessão no AppDynamics ("*https://companyname.saas.appdynamics.com*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na AppDynamics** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início único] (./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurar o início único")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa AppDynamics como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Definições**e, em seguida, clique em **Administração**.

    ![Administração] (./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administração")

7.  Clique no separador do **Fornecedor de autenticação** .

    ![Fornecedor de autenticação] (./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Fornecedor de autenticação")

8.  Na secção de **Fornecedor de autenticação** , execute os seguintes passos:

    ![Configuração do SAML] (./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configuração do SAML")

    1.  Como **Fornecedor de autenticação**, selecione **SAML**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na AppDynamics** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na AppDynamics** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .
    4.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **certificado**
    6.  Clique em **Guardar**.
        ![Guardar] (./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Guardar")

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início único] (./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurar o início único")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no AppDynamics, tem de ser aprovisionadas para AppDynamics.  
No caso de AppDynamics, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie a sessão no site da sua empresa AppDynamics como administrador.

2.  Aceda a **utilizadores**e, em seguida, clique em **+** para abrir a caixa de diálogo **Criar utilizador** .

    ![Utilizadores] (./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Utilizadores")

3.  Na secção **Criar utilizador** , execute os seguintes passos:

    ![Criar utilizador] (./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Criar utilizador")

    1.  Escreva o **nome de utilizador**, **nome**, **E-Mail**, **Nova palavra-passe**, **Repita a nova palavra-passe** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros AppDynamics utilizador conta ferramentas para a criação ou APIs fornecida pela AppDynamics aprovisionamento de contas de utilizador do Azure AD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Para atribuir utilizadores a AppDynamics, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **AppDynamics **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
