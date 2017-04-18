<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com um ambiente de trabalho Central | Microsoft Azure" 
    description="Saiba como utilizar Central de ambiente de trabalho com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Azure Active Directory com Central de ambiente de trabalho

Este tutorial objectivo Mostrar a integração do Azure e ambiente de trabalho Central. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Central ambiente de trabalho single sign-on subscrição activada / ambiente de trabalho Central de inquilinos

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para ambiente de trabalho Central
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Cenário")
##<a name="enabling-the-application-integration-for-central-desktop"></a>Ativar a integração de aplicação para ambiente de trabalho Central

O objectivo desta secção é destacar como ativar a integração de aplicação para ambiente de trabalho Central.

###<a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Para ativar a integração de aplicação para ambiente de trabalho Central, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-central-desktop-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Central de ambiente de trabalho**.

    ![Galeria de aplicação] (./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Ambiente de trabalho Central**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Ambiente de trabalho central] (./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Ambiente de trabalho central")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para ambiente de trabalho Central com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base 64 para o seu inquilino do ambiente de trabalho Central.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).



###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **Ambiente de trabalho Central** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão ambiente de trabalho Central** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos e, em seguida, clique em **seguinte**: 

    -   Na caixa de texto **Central ambiente de trabalho URL início de sessão** , escreva o URL do seu inquilino do ambiente de trabalho Central (por exemplo: *http://contoso.centraldesktop.com*).
    -   Na caixa de texto Central URL de resposta do ambiente de trabalho, escreva o URL da AssertionConsumerService Central do ambiente de trabalho (por exemplo: https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE] Pode obter o valor dos metadados de ambiente de trabalho central (por exemplo: *http://contoso.centraldesktop.com*).

    ![Configurar o URL da aplicação] (./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on no ambiente de trabalho Central** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurar serviço single sign-on")

5.  Inicie sessão no seu inquilino do **Ambiente de trabalho Central** .

6.  Aceda a **Definições**, clique em **Avançadas**e, em seguida, clique em **Início de sessão**.

    ![Configuração - avançadas] (./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Configuração - avançadas")

7.  Na página de **Início de sessão único no definições** , efetue os seguintes passos:

    ![Single Sign-On definições] (./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign-On definições")

    1.  Selecione **Ativar SAML v2 Single Sign-On**.
    2.  No Azure clássico portal do, na página **Configurar serviço single sign-on no ambiente de trabalho Central** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto do **URL de SSO** .
    3.  No portal clássico Azure, na página **Configurar serviço single sign-on no ambiente de trabalho Central** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do SSO** .
    4.  No Azure clássico portal do, na página **Configurar serviço single sign-on no ambiente de trabalho Central** , copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto do **URL do SSO termine a sessão** .

8.  Na secção **Método de confirmação de assinatura de mensagem** , execute os seguintes passos:

    ![Método de confirmação de assinatura de mensagem] (./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Método de confirmação de assinatura de mensagem")

    1.  Selecione o **certificado**.
    2.  A partir da lista de **Certificado de SSO** , selecione **RSH SHA256**.
    3.  Criar um ficheiro de texto a partir do certificado transferido, copie o conteúdo do ficheiro de texto e, em seguida, colá-la o campo do **Certificado de SSO** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Selecione **Mostrar uma ligação à sua página de início de sessão SAMLv2**.

9.  Clique em **Atualizar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para AAD os utilizadores possam iniciar sessão, tem de ser aprovisionadas para a aplicação de ambiente de trabalho Central. Esta secção descreve como criar contas de utilizador AAD no ambiente de trabalho Central.

###<a name="to-provision-user-accounts-to-central-desktop"></a>Aprovisionamento de contas de utilizador para ambiente de trabalho Central:

1.  Inicie sessão no seu inquilino do ambiente de trabalho Central.

2.  Aceda a **pessoas \> com membros internos**.

3.  Clique em **Adicionar membros internos**.

    ![Pessoas] (./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Pessoas")

4.  Na caixa de texto **Endereço de E-Mail de novos membros** , escreva uma conta AAD que pretende aprovisionar o e, em seguida, clique em **seguinte**.

    ![Endereços de e-mail de novos membros] (./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Endereços de e-mail de novos membros")

5.  Clique em **Adicionar interno membros da**.

    ![Adicionar membro interno] (./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Adicionar membro interno")

    >[AZURE.NOTE] Os utilizadores que tiver adicionado receberão um e-mail que inclui uma ligação de confirmação que precisam de clique para ativar a conta.

>[AZURE.NOTE] Pode utilizar quaisquer outros Central ferramentas ambiente de trabalho utilizador conta criação ou APIs fornecidos pelo Central de ambiente de trabalho para contas de utilizador AAD aprovisionar

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Para atribuir aos utilizadores para ambiente de trabalho Central, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação de **Ambiente de trabalho Central** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
