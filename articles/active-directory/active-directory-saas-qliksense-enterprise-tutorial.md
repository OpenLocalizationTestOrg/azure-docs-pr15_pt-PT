<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Qlik sentido Enterprise | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Qlik sentido Enterprise."
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
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: Integração do Azure Active Directory com Qlik sentido Enterprise

Neste tutorial, aprende integrar Qlik sentido empresa com o Azure Active Directory (Azure AD).

Integrar o Qlik sentido empresa com o Azure AD fornece os seguintes benefícios:

- Pode controlar no Azure AD quem tem acesso ao Qlik sentido Enterprise
- Pode ativar os seus utilizadores para automaticamente obter assinado-on para Qlik sentido Enterprise (Single Sign-On) com as respetivas contas do Azure AD
- Pode gerir as contas numa localização central - portal clássica do Azure

Se quiser saber mais detalhes sobre a integração de aplicação SaaS com Azure AD, consulte o artigo [o que é o acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Qlik sentido Enterprise, tem os seguintes itens:

- Uma subscrição do Azure AD
- Um Qlik sentido Enterprise sessão único na subscrição activada


> [AZURE.NOTE] Para testar os passos neste tutorial, recomendamos que não utilizar um ambiente de produção.


Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não deve utilizar o seu ambiente de produção, a menos que este procedimento é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um mês de uma avaliação [aqui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, testá-lo Azure AD serviço single sign-on num ambiente de teste.

O cenário descrito neste tutorial tem dois blocos modulares principais:

1. Adicionar Qlik sentido empresa a partir da Galeria
2. Configurar e testar o Azure AD único início de sessão


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionar Qlik sentido empresa a partir da Galeria
Para configurar a integração da empresa que sentido Qlik no Azure AD, é necessário adicionar Qlik sentido Enterprise a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Qlik sentido empresa a partir da galeria, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory][1]
2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações][2]

4. Clique em **Adicionar** na parte inferior da página.

    ![Aplicações][3]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Aplicações][4]

6. Na caixa de pesquisa, escreva **Qlik sentido Enterprise**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. No painel de resultados, selecione **Qlik sentido Enterprise**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD único início de sessão
Nesta secção, configurar e testar Azure AD serviço single sign-on com Qlik sentido empresa com base num utilizador teste denominado "Britta Simon".

Para single sign-on para trabalhar, Azure AD precisa de saber o que o utilizador homólogo Qlik sentido empresa é um utilizador no Azure AD. Por outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Qlik sentido Enterprise tem de ser estabelecida.

Relação esta ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** no Qlik sentido Enterprise.

Para configurar e testar Azure AD serviço single sign-on com Qlik sentido Enterprise, é necessário concluir os blocos modulares seguintes:

1. **[Configurar Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um Azure AD testar utilizador](#creating-an-azure-ad-test-user)** - para testar Azure AD serviço single sign-on com Britta Simon.
3. **[Criar uma empresa de sentido Qlik testar utilizador](#creating-a-qliksense-enterprise-test-user)** - ter homólogo para Britta Simon no Qlik sentido Enterprise que está ligado à representação do Azure AD da lhe.
4. **[Atribuir Azure AD testar utilizador](#assigning-the-azure-ad-test-user)** - ativar Britta Simon utilizar Azure AD serviço single sign-on.
5. **[Testes Single Sign-On](#testing-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurar o Azure AD serviço single sign-on

Nesta secção, ativar o Azure AD serviço single sign-on no portal do clássico e configurar o início de sessão único na sua aplicação Qlik sentido Enterprise.


**Para configurar Azure AD serviço single sign-on com Qlik sentido Enterprise, execute os seguintes passos:**

1. No portal do clássico, na página de integração de aplicação **Qlik sentido Enterprise** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign-On** .
     
    ![Configurar o Single Sign-On][6] 

2. Na página **como pretende utilizadores a iniciar sessão Qlik sentido Enterprise** , selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. Na página de diálogo **Configurar definições de aplicação** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão na sua aplicação de Qlik sentido Enterprise utilizando o seguinte padrão: **https://\<Qlik sentido totalmente Qualifed Hostname\>: 443 / < Proxy de prefixo\>/samlauthn/**.
    
    > [AZURE.NOTE] Tenha em atenção a barra invertida final no final deste URI.  É necessária.

    b. Clique em **seguinte**
 
4. Na página **Configurar serviço single sign-on na Qlik sentido Enterprise** , execute os seguintes passos:

    ![Configurar o Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    um. Clique em **transferir metadados**e, em seguida, guarde o ficheiro no seu computador.  Estar preparado para editar o ficheiro de metadados antes de a carregar para o servidor de Qlik sentido.

    b. Clique em **seguinte**.

5. Prepare o ficheiro de Federação metadados XML para que pode carregar que para o servidor de Qlik sentido.

    > [AZURE.NOTE] Antes de carregar os metadados IdP no servidor Qlik sentido, o ficheiro tem de ser editado para remover informações para assegurar o funcionamento entre Azure AD e server Qlik sentido.

    ![QlikSense][qs24]

    um. Abra o ficheiro de FederationMetaData.xml transferido a partir do Azure num editor de texto.

    b. Procure o valor **RoleDescriptor**.  Será quatro entradas (dois pares de abertura e de fecho códigos de elemento).

    c. Elimine entre as marcas de RoleDescriptor e todas as informações do ficheiro.

    d. Guarde o ficheiro e mantê-lo, para utilização posterior neste documento.

6. Navegue para o Qlik sentido Qlik Management Console (QMC) como um utilizador pode criar configurações de virtual proxy.

7. Na QMC, clique no item de menu Virtual Proxy.

    ![QlikSense][qs6] 

8. Na parte inferior do ecrã, clique no botão novo de criar.

    ![QlikSense][qs7]

9. É apresentado o ecrã de editar proxy Virtual.  No lado direito do ecrã é um menu para tornar as opções de configuração visíveis.

    ![QlikSense][qs9]

10. Com a opção de menu de identificação selecionada, introduza as informações de identificação para a configuração do Azure virtual proxy.

    ![QlikSense][qs8]  
    
    um. Descrição o campo é um nome amigável para a configuração do virtual proxy.  Introduza um valor para obter uma descrição.
    
    b. O campo prefixo identifica o ponto final virtual proxy para ligar à Qlik coerente com Azure AD Single Sign-On.  Introduza um nome exclusivo prefixo para este proxy virtual.

    c. Tempo limite da entrada sessão (minutos) é o tempo limite para ligações através deste proxy virtual.

    d. O nome de cabeçalho de cookie sessão é o nome de cookie armazenar o identificador da sessão para a sessão Qlik sentido que um utilizador recebe após autenticação com êxito.  Este nome tem de ser exclusivo.

11. Clique na opção de menu de autenticação para o tornar visível.  É apresentado o ecrã de autenticação.

    ![QlikSense][qs10]

    um. **Modo de acesso anónimo** pendente determina se os utilizadores anónimos podem aceder Qlik sentido através de um virtual proxy.  A opção predefinido não é nenhum utilizador anónimo.

    b. **Método de autenticação** pendente determina que o esquema de autenticação de proxy virtual irá utilizar.  Selecione SAML a partir da lista pendente.  Mais opções aparecerá como resultado.

    c. No **campo URI de anfitrião SAML**, introdução que dos utilizadores hostname irão introduzir para aceder a Qlik sentido através este proxy virtual SAML.  O nome do anfitrião é o uri do servidor Qlik sentido.

    d. Em **ID da entidade SAML**, introduza o mesmo valor introduzido para o campo URI SAML anfitrião.

    "e". Os **metadados SAML IdP** é o ficheiro editado anterior na secção **Editar metadados de Federação a partir do Azure AD configuração** .  **Antes de carregar os metadados IdP, o ficheiro tem de ser editado** para remover informações para assegurar o funcionamento entre Azure AD e server Qlik sentido.  **Consulte as instruções acima se o ficheiro ainda tem de ser editado.**  Se tiver sido editado o ficheiro, clique no botão Procurar e selecione o ficheiro de metadados editado para a carregar para a configuração do virtual proxy.

    f. Introduza o nome do atributo ou referência de esquema para o atributo SAML que representa o **ID de utilizador** Azure AD irá enviar para o servidor de Qlik sentido.  Informações de referência de esquema estão disponíveis na configuração da aplicação Azure ecrãs publicação.  Para utilizar o atributo nome, **introduza http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

    g. Introduza o valor para o **diretório de utilizador** que serão anexados a utilizadores quando autenticam ao servidor Qlik sentido através do Azure AD.  Valores tem de ser escritos **entre parênteses Retos**.  Para utilizar um atributo enviado a declaração de Azure AD SAML, introduza o nome do atributo este texto caixa **sem** entre parênteses Retos.

    h. O **algoritmo de assinatura SAML** define o serviço fornecedor (no servidor Qlik sentido neste caso) assinatura do certificado para a configuração do virtual proxy.  Se o servidor de Qlik sentido utiliza um certificado fidedigno gerado utilizando o Microsoft RSA avançada e AES fornecedor de criptografia, altere o algoritmo de assinatura SAML para **SHA 256**.

    posso. A secção de mapeamento de atributo SAML permite atributos adicionais, como grupos para ser enviada para Qlik sentido para utilização em regras de segurança.

12. Clique nas opção de menu para tornar visível de balanceamento de carga.  É apresentado o ecrã de balanceamento de carga.

    ![QlikSense][qs11]

13. Clique no botão de nó de servidor novo adicionar, nó do motor de selecionar ou nós Qlik sentido enviar sessões para fins de balanceamento de carga e clique no botão Adicionar.

    ![QlikSense][qs12]

14. Clique na opção de menu Avançadas para o tornar visível. É apresentado o ecrã avançado.

    ![QlikSense][qs13]

    um. A lista de branco anfitrião identifica nomes de anfitriões são aceites quando se liga ao servidor Qlik sentido.  **Introduza o nome do anfitrião os utilizadores irão especificar quando se liga ao servidor Qlik sentido.** O nome do anfitrião é o mesmo valor que o uri de anfitrião SAML sem o https://.

15. Clique no botão Aplicar.

    ![QlikSense][qs14]

16. Clique em OK para aceitar a mensagem de aviso que indica proxies ligados para o proxy virtual vai ser reiniciados.

    ![QlikSense][qs15]

17. No lado direito do ecrã, aparece o menu de itens associado.  Clique na opção de menu Proxies.

    ![QlikSense][qs16]

18. É apresentado o ecrã de proxy.  Clique no botão de ligação na parte inferior para ligar um proxy para o proxy virtual.

    ![QlikSense][qs17]

19. Selecione o nó do proxy que suportam esta ligação virtual proxy e clique no botão de ligação.  Depois de criar uma ligação, o proxy estará listado em proxies associados.

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. Depois de cinco a dez segundos será apresentada a mensagem de atualização QMC.  Clique no botão Atualizar QMC.

    ![QlikSense][qs20]

21. Quando atualiza o QMC, clique no item de menu proxies Virtual. A nova entrada de virtual proxy do SAML está listada na tabela no ecrã.  Clique único a entrada do virtual proxy.

    ![QlikSense][qs51]

22. Na parte inferior do ecrã, vai ativar o botão de metadados do SP transferir.  Clique no botão de metadados do SP transferir para guardar os metadados a um ficheiro.

    ![QlikSense][qs52]

23. Abra o ficheiro de metadados do sp.  Observe a entrada de **ID da entidade** e a entrada de **AssertionConsumerService** .  Estes valores são equivalentes para o **identificador** e a **Iniciar sessão no URL** da Azure AD configuração da aplicação. Se não correspondentes deve substituí-los no Assistente de configuração do Azure AD aplicação.

    ![QlikSense][qs53]

24. No portal do clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **seguinte**.
    
    ![Azure AD Single Sign-On][10]

25. Na página de **confirmação de início de sessão única** , clique em **Concluir**.  
 
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Criação de um utilizador de teste do Azure AD
Nesta secção, crie um utilizador de teste no portal do clássico denominado Britta Simon.


![Criar utilizador do Azure AD][20]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No **portal clássica Azure**, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3. Para apresentar a lista de utilizadores, no menu no canto superior, clique em **utilizadores**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Para abrir a caixa de diálogo **Adicionar utilizador** , na barra de ferramentas na parte inferior, clique em **Adicionar utilizador**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. Na página de diálogo **diga-nos acerca deste utilizador** , execute os seguintes passos:  ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    um. Como o tipo de utilizador, selecione novo utilizador na sua organização.

    b. Na **caixa de texto**o nome de utilizador, escreva **BrittaSimon**.

    c. Clique em **seguinte**.

6.  Na página de diálogo de **Perfil de utilizador** , efetue os seguintes passos: ![criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    um. Na caixa de texto **nome próprio** , escreva **Britta**.  

    b. Na **Apelido** caixa de texto, tipo, **Simon**.

    c. Na caixa de texto **Nome a apresentar** , escreva **Britta Simon**.

    d. Na lista de **funções** , selecione o **utilizador**.

    "e". Clique em **seguinte**.

7. Na página de diálogo **obter a palavra-passe temporária** , clique em **Criar**.

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. Na página de diálogo **obter a palavra-passe temporária** , execute os seguintes passos:

    ![Criação de um utilizador de teste do Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    um. Escreva o valor da **Nova palavra-passe**.

    b. Clique em **concluído**.   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Criação de um utilizador de teste Qlik sentido Enterprise

Nesta secção, crie um utilizador denominado Britta Simon no Qlik sentido Enterprise. Consulte trabalhe com a equipa de suporte Qlik sentido Enterprise para adicionar os utilizadores da plataforma Qlik sentido Enterprise.


### <a name="assigning-the-azure-ad-test-user"></a>Atribuir ao utilizador de teste do Azure AD

Nesta secção, ativar Britta Simon utilizar Azure serviço single sign-on ao conceder o acesso a Qlik sentido Enterprise.

![Atribuir utilizador][200] 

**Para atribuir Britta Simon Qlik sentido Enterprise, execute os seguintes passos:**

1. No portal de clássico, para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **Qlik sentido Enterprise**.

    ![Configurar o Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. No menu no canto superior, clique em **utilizadores**.

    ![Atribuir utilizador][203]

4. Na lista de utilizadores, selecione **Britta Simon**.

5. Na barra de ferramentas na parte inferior, clique em **atribuir**.

    ![Atribuir utilizador][205]


## <a name="testing-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testá-lo a Azure AD única início de sessão configuração utilizando o painel de acesso.

Quando clica no mosaico Qlik sentido Enterprise no painel do Access, pode deve obter automaticamente assinado-on à aplicação Qlik sentido Enterprise.


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar SaaS aplicações com o Azure Active Directory](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicação e único início de sessão com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png