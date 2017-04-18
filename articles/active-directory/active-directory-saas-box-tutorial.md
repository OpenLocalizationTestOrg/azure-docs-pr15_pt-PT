<properties 
    pageTitle="Tutorial: Azure Active Directory caixa integração com o | Microsoft Azure" 
    description="Saiba como utilizar caixa com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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




#<a name="tutorial-azure-active-directory-integration-with-box"></a>Tutorial: Azure Active Directory caixa integração com o


  
Este tutorial objectivo Mostrar a integração do Azure e caixa.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino teste na caixa
  
Depois de concluir este tutorial, os utilizadores do Azure AD que tiver atribuído à caixa poderão para o início de sessão único para a aplicação no site da sua empresa caixa (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação caixa de
2.  Configurar o início de sessão único
3.  Configurar o utilizador e grupo de aprovisionamento
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-box-tutorial/IC769537.png "Cenário")



##<a name="enabling-the-application-integration-for-box"></a>Ativar a integração da aplicação caixa de
  
Sobre o objetivo nesta secção é como ativar a integração de aplicação para a caixa de destaque.

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Para ativar a integração da aplicação caixa de, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-box-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-box-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-box-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-box-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **caixa**.

    ![Galeria de aplicação] (./media/active-directory-saas-box-tutorial/IC701023.png "Galeria de aplicação")

7.  No painel de resultados, selecione a **caixa**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Caixa] (./media/active-directory-saas-box-tutorial/IC701024.png "Caixa")



##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados à caixa com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque. Como parte deste procedimento, é necessário para carregar metadados para Box.com.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de **caixa de** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-box-tutorial/IC769538.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão caixa de** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-box-tutorial/IC769539.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Caixa inquilino URL** , escreva o URL da sua caixa inquilino (por exemplo: https://<mydomainname>. box.com) e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-box-tutorial/IC669826.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na caixa de** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, o ficheiro de dados localmente no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-box-tutorial/IC669824.png "Configurar serviço single sign-on")

5.  Reencaminhar esse metadados caixa ficheiro a equipa de suporte. Configura as necessidades da equipa de suporte serviço single sign-on por si.

6.  Selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-box-tutorial/IC769540.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Objectivo de nesta secção é como ativar o aprovisionamento de contas de utilizador do Active Directory à caixa de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1. No portal clássico Azure, na página de integração de **caixa de** aplicação, clique em **Configurar aprovisionamento de utilizador** para abrir a caixa de diálogo **Configurar aprovisionamento de utilizador** . 

    ![Ativar o aprovisionamento de utilizador automática] (./media/active-directory-saas-box-tutorial/IC769541.png "Ativar o aprovisionamento de utilizador automática")

2. Na página para **Permitir que o utilizador aprovisionamento à caixa de** diálogo, clique em **Ativar o aprovisionamento de utilizador**. 

    ![Ativar o aprovisionamento de utilizador automática] (./media/active-directory-saas-box-tutorial/IC769544.png "Ativar o aprovisionamento de utilizador automática")

3. Na página **Iniciar sessão para conceder acesso a caixa** , fornecer as credenciais necessárias e, em seguida, clique em **autorizar**. 

    ![Ativar o aprovisionamento de utilizador automática] (./media/active-directory-saas-box-tutorial/IC769546.png "Ativar o aprovisionamento de utilizador automática")


4. Clique em **conceder acesso a caixa** para autorizar esta operação e regressar ao portal do Azure clássico. 

    ![Ativar o aprovisionamento de utilizador automática] (./media/active-directory-saas-box-tutorial/IC769549.png "Ativar o aprovisionamento de utilizador automática")


5. Na página de **Opções de aprovisionamento** , as caixas de verificação **tipos de objeto aprovisionar** permitem-lhe selecionar se ou não agrupar objetos configurações à caixa para além de objetos de utilizador.  Consulte o artigo "Atribuir utilizadores e grupos secção" abaixo para obter mais informações.


6. Para concluir a configuração, clique no botão concluído. 

    ![Ativar o aprovisionamento de utilizador automática] (./media/active-directory-saas-box-tutorial/IC769551.png "Ativar o aprovisionamento de utilizador automática")



##<a name="assigning-a-test-user"></a>Atribuir um utilizador de teste
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>Para atribuir utilizadores a caixa, execute os seguintes passos:

1. No portal do Azure clássico, crie uma conta de teste.

2. Na página de integração de **caixa de **aplicação, clique em **atribuir aos utilizadores**. 

    ![Atribuir aos utilizadores] (./media/active-directory-saas-box-tutorial/IC769552.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição. 

    ![Sim] (./media/active-directory-saas-box-tutorial/IC767830.png "Sim")
  
Agora deverá aguardar 10 minutos e certifique-se de que a conta tenha sido sincronizada à caixa.

Como primeiro passo verificação, pode verificar o estado de aprovisionamento, ao clicar em Dashboard no D na página caixa aplicação integração no portal do Azure clássica.

![Dashboard] (./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

Um utilizador concluído com êxito aprovisionamento ciclo é indicado por um Estado relacionado:

![Estado de integração] (./media/active-directory-saas-box-tutorial/IC769555.png "Estado de integração")


No seu inquilino caixa, os utilizadores sincronizados são listados em **Utilizadores gerido** na **Consola de administração**.

![Estado de integração] (./media/active-directory-saas-box-tutorial/IC769556.png "Estado de integração")


##<a name="assigning-users-and-groups"></a>Atribuir utilizadores e grupos

O **caixa > utilizadores e grupos** separador no portal do Azure clássico permite-lhe especificar quais os utilizadores e grupos devem ser concedidos acesso caixa. Atribuição de um utilizador ou grupo faz com que as seguintes coisas a ocorrer:

* Azure AD permite que o utilizador atribuído (quer por atribuição direta ou membros do grupo) para autenticar à caixa. Se um utilizador não está atribuído, em seguida, Azure AD não permitirá-los para iniciar sessão na caixa e devolverão um erro na página de início de sessão do Azure AD.

* Num mosaico da aplicação caixa é adicionado para o utilizador [iniciador de aplicações](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

* Se aprovisionamento automática estiver ativado, em seguida, a utilizadores atribuídos e/ou grupos são adicionados à fila de aprovisionamento para ser automaticamente aprovisionada.

    * Se apenas objetos de utilizador foram configurados para ser aprovisionada, em seguida, todos os utilizadores directamente atribuído são colocados na fila de aprovisionamento e todos os utilizadores que são membros de grupos atribuídos irão ser colocados na fila de aprovisionamento. 
    
    * Se agrupar objetos foram configurados para ser aprovisionada, em seguida, todos os objetos de grupo atribuído configurações para a caixa, bem como todos os utilizadores que são membros dos grupos. Os membros do grupo e utilizador são preservados relativamente a ser escrito à caixa.
    
Pode utilizar o **atributos > Single Sign-On** separador para configurar os atributos de utilizador (ou em afirmações) são apresentadas à caixa durante a autenticação baseadas em SAML bem como a **atributos > aprovisionar** separador para configurar como atributos de utilizador e grupo fluam a partir do Azure AD para caixa durante o aprovisionamento de operações. Consulte os recursos abaixo para obter mais informações.


## <a name="additional-resources"></a>Recursos adicionais

* [Personalizar em afirmações emitidas no token de SAML](active-directory-saml-claims-customization.md)
* [Aprovisionamento: Personalizar mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md)
* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
