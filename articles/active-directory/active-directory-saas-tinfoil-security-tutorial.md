<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com segurança Tinfoil | Microsoft Azure"
    description="Saiba como utilizar Tinfoil segurança com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integração do Azure Active Directory com segurança Tinfoil
  
Este tutorial objectivo Mostrar a integração do Azure e Tinfoil segurança.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Uma segurança Tinfoil serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a segurança Tinfoil poderão para o início de sessão único para a aplicação no site da sua empresa Tinfoil segurança (sinal de fornecedor iniciados por identidade no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Tinfoil segurança
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Configurar o Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configurar o Single Sign-On")

##<a name="enabling-the-application-integration-for-tinfoil-security"></a>Ativar a integração da aplicação para Tinfoil segurança
  
O objectivo desta secção é destacar como ativar a integração da aplicação para Tinfoil segurança.

###<a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>Para ativar a integração da aplicação para Tinfoil segurança, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Tinfoil segurança**.

    ![Galeria de aplicação] (./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Tinfoil segurança**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Segurança tinfoil] (./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Segurança tinfoil")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados à segurança Tinfoil com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para Tinfoil segurança requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Tinfoil segurança** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão Tinfoil segurança** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Tinfoil segurança resposta URL** , escreva o URL da sua Tinfoil segurança afirmação consumidor serviço (ACS) (por exemplo: "*https://www.tinfoilsecurity.com/saml/consume*" e, em seguida, clique em **seguinte**.

    >[AZURE.NOTE] Deverá conseguir obter o URL de ACS de metadados de segurança Tinfoil (https://www.tinfoilsecurity.com/saml/metadata).

    ![Configurar o URL da aplicação] (./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na segurança Tinfoil** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente como **c:\\Tinfoil Security.cer**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa Tinfoil segurança como administrador.

6.  Na barra de ferramentas no canto superior, clique na **Minha conta**.

    ![Dashboard] (./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7.  Clique em **segurança**.

    ![Segurança] (./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Segurança")

8.  Na página de configuração **Single Sign-On** , execute os seguintes passos:

    ![Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")

    1.  Selecione **Ativar SAML**.
    2.  Clique em **configuração Manual**.
    3.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na Tinfoil segurança** , copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **SAML URL da mensagem** .
    4.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **Identificação de certificado SAML** .  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    5.  Copie o **seu ID de conta**.
    6.  Clique em **Guardar**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configurar o Single Sign-On")

10. No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Atributos")

11. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    ![Atributos] (./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Atributos")

    1.  Clique em **Adicionar utilizador atributo**.
    2.  Na caixa de texto **Nome do atributo** , escreva **accountid**.
    3.  Na caixa de texto **Valor do atributo** , cole o valor de ID de conta que copiou na secção anterior.
    4.  Clique em **concluído**.

12. Clique em **Aplicar alterações**.

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no Tinfoil segurança, tem de ser aprovisionadas para Tinfoil segurança.  
No caso de segurança Tinfoil, aprovisionamento é uma tarefa manual.

###<a name="to-get-a-user-provisioned-perform-the-following-steps"></a>Para obter um utilizador aprovisionado, execute os seguintes passos:

1.  Se o utilizador for uma parte de uma conta da empresa, terá de contactar a equipa de suporte de segurança Tinfoil para obter a conta de utilizador criada.

2.  Se o utilizador for um utilizador habitual da Tinfoil segurança SaaS, em seguida, o utilizador pode adicionar um collaborator para qualquer um dos sites do utilizador. Isto accionadores um processo para enviar um convite para o e-mail especificado para criar uma nova conta de utilizador Tinfoil segurança.

>[AZURE.NOTE] Pode utilizar quaisquer outras Tinfoil utilizador conta criação ferramentas de segurança ou APIs fornecida pela Tinfoil segurança às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Para atribuir utilizadores a segurança Tinfoil, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Tinfoil segurança **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).