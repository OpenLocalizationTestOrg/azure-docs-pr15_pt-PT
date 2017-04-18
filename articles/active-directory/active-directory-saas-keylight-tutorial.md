<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Keylight | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Keylight."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-keylight"></a>Tutorial: Integração do Azure Active Directory com Keylight

Neste tutorial, aprende integrar Keylight com o Azure Active Directory (Azure AD).

Integrar o Keylight com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Keylight
- Pode permitir que os utilizadores para automaticamente obter assinado-on para Keylight (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Keylight, tem os seguintes itens:

- Uma subscrição do Azure
- Um Keylight sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste. 

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Keylight a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-keylight-from-the-gallery"></a>Adicionar Keylight a partir da Galeria
Para configurar a integração da Keylight no Azure AD, é necessário adicionar Keylight a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Keylight a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Keylight**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_01.png)

7. No painel de resultados, selecione **Keylight**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com Keylight com base num utilizador teste denominado "Britta Simon".

Para configurar e testar Azure AD serviço single sign-on com Keylight, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um Keylight testar utilizador](#creating-a-keylight-test-user)** - ter homólogo para Britta Simon no Keylight que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

Nesta secção, ativar o Azure AD serviço single sign-on no portal clássico do Azure e configurar o início de sessão único na sua aplicação Keylight.


**Para configurar Azure AD serviço single sign-on com Keylight, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação **Keylight** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 


2. Na página **como pretende utilizadores a iniciar sessão Keylight** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:
 
    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_04.png) 


    um. Na caixa de texto URL no início de sessão, escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de Keylight utilizando o seguinte padrão: **"https://\<nome da empresa\>.keylightgrc.com/Login.aspx?saml=1"**.


4. Na página **Configurar serviço single sign-on na Keylight** , execute os seguintes passos:
 
    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_05.png) 

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Para ativar SSO no Keylight, execute os seguintes passos:
 
    um. Início de sessão na sua conta Keylight como administrador.

    b. No menu no canto superior, clique em **pessoa**e selecione **Keylight configuração**.
       
    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Em treeview à esquerda, clique em **SAML**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Na caixa de diálogo **Definições de SAML** , clique em **Editar**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/404.png) 
  

5. Na página de diálogo **Editar definições de SAML** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/405.png) 

    um. Definir a **autenticação de SAML** para **ativo**.


    b. No portal clássico do Azure AD, copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de identidade** .

    c. No portal clássico do Azure AD, copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto do **URL de termine a sessão do fornecedor de identidade** .

    d. Clique em **Selecionar ficheiro** para selecionar o seu certificado de Keylight transferido e, em seguida, clique em **Abrir** para carregar o certificado.


    "e". Definir a **localização de Id de utilizador do SAML** **NameIdentifier**elemento da instrução assunto.
   
    f. Fornecer a **fornecedor de serviços de Keylight utilizando o seguinte padrão: **https://&lt;nome da empresa&gt;. keylightgrc.com**.

    g. Defina **o automática aprovisionamento de utilizadores** para **ativo**.

    h. Definir o **tipo de conta automática aprovisionar** a **Utilizador completo**.

    posso. Como a **função de segurança automática aprovisionar**, selecione o **Utilizador padrão com SAML**.
   
    j. Como **aprovisionar automática config de segurança**, selecione **Padrão configuração do utilizador**.
   
    k. Na caixa de texto do atributo de correio eletrónico, escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    l. Na caixa de texto **nome próprio atributo** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    m. Na caixa de texto **último atributo nome** , escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    n. Clique em **Guardar**.
   
  
   
  
6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico Azure denominado Britta Simon.

Na lista de utilizadores, selecione **Britta Simon**.

![Criar utilizador do Azure AD][20]



**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_09.png) 


2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 


4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-keylight-test-user"></a>Criar um utilizador de teste Keylight

Nesta secção, crie um utilizador designado Britta Simon no Keylight. Keylight suporta-in-time aprovisionamento, que está ativado por predefinição.

Não existe nenhum item de ação para si nesta secção. Um novo utilizador for criado ao aceder ao Keylight se o utilizador ainda não existir. 

> [AZURE.NOTE] Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte Keylight.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Keylight.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Keylight, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Keylight**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico Keylight no painel do Access, pode deve obter automaticamente assinado-on à aplicação Keylight.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_205.png
