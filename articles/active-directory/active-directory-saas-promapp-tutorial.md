<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Promapp | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Promapp."
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
    ms.date="09/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: Integração do Azure Active Directory com Promapp

Objectivo deste tutorial é mostrar-lhe como integrar o Promapp com o Azure Active Directory (Azure AD).  
Integrar o Promapp com o Azure AD fornece os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso ao Promapp 
- Pode permitir que os utilizadores para automaticamente obter assinado-on para Promapp (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure Active Directory

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com Promapp, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Promapp sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Promapp a partir da Galeria 
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-promapp-from-the-gallery"></a>Adicionar Promapp a partir da Galeria
Para configurar a integração da Promapp no Azure AD, é necessário adicionar Promapp a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Promapp a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Promapp**.

    ![Aplicações][5]

7. No painel de resultados, selecione **Promapp**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Aplicações][500]

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com Promapp com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no Promapp a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Promapp tem de ser estabelecida.  
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Promapp.
 
Para configurar e testar Azure AD serviço single sign-on com Promapp, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um Promapp testar utilizador](#creating-a-halogen-software-test-user)** - ter homólogo para Britta Simon no Promapp que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure AD e para configurar o início de sessão único na sua aplicação Promapp.

**Para configurar Azure AD serviço single sign-on com Promapp, execute os seguintes passos:**

1. No portal clássico do Azure AD, na página de integração de aplicação **Promapp** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão Promapp** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][7] 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Azure AD Single Sign-On][8] 
 
     um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão no seu site Promapp (por exemplo: *https://companyname.promapp.com/instancename*).


     b. Clique em **seguinte**.
 
4. Na página **Configurar serviço single sign-on na Promapp** , execute os seguintes passos:

    ![Azure AD Single Sign-On][9] 

    um. Clique em Transferir certificado e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


6. Início de sessão no seu site de empresa Promapp como administrador. 

6. No menu no canto superior, clique em **Admin**. 

    ![Azure AD Single Sign-On][12]

6. Clique em **Configurar**. 

    ![Azure AD Single Sign-On][13]



4. Na caixa de diálogo **segurança** , execute os seguintes passos:

    ![Azure AD Single Sign-On][14] 

    um. No portal clássico Azure, na **Configurar serviço single sign-on na Promapp** caixa de diálogo, copie o **URL de início de sessão remoto**, colá-lo na caixa de texto de **Início de sessão SSO URL** e, em seguida, clique em **Guardar**.

    b. Como **SSO - Single Sign-on modo**, selecione **opcional**e, em seguida, clique em **Guardar**.

    c. Abra o certificado transferido no bloco de notas, copie o conteúdo de certificado sem a primeira linha (*---começar certificado---*) e a última linha (*---Terminar certificado---*), colá-lo na caixa de texto de **certificado SSO-x. 509** e, em seguida, clique em **Guardar**.




6. No portal do Azure AD clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png)  

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**. 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png)  

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) 
 
    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.
    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) 
  
    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   

  
 
### <a name="creating-a-promapp-test-user"></a>Criar um utilizador de teste Promapp

A aplicação Promapp suporta apenas em vez de aprovisionamento.
Isto significa, uma conta de utilizador é criada automaticamente se for necessário durante uma tentativa de aceder à aplicação utilizando o painel de acesso.  


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Promapp.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Promapp, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Promapp**.

    ![Atribuir utilizador][202] 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico Promapp no painel do Access, pode deve obter automaticamente assinado-on à aplicação Promapp.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png
