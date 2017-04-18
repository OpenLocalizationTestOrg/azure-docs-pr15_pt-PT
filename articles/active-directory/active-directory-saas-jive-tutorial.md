<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Jive | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Jive."
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


# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Tutorial: Integração do Azure Active Directory com Jive

Neste tutorial, aprende integrar Jive com o Azure Active Directory (Azure AD).

Integrar o Jive com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Jive
- Pode permitir que os utilizadores para automaticamente obter assinado-on para Jive (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Jive, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Jive sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Jive a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-jive-from-the-gallery"></a>Adicionar Jive a partir da Galeria
Para configurar a integração do Jive para Azure AD, é necessário adicionar Jive a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Jive a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory][1]
2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Jive**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. No painel de resultados, selecione **Jive**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com Jive com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na Jive é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Jive tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Jive.

Para configurar e testar Azure AD serviço single sign-on com Jive, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um Jive testar utilizador](#creating-a-jive-test-user)** - ter homólogo para Britta Simon no Jive que está ligado à representação do Azure AD da lhe.
4. **[Configurar o aprovisionamento de utilizador](#configuring-user-provisioning)** - destacar como ativar o aprovisionamento de utilizador do utilizador do Active Directory contas para Jive.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
6. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação Jive.

**Para configurar Azure AD serviço single sign-on com Jive, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação **Jive** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende que como os utilizadores para iniciar sessão Jive** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 

    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de Jive utilizando o seguinte padrão: **https://\<nome do cliente\>. jivecustom.com**.
    
    b. Clique em **seguinte**
 
4. Na página **Configurar serviço single sign-on na Jive** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Início de sessão ao seu inquilino Jive como administrador.

6. No menu no canto superior, clique em "**Saml**".

    ![Configurar o início de sessão único no lado da aplicação](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

    um. Selecione **ativado** no separador **Rápido** .

    b. Clique no botão "**Guardar todas as definições de saml**".

7. Navegue até ao separador "**Idp metadados**".

    ![Configurar o início de sessão único no lado da aplicação](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)

    um. Copie o conteúdo do ficheiro transferido metadados XML e, em seguida, colá-la a caixa de texto de **metadados do fornecedor de identidade (IDP)** .

    b. Clique no botão "**Guardar todas as definições de saml**". 

8. Aceda ao separador "**Mapeamento de atributo de utilizador**".

    ![Configurar o início de sessão único no lado da aplicação](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)

    um. Na caixa de **correio eletrónico** do texto, copie e cole o nome do atributo de valor de **correio** .

    b. Na caixa de texto **nome próprio** , copie e cole o nome do atributo de **givenname** valor.

    c. Na caixa de texto **Apelido** , copie e cole o nome do atributo de valor **Apelido** .
    
9. No portal do Azure AD, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
![Azure AD Single Sign-On][10]

10. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
  ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.


![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , execute os seguintes passos:  ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



###<a name="creating-a-jive-test-user"></a>Criar um utilizador de teste Jive

Nesta secção, crie um utilizador designado Britta Simon no Jive. Consulte trabalhe com a equipa de suporte Jive para adicionar os utilizadores da plataforma Jive.


###<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Objectivo de nesta secção é como ativar o utilizador aprovisionamento de contas de utilizador do Active Directory para Jive de destaque.  
Como parte deste procedimento, são necessários para fornecer um token de segurança de utilizador que precisa pedir a partir de Jive.com.
  
A captura de ecrã seguinte apresenta um exemplo da caixa de diálogo relacionado Azure AD:

![Configurar o aprovisionamento de utilizador] (./media/active-directory-saas-jive-tutorial/IC698794.png "Configurar o aprovisionamento de utilizador")

####<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  No Portal de gestão do Azure, na página de integração de aplicação **Jive** , clique em **Configurar aprovisionamento de utilizador** para abrir a caixa de diálogo **Configurar aprovisionamento de utilizador** .

2.  Na página **introduzir as suas credenciais Jive para ativar o aprovisionamento de utilizador automática** , forneça as seguintes definições de configuração:

    1.  Na caixa de texto **Nome de utilizador de administração Jive** , escreva um nome de conta Jive que tenham o perfil de **Administrador do sistema** no Jive.com atribuídas.

    2.  Na caixa de texto **Palavra-passe de administrador de Jive** , escreva a palavra-passe para esta conta.

    3.  Na caixa de texto **Jive inquilino URL** , escreva o URL de inquilino Jive.

        >[AZURE.NOTE]O URL de inquilino Jive é URL que é utilizado pela sua organização que inicie sessão no Jive.  
        Normalmente, o URL tem o seguinte formato: **www.\< organização\>. jive.com**.

    4.  Clique em **Validar** para confirmar a sua configuração.

    5.  Clique no botão **seguinte** para abrir a página de **confirmação** .

3.  Na página de **confirmação** , clique em marca de verificação para guardar a configuração.
  
Agora pode criar uma conta de teste, espere 10 minutos e certifique-se de que a conta foi sincronizada para Jive.com.




### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Jive.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Jive, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Jive**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 

3. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


### <a name="testing-single-sign-on"></a>Testes Single Sign-On

Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico Jive no painel do Access, pode deve obter automaticamente assinado-on à aplicação Jive.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png
