<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com ArcGIS | Microsoft Azure" 
    description="Saiba como utilizar ArcGIS com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Tutorial: Integração com o Azure Active Directory com ArcGIS

Este tutorial objectivo Mostrar a integração do Azure e ArcGIS. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um ArcGIS serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a ArcGIS poderão para o início de sessão único para a aplicação no site da sua empresa ArcGIS (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para ArcGIS
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-arcgis-tutorial/IC784735.png "Cenário")
##<a name="enabling-the-application-integration-for-arcgis"></a>Ativar a integração da aplicação para ArcGIS

Objectivo de nesta secção é como ativar a integração da aplicação para ArcGIS de destaque.

###<a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>Para ativar a integração da aplicação para ArcGIS, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-arcgis-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-arcgis-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-arcgis-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-arcgis-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **ArcGIS**.

    ![Galeria de Applcation] (./media/active-directory-saas-arcgis-tutorial/IC784736.png "Galeria de Applcation")

7.  No painel de resultados, selecione **ArcGIS**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ArcGIS] (./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para ArcGIS com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **ArcGIS** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão ArcGIS** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **ArcGIS URL início de sessão** , escreva o URL utilizado pelos utilizadores para iniciar sessão utilizando o padrão seguinte "*https://company.maps.arcgis.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na ArcGIS** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa ArcGIS como administrador.

6.  Clique em **Editar definições**.

    ![Editar definições] (./media/active-directory-saas-arcgis-tutorial/IC784742.png "Editar definições")

7.  Clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-arcgis-tutorial/IC784743.png "Segurança")

8.  Em **Enterprise inícios de sessão**, clique em **Definir fornecedor de identidade**.

    ![Enterprise inícios de sessão] (./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise inícios de sessão")

9.  Na página de configuração de **Fornecedor de identidade do conjunto** , execute os seguintes passos:

    ![Definir o fornecedor de identidades] (./media/active-directory-saas-arcgis-tutorial/IC784745.png "Definir o fornecedor de identidades")

    1.  Na caixa de texto nome, escreva o nome da sua organização.
    2.  Para **que ser fornecido metadados para o fornecedor de identidade do Enterprise utilizando**, selecione **Um ficheiro**.
    3.  Para carregar o ficheiro de metadados transferido, clique em **Escolher ficheiro**.
    4.  Clique em **definir o fornecedor de identidade**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no ArcGIS, tem de ser aprovisionadas para ArcGIS.  
No caso de ArcGIS, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **ArcGIS** .

2.  Clique em **convidar membros**.

    ![Convide membros] (./media/active-directory-saas-arcgis-tutorial/IC784747.png "Convide membros")

3.  Selecione **Adicionar membros automaticamente sem o enviar uma mensagem de e-mail**e, em seguida, clique em **seguinte**.

    ![Adicionar membros automaticamente] (./media/active-directory-saas-arcgis-tutorial/IC784748.png "Adicionar membros automaticamente")

4.  Na página de diálogo **Membros** , execute os seguintes passos:

    ![Adicionar e rever] (./media/active-directory-saas-arcgis-tutorial/IC784749.png "Adicionar e rever")

    1.  Introduza o **nome próprio**, **Apelido** e **E-Mail** de uma conta AAD válida que pretende aprovisionar.
    2.  Clique em **Adicionar e rever**.

5.  Reveja os dados que introduziu e, em seguida, clique em **Adicionar membros**.

    ![Adicionar membro] (./media/active-directory-saas-arcgis-tutorial/IC784750.png "Adicionar membro")

>[AZURE.NOTE] Pode utilizar quaisquer outros ArcGIS utilizador conta ferramentas para a criação ou APIs fornecida pela ArcGIS às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Para atribuir utilizadores a ArcGIS, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **ArcGIS **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-arcgis-tutorial/IC784751.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-arcgis-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
