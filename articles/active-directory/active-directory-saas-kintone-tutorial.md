<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Kintone | Microsoft Azure" 
    description="Saiba como utilizar Kintone com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integração com o Azure Active Directory com Kintone
  
Este tutorial objectivo Mostrar a integração do Azure e Kintone.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Kintone serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Kintone poderão para o início de sessão único para a aplicação no site da sua empresa Kintone (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Kintone
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-kintone-tutorial/IC785859.png "Cenário")
##<a name="enabling-the-application-integration-for-kintone"></a>Ativar a integração da aplicação para Kintone
  
Objectivo de nesta secção é como ativar a integração da aplicação para Kintone de destaque.

###<a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Para ativar a integração da aplicação para Kintone, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-kintone-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-kintone-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-kintone-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-kintone-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Kintone**.

    ![Galeria de aplicação] (./media/active-directory-saas-kintone-tutorial/IC785867.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Kintone**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Kintone] (./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Kintone com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Kintone** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Kintone** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Kintone início de sessão no URL** , escreva o seu URL utilizando o padrão seguinte "*https://company.kintone.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Kintone** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa **Kintone** como administrador.

6.  Clique em **Definições**.

    ![Definições] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Definições")

7.  Clique em **utilizadores e de administração do sistema**.

    ![Utilizadores e de administração do sistema] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Utilizadores e de administração do sistema")

8.  Em **Administração do sistema \> segurança** clique em **Login**.

    ![Início de sessão] (./media/active-directory-saas-kintone-tutorial/IC785881.png "Início de sessão")

9.  Clique em **Ativar SAML autenticação**.

    ![Autenticação do SAML] (./media/active-directory-saas-kintone-tutorial/IC785882.png "Autenticação do SAML")

10. Na secção autenticação SAML, execute os seguintes passos:

    ![Autenticação do SAML] (./media/active-directory-saas-kintone-tutorial/IC785883.png "Autenticação do SAML")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Kintone** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Kintone** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .
    3.  Clique em **Procurar** para carregar o seu certificado transferido.
    4.  Clique em **Guardar**.

11. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Kintone, tem de ser aprovisionadas para Kintone.  
No caso de Kintone, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **Kintone** como administrador.

2.  Clique em **definição**.

    ![Definições] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Definições")

3.  Clique em **utilizadores e de administração do sistema**.

    ![Administração do sistema & de utilizador] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Administração do sistema & de utilizador")

4.  Em **Administração de utilizador**, clique em **departamentos e utilizadores**.

    ![Departamento e utilizadores] (./media/active-directory-saas-kintone-tutorial/IC785888.png "Departamento e utilizadores")

5.  Clique em **novo utilizador**.

    ![Novos utilizadores] (./media/active-directory-saas-kintone-tutorial/IC785889.png "Novos utilizadores")

6.  Na secção **Novo utilizador** , execute os seguintes passos:

    ![Novos utilizadores] (./media/active-directory-saas-kintone-tutorial/IC785890.png "Novos utilizadores")

    1.  Escreva um **Nome a apresentar**, **Nome de início de sessão**, **Nova palavra-passe**, **Confirme a palavra-passe**, **Endereço de correio electrónico** e outros detalhes de uma conta AAD válida que pretende aprovisionar para o texboxes relacionados.
    2.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros Kintone utilizador conta ferramentas para a criação ou APIs fornecida pela Kintone às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Para atribuir utilizadores a Kintone, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Kintone **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-kintone-tutorial/IC785891.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-kintone-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).