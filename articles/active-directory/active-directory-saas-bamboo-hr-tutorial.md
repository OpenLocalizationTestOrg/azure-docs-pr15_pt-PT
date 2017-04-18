<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com bambu HR | Microsoft Azure" 
    description="Saiba como utilizar bambu HR com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Tutorial: Integração com o Azure Active Directory com bambu HR

Este tutorial objectivo Mostrar a integração do Azure e BambooHR.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um BambooHR serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a BambooHR poderão para o início de sessão único para a aplicação no site da sua empresa BambooHR (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para BambooHR
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")
##<a name="enabling-the-application-integration-for-bamboohr"></a>Ativar a integração da aplicação para BambooHR

Objectivo de nesta secção é como ativar a integração da aplicação para BambooHR de destaque.

###<a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Para ativar a integração da aplicação para BambooHR, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **BambooHR**.

    ![Galeria de aplicação] (./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galeria de aplicação")

7.  No painel de resultados, selecione **BambooHR**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![BambooHR] (./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para BambooHR com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **BambooHR** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Cenário] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")

2.  Na página **como pretende utilizadores a iniciar sessão BambooHR** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **BambooHR início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação BambooHR (por exemplo: https://company.bamboohr.com) e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na BambooHR** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa BambooHR como administrador.

6.  Na página principal do, execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Single Sign-On")

    1.  Clique em **aplicações**.
    2.  No menu aplicações à esquerda, clique em **Single Sign-On**.
    3.  Clique em **SAML Single Sign-On**.

7.  Na secção **SAML Single Sign-On** , execute os seguintes passos:

    ![SAML Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na BambooHR** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do SSO** .
    2.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    3.  Abrir o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado x. 509**
    4.  Clique em **Guardar**.

8.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no BambooHR, tem de ser aprovisionadas para BambooHR.  
No caso de BambooHR, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu site **BambooHR** como administrador.

2.  Na barra de ferramentas no canto superior, clique em **Definições**.

    ![Definir] (./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Definir")

3.  Clique em **Descrição geral**.

4.  No painel de navegação esquerdo, aceda a **segurança \> utilizadores**.

5.  Escreva o endereço de nome, palavra-passe e e-mail de utilizador de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.

6.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros BambooHR utilizador conta ferramentas para a criação ou APIs fornecida pela BambooHR às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Para atribuir utilizadores a BambooHR, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **BambooHR **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
