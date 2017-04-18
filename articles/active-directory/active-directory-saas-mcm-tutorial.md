<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com eventos MCM | Microsoft Azure" 
    description="Saiba como utilizar eventos MCM com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>Tutorial: Integração do Azure Active Directory com eventos MCM
  
Objectivo deste tutorial é mostrar-lhe como integrar o eventos MCM com o Azure Active Directory (Azure AD).

Integrar o eventos MCM com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao eventos MCM
- Pode permitir que os utilizadores para automaticamente obter assinado-on para eventos MCM (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com eventos MCM, tem os seguintes itens:

- Uma subscrição do Azure válida
- Um eventos MCM sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar eventos MCM a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão

## <a name="adding-mcm-from-the-gallery"></a>Adicionar eventos MCM a partir da Galeria
Para configurar a integração dos eventos MCM na Azure AD, é necessário adicionar eventos MCM a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar eventos MCM a partir da galeria, execute os seguintes passos:**

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **eventos MCM**.

    ![Galeria de aplicação] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Galeria de aplicação")

7.  No painel de resultados, selecione **eventos MCM**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Eventos MCM] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "Eventos MCM")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on, com eventos MCM com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no eventos MCM a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na eventos MCM tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no eventos MCM.

Para configurar e testar Azure AD serviço single sign-on com eventos MCM, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um eventos MCM testar utilizador](#creating-a-mcm-test-user)** - ter homólogo para Britta Simon no eventos MCM que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on
  
Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação de eventos MCM.

**Para configurar Azure AD serviço single sign-on com eventos MCM, execute os seguintes passos:**

1.  No portal clássico Azure, na página de integração de **eventos MCM** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão eventos MCM** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3.  Na página de diálogo Configurar definições de aplicação, execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configurar o URL da aplicação")

    um. Na caixa de texto **URL no início de sessão** , escreva: `https://myaba.co.uk/client-access/<company name>/saml.php`.
    
    b. Clique em **seguinte**

4.  Na página **Configurar serviço single sign-on na eventos MCM** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configurar o Single Sign-On")

5. Para obter SSO configurado para a sua aplicação, contacte a equipa de suporte de eventos MCM. Anexe o ficheiro transferido metadados e partilhá-lo com a equipa de eventos MCM para configurar o SSO no respetivo lado.

6.  No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configurar o Single Sign-On")

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configurar o Single Sign-On")


### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD

É o objectivo de nesta secção criar um utilizador de teste no portal do clássico denominado Britta Simon.

![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   

###<a name="creating-a-mcm-test-user"></a>Criar um utilizador de teste de eventos MCM
  
Nesta secção, crie um utilizador designado Britta Simon no eventos MCM. Consulte trabalhe com a equipa de suporte de eventos MCM para adicionar os utilizadores da plataforma de eventos MCM.

>[AZURE.NOTE]Pode utilizar quaisquer outros eventos MCM utilizador conta ferramentas para a criação ou APIs fornecida pela eventos MCM às contas de utilizador do aprovisionar AAD.


###<a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD
  
Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso aos eventos MCM.
    
![Atribuir aos utilizadores] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Atribuir aos utilizadores")

**Para atribuir Britta Simon eventos MCM, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Atribuir aos utilizadores] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Atribuir aos utilizadores")

2. Na lista de aplicações, selecione **eventos MCM**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. No menu no canto superior, clique em **utilizadores**.
    
    ![Atribuir aos utilizadores] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Atribuir aos utilizadores")

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir aos utilizadores] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Atribuir aos utilizadores")


### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.
 
Quando clica no mosaico de eventos MCM no painel do Access, pode deve obter automaticamente assinado-on à aplicação eventos MCM.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)