<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com eTouches | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e eTouches."
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


# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutorial: Integração do Azure Active Directory com eTouches

Neste tutorial, aprende integrar eTouches com o Azure Active Directory (Azure AD).

Integrar o eTouches com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao eTouches
- Pode permitir que os utilizadores para automaticamente obter assinado-on para eTouches (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com eTouches, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um eTouches sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar eTouches a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-etouches-from-the-gallery"></a>Adicionar eTouches a partir da Galeria
Para configurar a integração da eTouches no Azure AD, é necessário adicionar eTouches a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar eTouches a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory][1]
2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **eTouches**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_01.png)

7. No painel de resultados, selecione **eTouches**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão

Nesta secção, configure e teste Azure AD serviço single sign-on com eTouches com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na eTouches é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na eTouches tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no eTouches.

Para configurar e testar Azure AD serviço single sign-on com eTouches, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um eTouches testar utilizador](#creating-a-predictix-price-reporting-test-user)** - ter uma contrapartida de Britta Simon no eTouches que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação eTouches.

aplicação eTouches espera as declarações SAML num formato específico. Configure as seguintes afirmações para esta aplicação. Pode gerir os valores das seguintes atributos a partir do separador **"Atrribute"** da aplicação. A captura de ecrã seguinte mostra um exemplo para esta situação. 

![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_07.png) 

**Para configurar Azure AD serviço single sign-on com eTocuhes, execute os seguintes passos:**


1. No portal clássico Azure, na página de integração de aplicação de **eTouches** , no menu na parte superior, clique em **atributos**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_general_80.png) 


2. Na caixa de diálogo **atributos tokens SAML** , para cada linha apresentada na tabela abaixo, execute os seguintes passos:

  	| Nome do atributo | Valor do atributo |
  	| --- | --- |    
  	| Mensagem de correio electrónico | User.Mail |

    um. Clique em **Adicionar utilizador atributo** para abrir a caixa de diálogo **Adicionar Attribure de utilizador** .

    ![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_general_81.png) 


    b. Na caixa de texto **Attrubute nome** , escreva o nome do atributo apresentado para nessa linha.

    c. A partir da lista de **Valor do atributo** , selsect o valor do atributo apresentado para nessa linha.

    d. Clique em **concluído**.  
    

3. No portal do clássico, na página de integração de aplicação **eTouches** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

4. Na página **como pretende utilizadores a iniciar sessão eTouches** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_03.png) 

5. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_04.png) 

    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de eTouches utilizando o seguinte padrão: **https://www.eiseverywhere.com/saml/accounts/?sso&accountid=\<accountid\>**.
    
    b. Clique em **seguinte**
 
6. Na página **Configurar serviço single sign-on na eTouches** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_05.png)

    um. Clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


7. Para obter SSO configurada para a sua aplicação, execute os seguintes passos na aplicação eTouches:

    um. Início de sessão a aplicação de **eTouches** utilizando os direitos de administrador.
    
    b. Ir para a configuração do **SAML**

    c. Na secção **Definições gerais** cole o conteúdo de metadados de Federação do Azure AD a caixa de texto.

    d. Clique no botão **Guardar e manter-se**

    "e". Clique no botão **Atualizar Metadados** na secção SAML metadados. 

    f. Isto irá abrir a página e fará SSO. Assim que o SSO está a funcionar, em seguida, pode configurar o nome de utilizador

    g. No campo **Username** , selecione o **emailaddress** conforme mostrado na imagem abaixo. 

    h. Copiar o **SSO URL / ACS** valor e colocá-la para a Azure AD aplicação configuração assistente URL início de sessão na caixa de texto.

    ![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png)

8. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

9. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
    
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.


![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , execute os seguintes passos:  ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-etouches-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-an-etouches-test-user"></a>Criação de um utilizador de teste eTouches

Nesta secção, crie um utilizador designado Britta Simon no eTouches. Consulte trabalhe com eTouches equipa de suporte para adicionar os utilizadores da plataforma eTouches.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a eTouches.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon eTouches, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **eTouches**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_50.png) 

3. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


### <a name="testing-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico eTouches no painel do Access, pode deve obter automaticamente assinado-on à aplicação eTouches.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_205.png
