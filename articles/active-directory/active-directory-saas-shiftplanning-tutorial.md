<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com ShiftPlanning | Microsoft Azure" 
    description="Saiba como utilizar ShiftPlanning com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>Tutorial: Integração do Azure Active Directory com ShiftPlanning
  
Este tutorial objectivo Mostrar a integração do Azure e ShiftPlanning.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um ShiftPlanning serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a ShiftPlanning poderão para o início de sessão único para a aplicação no site da sua empresa ShiftPlanning (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para ShiftPlanning
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Cenário")
##<a name="enabling-the-application-integration-for-shiftplanning"></a>Ativar a integração da aplicação para ShiftPlanning
  
Objectivo de nesta secção é como ativar a integração da aplicação para ShiftPlanning de destaque.

###<a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>Para ativar a integração da aplicação para ShiftPlanning, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **ShiftPlanning**.

    ![Galeria de aplicação] (./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Galeria de aplicação")

7.  No painel de resultados, selecione **ShiftPlanning**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ShiftPlanning] (./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para ShiftPlanning com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **ShiftPlanning** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão ShiftPlanning** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **ShiftPlanning início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*https://company.shiftplanning.com/includes/saml/*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na ShiftPlanning** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa **ShiftPlanning** como administrador.

6.  No menu no canto superior, clique em **Admin**.

    ![Admin] (./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

7.  Em **integração**, clique em **Single Sign-On**.

    ![Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")

8.  Na secção **Single Sign-On** , execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")

    1.  Selecione **SAML ativado**.
    2.  Selecione **Permitir o início de sessão de palavra-passe**
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na ShiftPlanning** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **SAML emissor URL** .
    4.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na ShiftPlanning** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **Termine a sessão de URL remoto** .
    5.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    6.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado x. 509**
    7.  Clique em **Guardar definições**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no ShiftPlanning, tem de ser aprovisionadas para ShiftPlanning.  
No caso de ShiftPlanning, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **ShiftPlanning** como administrador.

2.  Clique em **Admin**.

    ![Admin] (./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

3.  Clique em **pessoal**.

    ![Pessoal] (./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Pessoal")

4.  Em **ações**, clique em **Adicionar funcionário**.

    ![Adicionar funcionários] (./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Adicionar funcionários")

5.  Na secção **Adicionar funcionários** , execute os seguintes passos:

    ![Guardar os seus funcionários] (./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Guardar os seus funcionários")

    1.  Escreva o **nome próprio**, **Apelido** e **E-Mail** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Guardar empregados**.

>[AZURE.NOTE]Pode utilizar quaisquer outros ShiftPlanning utilizador conta ferramentas para a criação ou APIs fornecida pela ShiftPlanning às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>Para atribuir utilizadores a ShiftPlanning, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **ShiftPlanning **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).