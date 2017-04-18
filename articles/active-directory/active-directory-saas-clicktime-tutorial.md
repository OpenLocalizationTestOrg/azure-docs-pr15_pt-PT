<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com ClickTime | Microsoft Azure" 
    description="Saiba como utilizar ClickTime com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Tutorial: Integração do Azure Active Directory com ClickTime

Neste tutorial, aprende integrar ClickTime com o Azure Active Directory (Azure AD).

Integrar o ClickTime com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao ClickTime
- Pode permitir que os utilizadores para automaticamente obter assinado-on para ClickTime (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ClickTime, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um ClickTime sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar ClickTime a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão

##<a name="adding-clicktime-from-the-gallery"></a>Adicionar ClickTime a partir da Galeria

Objectivo de nesta secção é como ativar a integração da aplicação para ClickTime de destaque.

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Para ativar a integração da aplicação para ClickTime, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-clicktime-tutorial/tic700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-clicktime-tutorial/tic700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-clicktime-tutorial/tic749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-clicktime-tutorial/tic749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **ClickTime**.

    ![Galeria de aplicação] (./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galeria de aplicação")

7.  No painel de resultados, selecione **ClickTime**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![ClickTime] (./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com ClickTime com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na ClickTime é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na ClickTime tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no ClickTime.

Para configurar e testar Azure AD serviço single sign-on com ClickTime, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um ClickTime testar utilizador](#creating-a-clicktime-test-user)** - ter homólogo para Britta Simon no ClickTime que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Objectivo de nesta secção é como permitir que os utilizadores autenticados para ClickTime com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  


>[AZURE.IMPORTANT] Para poder configurar o início de sessão único no seu inquilino ClickTime, tem primeiro contactar o suporte técnico ClickTime para aceder esta funcionalidade activada.

**Para configurar Azure AD serviço single sign-on com ClickTime, execute os seguintes passos:**

1.  No portal clássico Azure, na página de integração de aplicação **ClickTime** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Ativar o início de sessão único] (./media/active-directory-saas-clicktime-tutorial/tic777277.png "Ativar o início de sessão único")

2.  Na página **como pretende utilizadores a iniciar sessão ClickTime** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configurar serviço single sign-on")

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    um. Na caixa de texto **IdentifierL** , escreva o URL com o seguinte padrão: **https://app.clicktime.com/sp/**
    
    b. Na caixa de texto **Resposta URL** , escreva o URL com o seguinte padrão: **https://app.clicktime.com/Login/**

    c. Clique em **seguinte**

4.  Na página **Configurar serviço single sign-on na ClickTime** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configurar serviço single sign-on")

4.  Numa janela do browser web diferente, inicie sessão no site da sua empresa ClickTime como administrador.

5.  Na barra de ferramentas no canto superior, clique em **Preferências**e, em seguida, clique em **Definições de segurança**.

6.  Na secção de configuração de **Preferências de início de sessão único** , execute os seguintes passos:

    ![Definições de segurança] (./media/active-directory-saas-clicktime-tutorial/tic777280.png "Definições de segurança")

    um.  Selecione **Permitir** sessão utilizar único início de sessão (SSO) com o **Azure AD**.
    
    b.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na ClickTime** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto de **Ponto final do fornecedor de identidade** .

    c.  Abra o certificado codificado base-64 no **Bloco de notas**, copie o conteúdo e, em seguida, colá-la a caixa de texto do **Certificado x. 509** .
    
    d.  Clique em **Guardar**.

7.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no ClickTime, tem de ser aprovisionadas para ClickTime.  
No caso de ClickTime, aprovisionamento é uma tarefa manual.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aprovisionamento de contas de utilizador, execute os seguintes passos:

1.  Inicie sessão no seu inquilino **ClickTime** .

2.  Na barra de ferramentas no canto superior, clique em **empresa**e, em seguida, clique em **pessoas**.

    ![Pessoas] (./media/active-directory-saas-clicktime-tutorial/tic777282.png "Pessoas")

3.  Clique em **Adicionar pessoa**.

    ![Adicionar pessoa] (./media/active-directory-saas-clicktime-tutorial/tic777283.png "Adicionar pessoa")

4.  Na secção novo de uma pessoa, execute os seguintes passos:

    ![Pessoas] (./media/active-directory-saas-clicktime-tutorial/tic777284.png "Pessoas")

    um.  Na caixa de texto **endereço de correio eletrónico** , escreva o endereço de e-mail da sua conta do Azure AD.
    
    b.  Na caixa de texto **nome completo** , escreva o nome da sua conta do Azure AD.  

    >[AZURE.NOTE] Se pretender, pode definir propriedades adicionais do objeto pessoa nova.

    c.  Clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros ClickTime utilizador conta ferramentas para a criação ou APIs fornecida pela ClickTime aprovisionamento de contas de utilizador do Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a ClickTime.

![Atribuir utilizador][200]

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

**Para atribuir Britta Simon ClickTime, execute os seguintes passos**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **ClickTime**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]

## <a name="testing-single-sign-on"></a>Testar o início de sessão único
Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico ClickTime no painel do Access, pode deve obter automaticamente assinado-on à aplicação ClickTime.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png