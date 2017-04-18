<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com reconhecer | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e reconhecer."
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
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integração do Azure Active Directory com reconhecer

Objectivo deste tutorial é mostrar-lhe como integrar o reconhecer com o Azure Active Directory (Azure AD).

Integrar o reconhecer com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao reconhecer
- Pode permitir que os utilizadores para automaticamente obter assinado-on para reconhecer (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com reconhecer, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um reconhecer sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar reconhecer a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-recognize-from-the-gallery"></a>Adicionar reconhecer a partir da Galeria
Para configurar a integração de reconhecer no Azure AD, é necessário adicionar reconhecer a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar reconhecer a partir da galeria, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.
    
    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **reconhecer**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)

7. No painel de resultados, selecione **reconhecer**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Selecionar a aplicação na Galeria](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com reconhecer com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no reconhecer a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na reconhecer tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no reconhecer.

Para configurar e testar Azure AD serviço single sign-on com reconhecer, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar um reconhecer testar utilizador](#creating-a-recognize-test-user)** - ter homólogo para Britta Simon no reconhecer que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação de reconhecer.

**Para configurar Azure AD serviço single sign-on com reconhecer, execute os seguintes passos:**

1. No portal do clássico, na página de integração de **reconhecer** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão reconhecer** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos e clique em **seguinte**:

    ![Configurar o Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)

    um. Na caixa de texto **URL no início de sessão** , escreva um URL que utiliza o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/sso`.

    b. Na caixa de texto **identificador** , escreva um URL que utiliza o seguinte padrão: `https://recognizeapp.com/<your-domain>/saml/metadata`.

    c. Clique em **seguinte**

    > [AZURE.NOTE] Se não sabe sobre estes URLs, escreva os URLs de exemplo com padrão do exemplo. Para obter estes valores, pode referir-se passo 9 para obter mais detalhes ou contacte a equipa de suporte de reconhecer através do <mailto:support@recognizeapp.com>.

4. Na página **Configurar serviço single sign-on na reconhecer** , clique em **Transferir o certificado** e, em seguida, guarde o ficheiro no seu computador:

    ![Configurar o Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)

5. Numa janela do browser web diferente, início de sessão ao seu inquilino reconhecer como administrador.

6. No canto superior direito, clique em **Menu**. Vá para **administrador de empresa**.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

7. No painel de navegação esquerdo, clique em **Definições**.

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

8. Execute os seguintes passos na secção **Definições de SSO** .

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)

    um. Como **Ativar SSO**, selecione **Sucessivamente**.

    b. O **ID da entidade IDP** caixa de texto, coloque o valor de **URL emissor** a partir do Assistente de configuração de aplicação do Azure AD.

    c. No **url de destino Sso** caixa de texto, coloque o valor de **Único início de sessão URL do serviço da** partir do Assistente de configuração do Azure AD aplicação.

    d. No **url de destino Slo** caixa de texto, coloque o valor de **URL do serviço Sign-Out única** a partir do Assistente de configuração do Azure AD aplicação.

    "e". Abrir o ficheiro de certificado transferido no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **certificado** . 

    f. Clique no botão **Guardar as definições** . 

9. Junto a secção **Definições de SSO** , copie o URL em **url de metadados do fornecedor de serviço**.
    
    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

10. Abra a **ligação do URL de metadados** num browser em branco para transferir o documento de metadados. Em seguida, utilize o valor de EntityDescriptor que reconhecer fornecido para **identificador** na caixa de diálogo **Configurar definições de aplicação** .

    ![Configurar o lado único início de sessão na aplicação](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

11. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

12. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **Portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na caixa de texto **Apelido** , escreva **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-recognize-test-user"></a>Criar um utilizador de teste de reconhecer

Para permitir que utilizadores do Azure AD que inicie sessão no reconhecer, tem de ser aprovisionadas para reconhecer. No caso de reconhecer, aprovisionamento é uma tarefa manual.

Esta aplicação não suporta SCIM aprovisionamento, mas tem uma sincronização de utilizador alternativa fornece que os utilizadores. 

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Aprovisionamento de uma conta de utilizador, execute os seguintes passos:

1.  Inicie a sessão no site da sua empresa reconhecer como administrador.

2.  No canto superior direito, clique em **Menu**. Vá para **administrador de empresa**.

3.  No painel de navegação esquerdo, clique em **Definições**.

4.  Execute os seguintes passos na secção de **Sincronização do utilizador** .
    
    ![Novo utilizador] (./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Novo utilizador")

    um. Como **Sincronizar ativado**, selecione **Sucessivamente**.

    b. Como **Escolher fornecedor de sincronização**, selecione **Microsoft / Office 365**.

    c. Clique em **executar a sincronização de utilizador**

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a reconhecer.
    
![Atribuir utilizador][200]

**Para atribuir Britta Simon a reconhecer, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.
    
    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **reconhecer**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)

3. No menu no canto superior, clique em **utilizadores**.
    
    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.
    
    ![Atribuir utilizador][205]

### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.
 
Quando clica no mosaico de reconhecer no painel do Access, pode deve obter automaticamente assinado-on à aplicação reconhecer.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png
