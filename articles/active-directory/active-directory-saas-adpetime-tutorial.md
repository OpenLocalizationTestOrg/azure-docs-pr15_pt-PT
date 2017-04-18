<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com ADP eTime | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e ADP eTime."
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
    ms.date="10/17/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>Tutorial: Integração do Azure Active Directory com ADP eTime

Objectivo deste tutorial é mostrar-lhe como integrar o ADP eTime com o Azure Active Directory (Azure AD).  
Integrar o ADP eTime com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao ADP eTime
- Pode permitir que os utilizadores para automaticamente obter assinado-on para eTime ADP (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure


Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ADP eTime, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um ADP eTime sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Objectivo deste tutorial é permitem-lhe testar Azure AD serviço single sign-on num ambiente de teste.  
O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar ADP eTime a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-adp-etime-from-the-gallery"></a>Adicionar ADP eTime a partir da Galeria
Para configurar a integração dos ADP eTime na Azure AD, é necessário adicionar ADP eTime a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ADP eTime a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **ADP eTime**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)

7. No painel de resultados, selecione **ADP eTime**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Objectivo de nesta secção é mostrar-lhe como configurar e testar Azure AD serviço single sign-on com eTime ADP com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que é o utilizador homólogo no ADP eTime a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na ADP eTime tem de ser estabelecida.  
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no ADP eTime.

Para configurar e testar Azure AD serviço single sign-on com ADP eTime, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um eTime ADP testar utilizador](#creating-a-adpetime-test-user)** - ter uma contrapartida de Britta Simon no eTime ADP que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD Single Sign-On

O objectivo desta secção é para ativar o Azure AD serviço single sign-on no portal clássico do Azure e para configurar o início de sessão único na sua aplicação de eTime ADP.

A aplicação de eTime ADP espera as declarações SAML num formato específico, solicitar a adicionar mapeamentos de atributos personalizados para a configuração do token atributos SAML. A captura de ecrã seguinte mostra um exemplo para esta situação. O nome da afirmação será sempre **"PersonImmutableID"** e o valor que recomendamos ter mapeado para ExtensionAttribute2 que contém o campo IDdeEmpregado do utilizador. Aqui Fren de mapeamento de utilizador Azure AD para ADP eTime vai ser efetuado no campo IDdeEmpregado, mas isto pode mapear para um valor diferente também com base em definições da sua aplicação. Pelo que pedimos que trabalho com a equipa de eTime ADP pela primeira vez para utilizar o identificador correto de um utilizador e mapear esse valor com a afirmação **"PersonImmutableID"** .  

![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

Antes de poder configurar a declaração de SAML, tem de contactar a equipa de suporte de eTime ADP e pedir o valor do atributo Identificador exclusivo do seu inquilino. Precisa deste valor para configurar a afirmação personalizada para a sua aplicação.


**Para configurar Azure AD serviço single sign-on com ADP eTime, execute os seguintes passos:**

1. No portal clássico Azure, na página de integração de aplicação **ADP eTime** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .

    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão ADP eTime** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:.

    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 


    um. Na caixa de texto **Resposta URL** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de eTime de ADP utilizando o seguinte padrão: `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`.

    b. Clique em **seguinte**.

4. Na página **Configurar serviço single sign-on na ADP eTime** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png) 

    um. Clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.

    b. Clique em **seguinte**.


5. Para obter SSO configurado para a sua aplicação, contacte a equipa de suporte de eTime ADP e e-mail a anexar metadados um ficheiro transferido, para que pode ser configurados para a integração de SSO.

    > [AZURE.NOTE] Depois de equipa **ADP eTime** configurar a instância, obter o valor de **RelayState** das mesmas. Siga o abaixo mencionados passos configurá-lo. Após esta configuração pode testar a integração. Por isso, tenha em atenção que se trata de configuração importantes para esta integração de aplicação trabalhar.

6. Para configurar o valor de RelayState no Azure AD, execute os seguintes passos: 
    
    um. Início de sessão do [Portal de gestão do Azure](https://portal.azure.com) como administrador.

    b. No painel de navegação esquerdo, clique em **Mais serviços**. 
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png)

    c. Na caixa de texto **Procurar** , escreva o **Azure Active Directory**e, em seguida, clique na ligação relacionada.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)

    d. Clique em **aplicações empresariais**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)

    "e". Na secção **Gerir** , clique em **Todas as aplicações**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png)

    f. Na caixa de texto **Procurar** , escreva **ADP eTime**e, em seguida, clique na ligação relacionada. 
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)

    g. Na secção **Gerir** , clique em **serviço Single sign-on**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png)

    h. Selecione **Mostrar avançadas definições de URL**.
    
    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png)
    
    posso. Na caixa de texto **Estado do reencaminhamento** , escreva um valor utilizando os padrões de seguintes:
    
    - Ambiente de produção:`https://fed.adp.com/saml/fedlanding.html?<id>` 
    - Ambiente de teste:`https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`

    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png)

    j. Guarde as definições.

7. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.

    ![Azure AD Single Sign-On][10]

8. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  

    ![Azure AD Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
É o objectivo de nesta secção criar um utilizador de teste no portal do clássico Azure denominado Britta Simon.  
Na lista de utilizadores, selecione **Britta Simon**.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png) 

    um. Como o **Tipo de utilizador**, selecione **novo utilizador na sua organização**.

    b. Na caixa de texto **Nome de utilizador** , escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   



### <a name="creating-a-adp-etime-test-user"></a>Criar um utilizador de teste de eTime ADP

É o objectivo de nesta secção criar um utilizador designado Britta Simon no ADP eTime. Consulte trabalhe com a equipa de suporte eTime ADP para adicionar os utilizadores na conta de eTime ADP. 


> [AZURE.NOTE]Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte de eTime ADP.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Objectivo de nesta secção é ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a ADP eTime.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon ADP eTime, execute os seguintes passos:**

1. No portal do Azure clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **ADP eTime**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]



### <a name="testing-single-sign-on"></a>Testes Single Sign-On

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.  
Quando clica no mosaico de eTime ADP no painel do Access, pode deve obter automaticamente assinado-on à aplicação eTime ADP.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png
