<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Boomi | Microsoft Azure" 
    description="Saiba como utilizar Boomi com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integração do Azure Active Directory com Boomi

Este tutorial objectivo Mostrar a integração do Azure e Boomi.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Boomi serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Boomi poderão para o início de sessão único para a aplicação no site da sua empresa Boomi (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Boomi
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-boomi-tutorial/IC791134.png "Cenário")
##<a name="enabling-the-application-integration-for-boomi"></a>Ativar a integração da aplicação para Boomi

Objectivo de nesta secção é como ativar a integração da aplicação para Boomi de destaque.

###<a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Para ativar a integração da aplicação para Boomi, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-boomi-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-boomi-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-boomi-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-boomi-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Boomi**.

    ![Galeria de aplicação] (./media/active-directory-saas-boomi-tutorial/IC790822.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Boomi**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Boomi] (./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Boomi com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Boomi** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Boomi** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Boomi resposta URL** , escreva o **URL de início de sessão do Boomi AtomSphere** (por exemplo: "*https://platform.boomi.com/sso/AccountName/saml*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Boomi** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Boomi como administrador.

6.  Na barra de ferramentas no canto superior, clique em seu nome de empresa e, em seguida, **o programa de configuração**.

    ![Programa de configuração] (./media/active-directory-saas-boomi-tutorial/IC790828.png "Programa de configuração")

7.  Clique em **Opções de SSO**.

    ![Opções de SSO] (./media/active-directory-saas-boomi-tutorial/IC790829.png "Opções de SSO")

8.  Na secção **Opções de início de sessão único** , execute os seguintes passos:

    ![Opções de início de sessão únicos] (./media/active-directory-saas-boomi-tutorial/IC790830.png "Opções de início de sessão únicos")

    1.  Selecione **Ativar SAML Single Sign-On**.
    2.  Clique em **Importar**, para carregar o certificado transferido.
    3.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Boomi** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de identidade** .
    4.  Como **Localização de Id de Federação**, selecione a **Federação do Id está a NameID elemento do assunto**.
    5.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Boomi, tem de ser aprovisionadas para Boomi.  
No caso de Boomi, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Boomi** como administrador.

2.  Aceda a **Gestão de utilizadores \> utilizadores**.

    ![Utilizadores] (./media/active-directory-saas-boomi-tutorial/IC790831.png "Utilizadores")

3.  Clique em **Adicionar utilizador**.

    ![Adicionar utilizador] (./media/active-directory-saas-boomi-tutorial/IC790832.png "Adicionar utilizador")

4.  Na página de diálogo **Adicionar funções de utilizador** , execute os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-boomi-tutorial/IC790833.png "Adicionar utilizador")

    1.  Escreva o **nome próprio**, **Apelido** e **E-Mail** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em OK.

>[AZURE.NOTE] Pode utilizar quaisquer outros Boomi utilizador conta ferramentas para a criação ou APIs fornecida pela Boomi às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Para atribuir utilizadores a Boomi, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Boomi **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-boomi-tutorial/IC790834.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-boomi-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
