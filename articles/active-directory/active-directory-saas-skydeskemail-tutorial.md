<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Skydesk E-Mail | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Skydesk E-Mail."
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


# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Integração do Azure Active Directory com Skydesk E-Mail

Objectivo deste tutorial é mostrar-lhe como integrar o Skydesk E-Mail com o Azure Active Directory (Azure AD).

Integrar o Skydesk E-Mail com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao E-Mail Skydesk
- Pode ativar os seus utilizadores para automaticamente obter assinado-on ao E-Mail Skydesk (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure Active Directory

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o E-Mail Skydesk, tem os seguintes itens:

- Uma subscrição do Azure AD
- Uma mensagem de correio electrónico Skydesk sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste. 

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Skydesk E-Mail a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-skydesk-email-from-the-gallery"></a>Adicionar Skydesk E-Mail a partir da Galeria
Para configurar a integração de E-Mail Skydesk no Azure AD, é necessário adicionar Skydesk E-Mail a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Skydesk E-Mail a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Skydesk E-Mail**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_01.png)

7. No painel de resultados, selecione **E-Mail de Skydesk**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
O objectivo desta secção é para mostrar-lhe como configurar e testar o Azure AD serviço single sign-on com Skydesk E-Mail com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo para na mensagem de correio electrónico Skydesk a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no E-Mail Skydesk tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no E-Mail Skydesk.

Para configurar e testar Azure AD serviço single sign-on com o E-Mail Skydesk, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar uma mensagem de E-Mail Skydesk testar utilizador](#creating-a-Skydesk-Email-test-user)** - ter homólogo para Britta Simon Skydesk por correio eletrónico que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação de correio eletrónico de Skydesk.



**Para configurar Azure AD serviço single sign-on com Skydesk E-Mail, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação de **Correio eletrónico de Skydesk** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão E-Mail Skydesk** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_03.png) 


3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:
 
    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_04.png) 


    um. Na caixa de texto URL no início de sessão, escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de correio eletrónico de Skydesk utilizando o seguinte padrão: **"https://mail.skydesk.jp/portal/\<nome da empresa\>"**.

    b. Clique em **seguinte**.


4. Na página **Configurar serviço single sign-on na mensagem de correio electrónico Skydesk** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_05.png) 

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Para ativar SSO no **E-Mail Skydesk**, execute os seguintes passos:
 
    um. Início de sessão na sua conta de E-Mail Skydesk como administrador.

    b. No menu no canto superior, clique em configuração e selecione org. 

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)

    c. Clique em domínios a partir do painel da esquerda.

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Clique em Adicionar domínio.

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    "e". Introduza o seu nome de domínio e, em seguida, verifique o domínio.

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Clique no **SAML autenticação** a partir do painel da esquerda

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

6. Na página de diálogo **SAML autenticação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [AZURE.NOTE] Para utilizar SAML com base autenticação, deverá significa que tem **verificado o domínio** ou **portal URL** configuração. Pode definir o portal de URL com o nome exclusivo.

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)


    um. No portal clássico do Azure AD, copie o valor de **SAML SSO URL** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .

    b. No portal clássico do Azure AD, copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto do URL de **Terminar sessão** .

    c. **Alterar palavra-passe URL** é opcional por isso, deixe em branco.

    d. Clique em **Obter chave do ficheiro** para selecionar o seu certificado de E-Mail Skydesk transferido e, em seguida, clique em **Abrir** para carregar o certificado.

    "e". Como **algoritmo de**, selecione **RSA**.

    f. Clique em **Ok** para guardar as alterações.


7. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

8. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
  
    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-skydesk-email-test-user"></a>Criar um utilizador de teste Skydesk E-Mail

Nesta secção, crie um utilizador designado Britta Simon no E-Mail Skydesk.

um. Clique em **Acesso do utilizador** a partir do painel esquerdo por correio eletrónico de Skydesk e, em seguida, introduza o seu nome de utilizador. 

![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)


[AZURE.NOTE] Se precisar de criar utilizadores em volume, terá de contactar a equipa de suporte Skydesk E-Mail.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso ao E-Mail Skydesk.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Skydesk E-Mail, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
 
    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Skydesk E-Mail**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico de correio eletrónico de Skydesk no painel do Access, pode deve obter automaticamente assinado-on para a sua aplicação de correio eletrónico de Skydesk.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_205.png
