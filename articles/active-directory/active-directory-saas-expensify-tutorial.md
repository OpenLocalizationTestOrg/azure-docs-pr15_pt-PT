<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Expensify | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Expensify."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Tutorial: Integração do Azure Active Directory com Expensify

Neste tutorial, aprende integrar Expensify com o Azure Active Directory (Azure AD).

Integrar o Expensify com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Expensify
- Pode permitir que os utilizadores para automaticamente obter assinado-on para Expensify (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Expensify, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Expensify serviço single sign-on com capacidade de subscrição


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste. O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Expensify a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-expensify-from-the-gallery"></a>Adicionar Expensify a partir da Galeria
Para configurar a integração da Expensify no Azure AD, é necessário adicionar Expensify a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Expensify a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Expensify**.
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_01.png)

7. No painel de resultados, selecione **Expensify**e, em seguida, clique em **Concluir** para adicionar a aplicação.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com Expensify com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na Expensify é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Expensify tem de ser estabelecida.  
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Expensify.

Para configurar e testar Azure AD serviço single sign-on com Expensify, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um Expensify testar utilizador](#creating-an-expensify-test-user)** - ter homólogo para Britta Simon no Expensify que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação Expensify.


**Para configurar Azure AD serviço single sign-on com Expensify, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação **Expensify** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão Expensify** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_04.png) 


    um. Na caixa de texto URL no início de sessão, escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de Expensify utilizando o seguinte padrão de: **"https://www.expensify.com/authentication/saml/login"**.


4. Na página **Configurar serviço single sign-on na Expensify** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_05.png) 

    um. Clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Para ativar o SSO no Expensify tem primeiro de ativar o **Controlo de domínio** na aplicação. Pode ativar o controlo de domínio na aplicação. Os passos estão listados [aqui](http://help.expensify.com/domain-control). Para obter suporte adicional, pode contactar o suporte técnico da Expensify através do [help@expensify.com](mailto:help@expensify.com). Assim que tiver ativado de controlo de domínio, siga estes passos:
 
    ![Configurar o Single Sign-On](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_51.png) 

    um. Inicie sessão sua aplicação Expensify.
   
    b. Na barra de ferramentas no canto superior, clique em **Admin**.

    c. No painel da esquerda, clique em **Controlo de domínio**.

    d. Clique no seu nome de domínio verificado.

    "e". No painel da esquerda, clique em **SAML**e, em seguida, selecione **ativado**.

    f. Abrir os metadados de Federação transferidas a partir do Azure AD, copie o conteúdo e, em seguida, colá-la a caixa de texto de **Metadados do fornecedor de identidade** .

  
6. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
  
    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-expensify-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-an-expensify-test-user"></a>Criação de um utilizador de teste Expensify

Nesta secção, crie um utilizador designado Britta Simon no Expensify. Consulte trabalhe com Expensify equipa de suporte para adicionar os utilizadores da plataforma Expensify.

> [AZURE.NOTE] Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte Expensify.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Expensify.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Expensify, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Expensify**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-expensify-tutorial/tutorial_expensify_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico Expensify no painel do Access, pode deve obter automaticamente assinado-on à aplicação Expensify.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-expensify-tutorial/tutorial_general_205.png
