<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com FieldGlass | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e FieldGlass."
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


# <a name="tutorial-azure-active-directory-integration-with-fieldglass"></a>Tutorial: Integração do Azure Active Directory com FieldGlass

Objectivo deste tutorial é mostrar-lhe como integrar o FieldGlass com o Azure Active Directory (Azure AD).

Integrar o FieldGlass com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao FieldGlass
- Pode permitir que os utilizadores para automaticamente obter assinado-on para FieldGlass (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com FieldGlass, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um FieldGlass sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar FieldGlass a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-fieldglass-from-the-gallery"></a>Adicionar FieldGlass a partir da Galeria
Para configurar a integração da FieldGlass no Azure AD, é necessário adicionar FieldGlass a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar FieldGlass a partir da galeria, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **FieldGlass**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_01.png)
7. No painel de resultados, selecione **FieldGlass**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com FieldGlass com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no FieldGlass a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na FieldGlass tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no FieldGlass.

Para configurar e testar Azure AD serviço single sign-on com FieldGlass, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um FieldGlass testar utilizador](#creating-a-fieldglass-test-user)** - ter homólogo para Britta Simon no FieldGlass que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação FieldGlass.

**Para configurar Azure AD serviço single sign-on com FieldGlass, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação **FieldGlass** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão FieldGlass** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_04.png)

    um. Na caixa de texto **identificador** , escreva o URL `https://www.fieldglass.com` ou siga o padrão de:`https://<company name>.fgvms.com`

    b. Na caixa de texto **Resposta URL** , escreva um URL que utiliza os padrões de seguintes: 
    - `https://<company name>.fgvms.com/<company name>`
    
    - `https://www.fieldglass.net/<company name>`

    c. Clique em **seguinte**

    > [AZURE.NOTE] Tenha em atenção que estes não são os valores reais. Tem de atualizar os valores com o identificador e o URL de resposta real. Para obter estes valores, contacte o [FieldGlass](http://www.fieldglass.com/solutions/support).

4. Na página **Configurar serviço single sign-on na FieldGlass** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_05.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.

5. Para obter SSO configurado para a sua aplicação, contacte a equipa de suporte FieldGlass e forneça-lhe o seguinte procedimento: 

    -O ficheiro de **certificado transferidos**

    -O **ID da entidade**

    -O **URL do serviço de terminar sessão único**

6. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico denominado Britta Simon.
    
![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_09.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_04.png)

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_05.png)

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_06.png)

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_08.png)

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-fieldglass-test-user"></a>Criar um utilizador de teste FieldGlass

É o objectivo de nesta secção criar um utilizador designado Britta Simon no trabalho FieldGlass.Please com a sua equipa de suporte FieldGlass para adicionar os utilizadores na conta FieldGlass.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a FieldGlass.
    
![Atribuir utilizador][200]

**Para atribuir Britta Simon FieldGlass, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **FieldGlass**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_50.png)

3. No menu no canto superior, clique em **utilizadores**.
    
    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico FieldGlass no painel do Access, pode deve obter automaticamente assinado-on à aplicação FieldGlass.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_205.png
