<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com Bonus.ly | Microsoft Azure" 
    description="Saiba como utilizar Bonus.ly com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integração com o Azure Active Directory com Bonus.ly

Este tutorial objectivo Mostrar a integração do Azure e Bonus.ly. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino teste no Bonus.ly

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Bonus.ly
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-bonus-tutorial/IC773679.png "Cenário")
##<a name="enabling-the-application-integration-for-bonusly"></a>Ativar a integração da aplicação para Bonus.ly

Objectivo de nesta secção é como ativar a integração da aplicação para Bonus.ly de destaque.

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Para ativar a integração da aplicação para Bonus.ly, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Ativar o início de sessão único] (./media/active-directory-saas-bonus-tutorial/IC773680.png "Ativar o início de sessão único")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-bonus-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-bonus-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-bonus-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Bonus.ly**.

    ![Galeria de aplicação] (./media/active-directory-saas-bonus-tutorial/IC773681.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Bonus.ly**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Bonus.ly com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Bonus.ly requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Bonus.ly** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bonus-tutorial/IC749323.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Bonus.ly** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bonus-tutorial/IC773683.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Bonus.ly inquilino URL** , escreva o URL da sua utilizando o padrão seguinte "*https://\<inquilino nome\>. Bonus.LY*"e, em seguida, clique em **seguinte**: 

    ![Configurar o URL da aplicação] (./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Bonus.ly** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\Bonusly.cer**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bonus-tutorial/IC773685.png "Configurar serviço single sign-on")

5.  Numa janela do browser diferente, inicie sessão no seu inquilino **Bonus.ly** .

6.  Na barra de ferramentas no canto superior, clique em **Definições**e, em seguida, selecione **integrações e apps**.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  Em **Single Sign-On**, selecione **SAML**.

8.  Na página de diálogo **SAML** , execute os seguintes passos:

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Bonus.ly** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de destino IdP SSO** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Bonus.ly** , copie o valor de **ID do emissor** e, em seguida, colá-la a caixa de texto **IdP emissor** .
    3.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na Bonus.ly** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão IdP** .
    4.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de orientação do diapositivo notas** .

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

9.  Clique em **Guardar**.

10. No portal do Microsoft Azure clássico, selecione a confirmação de configuração e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-bonus-tutorial/IC773689.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no Bonus.ly, tem de ser aprovisionadas para Bonus.ly.  
No caso de Bonus.ly, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Numa janela do browser web, inicie sessão no seu inquilino Bonus.ly.

2.  Clique em **Definições**

    ![Definições] (./media/active-directory-saas-bonus-tutorial/IC781041.png "Definições")

3.  Clique no separador de **bónus e utilizadores** .

    ![Utilizadores e bónus] (./media/active-directory-saas-bonus-tutorial/IC781042.png "Utilizadores e bónus")

4.  Clique em **Gerir utilizadores**.

    ![Gerir utilizadores] (./media/active-directory-saas-bonus-tutorial/IC781043.png "Gerir utilizadores")

5.  Clique em **Adicionar utilizador**.

    ![Adicionar utilizador] (./media/active-directory-saas-bonus-tutorial/IC781044.png "Adicionar utilizador")

6.  Na caixa de diálogo **Adicionar utilizador** , efetue os seguintes passos:

    ![Adicionar utilizador] (./media/active-directory-saas-bonus-tutorial/IC781045.png "Adicionar utilizador")

    1.  Escreva o "**E-Mail**, **nome próprio**, **Apelido**" de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Guardar**.

    >[AZURE.NOTE] Titular da conta AAD receberão um e-mail que inclui uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Bonus.ly utilizador conta ferramentas para a criação ou APIs fornecida pela Bonus.ly às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Para atribuir utilizadores a Bonus.ly, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação Bonus.ly, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-bonus-tutorial/IC773690.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-bonus-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
