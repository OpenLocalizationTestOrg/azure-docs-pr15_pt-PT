<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com & franqueza | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e & franqueza."
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
    ms.date="08/12/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Tutorial: Integração do Azure Active Directory com & franqueza

O objectivo deste tutorial é mostrar-lhe como integrar o & franqueza com o Azure Active Directory (Azure AD).

Integrar o & franqueza com Azure AD fornece-lhe com os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso à & franqueza
- Pode permitir que os utilizadores obter automaticamente sessão iniciada à & franqueza (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o Azure AD integração com o & franqueza, tem os seguintes itens:

- Uma subscrição do Azure AD
- A & franqueza sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar e franqueza a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-frankly-from-the-gallery"></a>Adicionar e franqueza a partir da Galeria
Para configurar a integração de & franqueza para Azure AD, tem de adicionar & franqueza a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar & franqueza a partir da galeria, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **& franqueza**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_01.png)

7. No painel de resultados, selecione **& franqueza**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostram-lhe como configurar e testar Azure AD único início de sessão com & franqueza com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo & franqueza a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em & franqueza tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** em & franqueza.

Para configurar e testar o Azure AD serviço single sign-on com & franqueza, tem de concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um & franqueza utilizador de teste](#creating-a-&frankly-test-user)** - ter homólogo para Britta Simon em & franqueza seja ligada à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, pode ativar Azure AD serviço single sign-on no portal do clássico e configurar o serviço single sign-on no seu & franqueza aplicação.

**Para configurar o Azure AD único início de sessão com & franqueza, execute os seguintes passos:**

1. No portal do clássico, na página da **& franqueza** aplicação integração, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão à & franqueza** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_03.png)

3. Na página de diálogo **Configurar definições de aplicação** , se pretender configurar a aplicação no **modo de iniciados por IDP**, execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_04.png)

    um. Na caixa de texto **identificador** , escreva um URL utilizando o padrão seguinte:`https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`

    b. Na caixa de texto **Resposta URL** , escreva um URL utilizando o padrão seguinte:`https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`

    c. Clique em **seguinte**

4. Se pretender configurar a aplicação no **modo de iniciados por SP** na página de diálogo **Configurar definições de aplicação** , em seguida, clique no **"Mostrar avançadas definições (opcional)"** e, em seguida, introduza o **URL no início de sessão** e clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_05.png)

    um. Na caixa de texto **URL no início de sessão** , escreva um URL utilizando o padrão seguinte:`https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`

    b. Clique em **seguinte**

    > [AZURE.NOTE] Tenha em atenção que estes não são os valores reais. Tem de atualizar estes valores com o URL no início de sessão, identificador e URL de resposta real. Contacto [help@andfrankly.com](emailTo:help@andfrankly.com) para obter estes valores.

5. No **configurar o serviço single sign-on na & franqueza** da página, execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_06.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.

6. Para obter SSO configurado para a sua aplicação, contacte o seu & equipa através de suporte franqueza [help@andfrankly.com](emailTo:help@andfrankly.com). Anexe o ficheiro transferido metadados e partilhá-lo com e franqueza de equipa para configurar o SSO no respetivo lado.

7. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

8. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_09.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_04.png)

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_05.png)

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_06.png)

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_08.png)

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-frankly-test-user"></a>Criar um & franqueza utilizador de teste

Nesta secção, crie um utilizador denominado Britta Simon em & franqueza. Consulte Trabalhar com & equipa através de suporte franqueza [help@andfrankly.com](emailTo:help@andfrankly.com) para adicionar os utilizadores na & franqueza plataforma.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso à & franqueza.
    
![Atribuir utilizador][200]

**Para atribuir Britta Simon à & franqueza, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **e franqueza**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_50.png)

1. No menu no canto superior, clique em **utilizadores**.
    
    ![Atribuir utilizador][203]

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.
 
Quando clica em & franqueza dispor em mosaico no painel do Access, deve obter automaticamente assinado-on para sua & franqueza aplicação.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_205.png
