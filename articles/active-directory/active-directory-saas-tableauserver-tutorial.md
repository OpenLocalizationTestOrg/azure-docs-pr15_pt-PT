<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com quadro servidor | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e servidor do quadro."
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


# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Azure Active Directory com o servidor de quadro

Objectivo deste tutorial é mostrar-lhe como integrar o quadro Server com o Azure Active Directory (Azure AD).

Integrar o quadro Server com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao servidor do quadro
- Pode ativar os seus utilizadores para automaticamente obter tiver sessão iniciada no quadro servidor (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o servidor do quadro, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um servidor de quadro sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste. 

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar o servidor de quadro a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-tableau-server-from-the-gallery"></a>Adicionar o servidor de quadro a partir da Galeria
Para configurar a integração do servidor de quadro no Azure AD, é necessário adicionar o servidor de quadro a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o servidor de quadro a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 
 
    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva o **Servidor de quadro**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)

7. No painel de resultados, selecione **Servidor do quadro**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com quadro Server com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no quadro Server a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no quadro Server tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no servidor do quadro.

Para configurar e testar Azure AD serviço single sign-on com o servidor do quadro, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um servidor de quadro testar utilizador](#creating-a-tableauserver-test-user)** - ter homólogo para Britta Simon no servidor de quadro que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação de servidor do quadro.

Aplicação de servidor do quadro espera as declarações SAML num formato específico. A captura de ecrã seguinte mostra um exemplo para esta situação. 

![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png) 

**Para configurar Azure AD serviço single sign-on com o servidor do quadro, execute os seguintes passos:**


1. No portal clássico Azure, na página de integração de aplicação **Servidor de quadro** , no menu na parte superior, clique em **atributos**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png) 


1. Na caixa de diálogo **atributos tokens SAML** , execute os seguintes passos:

    

    um. Clique em **Adicionar utilizador atributo** para abrir a caixa de diálogo **Adicionar Attribure de utilizador** .

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png) 


    b. Na caixa de texto **Attrubute nome** , escreva o **nome de utilizador**.

    c. A partir da lista de **Valor do atributo** , selsect **user.displayname**.

    d. Clique em **concluído**.  
    



1. No menu no canto superior, clique em **Guia de introdução**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)  








1. Clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 



2. Na página **como pretende utilizadores para iniciar sessão servidor do quadro** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png) 


3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png) 



    um. Na caixa de texto **URL início de sessão** , escreva o URL do seu servidor de quadro. 

    b. No identificador caixa copiar o 

    c. Clique em **seguinte**


4. Na página **Configurar serviço single sign-on no servidor do quadro** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png) 


    um. Clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


6. Para obter SSO configurado para a sua aplicação, tem de início de sessão no seu inquilino do servidor de quadro como administrador.

    um. Na configuração do servidor do quadro, clique no separador **SAML** .

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 


    b. Selecione a caixa de verificação **Utilizar SAML do single sign-on**.

    c. Localizar o ficheiro de metadados de Federação transferido a partir do Azure portal clássico e, em seguida, carregue-o no **ficheiro de metadados SAML Idp**.

    d. Servidor de quadro devolver URL — o URL que os utilizadores do servidor de quadro terão acesso, tal como http://tableau_server. Não é recomendado utilizar http://localhost. Não é suportada utilizando um URL com uma barra final (por exemplo, http://tableau_server/). Copiar o **servidor de quadro devolver URL** e colá-lo para a caixa de texto do Azure AD **URL no início de sessão** , conforme mostrado no passo 3

    "e". ID da entidade SAML — o ID de entidade identifica exclusivamente a instalação do servidor de quadro para o IdP. Aqui pode introduzir o URL do servidor quadro novamente, se preferir, mas não tem de ser o seu URL do servidor de quadro. Copie **o ID da entidade SAML** e colá-lo para a caixa de texto do Azure AD **identificador** , conforme mostrado no passo 3.

    f. Clique no **Ficheiro de exportação de metadados** e abra-o na aplicação de editor de texto. Localize o URL do serviço de declaração consumidor com Http Post e indexar 0 e copie o URL. Agora colá-lo para a caixa de texto do Azure AD **URL de resposta** conforme mostrado no passo 3. 

    g. Clique em botão **OK** na página Configiuration de servidor do quadro.

    > [AZURE.NOTE] Se precisar de ajuda a configurar SAML no servidor do quadro, em seguida, consulte este artigo [Configurar SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm) 

6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**. 
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

Na lista de utilizadores, selecione **Britta Simon**.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 


4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png) 

    um. Como o **Tipo de utilizador**, selecione **novo utilizador na sua organização**.

    b. Na caixa de texto **Nome de utilizador** , escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png) 


8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-tableau-server-test-user"></a>Criar um utilizador de teste do quadro servidor

É o objectivo de nesta secção criar um utilizador designado Britta Simon no servidor do quadro. Tem de aprovisionamento de todos os utilizadores no servidor do quadro. Tenha também em atenção que nome de utilizador do utilizador deve corresponder o valor que tenha configurado no atributo Azure AD personalizado do **nome de utilizador**. Com o mapeamento correcto a integração deve trabalhar [Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se precisar de criar manualmente um utilizador, terá de contactar o administrador do servidor do quadro na sua organização.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso ao servidor do quadro.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon ao servidor do quadro, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
 
    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Servidor do quadro**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png) 


1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203]

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico do servidor de quadro no painel do Access, pode deve obter automaticamente assinado-on a sua aplicação de servidor do quadro.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png
