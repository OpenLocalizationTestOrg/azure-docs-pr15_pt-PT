<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com um Software de halogéneo"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e halogéneo Software."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Tutorial: Integração do Azure Active Directory com um Software de halogéneo

Objectivo deste tutorial é mostrar-lhe como integrar o Software de halogéneo com o Azure Active Directory (Azure AD).

Integrar o Software de halogéneo com o Azure AD fornece os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso ao Software de halogéneo 
- Pode ativar os seus utilizadores para automaticamente obter assinado-on ao Software de halogéneo (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com um Software de halogéneo, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Software de halogéneo sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste. 

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar halogéneo Software a partir da Galeria 
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-halogen-software-from-the-gallery"></a>Adicionar halogéneo Software a partir da Galeria
Para configurar a integração do Software de halogéneo no Azure AD, é necessário adicionar halogéneo Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar halogéneo Software a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página. 

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **software halogéneo**.

    ![Aplicações][5]

7. No painel de resultados, selecione **Halogéneo Software**e, em seguida, clique em **Concluir** para adicionar a aplicação.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com um Software de halogéneo com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no Software halogéneo a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Software halogéneo tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Software halogéneo.
 
Para configurar e testar Azure AD serviço single sign-on com um Software de halogéneo, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD única Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um Software de halogéneo testar utilizador](#creating-a-halogen-software-test-user)** - ter homólogo para Britta Simon no Software halogéneo que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurar o Azure AD único Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na aplicação de Software de halogéneo.


**Para configurar o Azure AD serviço single sign-on com um Software de halogéneo, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação de **Software de halogéneo** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][8]

2. Na página **como pretende utilizadores a iniciar sessão Software halogéneo** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][9]

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:  ![configurar definições de aplicação][10]
 
     um. na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão sua aplicação de Software de halogéneo utilizando o seguinte padrão: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Clique em **seguinte**.
 
4. Na página **Configurar serviço single sign-on em halogéneo Software** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados localmente no seu computador.
    
    ![O que é a ligação do Azure AD][11]

5. Numa janela do browser diferente, início de sessão na aplicação de **Software de halogéneo** como administrador.

6. Clique no separador **Opções** . 

    ![O que é a ligação do Azure AD][12]


7. No painel de navegação esquerdo, clique em **SAML configuração**. 

    ![O que é a ligação do Azure AD][13]

8. Na página **Configuração de SAML** , execute os seguintes passos:  ![o que é a ligação do Azure AD][14]

    um. Como **Identificador exclusivo**, selecione **NameID**.

    b. Como **Exclusivo mapas de identificador a para**, selecione o **nome de utilizador**.

    c. Para carregar o ficheiro de metadados transferido, clique em **Procurar** para selecionar o ficheiro e, em seguida, **Carregar ficheiro**.

    d. Para testar a configuração, clique em **Executar testar**. 

    > [AZURE.NOTE] Ter de esperar para a mensagem "*a SAML teste está concluída. Feche esta janela*". Em seguida, feche a janela do browser aberto. A caixa de verificação **Ativar SAML** apenas é activada se o teste tenha sido concluído.

    "e". Selecione **Ativar SAML**.
    
    f. Clique em **Guardar alterações**. 


9. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** . 

    ![O que é a ligação do Azure AD][15]

10. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![O que é a ligação do Azure AD][16]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O que é a ligação do Azure AD][100] 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![O que é a ligação do Azure AD][101] 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**. 

    ![O que é a ligação do Azure AD][102] 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![O que é a ligação do Azure AD][103] 
 
    um. Como o **Tipo de utilizador**, selecione **novo utilizador na sua organização**.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em seguinte.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: 

    ![O que é a ligação do Azure AD][104] 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. No **Apelido** txtbox, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![O que é a ligação do Azure AD][105]  

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![O que é a ligação do Azure AD][106]   

    um. Escreva o valor da **Nova palavra-passe**.
    b. Clique em **concluído**.   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Criar um utilizador de teste do Software de halogéneo

É o objectivo de nesta secção criar um utilizador designado Britta Simon no Software halogéneo.

**Para criar um utilizador designado Britta Simon no Software halogéneo, execute os seguintes passos:**

1. Inicie sessão aplicação de **Software de halogéneo** como administrador.

2. Clique no separador **Centro de utilizador** e, em seguida, clique em **Criar utilizador**.

    ![O que é a ligação do Azure AD][300]  

3. Na página de diálogo **Novo utilizador** , execute os seguintes passos:

    ![O que é a ligação do Azure AD][301]

    um. Na caixa de texto **nome próprio** , escreva **Britta**. 
  
    b. Na caixa de texto **Apelido** , escreva **Simon**.
  
    c. Na caixa de texto **nome de utilizador** , escreva o **nome de utilizador do Brita Simon no portal do Azure clássico**.
  
    d. Na caixa de texto **palavra-passe** , escreva uma palavra-passe para Britta.
  
    "e". Clique em **Guardar**.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso ao Software de halogéneo.

![O que é a ligação do Azure AD][200]

**Para atribuir Britta Simon halogéneo Software, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![O que é a ligação do Azure AD][201]

2. Na lista de aplicações, selecione **Halogéneo Software**.

    ![O que é a ligação do Azure AD][202]

1. No menu no canto superior, clique em **utilizadores**.

    ![O que é a ligação do Azure AD][203]

1. Na lista de utilizadores, selecione **Britta Simon**.

    ![O que é a ligação do Azure AD][204]

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![O que é a ligação do Azure AD][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico do Software de halogéneo no painel do Access, pode deve obter automaticamente assinado-on para a sua aplicação de Software de halogéneo.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png