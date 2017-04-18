<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Questetra secção BPM Suite | Microsoft Aure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e o conjunto de aplicações do Questetra secção BPM."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Azure Active Directory com Questetra secção BPM Suite

Objectivo deste tutorial é mostrar-lhe como integrar o conjunto de aplicações do Questetra secção BPM com o Azure Active Directory (Azure AD).  
Integrar o conjunto de aplicações do Questetra secção BPM com o Azure AD fornece os seguintes benefícios: 

- Pode controlar no Azure AD quem tem acesso ao conjunto de aplicações do Questetra secção BPM 
- Pode ativar os seus utilizadores para automaticamente obter assinado-on ao conjunto de aplicações do Questetra secção BPM (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos 

Para configurar a integração do Azure AD com o conjunto de aplicações do Questetra secção BPM, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um [Conjunto de aplicações do Questetra secção BPM](https://senbon-imadegawa-988.questetra.net/) sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial é constituído por três blocos modulares principais:

1. Adicionar conjunto de aplicações do Questetra secção BPM a partir da Galeria 
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Adicionar conjunto de aplicações do Questetra secção BPM a partir da Galeria
Para configurar a integração do conjunto de aplicações de secção BPM Questetra no Azure AD, é necessário adicionar conjunto de aplicações do Questetra secção BPM a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar conjunto de aplicações do Questetra secção BPM a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **o conjunto de aplicações do Questetra secção BPM**.

    ![Aplicações][5]

7. No painel de resultados, selecione **o conjunto de aplicações do Questetra secção BPM**e, em seguida, clique em **Concluir** para adicionar a aplicação.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com Questetra secção BPM conjunto de aplicações com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no conjunto de aplicações do Questetra secção BPM a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no conjunto de aplicações do Questetra secção BPM tem de ser estabelecida.  
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no conjunto de aplicações do Questetra secção BPM.
 
Para configurar e testar Azure AD serviço single sign-on com Questetra secção BPM Suite, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um conjunto de aplicações do Questetra secção BPM testar utilizador](#creating-a-questetra-bpm-suite-test-user)** - ter homólogo para Britta Simon no conjunto de aplicações do Questetra secção BPM que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação do conjunto de aplicações do Questetra secção BPM.

**Para configurar Azure AD serviço single sign-on com conjunto de aplicações do Questetra secção BPM, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração do **Conjunto de aplicações do Questetra secção BPM** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][8]

2. Na página **como pretende utilizadores a iniciar sessão conjunto de aplicações do Questetra secção BPM** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][9]


3. Numa janela do browser web diferente, inicie sessão no site da sua empresa **o conjunto de aplicações do Questetra secção BPM** como administrador.

4. No menu no canto superior, clique em **Definições do sistema**. 

    ![Azure AD Single Sign-On][10]

5. Para abrir a página de **SingleSignOnSAML** , clique em **SSO (SAML)**. 

    ![Azure AD Single Sign-On][11]


6. No portal do Azure clássico, na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos: 

    ![Configurar definições de aplicação][13]
 
    um. Em que **o conjunto de aplicações do Questetra secção BPM** site de empresa, na secção informações do SP, copie o **URL de ACS**e, em seguida, colá-la a caixa de texto de **Início de sessão no URL** .

    b. Por que **Questetra secção BPM Suite** site de empresa, na secção informações do SP, copie o **ID da entidade**e, em seguida, colá-la a caixa de texto **Emissor URL** .

    c. Em que **o conjunto de aplicações do Questetra secção BPM** site de empresa, na secção informações do SP, copie o **URL de ACS**e, em seguida, colá-la a caixa de texto do **URL de resposta** .

    d. Clique em **seguinte**.

 
7. Na página **Configurar serviço single sign-on no conjunto de aplicações do Questetra secção BPM** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On][14]


8. No site de empresa do **Conjunto de aplicações do Questetra secção BPM** , execute os seguintes passos: 

    ![Configurar o Single Sign-On][15]

    um. Selecione **Ativar Single Sign-On**.
     
    b. No portal do Azure clássico, copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto de **ID da entidade** .

    c. No portal do Azure clássico, copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto de **início de sessão no URL da página** .

    d. No portal do Azure clássico, copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto do **URL da página terminar sessão** .

    "e". Na caixa de texto **Formatar NameID** , escreva **urn: organização de normalização: nomes: tc: SAML:1.1:nameid-formato: emailAddress**.


    f. Crie um ficheiro de codificado de base-64 a partir do seu certificado transferido. 

    >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

    g. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, colá-la a caixa de texto do **certificado de validação** . 

    h. Clique em **Guardar**.


9. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 

    ![O que é a ligação do Azure AD][17]


10. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![O que é a ligação do Azure AD][18]




### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criar utilizador de teste do Azure AD][100] 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criar utilizador de teste do Azure AD][101] 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**. 

    ![Criar utilizador de teste do Azure AD][102] 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criar utilizador de teste do Azure AD][103]
 
    um. Como o **Tipo de utilizador**, selecione **novo utilizador na sua organização**.
  
    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em seguinte.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: 

    ![Criar utilizador de teste do Azure AD][104] 
  
    um. Na caixa de texto **nome próprio** , escreva **Britta**. 
 
    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criar utilizador de teste do Azure AD][105]  

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criar utilizador de teste do Azure AD][106]   
  
    um. Escreva o valor da **Nova palavra-passe**.
  
    b. Clique em **concluído**.   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>Criar um utilizador de teste do conjunto de aplicações do Questetra secção BPM

É o objectivo de nesta secção criar um utilizador denominado Britta Simon no conjunto de aplicações do Questetra secção BPM.

**Para criar um utilizador denominado Britta Simon no conjunto de aplicações do Questetra secção BPM, execute os seguintes passos:**

1.  Sign-on para o site da sua empresa de conjunto de aplicações do Questetra secção BPM como administrador.
2.  Aceda a **definições do sistema > lista de utilizador > novo utilizador**. 
3.  Na caixa de diálogo novo utilizador, efetue os seguintes passos: 

    ![Crie utilizador de teste][300] 

    um. Na caixa de texto **nome** , escreva o nome de utilizador de Britta no Azure AD.

    b. Na caixa de **correio eletrónico** texto, escreva o nome de utilizador de Britta no Azure AD.

    c. Na caixa de texto **palavra-passe** , escreva uma palavra-passe.

4.  Clique em **Adicionar novo utilizador**.



### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on concedendo o acesso ao conjunto de aplicações do Questetra secção BPM.

![O que é a ligação do Azure AD][200]

**Para atribuir Britta Simon ao conjunto de aplicações do Questetra secção BPM, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![O que é a ligação do Azure AD][201]

2. Na lista de aplicações, selecione **o conjunto de aplicações do Questetra secção BPM**.

    ![O que é a ligação do Azure AD][205]

1. No menu no canto superior, clique em **utilizadores**.

    ![O que é a ligação do Azure AD][202]

1. Na lista de utilizadores, selecione **Britta Simon**.

    ![O que é a ligação do Azure AD][203]

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![O que é a ligação do Azure AD][204]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico do conjunto de aplicações do Questetra secção BPM no painel do Access, pode deve obter automaticamente assinado-on à aplicação Questetra secção BPM Suite.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 