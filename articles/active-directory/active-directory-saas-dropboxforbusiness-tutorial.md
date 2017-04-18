<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Dropbox para empresas | Microsoft Azure" 
    description="Saiba como utilizar o Dropbox para empresas com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Tutorial: Integração do Azure Active Directory com Dropbox para empresas
  
Este tutorial objectivo Mostrar a integração do Azure e Dropbox para empresas.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino teste no Dropbox para empresas
  
Depois de concluir este tutorial, os utilizadores do Azure AD tiver atribuído no Dropbox para empresas poderão único início de sessão para a aplicação no seu Dropbox para empresas da empresa site (sinal de fornecedor iniciados por serviço no) ou utilizando a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o Dropbox para empresas
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Cenário")



##<a name="enabling-the-application-integration-for-dropbox-for-business"></a>Ativar a integração de aplicação para o Dropbox para empresas
  
O objectivo desta secção é destacar como ativar a integração de aplicação para o Dropbox para empresas.

###<a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Para ativar a integração de aplicação para o Dropbox para empresas, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva o **Dropbox para empresas**.

    ![Galeria de aplicação] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Dropbox para empresas**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Dropbox para empresas] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox para empresas")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados no Dropbox para empresas com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

Como parte deste procedimento, é necessário para carregar um certificado codificado base-64 para o seu Dropbox do inquilino de empresas. Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração do **Dropbox para empresas** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Dropbox para empresas** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos:

    um. Início de sessão na sua Dropbox do inquilino de empresas. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configurar serviço single sign-on")

    b. No painel de navegação no lado esquerdo, clique em **Consola de administração**. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configurar serviço single sign-on")

    c. Na **Consola de administração**, clique em **autenticação** no painel de navegação à esquerda. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configurar serviço single sign-on")

    d. Na secção **serviço Single sign-on** , selecione **Ativar o início de sessão único**e, em seguida, clique em **mais** para expandir nesta secção.  

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configurar serviço single sign-on")

    "e". Copie o URL ao lado dos **utilizadores possam iniciar sessão ao introduzir o respetivo endereço de e-mail ou podem ir diretamente para o**. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configurar serviço single sign-on")

    f. No portal do Azure clássico, na caixa de **início de sessão DropBox para empresas** URL texto, cole o URL. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configurar serviço single sign-on")  



4. Na página **Configurar serviço single sign-on no Dropbox para empresas** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.  

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configurar serviço single sign-on")


5. No seu Dropbox do inquilino de negócio, na secção de **início de sessão único** da página de **autenticação** , execute os seguintes passos: 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurar serviço single sign-on")

    um. Clique em **necessária**.

    b. No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on no Dropbox para empresas** , copie o **URL da página de início de sessão no** valor e, em seguida, colá-la a caixa de texto de **Iniciar sessão no URL** .


    c. Crie um ficheiro de **Base-64 codificado** a partir do seu certificado transferido. 

    > [AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).


    d. Clique o botão de **"Escolher certificado"** e, em seguida, procure o **ficheiro de certificado de codificado base-64**.


    "e". Clique em botão **"Guardar alterações"** para concluir a configuração no seu DropBox do inquilino de empresas.


6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** . 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configurar serviço single sign-on")



##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
O objectivo desta secção é destacar como ativar o aprovisionamento de utilizador do Active Directory contas de utilizador no Dropbox para empresas.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1. No Portal clássico Azure, na página de integração do **Dropbox para empresas** aplicação, clique em **Configurar aprovisionamento de utilizador** para abrir a caixa de diálogo **Configurar aprovisionamento de utilizador** .

2. Na ativar utilizador de aprovisionamento no DropBox para página Business, clique em Ativar aprovisionamento de utilizador para abrir o início de sessão no Dropbox para criar uma ligação com caixa de diálogo do Azure AD.  

    ![Aprovisionamento de utilizador] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Aprovisionamento de utilizador")

3. Na caixa de diálogo **para iniciar sessão no Dropbox para criar uma ligação com o Azure AD** , inicie sessão no seu Dropbox do inquilino de empresas. 

    ![Aprovisionamento de utilizador] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Aprovisionamento de utilizador")



4. Clique em **Permitir** para conceder Azure AD para aceder no Dropbox. 

    ![Aprovisionamento de utilizador] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Aprovisionamento de utilizador")



5. Para concluir a configuração, clique no botão **concluída** .  

    ![Aprovisionamento de utilizador] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Aprovisionamento de utilizador")




##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Para atribuir utilizadores no Dropbox para empresas, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do **Dropbox para empresas **aplicação, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Sim")
  


Agora que deve esperar 10 minutos e certifique-se de que a conta tenha sido sincronizada no Dropbox para empresas.

Como primeiro passo verificação, pode verificar o estado de aprovisionamento, ao clicar em **Dashboard** na página de integração do **Dropbox para empresas** aplicação no Portal do Azure clássica.

![Atribuir aos utilizadores] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Atribuir aos utilizadores")


Um utilizador concluído com êxito aprovisionamento ciclo é indicado por um Estado relacionado.

![Atribuir aos utilizadores] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Atribuir aos utilizadores")


Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso.
Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).




## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)