<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com um Software de HR Cezanne | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Cezanne HR Software."
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
    ms.date="10/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integração do Azure Active Directory com um Software de HR Cezanne

Objectivo deste tutorial é mostrar-lhe como integrar o Software de HR Cezanne com o Azure Active Directory (Azure AD).

Integrar o Software de HR Cezanne com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Cezanne HR Software
- Pode ativar os seus utilizadores para automaticamente obter assinado-on ao Software de HR Cezanne (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com um Software de HR Cezanne, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Software de HR Cezanne sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Cezanne HR Software a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adicionar Cezanne HR Software a partir da Galeria
Para configurar a integração do Software de HR Cezanne no Azure AD, é necessário adicionar Cezanne HR Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Cezanne HR Software a partir da galeria, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Cezanne HR Software**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. No painel de resultados, selecione **Cezanne HR Software**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com um Software de HR Cezanne com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no Cezanne HR Software a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cezanne HR Software tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Cezanne HR Software.

Para configurar e testar Azure AD serviço single sign-on com um Software de HR Cezanne, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um Software de HR Cezanne testar utilizador](#creating-a-cezanne-hr-software-test-user)** - ter homólogo para Britta Simon no Cezanne HR Software que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na aplicação de Software de HR Cezanne.

**Para configurar o Azure AD serviço single sign-on com um Software de HR Cezanne, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação de **Software de HR Cezanne** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão Cezanne HR Software** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

    um. Na caixa de texto **URL no início de sessão** , escreva um URL que utiliza o seguinte padrão: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. Na caixa de texto **Resposta URL** , escreva um URL que utiliza o seguinte padrão: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

    c. Clique em **seguinte**

    > [AZURE.NOTE] Tenha em atenção que tem de atualizar estes valores com o início de sessão no URL real e o URL de resposta. Para obter estes valores, contacte a equipa de suporte do Software de HR Cezanne através do <mailto:info@cezannehr.com>.

4. Na página **Configurar serviço single sign-on em Cezanne HR Software** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.

5. Numa janela do browser web diferente, sign-on para o inquilino do Software de HR Cezanne como administrador.

6. No painel de navegação esquerdo, clique em **Configuração do sistema**. Aceda a **definições de segurança**. Em seguida, navegue para a **Configuração de início de sessão único**.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. Em **Permitir que os utilizadores inicie sessão com o serviço de único início de sessão (SSO) seguintes** painel selecione a caixa de **SAML 2.0** e selecione a opção **Configuração avançada** junto à mesma.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Clique em **Adicionar novo** botão.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Execute os seguintes passos na secção **Fornecedores de identidade do SAML 2.0** .

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    um. Introduza o nome do seu fornecedor de identidade como o **Nome a apresentar**.

    b. No **Identificador de entidade** caixa de texto, coloque o valor de **ID da entidade** a partir do Assistente de configuração do Azure AD aplicação.

    c. Altere o **SAML encadernação** 'LANÇAR'.

    d. No **Ponto final de serviço Token segurança** caixa de texto, coloque o valor de **Único início de sessão URL do serviço da** partir do Assistente de configuração de aplicação do Azure AD.

    "e". Introduza 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' no **nome do atributo ID de utilizador**.

    f. Clique em **carregar** o ícone para carregar o certificado transferido a partir do Azure AD.

    g. Clique em **Ok** . 

10. Clique em botão **Guardar** .

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

12. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na caixa de texto **Apelido** , escreva **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-cezanne-hr-software-test-user"></a>Criar um utilizador de teste Cezanne HR Software

Para permitir que utilizadores do Azure AD que inicie sessão no Cezanne HR Software, tem de ser aprovisionadas para Cezanne HR Software. No caso de Cezanne HR Software, aprovisionamento é uma tarefa manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Aprovisionamento de uma conta de utilizador, execute os seguintes passos:

1.  Inicie a sessão no site da sua empresa Cezanne HR Software como administrador.

2.  No painel de navegação esquerdo, clique em **Configuração do sistema**. Aceda a **Gerir utilizadores**. Em seguida, navegue até ao **Adicionar novo utilizador**.

    ![Novo utilizador] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Novo utilizador")

3.  Na secção de **Detalhes sobre a pessoa** , efetue abaixo passos:

    ![Novo utilizador] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Novo utilizador")

    um. Definir **interno utilizador** como desligado.

    b. Na caixa de texto **nome próprio** , escreva **Britta**.  

    c. Na caixa de texto **Apelido** , escreva **Simon**.

    d. Na caixa de **correio electrónico** do texto, escreva o endereço de e-mail da conta Britta Simon.

4.  Na secção de **Informações da conta** , efetue abaixo passos:

    ![Novo utilizador] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Novo utilizador")

    um. Na caixa de texto **nome de utilizador** , escreva o endereço de e-mail da Britta Simon.

    b. Na caixa de texto **palavra-passe** , escreva a palavra-passe da conta Britta Simon.

    c. Selecione **HR Professional** como **função de segurança**.

    d. Clique em **OK**.

5. Navegue até ao separador **Single Sign-On** e selecione **Adicionar novo** na área do **SAML 2.0 identificadores** .

    ![Utilizador] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilizador")

6. Selecione o seu fornecedor de identidade para o **Fornecedor de identidade** e na caixa de texto do **Identificador de utilizador**, introduza o endereço de e-mail da conta Britta Simon.

    ![Utilizador] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilizador")
    
7. Clique em botão **Guardar** .

    ![Utilizador] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilizador")


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso ao Cezanne HR Software.
    
![Atribuir utilizador][200]

**Para atribuir Britta Simon Cezanne HR Software, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Cezanne HR Software**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. No menu no canto superior, clique em **utilizadores**.
    
    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir utilizador][205]

### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.
 
Quando clica no mosaico Cezanne HR Software no painel do Access, pode deve obter automaticamente assinado-on para a sua aplicação de Software de HR Cezanne.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png
