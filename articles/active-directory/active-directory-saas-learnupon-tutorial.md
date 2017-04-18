<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Novatus | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e LearnUpon."
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Azure Active Directory com LearnUpon

Objectivo deste tutorial é mostrar-lhe como integrar o LearnUpon com o Azure Active Directory (Azure AD).  
Integrar o LearnUpon com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao LearnUpon
- Pode permitir que os utilizadores para automaticamente obter assinado-on para LearnUpon (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - o Azure Active Directory clássico 


Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LearnUpon, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um LearnUpon sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar LearnUpon a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-learnupon-from-the-gallery"></a>Adicionar LearnUpon a partir da Galeria
Para configurar a integração da LearnUpon no Azure AD, é necessário adicionar LearnUpon a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar LearnUpon a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **LearnUpon**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)

7. No painel de resultados, selecione **LearnUpon**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com LearnUpon com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no LearnUpon a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na LearnUpon tem de ser estabelecida.  
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no LearnUpon.

Para configurar e testar Azure AD serviço single sign-on com LearnUpon, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um LearnUpon testar utilizador](#creating-a-learnupon-test-user)** - ter homólogo para Britta Simon no LearnUpon que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação LearnUpon.



**Para configurar Azure AD serviço single sign-on com LearnUpon, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação **LearnUpon** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão LearnUpon** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:.

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png) 


    um. Na caixa de texto **Resposta URL** , escreva o URL do serviço afirmação consumidor utilizando o padrão seguinte:`https://\<companyname\>.learnupon.com/saml/consumer`

    b. Clique em **seguinte**. 


4. Na página **Configurar serviço single sign-on na LearnUpon** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png) 

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador. Irá precisamos este certificado e URLs de metadados (ID da entidade, SSO início de sessão no URL e o início de sessão saída URL) para configurar o SSO LearnUpon lado.

    b. Clique em **seguinte**.




1. Abra outra instância do browser e inicie sessão para LearnUpon com uma conta de administrador. 

1. Clique no separador **Definições** .

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png) 


1. Clique **Single Sign On - SAML**e, em seguida, clique em **Definições gerais** para configurar as definições de SAML.

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 


5. Na secção **Definições gerais** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png) 

    um. Selecione **ativado**.

    b. Como **versão**, selecione **2.0**.

    c. Como **condições de ignorar**, selecione **não**.

    d. Na caixa de texto **nome de param SAML Token publicar** , escreva o nome do parâmetro de mensagem de pedido para o URL do consumidor SAML indicado acima que contém a declaração de SAML para ser verificadas e autenticados - por exemplo **SAMLResponse**.

    "e". Na caixa de texto **Nome identificador formato** , escreva o valor que indica onde no seu afirmação SAML o identificador de utilizadores (endereço de E-Mail) reside - por exemplo **urn: organização de normalização: nomes: tc: SAML:1.1:nameid-formato: emailAddress**.

    f. Na caixa de texto **Identificar o fornecedor de localização** , escreva o valor que indica onde os utilizadores são enviados se clicarem no seu ícone carregado a partir do seu ecrã Azure clássica portal início de sessão.

    g.in portal clássico do Azure, copie o **URL do serviço Sign-Out único**e, em seguida, colá-la a textbos **termine URL** .

    h. Clique em **Gerir dedo impressões**e, em seguida, carregue a impressão de dedo do seu certificado transferido. 


1. Clique em **Definições de utilizador**e, em seguida, execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png) 

    um. Na caixa de texto **Nome próprio identificador formato** , escreva o valor que lhe pede-nos onde no seu afirmação SAML NomePróprio a utilizadores reside - por exemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ givenname**.

    b. Na caixa de texto **Último formato identificador de nome** , escreva o valor que lhe pede-nos onde no seu afirmação SAML o apelido de utilizadores reside - por exemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ apelido**.


6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-learnupon-test-user"></a>Criar um utilizador de teste LearnUpon

É o objectivo de nesta secção criar um utilizador designado Britta Simon no LearnUpon. LearnUpon suporta-in-time aprovisionamento, que está por predefinição ativada.

Não existe nenhum item de ação para si nesta secção. Durante uma tentativa de acesso LearnUpon se não existir ainda será criado um novo utilizador. [Configurar o Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte LearnUpon.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a LearnUpon.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon LearnUpon, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **LearnUpon**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico LearnUpon no painel do Access, pode deve obter automaticamente assinado-on à aplicação LearnUpon.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png
