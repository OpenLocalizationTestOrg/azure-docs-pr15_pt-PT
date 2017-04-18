<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com AirWatch | Microsoft Azure" 
    description="Saiba como utilizar AirWatch com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Integração do Azure Active Directory com AirWatch

Este tutorial objectivo Mostrar a integração do Azure e AirWatch.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um AirWatch serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a AirWatch poderão para o início de sessão único para a aplicação no site da sua empresa AirWatch (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para AirWatch
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>Ativar a integração da aplicação para AirWatch

Objectivo de nesta secção é como ativar a integração da aplicação para AirWatch de destaque.

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>Para ativar a integração da aplicação para AirWatch, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-airwatch-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-airwatch-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-airwatch-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-airwatch-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **AirWatch**.

    ![Galeria de aplicação] (./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galeria de aplicação")

7.  No painel de resultados, selecione **AirWatch**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para AirWatch com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **AirWatch** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão AirWatch** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **AirWatch início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão na sua aplicação AirWatch (por exemplo: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na AirWatch** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa AirWatch como administrador.

6.  No painel de navegação esquerdo, clique em **contas**e, em seguida, clique em **administradores**.

    ![Administradores] (./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administradores")

7.  Expandir o menu de **Definições** e, em seguida, clique em **Serviços de diretório**.

    ![Definições] (./media/active-directory-saas-airwatch-tutorial/IC791921.png "Definições")

8.  Clique no separador de **utilizador** , na textfield **Base DN** , escreva o seu nome de domínio e, em seguida, clique em **Guardar**.

    ![Utilizador] (./media/active-directory-saas-airwatch-tutorial/IC791922.png "Utilizador")

9.  Clique no separador **servidor** .

    ![Servidor] (./media/active-directory-saas-airwatch-tutorial/IC791923.png "Servidor")

10. Execute os seguintes passos:

    ![Carregar] (./media/active-directory-saas-airwatch-tutorial/IC791924.png "Carregar")

    1.  Com o **Tipo de diretório**, selecione **nenhum**.
    2.  Selecione **utilizar SAML para autenticação**.
    3.  Para carregar o certificado transferido, clique em **carregar**.

11. Na secção **Pedir** , execute os seguintes passos:

    ![Pedir] (./media/active-directory-saas-airwatch-tutorial/IC791925.png "Pedir")

    1.  Com o **Tipo de encadernação pedido**, selecione **Publicar**.
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Airwatch** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto de **Identidade único início de sessão no URL do fornecedor** .
    3.  Como **Formatar NameID**, selecione **Endereço de E-Mail**.
    4.  Clique em **Guardar**.

12. Clique no separador de **utilizador** novamente.

    ![Utilizador] (./media/active-directory-saas-airwatch-tutorial/IC791926.png "Utilizador")

13. Na secção de **atributo** , execute os seguintes passos:

    ![Atributo] (./media/active-directory-saas-airwatch-tutorial/IC791927.png "Atributo")

    1.  Na caixa de texto **Identificador de objecto** , escreva **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  Na caixa de texto **nome de utilizador** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  Na caixa de texto **Nome a apresentar** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Na caixa de texto **nome próprio** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  Na caixa de texto **Apelido** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  Na caixa de **correio eletrónico** do texto, escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Clique em **Guardar**.

14. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no AirWatch, tem de ser aprovisionadas para AirWatch.  
No caso de AirWatch, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **AirWatch** como administrador.

2.  No painel de navegação no lado esquerdo, clique em **contas**e, em seguida, clique em **utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-airwatch-tutorial/IC791929.png "Utilizadores")

3.  No menu de **utilizadores** , clique em **Vista de lista**e, em seguida, clique em **Adicionar \> adicionar utilizador**.

    ![Adicionar utilizador] (./media/active-directory-saas-airwatch-tutorial/IC791930.png "Adicionar utilizador")

4.  Na caixa de diálogo **Adicionar / Editar utilizador** , efetue os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-airwatch-tutorial/IC791931.png "Adicionar utilizador")

    1.  Escreva o **nome de utilizador**, **palavra-passe**, **Confirme a palavra-passe**, **nome próprio**, **Apelido**, **Endereço de E-Mail** de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros AirWatch utilizador conta ferramentas para a criação ou APIs fornecida pela AirWatch às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Para atribuir utilizadores a AirWatch, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **AirWatch **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-airwatch-tutorial/IC791932.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-airwatch-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
