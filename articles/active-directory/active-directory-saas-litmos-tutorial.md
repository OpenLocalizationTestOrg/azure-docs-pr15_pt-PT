<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Litmos | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Litmos."
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


# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Tutorial: Integração do Azure Active Directory com Litmos

Objectivo deste tutorial é mostrar-lhe como integrar o Litmos com o Azure Active Directory (Azure AD).  
Integrar o Litmos com o Azure AD fornece os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso ao Litmos 
- Pode permitir que os utilizadores para automaticamente obter assinado-on para Litmos (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - o Azure Active Directory 

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com Litmos, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Litmos sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial é constituído por três blocos modulares principais:

1. Adicionar Litmos a partir da Galeria 
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-litmos-from-the-gallery"></a>Adicionar Litmos a partir da Galeria
Para configurar a integração da Litmos no Azure AD, é necessário adicionar Litmos a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Litmos a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Litmos**.

    ![Aplicações][5]

7. No painel de resultados, selecione **Litmos**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Aplicações][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com Litmos com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no Litmos a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Litmos tem de ser estabelecida.  
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Litmos.
 
Para configurar e testar Azure AD serviço single sign-on com Litmos, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um Litmos testar utilizador](#creating-a-halogen-software-test-user)** - ter homólogo para Britta Simon no Litmos que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure AD e para configurar o início de sessão único na sua aplicação Litmos.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

Como parte da configuração, tem de personalizar os **Atributos Token SAML** para a sua aplicação Litmos.  

![Azure AD Single Sign-On][17] 

**Para configurar Azure AD serviço single sign-on com Litmos, execute os seguintes passos:**

1. No portal clássico do Azure AD, na página de integração de aplicação **Litmos** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão Litmos** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.
 
    ![Azure AD Single Sign-On][7] 


1. Início de sessão no site da sua empresa Litmos (por exemplo: *https://azureapptest.litmos.com/account/Login*) como administrador.

    ![Azure AD Single Sign-On][21] 


1. Na barra de navegação no lado esquerdo, clique em **contas**.

    ![Azure AD Single Sign-On][22] 


1. Clique no separador **integrações** .

    ![Azure AD Single Sign-On][23] 


1. No separador **integrações** , desloque para baixo até **3º festa integrações**e, em seguida, clique em separador de **SAML 2.0** .

    ![Azure AD Single Sign-On][24] 

1. Copie o valor em **é o SAML endoiint para litmos:**.

    ![Azure AD Single Sign-On][26] 


3. No portal do Azure clássico, na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Azure AD Single Sign-On][8] 
 
    um. Na caixa de texto **identificador** , escreva o URL utilizado pelos seus utilizadores para o início de sessão à aplicação Litmos (por exemplo: *https://azureapptest.litmos.com/account/Login*).
     
    b. Na caixa de texto **URL de resposta** , cole o valor que copiou a partir da aplicação Litmos no passo anterior.

    c. Clique em **seguinte**.
 
4. Na página **Configurar serviço single sign-on na Litmos** , execute os seguintes passos:

    ![Azure AD Single Sign-On][2] 

    um. Clique em Transferir certificado e, em seguida, guarde o ficheiro no seu computador.


1. Na aplicação de **Litmos** , execute os seguintes passos:

    ![Azure AD Single Sign-On][25] 

    um. Clique em **Ativar SAML**.

    b. Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

    >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    c. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado de x. 509 SAML** .

    d. Clique em **Guardar alterações**.


6. No portal do Azure AD clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 

    ![Azure AD Single Sign-On][10]

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
  
    ![Azure AD Single Sign-On][11]


20. No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** . 

    ![Configurar o Single Sign-On][12]


24. Na caixa de diálogo **Adicionar utilizador atributo** , execute os seguintes passos: 

    ![Configurar o Single Sign-On][14]

  	| Nome do atributo | Valor do atributo |
  	| ---            | ---             |
  	| Mensagem de correio electrónico          | User.Mail       |
  	| NomePróprio      | User.givenName  |
  	| Apelido       | User.Surname    |

    Para cada linha de dados na tabela acima, execute os seguintes passos:
   
    um. Clique em **Adicionar utilizador atributo**. 

    ![Configurar o Single Sign-On][15]


    um. Na caixa de texto **Nome do atributo** , escreva o **Nome do atributo** apresentados para nessa linha.

    b. Selecione o **Valor do atributo** apresentados para nessa linha.

    c. Clique em **Concluir** para fechar a caixa de diálogo **Adicionar atributo do utilizador** .


25. Clique em **Aplicar alterações**. 

    ![Configurar o Single Sign-On][16]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.  

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal do Azure clasic**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
 
4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**. 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  

    um. Como o **Tipo de utilizador**, selecione **novo utilizador na sua organização**.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: 

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
 
    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.
    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
 
8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
  
    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   

  
 
### <a name="creating-a-litmos-test-user"></a>Criar um utilizador de teste Litmos

É o objectivo de nesta secção criar um utilizador designado Britta Simon no Litmos.  
A aplicação Litmos suporta apenas em vez de aprovisionamento. Isto significa, uma conta de utilizador é criada automaticamente se for necessário durante uma tentativa de aceder à aplicação utilizando o painel de acesso.

**Para criar um utilizador designado Britta Simon no Litmos, execute os seguintes passos:**


1. Início de sessão no site da sua empresa Litmos (por exemplo: *https://azureapptest.litmos.com/account/Login*) como administrador.

    ![Azure AD Single Sign-On][21] 


1. Na barra de navegação no lado esquerdo, clique em **contas**.

    ![Azure AD Single Sign-On][22] 


1. Clique no separador **integrações** .

    ![Azure AD Single Sign-On][23] 


1. No separador **integrações** , desloque para baixo até **3º festa integrações**e, em seguida, clique em separador de **SAML 2.0** .

    ![Azure AD Single Sign-On][24] 

1. Selecione **Gerar automaticamente utilizadores:**.

    ![Azure AD Single Sign-On][27] 


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Litmos.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Litmos, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Litmos**.

    ![Atribuir utilizador][202] 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico Litmos no painel do Access, pode deve obter automaticamente assinado-on à aplicação Litmos.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png





