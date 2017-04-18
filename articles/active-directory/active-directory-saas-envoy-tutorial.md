<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Envoy | Microsoft Azure" 
    description="Saiba como utilizar Envoy com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-envoy"></a>Tutorial: Integração do Azure Active Directory com Envoy
  
Este tutorial objectivo Mostrar a integração do Azure e Envoy.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Envoy
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Envoy poderão para o início de sessão único para a aplicação no site da sua empresa Envoy (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Envoy
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-envoy-tutorial/IC776759.png "Cenário")
##<a name="enabling-the-application-integration-for-envoy"></a>Ativar a integração da aplicação para Envoy
  
Objectivo de nesta secção é como ativar a integração da aplicação para Envoy de destaque.

###<a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Para ativar a integração da aplicação para Envoy, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-envoy-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-envoy-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-envoy-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-envoy-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Envoy**.

    ![Galeria de aplicação] (./media/active-directory-saas-envoy-tutorial/IC776760.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Envoy**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Envoy] (./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Envoy com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Envoy requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de **Envoy** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Ativar o início de sessão único] (./media/active-directory-saas-envoy-tutorial/IC776778.png "Ativar o início de sessão único")

2.  Na página **como pretende utilizadores a iniciar sessão Envoy** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-envoy-tutorial/IC776779.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Envoy URL início de sessão** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. Envoy.com*"e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Envoy** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\Envoy.cer**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-envoy-tutorial/IC776781.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Envoy como administrador.

6.  Na barra de ferramentas no canto superior, clique em **Definições**.

    ![Envoy] (./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")

7.  Clique em **empresa**.

    ![Empresa] (./media/active-directory-saas-envoy-tutorial/IC776783.png "Empresa")

8.  Clique em **SAML**.

    ![SAML] (./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  Na secção de configuração de **Autenticação de SAML** , execute os seguintes passos:

    ![Autenticação do SAML] (./media/active-directory-saas-envoy-tutorial/IC776785.png "Autenticação do SAML")

    >[AZURE.NOTE] O valor para o ID da localização Centro é automática gerada pela aplicação.

    1.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **identificação** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Envoy** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **URL do SAML de HTTP de fornecedor de identidade** .
    3.  Clique em **Guardar alterações**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-envoy-tutorial/IC776786.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item de ação que pode configurar Envoy do aprovisionamento de utilizador.  
Quando um utilizador atribuído tenta que inicie sessão no Envoy utilizando o painel de acesso, Envoy verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo Envoy.
##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Para atribuir utilizadores a Envoy, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de **Envoy **aplicação, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-envoy-tutorial/IC776787.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-envoy-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).