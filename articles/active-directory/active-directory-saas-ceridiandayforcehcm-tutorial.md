<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Ceridian Dayforce HCM | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Ceridian Dayforce HCM."
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


# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integração do Azure Active Directory com Ceridian Dayforce HCM

Objectivo deste tutorial é mostrar-lhe como integrar o Ceridian Dayforce HCM com o Azure Active Directory (Azure AD).  
Integrar o Ceridian Dayforce HCM com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Ceridian Dayforce HCM
- Pode ativar os seus utilizadores para automaticamente obter assinado-on para Ceridian Dayforce HCM (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure


Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Ceridian Dayforce HCM, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Ceridian Dayforce HCM sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Ceridian Dayforce HCM a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Adicionar Ceridian Dayforce HCM a partir da Galeria
Para configurar a integração da Ceridian Dayforce HCM no Azure AD, é necessário adicionar Ceridian Dayforce HCM a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Ceridian Dayforce HCM a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Ceridian Dayforce HCM**.

    ![Aplicações](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_01.png)

7. No painel de resultados, selecione **Ceridian Dayforce HCM**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Aplicações](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com Ceridian Dayforce HCM com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no Ceridian Dayforce HCM a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Ceridian Dayforce HCM tem de ser estabelecida.

Para configurar e testar Azure AD serviço single sign-on com Ceridian Dayforce HCM, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um HCM de Dayforce Ceridian testar utilizador](#creating-a-ceridian-dayforce-hcm-test-user)** - ter homólogo para Britta Simon no Ceridian Dayforce HCM que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação Ceridian Dayforce HCM.

A aplicação Ceridian Dayforce HCM espera as declarações SAML num formato específico. Consulte trabalhe com a equipa de Dayforce HCM pela primeira vez para identificar o identificador de utilizador corretas. A Microsoft recomenda utilizando o atributo **"nome"** como o identificador de utilizador. Pode gerir o valor deste atributo na caixa de diálogo **"Atrribute"** . A captura de ecrã seguinte mostra um exemplo para esta situação. 

![Configurar o Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_02.png) 

**Para configurar Azure AD serviço single sign-on com Ceridian Dayforce HCM, execute os seguintes passos:**




1. No portal clássico Azure, na página de integração de aplicação de **Ceridian Dayforce HCM** , no menu na parte superior, clique em **atributos**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_81.png) 


1. Na lista de **atributos token saml** atributos, selecione o atributo de nome e, em seguida, clique em **Editar**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_06.png) 


1. Na caixa de diálogo **Editar atributo do utilizador** , efetue os seguintes passos:
 
    um. A partir da lista de **Valor do atributo** , selecione o atributo de utilizador que pretende utilizar para a sua implementação.  
    Por exemplo, se pretende utilizar o campo IDdeEmpregado como identificador de utilizador exclusivo e armazenados o valor do atributo na ExtensionAttribute2, em seguida, selecione **user.extensionattribute2**. 

    b. Clique em **concluído**.  
    



1. No menu no canto superior, clique em **Guia de introdução**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  





1. No portal clássico Azure, na página de integração de aplicação **Ceridian Dayforce HCM** , clique em **Configurar serviço single sign-on**.

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão Ceridian Dayforce HCM** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:.

    ![Configurar o Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_04.png) 


    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão à aplicação Ceridian Dayforce HCM. Para ambientes de produção, utilize o seguinte formato de URL:`https://sso.dayforcehcm.com/DayforcehcmNamespace`

    Para maior clareza, substitua DayforcehcmNamespace espaço de nomes do seu ambiente ou o seu ID da empresa; Por exemplo:`https://sso.dayforcehcm.com/contoso`
    
    Ambientes de teste, utilize o seguinte formato de URL:`https://ssotest.dayforcehcm.com/DayforcehcmNamespace` 

    b. Na caixa de texto **Resposta URL** , escreva o URL utilizado pelo Azure AD para publicar a resposta.  
    Para ambientes de produção, utilize:`https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2`  
    Ambientes de teste, utilize:`https://fs-test.dayforcehcm.com/sp/ACS.saml2`  
   

4. Na página **Configurar serviço single sign-on na Ceridian Dayforce HCM** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_05.png) 

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Para obter SSO configurado para a sua aplicação, contacte a equipa de suporte Ceridian Dayforce HCM por correio eletrónico e forneça-o com o seguinte:

    - O ficheiro de certificado transferido
    - O **URL de emissor**
    - O **URL do SAML SSO** 
    - O **início de sessão único saída URL do serviço** 


6. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-ceridiandayforcehcm-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-ceridian-dayforce-hcm-test-user"></a>Criar um utilizador de teste Ceridian Dayforce HCM

É o objectivo de nesta secção criar um utilizador designado Britta Simon no Ceridian Dayforce HCM. 

Consulte Trabalhar com a equipa de suporte Ceridian Dayforce HCM de colocar os utilizadores adicionados à sua na aplicação Ceridian Dayforce HCM. 





### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Ceridian Dayforce HCM.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Ceridian Dayforce HCM, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Ceridian Dayforce HCM**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico Ceridian Dayforce HCM no painel do Access, pode deve obter automaticamente assinado-on à aplicação Ceridian Dayforce HCM.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ceridiandayforcehcm-tutorial/tutorial_general_205.png
