<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com o serviço de Web Amazon (AWS) | Microsoft Azure"
    description="Saiba como utilizar Amazon Web Services (AWS) com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!"
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


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>Tutorial: Integração do Azure Active Directory com o serviço de Web Amazon (AWS)

Objectivo deste tutorial é mostrar-lhe como integrar o serviço de Web Amazon (AWS) com o Azure Active Directory (Azure AD).  
Integrar o serviço de Web Amazon (AWS) com o Azure AD fornece os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso ao serviço do Amazon Web (AWS) 
- Pode ativar os seus utilizadores para automaticamente obter tiver sessão iniciada no serviço Web para Amazon (AWS) (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com o serviço de Web Amazon (AWS), tem os seguintes itens:

- Uma subscrição do Azure AD
- Um serviço de Web Amazon (AWS) sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial é constituído por três blocos modulares principais:

1. Adicionar Amazon Web Service (AWS) a partir da Galeria 
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>Adicionar Amazon Web Service (AWS) a partir da Galeria
Para configurar a integração do serviço de Web Amazon (AWS) no Azure AD, é necessário adicionar Amazon Web Service (AWS) a partir da Galeria à sua lista de aplicações de SaaS geridas.

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>Para adicionar Amazon Web Service (AWS) a partir da galeria, execute os seguintes passos:

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1] 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior. 
   
    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página. 
   
    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**. 
   
    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Amazon Web Service (AWS)**.
   
    ![Aplicações][5]

7. No painel de resultados, selecione **Amazon Web Service (AWS)**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Aplicações][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com o serviço de Web de Amazon (AWS) com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no serviço de Web Amazon (AWS) a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no serviço de Web Amazon (AWS) tem de ser estabelecida.  
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor de **nome de utilizador** no serviço de Web Amazon (AWS).
 
Para configurar e testar Azure AD serviço single sign-on com o serviço de Web Amazon (AWS), é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD única Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um serviço de Web Amazon (AWS) testar utilizador](#creating-a-halogen-software-test-user)** - ter homólogo para Britta Simon no serviço de Web Amazon (AWS), que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurar o Azure AD único Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação de serviço de Web Amazon (AWS).  
A aplicação de serviço de Web Amazon (AWS) espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** . A captura de ecrã seguinte mostra um exemplo para esta situação.


![Configurar o Single Sign-On][27]

**Para configurar Azure AD serviço single sign-on com o serviço de Web Amazon (AWS), execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação de **Serviço de Web Amazon (AWS)** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][7]

2. Na página **como pretende utilizadores a iniciar sessão serviço de Web para Amazon (AWS)** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On][8]

3. Na página de diálogo **Configurar definições de aplicação** , clique em seguinte. 

    ![Configurar definições de aplicação][9]
 
4. Na página **Configurar serviço single sign-on no serviço de Web Amazon (AWS)** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.

    ![Configurar o Single Sign-On][10]

5. Numa janela do browser diferente, início de sessão no seu site de empresa Amazon Web Service (AWS) como administrador.

6. Clique em **Base consola**.

    ![Configurar o Single Sign-On][11]

7. Clique em **Gestão de acesso e de identidade**. 

    ![Configurar o Single Sign-On][12]

8. Clique **Fornecedores de identidade**e, em seguida, clique em **Criar fornecedor**. 

    ![Configurar o Single Sign-On][13]

9. Na página de diálogo **Configurar fornecedor** , execute os seguintes passos: 

    ![Configurar o Single Sign-On][14]

     um. Como **O tipo de fornecedor**, selecione **SAML**.

     b. Na caixa de texto **Nome do fornecedor** , escreva um nome de fornecedor (por exemplo: *madeira não tratados*).

     c. Para carregar o ficheiro de metadados transferido, clique em **Escolher ficheiro**.

     d. Clique em **próximo passo**.


10. Na página de diálogo **Informações do fornecedor de verificar** , clique em **Criar**. 

    ![Configurar o Single Sign-On][15]

11. Clique em **funções**e, em seguida, clique em **Criar novo grupo**. 

    ![Configurar o Single Sign-On][16]

12. Na caixa de diálogo **Definir nome da função** , execute os seguintes passos: 

    ![Configurar o Single Sign-On][17]

    um. Na caixa de texto **Nome de função** , escreva um nome de função (por exemplo: *utilizadorteste*).

    b. Clique em **próximo passo**.

13. Na caixa de diálogo **Selecione o tipo de função** , execute os seguintes passos: 

    ![Configurar o Single Sign-On][18]

    um. Selecione **a função de acesso do fornecedor de identidade**.

    b. Na secção **acesso conceder Web Single Sign-On (WebSSO) para fornecedores de SAML** , clique em **Selecionar**.


14. Na caixa de diálogo **Estabelecer confiar** , execute os seguintes passos:  

    ![Configurar o Single Sign-On][19]
     
     um. Como fornecedor SAML, selecione o fornecedor SAML ter criado previousley (por exemplo: *madeira não tratados*) 

     b. Clique em **próximo passo**.


15. Na caixa de diálogo **Verificar função fidedignidade** , clique em **Próximo passo**. 

    ![Configurar o Single Sign-On][32]


16. Na caixa de diálogo **Anexar política** , clique em **Próximo passo**.  

    ![Configurar o Single Sign-On][33]


17. Na caixa de diálogo **Rever** , execute os seguintes passos:   

    ![Configurar o Single Sign-On][34]

     um. Copie o valor de **Informações de função** .

     b. Copie o valor de informações de **Entidades fidedignas** .

     c. Clique em **criar a função**. 

18. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![O que é a ligação do Azure AD][20]

19. Na página de **confirmação de início de sessão única** , clique em **Concluir** para fechar a caixa de diálogo **Configurar serviço single sign-on** .

    ![O que é a ligação do Azure AD][22]


20. No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** . 

    ![Configurar o Single Sign-On][21]

21. Clique em **Adicionar utilizador atributo**. 

    ![Configurar o Single Sign-On][23]

22. Na caixa de diálogo Adicionar utilizador atributo, execute os passos seguintes. 

    ![Configurar o Single Sign-On][24] 

    um. Na caixa de texto **Nome do atributo** , escreva **https://aws.amazon.com/SAML/Attributes/Role**.

    b. Na caixa de texto **Valor do atributo** , escreva **[o valor de informações de função], [o valor de informações de entidade fidedignos]**.

    >[AZURE.TIP] Estes são os valores que copiou da caixa de diálogo rever quando tiver criado ao seu cargo. 

    c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar atributo do utilizador** .

23. Clique em **Adicionar utilizador atributo**. 

    ![Configurar o Single Sign-On][23]


24. Na caixa de diálogo Adicionar utilizador atributo, execute os passos seguintes. 

    ![Configurar o Single Sign-On][25]


     um. Na caixa de texto **Nome do atributo** , escreva **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Na caixa de texto **Valor do atributo** , escreva ou selecione **user.userprincipalname** da lista pendente.
     
    ![Configurar o Single Sign-On][35]
    

     c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar atributo do utilizador** .


25. Clique em **Aplicar alterações**. 

    ![Configurar o Single Sign-On][26]





### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD

É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**. 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. Como o tipo de utilizador, selecione novo utilizador na sua organização.
  2. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.
  3. Clique em seguinte.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. No **Apelido** txtbox, tipo, **Simon**.
  
    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.
  
    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.
  
    b. Clique em **concluído**.   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>Criar um utilizador de teste do serviço de Web Amazon (AWS)

É o objectivo de nesta secção criar um utilizador denominado Britta Simon no serviço de Web Amazon (AWS).

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>Para criar um utilizador denominado Britta Simon no serviço de Web Amazon (AWS), execute os seguintes passos:

1. Inicie sessão no site da sua empresa **Amazon Web Service (AWS)** como administrador.

2. Clique no ícone de **Consola base** . 

    ![Configurar o Single Sign-On][11]

3. Clique em de identidade e gestão de acesso. 

    ![Configurar o Single Sign-On][28]

4. No Dashboard de, clique em utilizadores e, em seguida, clique em criar novos utilizadores. 

    ![Configurar o Single Sign-On][29]

5. Na caixa de diálogo Criar utilizador, efetue os seguintes passos: 

    ![Configurar o Single Sign-On][30]

     um. Nas caixas de texto **Introduzir nomes de utilizador** , escreva o nome de utilizador de Brita Simon (userprincipalname) no Azure AD.

     b. Clique em **Criar**.




### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso ao serviço do Amazon Web (AWS).

![Atribuir utilizador][31]

**Para atribuir Britta Simon CloudPassage, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][26]

2. Na lista de aplicações, selecione **Amazon Web Service (AWS)**.

    ![Atribuir utilizador][27]

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][25]

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][29]

### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico do serviço de Web Amazon (AWS) no painel do Access, pode deve obter automaticamente assinado-on para a sua aplicação de serviço de Web Amazon (AWS).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png






















