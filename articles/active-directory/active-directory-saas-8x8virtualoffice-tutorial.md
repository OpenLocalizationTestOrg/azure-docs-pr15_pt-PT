<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com 8 x 8 Virtual Office | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active directório e 8 x 8 Virtual do Office."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Tutorial: Integração do Azure Active Directory com o Office Virtual de 8 x 8

Objectivo deste tutorial é mostrar-lhe como integrar o Office virtuais de 8 x 8 com o Azure Active Directory (Azure AD).

Integração de 8 x 8 Virtual Office com o Azure AD fornece-lhe com os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Office Virtual de 8 x 8
- Pode ativar os seus utilizadores para automaticamente obter tiver sessão iniciada no Office Virtual de 8 x 8 (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Office Virtual de 8 x 8, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um 8 x 8 Virtual Office sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Microsoft Azure AD Single Sign-On num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar 8 x 8 Virtual Office a partir da Galeria
2. Configurar e testar o Microsoft Azure AD Single Sign-On


## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Adicionar 8 x 8 virtuais Office a partir da Galeria
Para configurar a integração do Office Virtual de 8 x 8 no Azure AD, é necessário adicionar 8 x 8 Virtual Office a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar 8 x 8 Virtual Office a partir da galeria, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **8 x 8 virtuais Office**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. No painel de resultados, selecione **8 x 8 Virtual Office**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurar e testar o Microsoft Azure AD Single Sign-On
Objectivo de nesta secção é mostrar-lhe como configurar e testar o Microsoft Azure AD Single Sign-On com 8 x 8 que virtual Office com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber que o utilizador de homólogo para o Office virtuais a um utilizador no Azure AD é de 8 x 8. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na 8 x 8 Virtual Office tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Office Virtual de 8 x 8.

Para configurar e testar o Microsoft Azure AD Single Sign-On com o Office virtuais de 8 x 8, é necessário concluir os blocos modulares seguintes:

1. **[Configurar o Microsoft Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Microsoft Azure AD Single Sign-On com Britta Simon.
3. A **[criação de um utilizador de teste do Virtual Office de 8 x 8](#creating-a-8x8-virtual-office-test-user)** - ter homólogo para Britta Simon no 8 x 8 Virtual Office que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar o Microsoft Azure AD Single Sign-On.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configurar o Microsoft Azure AD Single Sign-On

Nesta secção, ativar Microsoft Azure AD Single Sign-On no portal do clássico e configurar o início de sessão único na aplicação do Virtual Office de 8 x 8.

**Para configurar o Microsoft Azure AD Single Sign-On com o Office Virtual de 8 x 8, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação de **8 x 8 Virtual Office** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende que como os utilizadores para iniciar sessão no Office Virtual de 8 x 8** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    um. Na caixa de texto **Resposta URL** , escreva:`https://sso.8x8.com/saml2`

    b. Clique em **seguinte**

4. Na página **configurar o serviço single sign-on no Office Virtual de 8 x 8** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.

5. Início de sessão ao seu inquilino do Virtual Office de 8 x 8 como administrador.
6. Selecione o **Gestor de conta do Virtual Office** no painel de aplicação.

    ![Configurar no lado da aplicação](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. Selecione a conta de **negócio** para gerir e clique em botão **Iniciar sessão** .

    ![Configurar no lado da aplicação](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. Clique em separador de **contas** na lista de menu.

    ![Configurar no lado da aplicação](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. Clique em **Início de sessão** na lista de contas.

    ![Configurar no lado da aplicação](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. Selecione **Signle início de sessão** em método de autenticação e clique em **SAML**.

    ![Configurar no lado da aplicação](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. Copie o **URL SAML SSO**, **início de sessão único saída URL do serviço** e o **URL de emissor** a partir do Azure AD para **Iniciar sessão no URL**, **termine URL** e **emissor URL** no Office Virtual de 8 x 8. 

    ![Configurar no lado da aplicação](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    ![Configurar no lado da aplicação](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. Clique em botão de **Browser** para carregar o certificado que transferiu a partir do Azure AD.

13. Clique no botão **Guardar** .

14. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

15. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico denominado Britta Simon.
    
![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. Na página de diálogo **Dê-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-8x8-virtual-office-test-user"></a>Criar um utilizador de teste do Virtual Office de 8 x 8

É o objectivo de nesta secção criar um utilizador designado Britta Simon no Office Virtual de 8 x 8. 8 x 8 Virtual Office suporta-in-time aprovisionamento, que está por predefinição ativada.

Não existe nenhum item de ação para si nesta secção. Durante uma tentativa de acesso de 8 x 8 Virtual Office se não existir ainda será criado um novo utilizador. 

> [AZURE.NOTE] Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte do Virtual Office de 8 x 8.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso ao Office Virtual de 8 x 8.
    
![Atribuir utilizador][200]

**Para atribuir Britta Simon 8 x 8 Virtual Office, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Office Virtual de 8 x 8**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. No menu no canto superior, clique em **utilizadores**.
    
    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Microsoft Azure AD Single Sign-On configuração, utilizando o painel de acesso.

Quando clica no mosaico do Virtual Office 8 x 8 no painel do Access, pode deve obter automaticamente assinado-on aplicação do Virtual Office de 8 x 8.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png
