<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com ServiceNow e ServiceNow Express | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e ServiceNow e ServiceNow Express."
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


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>Tutorial: Azure Active Directory a integração com ServiceNow e ServiceNow Express.


Neste tutorial, saiba como integrar ServiceNow e ServiceNow Express com o Azure Active Directory (Azure AD).

Integrar o ServiceNow e ServiceNow Express Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao ServiceNow e ServiceNow Express
- Pode permitir que os utilizadores para automaticamente obter assinado-on ServiceNow e ServiceNow Express (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ServiceNow e ServiceNow Express, tem os seguintes itens:

- Uma subscrição do Azure AD
- Para ServiceNow, uma instância ou o inquilino do ServiceNow, versão Calgary ou superior
- Para ServiceNow Express, uma instância do ServiceNow Express, versão Helsínquia ou superior
- ServiceNow inquilino tem de ter [Várias fornecedor único início de sessão no plug-in](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) ativado. Isto pode ser feito ao submeter um pedido de serviço no <https://hi.service-now.com/> 


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste. O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar ServiceNow a partir da Galeria
2. Configurar e testar o Azure AD único sign-on para ServiceNow ou ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>Adicionar ServiceNow a partir da Galeria
Para configurar a integração de ServiceNow ou ServiceNow Express no Azure AD, é necessário adicionar ServiceNow a partir da Galeria à sua lista de aplicações de SaaS geridas. 

**Para adicionar ServiceNow a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**. 

    ![O Active Directory][1]

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **ServiceNow**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. No painel de resultados, selecione **ServiceNow**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configure e teste Azure AD serviço single sign-on com ServiceNow ou ServiceNow Express com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo na ServiceNow é a um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na ServiceNow tem de ser estabelecida.
Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no ServiceNow. Para configurar e testar Azure AD serviço single sign-on com ServiceNow, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On para ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Azure AD Single Sign-On para ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - para permitir que os utilizadores utilizar esta funcionalidade.
3. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
4. **[Criar um ServiceNow testar utilizador](#creating-a-servicenow-test-user)** - ter homólogo para Britta Simon no ServiceNow que está ligado à representação do Azure AD da lhe.
5. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
6. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

> [AZURE.NOTE] Se pretender configurar ServiceNow omitir passo 2. Da mesma forma, se pretender configurar ServiceNow Express omitir passo 1.

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configuração do Azure AD Single Sign-On para ServiceNow

1.  No portal clássico do Azure AD, na página de integração de aplicação **ServiceNow** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão ServiceNow** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar serviço single sign-on")

3.  Na página **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar o URL da aplicação")

    um. na caixa de texto **ServiceNow início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para o início de sessão à aplicação ServiceNow seguir o padrão de: `https://<instance-name>.service-now.com`.

    b. Na caixa de texto **identificador** , escreva o URL da sua utilizado pelos seus utilizadores para o início de sessão à aplicação ServiceNow seguir o padrão de: `https://<instance-name>.service-now.com`.

    c. Clique em **seguinte**

4.  Para que o Azure AD configurar automaticamente ServiceNow para baseadas em SAML autenticação, introduza o nome da instância ServiceNow, administração nome de utilizador e palavra-passe de administrador no formulário de **configurar automaticamente o início de sessão único** e clique em *Configurar*. Tenha em atenção que o nome de utilizador do administrador fornecido tem de ter a função **security_admin** atribuída em ServiceNow para que isto funcione. Caso contrário, para configurar manualmente ServiceNow para utilizar o Azure AD como um fornecedor de identidade do SAML, clique em **configurar manualmente a aplicação para o início de sessão único**, em seguida, clique em **seguinte** e concluir os passos seguintes.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar o URL da aplicação")



5.  Na página **Configurar serviço single sign-on na ServiceNow** , clique em **certificado de transferência**, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar serviço single sign-on")

1. Início de sessão na sua aplicação ServiceNow como administrador.
2. Ativar o plug-in de _integração com o-vários fornecedor único início de sessão Installer_ ao seguir os passos seguintes:

    um. No painel de navegação no lado esquerdo, aceda à secção de **Definição de sistema** e, em seguida, clique em **Plug-ins**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Plug-in de ativar")
    
    b. Procurar _integração com o-vários fornecedor único início de sessão Installer_.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Plug-in de ativar")

    c. Selecione o plug-in. Rigth clique e selecione **Ativar/atualização**.
    
    d. Clique no botão **Ativar** .

2. No painel de navegação no lado esquerdo, clique em **Propriedades**.  

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurar o URL da aplicação")


3. Na caixa de diálogo **Propriedades do fornecedor de SSO várias** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurar o URL da aplicação")

    um. Como **Ativar o fornecedor de vários SSO**, selecione **Sim**.

    b. Como **Ativar registo de depuração tem o fornecedor de vários integração de SSO**, selecione **Sim**.

    c. Na caixa de texto **o campo no utilizador de tabela que...** , escreva **nomedoutilizador**.

    d. Clique em **Guardar**.



1. No painel de navegação no lado esquerdo, clique em **certificados x509**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configurar serviço single sign-on")


1. Na caixa de diálogo **Certificados x. 509** , clique em **Novo**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configurar serviço single sign-on")


1. Na caixa de diálogo **Certificados x. 509** , execute os seguintes passos:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar serviço single sign-on")

    um. Clique em **Novo**.

    b. Na caixa de texto **nome** , escreva um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    c. Selecione **ativo**.

    d. Como **Formatar**, selecione **PEM**.

    "e". Como **tipo**, selecione o **Certificado de arquivo de fidedignidade**.
    
    f. Abra o seu certificado de codificação Base64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto **PEM certificado** .

    g. Clique em **Atualizar**.


1. No painel de navegação no lado esquerdo, clique em **Fornecedores de identidade**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configurar serviço single sign-on")

1. Na caixa de diálogo **Fornecedores de identidade** , clique em **Novo**:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configurar serviço single sign-on")


1. Na caixa de diálogo **Fornecedores de identidade** , clique em **SAML2 Update1?**:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configurar serviço single sign-on")


1. Na caixa de diálogo Propriedades de Update1 SAML2, execute os seguintes passos:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configurar serviço single sign-on")


    um. na caixa de texto **nome** , escreva um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No **Campo utilizador** caixa de texto, tipo de **correio eletrónico** ou **user_id**, dependendo de quais o campo é utilizado para identificar exclusivamente os utilizadores na sua implementação ServiceNow. 
    
    > [AZURE.NOTE] Pode configue Azure AD a emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no token de SAML ao aceder ao **ServiceNow > atributos > Single Sign-On** portal clássico do Azure e mapeamento de campo pretendido para o atributo **nameidentifier** secção. O valor armazenado para o atributo seleccionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder o valor armazenado na ServiceNow para o campo introduzido (por exemplo, user_id)

    c. No portal do Azure AD clássico, copie o valor de **ID do fornecedor de identidade** e, em seguida, colá-la a caixa de texto do **URL do fornecedor de identidade** .

    d. No portal do Azure AD clássico, copie o valor de **URL de pedido de autenticação** e, em seguida, colá-la a caixa de texto **do fornecedor de identidade AuthnRequest** .

    "e". No portal do Azure AD clássico, copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto **do fornecedor de identidade SingleLogoutRequest** .

    f. Na caixa de texto **Home page do ServiceNow** , escreva o URL da sua home page instância de ServiceNow.

    > [AZURE.NOTE] Na home page do ServiceNow instância é uma concatenação dos seus **ServieNow inquilino URL** e **/navpage.do** (por exemplo:`https://fabrikam.service-now.com/navpage.do`).
 

    g. No **ID da entidade / emissor** caixa de texto, escreva o URL do seu inquilino ServiceNow.

    h. Na caixa de texto **Audiência URL** , escreva o URL do seu inquilino ServiceNow. 

    posso. Na caixa de texto **Encadernação de protocolo para os IDP SingleLogoutRequest** , escreva **urn: organização de normalização: nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.

    j. Na caixa de texto NameID política, escreva **urn: organização de normalização: nomes: tc: SAML:1.1:nameid-formato: desconhecidas**.

    k. Desmarque a opção **criar uma AuthnContextClass**.

    l. **Método de AuthnContextClassRef**, escreva `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Só é necessário se estiver organização apenas na nuvem. Se estiver a utilizar local ADFS ou MFA para autenticação, em seguida, não deve configurar este valor. 

    m. Na caixa de texto **Relógio prejudicar** , escreva **60**.

    n. Como **Início de sessão único no Script**, selecione **MultiSSO_SAML2_Update1**.

    your. Como **x509 certificado**, selecione o certificado que criou no passo anterior.

    p. Clique em **Submeter**. 



6. No portal do Azure AD clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar serviço single sign-on")

7. Na página de **confirmação de início de sessão única** , clique em **Concluir**.
 
    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar serviço single sign-on")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configuração do Azure AD Single Sign-On para ServiceNow Express

1.  No portal clássico do Azure AD, na página de integração de aplicação **ServiceNow** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão ServiceNow** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar serviço single sign-on")

3.  Na página **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar o URL da aplicação")

    um. na caixa de texto **ServiceNow início de sessão no URL** , escreva o URL utilizado pelos seus utilizadores para o início de sessão à aplicação ServiceNow seguir o padrão de: `https://<instance-name>.service-now.com`.

    b. Na caixa de texto **Emissor URL** , escreva o URL da sua utilizado pelos seus utilizadores para o início de sessão à aplicação ServiceNow seguir o padrão de `https://<instance-name>.service-now.com`.

    c. Clique em **seguinte**

4.  Clique em **configurar manualmente a aplicação para o início de sessão único**, em seguida, clique em **seguinte** e concluir os passos seguintes.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurar o URL da aplicação")

5.  Na página **Configurar serviço single sign-on na ServiceNow** , clique em **Transferir o certificado**, guarde o ficheiro de certificado localmente no seu computador e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar serviço single sign-on")

6. Início de sessão na sua aplicação ServiceNow Express como administrador.

7. No painel de navegação no lado esquerdo, clique em **Single Sign-On**.  

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurar o URL da aplicação")


8. No diálogo **Single Sign-On** , clique no ícone de configuração no canto superior direito e definir as seguintes propriedades:

    ![Configurar o URL da aplicação] (./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurar o URL da aplicação")

    um. Alternar entre **Ativar vários fornecedor SSO** para a direita.

    b. Ativar/desativar a **Ativar o registo para a integração de SSO vários do fornecedor de depuração** para a direita.

    c. Na caixa de texto **o campo no utilizador de tabela que...** , escreva **nomedoutilizador**.


9. No diálogo **Single Sign-On** , clique em **Adicionar novo certificado**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configurar serviço single sign-on")



10. Na caixa de diálogo **Certificados x. 509** , execute os seguintes passos:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configurar serviço single sign-on")

    um. Na caixa de texto **nome** , escreva um nome para a sua configuração (por exemplo: **TestSAML2.0**).

    b. Selecione **ativo**.

    c. Como **Formatar**, selecione **PEM**.

    d. Como **tipo**, selecione o **Certificado de arquivo de fidedignidade**.

    "e". Crie um ficheiro de codificação Base64 a partir do seu certificado transferido.
   
    > [AZURE.NOTE] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).
    
    f. Abra o seu certificado de codificação Base64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto **PEM certificado** .

    g. Clique em **Atualizar**.


11. No diálogo **Single Sign-On** , clique em **Adicionar novo IdP**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configurar serviço single sign-on")


12. Na caixa de diálogo **Adicionar fornecedor de identidade nova** , em **Configurar o fornecedor de identidades**, execute os seguintes passos:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configurar serviço single sign-on")


    um. Na caixa de texto **nome** , escreva um nome para a sua configuração (por exemplo: **SAML 2.0**).

    b. No portal do Azure AD clássico, copie o valor de **ID do fornecedor de identidade** e, em seguida, colá-la a caixa de texto do **URL do fornecedor de identidade** .

    c. No portal do Azure AD clássico, copie o valor de **URL de pedido de autenticação** e, em seguida, colá-la a caixa de texto **do fornecedor de identidade AuthnRequest** .

    d. No portal do Azure AD clássico, copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto **do fornecedor de identidade SingleLogoutRequest** .

    "e". Como **Certificado de fornecedor de identidade**, selecione o certificado que criou no passo anterior.


13. Clique em **Definições avançadas**e, em **Propriedades do fornecedor de identidade adicionais**, execute os seguintes passos:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configurar serviço single sign-on")

    um. Na caixa de texto **Encadernação de protocolo para os IDP SingleLogoutRequest** , escreva **urn: organização de normalização: nomes: tc: SAML:2.0:bindings:HTTP-redirecionar**.

    b. Na caixa de texto **NameID política** , escreva **urn: organização de normalização: nomes: tc: SAML:1.1:nameid-formato: desconhecidas**.    

    c. **Método de AuthnContextClassRef**, escreva **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
    
    d. Desmarque a opção **criar uma AuthnContextClass**.

14. Em **Propriedades do fornecedor de serviços adicionais**, execute os seguintes passos:

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configurar serviço single sign-on")

    um. Na caixa de texto **Home page do ServiceNow** , escreva o URL da sua home page instância de ServiceNow.

    > [AZURE.NOTE] Na home page do ServiceNow instância é uma concatenação dos seus **ServieNow inquilino URL** e **/navpage.do** (por exemplo: `https://fabrikam.service-now.com/navpage.do`).

    b. No **ID da entidade / emissor** caixa de texto, escreva o URL do seu inquilino ServiceNow.

    c. Na caixa de texto **URI de audiência** , escreva o URL do seu inquilino ServiceNow. 

    d. Na caixa de texto **Relógio prejudicar** , escreva **60**.

    "e". No **Campo utilizador** caixa de texto, tipo de **correio eletrónico** ou **user_id**, dependendo de quais o campo é utilizado para identificar exclusivamente os utilizadores na sua implementação ServiceNow.
    
    > [AZURE.NOTE] Pode configue Azure AD a emitir o ID de utilizador do Azure AD (nome principal de utilizador) ou o endereço de e-mail como o identificador exclusivo no token de SAML ao aceder ao **ServiceNow > atributos > Single Sign-On** portal clássico do Azure e mapeamento de campo pretendido para o atributo **nameidentifier** secção. O valor armazenado para o atributo seleccionado no Azure AD (por exemplo, nome principal de utilizador) tem de corresponder o valor armazenado na ServiceNow para o campo introduzido (por exemplo, user_id)

    f. Clique em **Guardar**. 


15. No portal do Azure AD clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configurar serviço single sign-on")

16. Na página de **confirmação de início de sessão única** , clique em **Concluir**.
 
    ![Configurar serviço single sign-on] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Objectivo de nesta secção é como ativar o utilizador aprovisionamento de contas de utilizador do Active Directory para ServiceNow de destaque.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1. No portal de clássico de gestão do Azure, na página de integração de aplicação **ServiceNow** , clique em **Configurar aprovisionamento de utilizador**. 

    ![Aprovisionamento de utilizador] (./media/active-directory-saas-servicenow-tutorial/IC769498.png "Aprovisionamento de utilizador")


2. Na página **introduzir as suas credenciais ServiceNow para ativar o aprovisionamento de utilizador automática** , forneça as seguintes definições de configuração: configurar o aprovisionamento de utilizador 

     um. Na caixa de texto **Nome da instância ServiceNow** , escreva o nome da instância ServiceNow.

     b. Na caixa de texto **Nome de utilizador de administração ServiceNow** , escreva o nome da conta de administrador ServiceNow.

     c. Na caixa de texto **Palavra-passe de administrador de ServiceNow** , escreva a palavra-passe para esta conta.

     d. Clique em **Validar** para confirmar a sua configuração.

     "e". Clique no botão **seguinte** para abrir a página de **passos seguintes** .

     f. Se pretender de aprovisionamento de todos os utilizadores para esta aplicação, selecione "**aprovisionar automaticamente todas as contas de utilizador no diretório para esta aplicação**". 

    ![Próximos passos] (./media/active-directory-saas-servicenow-tutorial/IC698804.png "Próximos passos")

     g. Na página **os passos seguintes** , clique em **concluído** para guardar a configuração.

### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.

![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.
    
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , efetue os seguintes passos:
 
    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   


### <a name="creating-a-servicenow-test-user"></a>Criar um utilizador de teste ServiceNow

Nesta secção, crie um utilizador designado Britta Simon no ServiceNow. Nesta secção, crie um utilizador designado Britta Simon no ServiceNow. Se não sabe como adicionar um utilizador na sua conta ServiceNow ou ServiceNow Express, contacte a equipa de suporte ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a ServiceNow.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon ServiceNow, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **ServiceNow**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203] 

1. Na lista de todos os utilizadores, selecione **Britta Simon**.

2. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


### <a name="testing-single-sign-on"></a>Testar o início de sessão único

O objectivo desta secção é testar a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico ServiceNow no painel do Access, pode deve obter automaticamente assinado-on à aplicação ServiceNow.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
