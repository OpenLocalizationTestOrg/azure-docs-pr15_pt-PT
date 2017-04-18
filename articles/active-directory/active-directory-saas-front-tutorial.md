<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com frente | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e frente."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-front"></a>Tutorial: Integração do Azure Active Directory com frente

Objectivo deste tutorial é mostrar-lhe como integrar o frente com o Azure Active Directory (Azure AD).

Integrar o frente com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso para a frente
- Pode permitir que os utilizadores para automaticamente obter assinado-on para a frente (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com frente, tem os seguintes itens:

- Uma subscrição do Azure AD
- Uma parte da frente sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar frente a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-front-from-the-gallery"></a>Adicionar frente a partir da Galeria
Para configurar a integração da frente no Azure AD, é necessário adicionar frente a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar frente a partir da galeria, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **parte da frente**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)

7. No painel de resultados, selecione a **parte da frente**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com frente com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo à frente a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado à frente tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor de **nome de utilizador** à frente.

Para configurar e testar Azure AD serviço single sign-on com frente, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar uma parte da frente testar utilizador](#creating-a-front-test-user)** - ter homólogo para Britta Simon à frente que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação frente.

**Para configurar Azure AD serviço single sign-on com frente, execute os seguintes passos:**

1. No portal do clássico, na página de integração de **parte da frente** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende que como os utilizadores ao iniciar sessão para a frente** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)

3. Na página de diálogo **Configurar definições de aplicação** , se pretender configurar a aplicação no **modo de iniciados por IDP**, execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)

    um. Na caixa de texto **identificador** , escreva um URL utilizando o padrão seguinte:`https://<company name>.frontapp.com`

    b. Na caixa de texto **Resposta URL** , escreva um URL utilizando o padrão seguinte:`https://<company name>.frontapp.com/sso/saml/callback`

    c. Clique em **seguinte**

4. Se pretender configurar a aplicação no **modo de iniciados por SP** na página de diálogo **Configurar definições de aplicação** , em seguida, clique no **"Mostrar avançadas definições (opcional)"** e, em seguida, introduza o **URL no início de sessão** e clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)

    um. Na caixa de texto **URL no início de sessão** , escreva um URL utilizando o padrão seguinte:`https://<company name>.frontapp.com`

    b. Clique em **seguinte**

    > [AZURE.NOTE] Tenha em atenção que estes não são os valores reais. Tem de atualizar estes valores com o URL no início de sessão, identificador e URL de resposta real. Para obter estes valores, pode referir-se **passo 12** para obter mais detalhes ou contacte frente através do [support@frontapp.com](emailTo:support@frontapp.com).

5. Na página **Configurar serviço single sign-on à frente** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.

6. Início de sessão ao seu inquilino frente como administrador.

7. Aceda a **definições (ícone de roda dentada na parte inferior da barra lateral esquerda) > Preferências**.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

8. Clique em ligação de **Início de sessão** .

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

9. Selecione **SAML** a lista pendente de **Início de sessão**.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

10. No **Ponto de entrada** caixa de texto, coloque o valor de **Único início de sessão URL do serviço da** partir do Assistente de configuração do Azure AD aplicação.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

11. Copie o conteúdo do ficheiro transferido certificado e, em seguida, colá-la a caixa de texto do **certificado de assinatura** .

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

12. Confirme que estes URls correspondem a configuração no passo 3.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

13. Clique em botão **Guardar** .

14. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

15. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-front-test-user"></a>Criar um utilizador de teste frente

É o objectivo de nesta secção criar um utilizador designado Britta Simon no trabalho Front.Please com a sua equipa de suporte frente para adicionar os utilizadores na conta frente.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso para a frente.
    
![Atribuir utilizador][200]

**Para atribuir Britta Simon para a frente, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **frente**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)

1. No menu no canto superior, clique em **utilizadores**.
    
    ![Atribuir utilizador][203]

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.
 
Quando clica em mosaico à frente no painel do Access, pode deve obter automaticamente assinado-on à aplicação frente.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png
