<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com DocuSign | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e DocuSign."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integração do Azure Active Directory com DocuSign

Este tutorial objectivo Mostrar a integração do Azure e DocuSign.
O cenário descrito neste tutorial assume que já tem os seguintes itens:

- Uma subscrição do Azure válida
- Um inquilino no DocuSign



O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1. [Ativar a integração da aplicação para DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configurar o início de sessão único](#configuring-single-sign-on) 


3. [Configurar o aprovisionamento de conta](#configuring-account-provisioning) 


4. [Atribuir utilizadores](#assigning-users) 

    ![Configurar o início de sessão único][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>Ativar a integração da aplicação para DocuSign

Objectivo de nesta secção é como ativar a integração da aplicação para DocuSign de destaque.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Para ativar a integração da aplicação para DocuSign, execute os seguintes passos:

1. No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Configurar o início de sessão único][1]

2. A partir da lista de diretório, selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Configurar o início de sessão único][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Sobre o que pretende fazer a caixa de diálogo, clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Configurar o início de sessão único][4]


6. Na caixa de pesquisa, escreva **DocuSign**.

    ![Configurar o início de sessão único][5]

7. No painel de resultados, selecione **DocuSign**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Configurar o início de sessão único][6]


## <a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para DocuSign com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1. No portal clássico Azure, na página **DocuSign integração de aplicações** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo Configurar Single Sign On.

    ![Configurar o início de sessão único][7]

2. Na página **como pretende utilizadores a iniciar sessão DocuSign** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em seguinte.

    ![Configurar o início de sessão único][8]

3. Na página **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o início de sessão único][61]

    um. Na caixa de texto **ao iniciar sessão no URL** , escreva `https://account.docusign.com/*`.  

    b. Na caixa de texto **identificador** , escreva `https://account.docusign.com/*`.  
   
    c. Clique em **seguinte**. 


    > [AZURE.TIP] O início de sessão no URL e os valores de identificador são apenas marcadores de posição. As instruções sobre como obter os valores reais para o seu ambiente são abrangidas mais adiante.
 

4. Na página **Configurar serviço single sign-on na DocuSign** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o início de sessão único][10]


5. Numa janela do browser web diferente, inicie sessão no seu **portal de administração DocuSign** como administrador.


6. No menu de navegação à esquerda, clique em **domínios**.

    ![Configurar o início de sessão único][51]

7. No painel direito, clique em **Afirmação domínio**.

    ![Configurar o início de sessão único][52]

8. Na caixa de diálogo **afirmação um domínio** , na caixa de texto **Nome de domínio** , escreva o seu domínio de empresa e, em seguida, clique em **afirmação**. Certifique-se de que verifique o domínio e o estado está ativo.

    ![Configurar o início de sessão único][53]

9. No menu no lado esquerdo, clique em **Fornecedores de identidade**  

    ![Configurar o início de sessão único][54]

10. No painel direito, clique em **Adicionar fornecedor de identidade**. 
    
    ![Configurar o início de sessão único][55]

11. Na página **Definições do fornecedor de identidade** , execute os seguintes passos:

    ![Configurar o início de sessão único][56]


    um. Na caixa de texto **nome** , escreva um nome exclusivo para a configuração. Não utilize espaços.

    b. No portal do Azure clássico, copie o URL de emissor e, em seguida, colá-la a caixa de texto **Emissor do fornecedor de identidade** .

    c. No portal do Azure clássico, copie o **URL remoto de início de sessão**e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de identidade** .

    d. No portal do Azure clássico, copie o **URL do remoto termine a sessão**e, em seguida, colá-la a caixa de texto do **URL de termine a sessão do fornecedor de identidade** .

    "e". Selecione o **Pedido de AuthN de início de sessão**.

    f. Como **AuthN enviar pedido por**, selecione **Publicar**.

    g. Como **Enviar termine a sessão pedido por**, selecione **Publicar**. 


12. Na secção de **Mapeamento de atributos personalizados** , escolha o campo que pretende mapear com Azure AD afirmação. Neste exemplo, a afirmação **emailaddress** está mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Este é o nome da afirmação predefinidas a partir do Azure AD para afirmação de correio eletrónico. 

    > [AZURE.NOTE] Utilize o **identificador de utilizador** de adequado para mapear o utilizador a partir do Azure AD Docusign mapeamento de utilizador. Selecione o campo inicial e introduza o valor adequado com base em definições da sua organização.

    ![Configurar o início de sessão único][57]

13. Na secção de **Certificado de fornecedor de identidade** , clique em **Adicionar certificado**e, em seguida, carregue o certificado que transferiu a partir do portal clássico do Azure AD.   

    ![Configurar o início de sessão único][58]

14. Clique em **Guardar**.

15. Na secção **Fornecedores de identidade** , clique em **ações**e, em seguida, clique em **pontos finais**.   

    ![Configurar o início de sessão único][59]



10. No portal do Azure clássico, volte à página **Configurar definições de aplicação** . 

16. No **portal de administração DocuSign**, na secção **Ver SAML 2.0 os pontos finais** efetue, os seguintes passos:

    ![Configurar o início de sessão único][60]

    um. Copie o **URL do serviço de fornecedor emissor**e, em seguida, colá-la a caixa de texto do **identificador** no portal do Azure clássica.

    b. Copie o **URL do serviço de fornecedor de início de sessão**e, em seguida, cole a caixa de texto do **URL no início de sessão** no portal do Azure clássica.

    c.  Clique em **Fechar**  


10. No portal do Azure clássico, clique em **seguinte**. 


15. No portal do Azure clássico, selecione a **confirmação de configuração de início de sessão único**e, em seguida, clique em **seguinte**.

    ![Aplicações][14]

10. Na página de **confirmação de início de sessão única** , clique em **Concluir**.

    ![Aplicações][15]
 

## <a name="configuring-account-provisioning"></a>Configurar o aprovisionamento de conta

Objectivo de nesta secção é como ativar o utilizador aprovisionamento de contas de utilizador do Active Directory para DocuSign de destaque.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1. No **portal clássica Azure**, na página **DocuSign integração de aplicações** , clique em **Configurar aprovisionamento de conta** para abrir a caixa de diálogo Configurar aprovisionamento de utilizador.

    ![Configurar o aprovisionamento de conta][30]

2. Na página de **definições e as credenciais de administrador** , para ativar o utilizador automática de aprovisionamento, fornecer as credenciais de uma conta de DocuSign com direitos suficientes e, em seguida, clique em **seguinte**. 

    ![Configurar o aprovisionamento de conta][31]

3. Na caixa de diálogo **testar a ligação** , clique em **Iniciar o teste**e relativamente a um teste com êxito, clique em **seguinte**.

    ![Configurar o aprovisionamento de conta][32]

3. Na página de **confirmação** , clique em **Concluir**.

    ![Configurar o aprovisionamento de conta][33]
 

## <a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Para atribuir utilizadores a DocuSign, execute os seguintes passos:

1. No **portal clássica Azure**, crie uma conta de teste.

2. Na página **DocuSign integração de aplicações** , clique em **atribuir aos utilizadores**.

    ![Atribuir utilizadores][40]
 

3. Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Atribuir utilizadores][41]


Agora que deve esperar 10 minutos e certifique-se de que a conta foi sincronizada para DocuSign.

Como primeiro passo verificação, pode verificar o estado de aprovisionamento, ao clicar em Dashboard no D na página DocuSign aplicação integração no portal do Azure clássica.

![Atribuir utilizadores][42]

Um utilizador concluído com êxito aprovisionamento ciclo é indicado por um Estado relacionado:

![Atribuir utilizadores][43]


Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso.

Para obter mais detalhes acerca do painel de acesso, consulte Introdução ao painel do Access.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png