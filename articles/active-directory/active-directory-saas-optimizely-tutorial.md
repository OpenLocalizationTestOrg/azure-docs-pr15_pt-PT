<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Optimizely | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Optimizely."
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
    ms.date="09/11/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Tutorial: Integração do Azure Active Directory com Optimizely

Neste tutorial, aprende integrar Optimizely com o Azure Active Directory (Azure AD).

Integrar o Optimizely com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Optimizely
- Pode permitir que os utilizadores para automaticamente obter assinado-on para Optimizely (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Optimizely, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um **Optimizely** sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste. O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Optimizely a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-optimizely-from-the-gallery"></a>Adicionar Optimizely a partir da Galeria
Para configurar a integração da Optimizely no Azure AD, é necessário adicionar Optimizely a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Optimizely a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Optimizely**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. No painel de resultados, selecione **Optimizely**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com Optimizely com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na Optimizely é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Optimizely tem de ser estabelecida.
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Optimizely.

Para configurar e testar Azure AD serviço single sign-on com Optimizely, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um Optimizely testar utilizador](#creating-an-optimizely-test-user)** - ter homólogo para Britta Simon no Optimizely que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação Optimizely.

Aplicação Optimizely espera as declarações de SAML para conter um atributo com o nome "e-mail". O valor de "e-mail" deve ser uma mensagem de e-mail Optimizely reconhecida que pode obter autenticada pelo Azure AD. Configure a afirmação "e-mail" para esta aplicação. Pode gerir os valores das seguintes atributos a partir do separador **"Atrributes"** da aplicação. A captura de ecrã seguinte mostra um exemplo para esta situação. 


![Configurar o Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 


**Para configurar Azure AD serviço single sign-on com Optimizely, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação de **Optimizely** , no menu na parte superior, clique em **atributos**.
     
    ![Configurar o Single Sign-On][5]

2. Na caixa de diálogo SAML atributos token, adicione o atributo "e-mail".

    um. Clique em **Adicionar utilizador atributo** para abrir a caixa de diálogo **Adicionar atributo do utilizador** . 
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

    b. Na caixa de texto **Nome do atributo** , escreva o atributo nome "correio eletrónico".

    c. A partir da lista de **Valor do atributo** , selecione o valor do atributo "userprincipalname" ou qualquer valor que contém uma mensagem de e-mail reconhecida pelo Azure AD e Optimizely.

    d. Clique em **concluído**.
3. No menu no canto superior, clique em **Guia de introdução**.

    ![Configurar o Single Sign-On][6]
4. No portal do clássico, na página de integração de aplicação **Optimizely** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][7] 

5. Na página **como pretende utilizadores a iniciar sessão Optimizely** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos: 

    ![Configurar o Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    um. Na caixa de texto **URL no início de sessão** , escreva:`https://app.optimizely.net/contoso`

    b. Na caixa de texto **identificador** , escreva:`urn:auth0:optimizely:contoso`

    c. Clique em **seguinte**. 


    > [AZURE.NOTE] Os valores para o **Início de sessão no URL** e o **identificador** são apenas marcadores de posição para os valores reais. Pode encontrar instruções para aquiring os valores reais da Optimizely posteriormente neste tutorial.

7. Na página **Configurar serviço single sign-on na Optimizely** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Copie o **URL do serviço Single Sign-On**.

8. Para obter SSO configurado para a sua aplicação, contacte o seu Gestor de conta Optimizely e forneça as seguintes informações:

    - O certificado transferido 
    - O URL do serviço de início de sessão único
 
    Em resposta ao seu e-mail, Optimizely fornece-lhe com o início de sessão no URL (iniciados por SP SSO) e os valores de identificador (ID da entidade de fornecedor de serviço).

9. Regressar à página de diálogo **Configurar definições de aplicação** e, em seguida, execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    um. Na caixa de texto **URL no início de sessão** , escreva o **URL de SSO iniciados por SP** fornecido pelo Optimizely.

    b. Na caixa de texto **identificador** , escreva o **ID da entidade de fornecedor de serviço** fornecido pelo Optimizely.

    c. Clique em **seguinte**.

10. Na página **Configurar serviço single sign-on na Optimizely** , execute os seguintes passos:
    
    ![Azure AD Single Sign-On][10]

    um. Selecione a confirmação de configuração de início de sessão único.

    b. Clique em **seguinte**.

11. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]

12. Numa janela do browser diferente, início de sessão na sua aplicação Optimizely.
13. Clique em que nome no canto superior direito e, em seguida, **Definições de conta**da conta.

    ![Azure AD Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. No separador conta, selecione a caixa **Ativar SSO** em Single Sign On na secção **Overview** .

    ![Azure AD Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.
Na lista de utilizadores, selecione **Britta Simon**.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-an-optimizely-test-user"></a>Criação de um utilizador de teste Optimizely

Nesta secção, crie um utilizador designado Britta Simon no Optimizely.

1. Na home page, selecione o separador dos **colaboradores**
2. Clique em **Novo Collaborator** para adicionar um novo collaborator ao projeto.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Preencha o endereço de e-mail e atribua-lhes uma função. Clique em **Convidar**.


    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Estes irão receber um convite de e-mail. Utilizar o endereço de e-mail. possuem que inicie sessão no Optimizely.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Optimizely.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Optimizely, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Optimizely**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de todos os utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico Optimizely no painel do Access, pode deve obter automaticamente assinado-on à aplicação Optimizely.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png
