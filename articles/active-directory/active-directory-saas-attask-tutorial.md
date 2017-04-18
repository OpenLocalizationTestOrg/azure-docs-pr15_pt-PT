<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com @Task| Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e @Task."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-task"></a>Tutorial: Integração do Azure Active Directory com@Task

O objectivo deste tutorial é mostrar-lhe como integrar o @Task com o Azure Active Directory (Azure AD).  
Integrar o @Task com Azure AD fornece-lhe com os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso a@Task
- Pode permitir que os utilizadores obter automaticamente a sessão iniciada para @Task (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - Portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com @Task, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um @Task sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial é constituído por três blocos modulares principais:

1. Adicionar @Task a partir da Galeria 
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-task-from-the-gallery"></a>Adicionar @Task a partir da Galeria
Para configurar a integração do @Task para Azure AD, tem de adicionar @Task a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar @Task a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1] 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2] 

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3] 

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4] 

6. Na caixa de pesquisa, escreva **@Task**.

    ![Aplicações][5] 

7. No painel de resultados, selecione **@Task**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Aplicações][30] 



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão

O objectivo desta secção é mostrar-lhe como configurar e testar o Azure AD serviço single sign-on com @Task com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na @Task a um utilizador no Azure AD é. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na @Task tem de ser estabelecida.   
Este relação ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no @Task.
 
Para configurar e testar Azure AD serviço single sign-on com @Task, necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um @Tasktest utilizador](#creating-a-halogen-software-test-user) ** - ter homólogo para Britta Simon no @Taskthat está ligada a representação de Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo nesta secção é para ativar Azure AD serviço single sign-on no portal clássico do Azure e para configurar o serviço single sign-on no seu @Task aplicação.

**Para configurar o Azure AD serviço single sign-on com @Task, efetuar os seguintes passos:**

1. No portal do Azure clássico, sobre a **@Task** integração de aplicações da página, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. No **como pretende utilizadores a iniciar sessão no @Task ** da página, selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][7] 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar definições de aplicação][8] 
 
     um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua @Task aplicação (por exemplo:*https://<Tenant name>.attask ondemand.com*).

     b. Clique em **seguinte**.

4. No **configurar o serviço single sign-on na @Task ** da página, clique em **transferir metadados**, guarde o ficheiro de metadados localmente no seu computador e, em seguida, clique em **seguinte**.

    ![O que é a ligação do Azure AD][9] 



1. Início de sessão na sua @Task site de empresa como administrador.

2. Vá para **Single Sign-On configuração**.


1. No diálogo **Single Sign-On** , execute os passos seguintes

    ![Configurar o Single Sign-On][23]

    um. Como **tipo**, selecione **SAML 2.0**.

    b. Selecione o **ID do fornecedor de serviço**.

    c. No portal do Azure clássico, copie o **URL remoto de início de sessão**e, em seguida, colá-la a caixa de texto do **URL do Portal de início de sessão** .

    d. No portal do Azure clássico, copie o **URL do serviço Sign-Out único**e, em seguida, colá-la a caixa de texto **Sign-Out URL** .

    "e". No portal do Azure clássico, copie o **URL do alterar palavra-passe**e, em seguida, colá-la a caixa de texto **Alterar URL de palavra-passe** .

    f. Clique em **Guardar**.

6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 

    ![O que é a ligação do Azure AD][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![O que é a ligação do Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.  

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**. 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
 
    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.
    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
  
    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   

  
 
### <a name="creating-an-task-test-user"></a>Criar um @Task utilizador de teste

O objectivo nesta secção é criar um utilizador designado Britta Simon no @Task.


**Para criar um utilizador designado Britta Simon no @Task, efetuar os seguintes passos:**

1. Inicie sessão no seu @Task site de empresa como administrador.

2. No menu no canto superior, clique em **pessoas**.

3. Clique em **novo de uma pessoa**. 

4. Na caixa de diálogo novo de uma pessoa, execute os seguintes passos:

    ![Criar um @Task utilizador de teste][21] 

    um. Na caixa de texto **nome próprio** , escreva "Britta".

    b. Na caixa de texto **Apelido** , escreva "Simon".

    c. Na caixa de texto **Endereço de correio eletrónico** , escreva o endereço de e-mail de Britta Simon no Azure Active Directory.

    d. Clique em **Adicionar pessoa**.




### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

O objectivo desta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a @Task.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon para @Task, efetuar os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **@Task**.

    ![Atribuir utilizador][202] 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no @Task dispor em mosaico no painel do Access, deve obter automaticamente assinado-on para sua @Task aplicação.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






