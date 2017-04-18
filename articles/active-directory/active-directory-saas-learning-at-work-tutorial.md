<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com formação no trabalho | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e aprendizagem no local de trabalho."
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


# <a name="tutorial-azure-active-directory-integration-with-learning-at-work"></a>Tutorial: Integração do Azure Active Directory com formação no trabalho

Neste tutorial, aprende integrar formação no local de trabalho com o Azure Active Directory (Azure AD).

Integrar o formação no local de trabalho com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao aprendizagem no trabalho
- Pode permitir que os utilizadores para automaticamente obter assinado-na aprendizagem no trabalho (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com formação no trabalho, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um de formação no trabalho (Saba nuvem) sessão único com capacidade de subscrição


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar aprendizagem no trabalho a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-learning-at-work-from-the-gallery"></a>Adicionar aprendizagem no trabalho a partir da Galeria
Para configurar a integração de aprendizagem no trabalho para o Azure AD, é necessário adicionar aprendizagem no trabalho a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar aprendizagem no trabalho a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory][1]
2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva a **aprendizagem no trabalho**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_01.png)

7. No painel de resultados, selecione **formação no local de trabalho**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_06.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configurar e testar o Azure AD serviço single sign-on com formação em funcionamento com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo em aprender no trabalho é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em aprender no trabalho tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** em aprender no local de trabalho.

Para configurar e testar Azure AD serviço single sign-on com formação no trabalho, tem de concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar uma formação no trabalho testar utilizador](#creating-a-predictix-price-reporting-test-user)** - ter homólogo para Britta Simon no aprendizagem no trabalho que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aprendizagem na aplicação de trabalho.


**Para configurar Azure AD serviço single sign-on com formação no trabalho, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação de **aprendizagem no trabalho** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão aprendizagem no trabalho** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_04.png) 

    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aprendizagem na aplicação de trabalho utilizando o padrão de seguinte:`https://\<company name\>.sabacloud.com/Saba/Web/<company code>`
    
    b. Na caixa de texto **identificador** , escreva o URL com o seguinte padrão: ' https://<company name>.sabacloud.com/Saba/SAML/sso/alias/<company name>``

    c. Clique em **seguinte**
 
4. Na página **Configurar serviço single sign-on na formação no trabalho** , efetue os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_05.png)

    um. Clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Para obter SSO configurado para a sua aplicação, contactar formação a equipa de suporte do trabalho (Saba nuvem) e forneça-lhe o seguinte procedimento:

    • Os metadados transferido

    • O **emissor Url**

    • O **URL do SAML SSO**

    • O **início de sessão único saída URL do serviço**

6. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.


![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , execute os seguintes passos:  ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learning-at-work-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-an-learning-at-work-test-user"></a>Criar uma formação no utilizador de teste de trabalho

Nesta secção, crie um utilizador denominado Britta Simon em aprender no local de trabalho. Trabalhe com formação a equipa de suporte de trabalho para adicionar os utilizadores em aprender a plataforma de trabalho.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a aprendizagem no local de trabalho.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon a aprendizagem no trabalho, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **formação no local de trabalho**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-learning-at-work-tutorial/tutorial_learningatwork_50.png) 

3. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


### <a name="testing-single-sign-on"></a>Testes Single Sign-On

Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica em aprendizagem no mosaico de trabalho no painel do Access, pode deve obter automaticamente assinado-on para sua aprendizagem na aplicação de trabalho.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learning-at-work-tutorial/tutorial_general_205.png
