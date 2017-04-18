<properties
    pageTitle="Tutorial: Integração do Azure Active Directory da Ajuda rápida | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e da Ajuda rápida."
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


# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Tutorial: Integração do Azure Active Directory da Ajuda rápida

Objectivo deste tutorial é mostrar-lhe como integrar da Ajuda rápida com o Azure Active Directory (Azure AD).  
Integração da Ajuda rápida com Azure AD fornece os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso da Ajuda rápida 
- Pode permitir que os utilizadores para automaticamente obter assinado-on para da Ajuda rápida (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD da Ajuda rápida, tem os seguintes itens:

- Uma subscrição do Azure AD
- Da Ajuda rápida sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar da Ajuda rápida a partir da Galeria 
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-quickhelp-from-the-gallery"></a>Adicionar da Ajuda rápida a partir da Galeria
Para configurar a integração da Ajuda rápida no Azure AD, é necessário adicionar da Ajuda rápida a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar da Ajuda rápida a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **QuickHelp**.

    ![Aplicações][5]

7. No painel de resultados, selecione **QuickHelp**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Aplicações][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com da Ajuda rápida com base num utilizador teste denominado "Britta Simon".


Para configurar e testar Azure AD serviço single sign-on da Ajuda rápida, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar da Ajuda rápida testar utilizador](#creating-a-quickhelp-test-user)** - ter homólogo para Britta Simon da Ajuda rápida que está ligada à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação da Ajuda rápida.


**Para configurar Azure AD serviço single sign-on com da Ajuda rápida, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação **QuickHelp** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão da Ajuda rápida** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][7] 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar definições de aplicação][8] 
 
     um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão no seu site da Ajuda rápida (por exemplo:* https://quickhelp.com/bsiazure/*).

     > [AZURE.NOTE] Contacte a equipa de suporte da Ajuda rápida se não souber o valor do início de sessão no URL.

     b. Clique em **seguinte**.

 
4. Na página **Configurar serviço single sign-on da Ajuda rápida** , execute o seguinte passos: clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![O que é a ligação do Azure AD][9] 



1. Início de sessão no seu site de empresa da Ajuda rápida como administrador.

2. No menu no canto superior, clique em **Admin**.

    ![Configurar o Single Sign-On][21]


1. No menu de **Administração da Ajuda rápida** , clique em **Definições**.

    ![Configurar o Single Sign-On][22]

1. Clique em **definições de autenticação**.

1. Na página **Definições de autenticação** , execute os seguintes passos

    ![Configurar o Single Sign-On][23]

    um. Como **SSO tipo**, selecione **WSFederation**.

    b. Carregue o seu ficheiro de metadados Azure transferido, clique em **Procurar**, navegue para o ficheiro, terminar, em seguida, clique em **Carregar metadados**.

    c. Na caixa de **correio eletrónico** do texto, escreva **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    d. Na caixa de **nome próprio** texto, **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname tipo**.

    "e". Na caixa de texto **Apelido** , **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname tipo**

    f. Na **Barra de ação**, clique em **Guardar**.







6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 

    ![O que é a ligação do Azure AD][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![O que é a ligação do Azure AD][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.  
Na lista de utilizadores, selecione **Britta Simon**.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**. 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos: 
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_06.png) 
 
    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.
    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_08.png) 
  
    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   

  
 
### <a name="creating-a-quickhelp-test-user"></a>Criar um utilizador de teste da Ajuda rápida

É o objectivo de nesta secção criar um utilizador designado Britta Simon da Ajuda rápida.
Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo da Ajuda rápida para um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado da Ajuda rápida tem de ser estabelecida.

QuickHelp suporta-in-time aprovisionamento. Isto significa que, se necessário, uma conta de utilizador é criada automaticamente da Ajuda rápida e a conta está ligada para a conta do Azure AD.

Não existe nenhum item de ação para si nesta secção.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso da Ajuda rápida.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon da Ajuda rápida, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **QuickHelp**.

    ![Atribuir utilizador][202] 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico da Ajuda rápida no painel do Access, pode deve obter automaticamente assinado-on à aplicação da Ajuda rápida.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_01.png
[500]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_14.png


[6]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_02.png
[8]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_03.png
[9]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_04.png
[10]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
[24]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_08.png
[25]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_09.png
[26]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_10.png
[27]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_11.png
[28]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_12.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_13.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_400.png
[401]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_401.png
[402]: ./media/active-directory-saas-QuickHelp-tutorial/tutorial_QuickHelp_402.png




