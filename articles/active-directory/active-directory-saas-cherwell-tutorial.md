<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Cherwell | Microsoft Azure" 
    description="Saiba como utilizar Cherwell com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="10/14/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Tutorial: Integração do Azure Active Directory com Cherwell

Este tutorial objectivo Mostrar a integração do Azure e Cherwell. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Cherwell serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Cherwell poderão para o início de sessão único para a aplicação no site da sua empresa Cherwell (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Cherwell
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-cherwell-tutorial/IC798988.png "Cenário")
##<a name="enabling-the-application-integration-for-cherwell"></a>Ativar a integração da aplicação para Cherwell

Objectivo de nesta secção é como ativar a integração da aplicação para Cherwell de destaque.

###<a name="to-enable-the-application-integration-for-cherwell-perform-the-following-steps"></a>Para ativar a integração da aplicação para Cherwell, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-cherwell-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-cherwell-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-cherwell-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-cherwell-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Cherwell**.

    ![Cherwell] (./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  No painel de resultados, selecione **Cherwell**e, em seguida, clique em **Concluir** para adicionar a aplicação.
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Cherwell com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Cherwell** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Cherwell** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurar o URL da aplicação")

    um.  Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos utilizadores para iniciar sessão no seu **Cherwell** (por exemplo: *https://\<nome da empresa\>.cherwellondemand.com/cherwellclient*).

    b.  Clique em **seguinte**

4.  Na página **Configurar serviço single sign-on na Cherwell** , execute os seguintes passos:

    ![Configurar o Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurar o Single Sign-On")

    um.  Clique em **Transferir o certificado**e, em seguida, guarde o certificado localmente no seu computador.

    b.  Copie o **URL do fornecedor de identidade**.

    c.  Copie o **URL do serviço Single Sign-On**.

    d.  Clique em **seguinte**.

5.  Submeta o certificado transferido, o **URL do fornecedor de identidade** e o **Único início de sessão URL do serviço de** para a equipa de suporte Cherwell.

    >[AZURE.NOTE] A equipa de suporte Cherwell tem de fazer a configuração de SSO real.
Irá obter uma notificação quando SSO tenha sido ativado para a sua subscrição.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Cherwell, tem de ser aprovisionadas para Cherwell.  
No caso de Cherwell, as contas de utilizador tem de ser criada pela sua equipa de suporte Cherwell.

>[AZURE.NOTE] Pode utilizar quaisquer outros Cherwell utilizador conta ferramentas para a criação ou APIs fornecida pela Cherwell aprovisionar o Azure Active Directory contas de utilizador.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-cherwell-perform-the-following-steps"></a>Para atribuir utilizadores a Cherwell, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Cherwell** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-cherwell-tutorial/IC798995.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
