<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Cisco motores | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Cisco motores."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Tutorial: Integração do Azure Active Directory com Cisco motores

Neste tutorial, aprende integrar Cisco motores com o Azure Active Directory (Azure AD).

Integrar o Cisco motores com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Cisco motores
- Pode ativar os seus utilizadores para automaticamente obter assinado-on para motores de Cisco (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Cisco motores, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um **Cisco motores** sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste. O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar motores Cisco a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-cisco-spark-from-the-gallery"></a>Adicionar motores Cisco a partir da Galeria
Para configurar a integração dos motores de Cisco na Azure AD, é necessário adicionar motores Cisco a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar motores Cisco a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Cisco motores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. No painel de resultados, selecione **Cisco motores**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com motores de Cisco com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo no Cisco motores é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cisco motores tem de ser estabelecida.
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Cisco motores. Para configurar e testar Azure AD serviço single sign-on com Cisco motores, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um motores Cisco testar utilizador](#creating-a-cisco-spark-test-user)** - ter homólogo para Britta Simon no motores de Cisco que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação Cisco motores.

Aplicação de motores de Cisco espera as declarações de SAML para conter atributos específicos. Configure os seguintes atributos para esta aplicação. Pode gerir os valores das seguintes atributos a partir do separador **"Atrributes"** da aplicação. A captura de ecrã seguinte mostra um exemplo para esta situação.

![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Para configurar Azure AD serviço single sign-on com Cisco motores, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação de **Cisco motores** , no menu na parte superior, clique em **atributos**.
     
    ![Configurar o Single Sign-On][5]


2. Na caixa de diálogo **atributos tokens SAML** , execute os seguintes passos:

    um. Clique em **Adicionar utilizador atributo** para abrir a caixa de diálogo **Adicionar Attribure de utilizador** .

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b. Na caixa de texto **Nome do atributo** , escreva **uid**.
    
    c. A partir da lista de **Valor do atributo** , selecione **user.userprincipal**.
    
    d. Clique em **concluído**. Em seguida, **Aplicar alterações** na parte inferior da página.

3. No menu no canto superior, clique em **Guia de introdução**.

    ![Configurar o Single Sign-On][6]

4. No portal do clássico, na página de integração de **Motores de Cisco** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][7] 

5. Na página **como pretende utilizadores a iniciar sessão Cisco motores** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    um. Na caixa de texto URL no início de sessão, escreva um URL que utiliza o seguinte padrão: `https://web.ciscospark.com/#/signin`.

    b. Clique em **seguinte**.


7. Na página **Configurar serviço single sign-on em motores de Cisco** , clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. Inicie sessão na [Gestão de colaboração de nuvem Cisco](https://admin.ciscospark.com/) com as credenciais de administrador completo.
9. Selecione **Definições** e, na secção de **autenticação** , clique em **Modificar**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. Selecione **integrar um fornecedor de identidade de terceiros 3. (Avançadas)** e aceda ao ecrã seguinte.
11. Clique em **Transferir o ficheiro de metadados** e guardar o ficheiro no seu computador.
12. Na página **Importar Idp metadados** , arraste e largue o ficheiro de metadados do Azure AD para a página ou utilize a opção de browser do ficheiro para localizar e carregue o ficheiro de metadados do Azure AD. Em seguida, selecione **exigir certificado assinado por uma autoridade de certificação nos metadados (mais segura)** e clique em **seguinte**. 

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. Selecione **Testar SSO ligação**e, quando o abre num novo separador do browser, autenticar com Azure AD ao iniciar sessão.
14. Voltar para o separador do browser de **Gestão de colaboração Cisco na nuvem** . Se o teste foi efetuada com êxito, selecione **Este teste foi efetuada com êxito. Ativar a opção Single Sign-On** e clique em **seguinte**.

7. No portal do Azure AD clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

8. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
    
    ![Azure AD Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-cisco-spark-test-user"></a>Criar um utilizador de teste Cisco motores

Nesta secção, crie um utilizador designado Britta Simon no Cisco motores. Nesta secção, crie um utilizador designado Britta Simon no Cisco motores.

1. Vá para a [Gestão de colaboração de nuvem Cisco](https://admin.ciscospark.com/) com as credenciais de administrador completo.
2. Clique em **utilizadores** e, em seguida, **Gerir utilizadores**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Na janela do **Utilizador gerir** , selecione **Adicionar ou modificar utilizadores manualmente** e clique em **seguinte**.
4. Selecione **nomes e endereços de E-Mail**. Em seguida, preencha a caixa de texto, tal como a seguir:

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**

    b. Na caixa de texto **Apelido** , escreva **Simon**

    c. Na caixa de texto **endereço de correio eletrónico** , escreva**britta.simon@contoso.com**

5. Clique no sinal de adição para adicionar Britta Simon. Em seguida, clique em **seguinte**.
6. Na janela **Adicionar serviços para utilizadores** , clique em **Guardar** e, em seguida, **Concluir**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Cisco motores.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Cisco motores, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Cisco motores**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de todos os utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico de motores de Cisco no painel do Access, pode deve obter automaticamente assinado-on à aplicação Cisco motores.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png
