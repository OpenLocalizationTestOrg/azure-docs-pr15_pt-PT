<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Citrix GoToMeeting | Microsoft Azure" 
    description="Saiba como utilizar Citrix GoToMeeting com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Tutorial: Integração do Azure Active Directory com Citrix GoToMeeting  
Aplica-se ao: Azure

>[AZURE.TIP]Comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=522412).

Este tutorial objectivo Mostrar a integração do Azure e Citrix GoToMeeting. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino no Citrix GoToMeeting

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Citrix GoToMeeting
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Configuração] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuração")



##<a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Ativar a integração da aplicação para Citrix GoToMeeting

Objectivo de nesta secção é como ativar a integração da aplicação para Citrix GoToMeeting de destaque.

###<a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Para ativar a integração da aplicação para Citrix GoToMeeting, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir de galeria] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Adicionar uma aplicação a partir de galeria")

6.  Na **caixa de pesquisa**, escreva **Citrix GoToMeeting**.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  No painel de resultados, selecione **Citrix GoToMeeting**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para Citrix GoToMeeting com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado codificado base-64 ao seu inquilino Citrix GoToMeeting.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  Na página de integração de aplicação **Citrix GoToMeeting** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Sucessivamente de início de sessão único** .

    ![Ativar o início de sessão único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Ativar o início de sessão único")

2.  Na página **como pretende utilizadores a iniciar sessão Citrix GoToMeeting** , selecione **Microsoft Azure AD Single Sign-On**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurar serviço single sign-on")


3. Na página **Configurar definições de aplicação** , clique em **seguinte**. 

    ![Ativar o início de sessão único] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Ativar o início de sessão único")

4.  Na página **Configurar serviço single sign-on na Citrix GoToMeeting** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurar serviço single sign-on")

5.  Numa janela do browser diferente, inicie sessão no seu centro de organização Citrix ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Clique no separador do **Fornecedor de identidade** e, em seguida, execute os seguintes passos:  

    ![Programa de configuração do SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Programa de configuração do SAML")

    um. Selecione **Manual**

    
    b. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Citrix GoToMeeting** , copie o **URL da página de início de sessão no** valor e, em seguida, colá-la a caixa de texto de **início de sessão no URL da página** . 

    
    c. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Citrix GoToMeeting** , copie o valor de **Sign-Out o URL da página** e, em seguida, colá-la a caixa de texto do **URL da página terminar sessão** .

    
    d. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Citrix GoToMeeting** , copie o valor de **ID da entidade de** e, em seguida, colá-la a caixa de texto de **ID da entidade de fornecedor de identidade** .

   
    "e". Para carregar o certificado transferido, clique em **Carregar certificado**.

    
    f. Clique em **Guardar**.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurar serviço single sign-on")


7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.

    ![Programa de configuração do SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Programa de configuração do SAML")





##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Objectivo de nesta secção é como ativar o aprovisionamento de contas de utilizador do Active Directory para Citrix GoToMeeting de destaque.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Citrix GoToMeeting** , clique em **Configurar aprovisionamento de utilizador** para abrir a caixa de diálogo **Configurar aprovisionamento de utilizador** .

    ![Configurar o aprovisionamento de utilizador] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurar o aprovisionamento de utilizador")

2.  Na página de **definições e as credenciais de administrador** , efetue os seguintes passos:

    ![Configurar o aprovisionamento de utilizador] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurar o aprovisionamento de utilizador")

    um. Na caixa de texto **Nome de utilizador do Citrix GoToMeeting administrador** , escreva o nome de utilizador de um administrador.

    
    b. Na caixa de **Palavra-passe de administrador do Citrix GoToMeeting** texto, palavra-passe de administrador.

    
    c. Clique em **seguinte**.

3.  Na página de **confirmação** , clique em marca de verificação para guardar a configuração.

4.  Clique no botão **Validar** , para verificar a sua configuração.


##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Para atribuir utilizadores a Citrix GoToMeeting, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Citrix GoToMeeting** , clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sim")

Agora que deve esperar 10 minutos e certifique-se de que a conta tenha sido sincronizada no Dropbox para empresas.

Como primeiro passo verificação, pode verificar o estado de aprovisionamento, ao clicar em Dashboard no D na página **Citrix GoToMeeting** aplicação integração no portal do Azure clássica.

![Dashboard] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

Um utilizador concluído com êxito aprovisionamento ciclo é indicado por um Estado relacionado:

![Estado de integração] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Estado de integração")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso.

Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](https://msdn.microsoft.com/library/dn308586).
