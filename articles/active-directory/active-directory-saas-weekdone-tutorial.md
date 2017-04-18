<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Weekdone | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Weekdone."
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


# <a name="tutorial-azure-active-directory-integration-with-weekdone"></a>Tutorial: Integração do Azure Active Directory com Weekdone

Objectivo deste tutorial é mostrar-lhe como integrar o Weekdone com o Azure Active Directory (Azure AD).

Integrar o Weekdone com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Weekdone
- Pode permitir que os utilizadores para automaticamente obter assinado-on para Weekdone (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Weekdone, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Weekdone sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste. 

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Weekdone a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-weekdone-from-the-gallery"></a>Adicionar Weekdone a partir da Galeria
Para configurar a integração da Weekdone no Azure AD, é necessário adicionar Weekdone a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Weekdone a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Weekdone**.
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_01.png)

7. No painel de resultados, selecione **Weekdone**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com Weekdone com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no Weekdone a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Weekdone tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Weekdone.

Para configurar e testar Azure AD serviço single sign-on com Weekdone, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um Weekdone testar utilizador](#creating-a-weekdone-test-user)** - ter homólogo para Britta Simon no Weekdone que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação Weekdone.



**Para configurar Azure AD serviço single sign-on com Weekdone, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação **Weekdone** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão Weekdone** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
 
    ![Configurar o Single Sign-On](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , se pretender configurar a aplicação no **modo de iniciados por IDP**, execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_04.png) 


    um. Na caixa de texto **Resposta URL** , escreva o URL no seguinte padrão: **"https://weekdone.com/a/azure"**.

    b. Na caixa de texto **identificador** , escreva o URL no seguinte padrão: **"https://weekdone.com/a/azure/metadata"**.

    c. Clique em **seguinte**.

4. Se pretender configurar a aplicação no **modo de iniciados por SP**, na página de diálogo **Configurar definições de aplicação** , selecione **"Mostrar avançadas definições (opcional)"**e, em seguida, introduza o **URL no início de sessão** e o **identificador**, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_06.png) 

    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de Weekdone utilizando o seguinte padrão de: **"https://weekdone.com/a/azure"**.

    b. Na caixa de texto **identificador** , escreva o URL no seguinte padrão: **"https://weekdone.com/a/azure/metadata"**.

    c. Clique em **seguinte**.

5. Na página **Configurar serviço single sign-on na Weekdone** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_05.png) 

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    b. Clique em **seguinte**.


5. Para obter SSO configurado para a sua aplicação, contacte a equipa de suporte Weekdone através do hello@weekdone.com. Anexe o ficheiro de certificado transferido para o seu mail e partilhar os urls de metadados (emissor URL, SAML SSO URL e URL do serviço SIGN-OUT único) com a equipa de Weekdone para configurar o SSO no respetivo lado.

6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
  
    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , execute os seguintes passos:  ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:  ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-weekdone-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-weekdone-test-user"></a>Criar um utilizador de teste Weekdone

É o objectivo de nesta secção criar um utilizador designado Britta Simon no Weekdone. Weekdone suporta-in-time aprovisionamento, que está por predefinição ativada.

Não existe nenhum item de ação para si nesta secção. Durante uma tentativa de acesso Weekdone se não existir ainda será criado um novo utilizador. [Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte Weekdone através do hello@weekdone.com.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Weekdone.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Weekdone, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Weekdone**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-weekdone-tutorial/tutorial_weekdone_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico Weekdone no painel do Access, pode deve obter automaticamente assinado-on à aplicação Weekdone.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-weekdone-tutorial/tutorial_general_205.png
