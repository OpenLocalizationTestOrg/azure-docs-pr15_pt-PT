<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Lifesize nuvem | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Lifesize nuvem."
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
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integração do Azure Active Directory com Lifesize nuvem

Neste tutorial, aprende integrar Lifesize nuvem com o Azure Active Directory (Azure AD).

Integrar o Lifesize nuvem com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD que tenha acesso à nuvem Lifesize
- Pode ativar os seus utilizadores para automaticamente obter assinado-on Lifesize nuvem (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Lifesize nuvem, tem os seguintes itens:

- Uma subscrição do Azure AD
- Uma nuvem Lifesize sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Lifesize nuvem a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-lifesize-cloud-from-the-gallery"></a>Adicionar Lifesize nuvem a partir da Galeria
Para configurar a integração da nuvem Lifesize no Azure AD, é necessário adicionar Lifesize nuvem a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Lifesize nuvem a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory][1]
2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Lifesize nuvem**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. No painel de resultados, selecione **Lifesize nuvem**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com Lifesize nuvem com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na nuvem Lifesize é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na nuvem Lifesize tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** na nuvem Lifesize.

Para configurar e testar Azure AD serviço single sign-on com Lifesize nuvem, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar uma nuvem Lifesize testar utilizador](#creating-a-lifesize-cloud-test-user)** - ter homólogo para Britta Simon na nuvem Lifesize que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação Lifesize nuvem.


**Para configurar Azure AD serviço single sign-on com Lifesize nuvem, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação **Lifesize nuvem** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão nuvem Lifesize** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de nuvem Lifesize utilizando o seguinte padrão: **https://login.lifesizecloud.com/ls/?acs**.
    
    b. Clique em **seguinte**
 
4. Na página **Configurar serviço single sign-on na nuvem Lifesize** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    um. Clique em **Transferir o certificado**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Para obter SSO configurado para a sua aplicação, início de sessão para a aplicação de nuvem Lifesize com privilégios de administrador.

6. No canto superior direito clique no seu nome e, em seguida, clique nas **Definições avançadas**

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. Definições de avançar agora clique na ligação da **Configuração de SSO** . Isto irá abra a página de configuração de SSO para sua instância.

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. Agora configure os seguintes valores na configuração do SSO IU.    

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • Copie o valor de URL emissor a partir do Azure AD e cole na caixa de texto **Emissor do fornecedor de identidade** .

    • Copie o valor de URL remoto de início de sessão a partir do Azure AD e cole na caixa de texto do **URL de início de sessão** .

    • Abrir o certificado transferido no bloco de notas e copie o conteúdo do certificado, excluindo as linhas começar e o certificado de fim, cole o seguinte na caixa de texto **Certificado x. 509** .

    • No mapeamento de atributo SAML para a caixa de texto **nome próprio** , introduza o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • No mapeamento de atributo SAML para a caixa de texto **Nome do último** introduza um valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • No mapeamento de atributo SAML para a caixa de texto de **mensagem de correio electrónico** , introduza o valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. Para verificar a configuração pode clicar no botão de **teste** .

    > [AZURE.NOTE] Para testar a ligação com êxito, é necessário concluir o Assistente de configuração no Azure AD e também fornecem acesso a utilizadores ou grupos que podem desempenhar o teste.
    
10. Ative o SSO clicando no botão **Ativar SSO** .

11. Agora, clique no botão de **atualização** para que todas as definições são guardadas. Isto irá gerar o valor de RelayState. Copie o valor de RelayState que é gerado na caixa de texto. Irá precisamos este valor nos próximos passos.

12. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

13. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]

14. Agora início de sessão para o Portal de gestão do Azure **https://portal.azure.com** com as credenciais de administrador

15. Clique na ligação **Mais serviços** no painel de navegação à esquerda
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Procurar Azure Active Directory e clique na ligação do **Azure Active Directory**
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. Irá encontrar todas as suas aplicações de SaaS no botão de **Aplicações empresariais** .

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. Agora, clique na ligação de **Todas as aplicações** na pá seguinte
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. Procurar Lifesize aplicação para o qual pretende configurar a RelayState. 
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. Agora, clique ligação **o início de sessão único** na pá

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. Irá ver a caixa de verificação **Mostrar avançadas definições de URL** . Clique na caixa de verificação.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. Agora, configure RelayState para a aplicação, que poderá ver na página de configuração de Lifesize aplicação SSO. 

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. Guarde as definições.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.


![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , execute os seguintes passos:  ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-an-lifesize-cloud-test-user"></a>Criação de um utilizador de teste Lifesize nuvem

Nesta secção, crie um utilizador denominado Britta Simon na nuvem Lifesize. Nuvem de Lifesize suporta aprovisionamento de utilizador automática. Depois de autenticação com êxito no Azure AD, o utilizador será automaticamente aprovisionado na aplicação. 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso à nuvem Lifesize.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Lifesize nuvem, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Lifesize nuvem**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


### <a name="testing-single-sign-on"></a>Testes Single Sign-On

Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clicar no mosaico da nuvem Lifesize no painel do Access, pode deve obter automaticamente assinado-on à aplicação Lifesize nuvem.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png
