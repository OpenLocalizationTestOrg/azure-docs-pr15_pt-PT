<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Salesforce Sandbox | Microsoft Azure"
    description="Saiba como utilizar Salesforce Sandbox com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/28/2016" 
    ms.author="jeedes" />


#<a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Azure Active Directory com Salesforce Sandbox
>[AZURE.TIP]Comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=521878).
  
Este tutorial objectivo Mostrar a integração do Azure e Salesforce Sandbox.  
Areeiros dar-lhe a capacidade de criar várias cópias da sua organização no ambientes separados para uma variedade de efeitos, tais como desenvolvimento, testar e formação, sem comprometer os dados e as aplicações na sua organização de produção Salesforce.  
Para obter mais detalhes, consulte o artigo [Descrição geral de Sandbox](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Uma sandbox no Salesforce.com
  
Se ainda não tenha uma sandbox válida no Salesforce.com, terá de contactar a equipa de vendas.
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Salesforce Sandbox
2.  Configurar o início de sessão único
3.  Ativar o seu domínio
4.  Configurar o aprovisionamento de utilizador
5.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Cenário")
##<a name="enabling-the-application-integration-for-salesforce-sandbox"></a>Ativar a integração da aplicação para Salesforce Sandbox
  
O objectivo desta secção é destacar como ativar a integração da aplicação para Salesforce sandbox.

###<a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>Para ativar a integração da aplicação para Salesforce sandbox, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Aplicações")

4.  Para abrir a **Galeria de aplicação**, clique em **Adicionar uma aplicação**e, em seguida, clique em **Adicionar uma aplicação para a minha organização utilizar**.

    ![o que pretende fazer?] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "o que pretende fazer?")

5.  Na **caixa de pesquisa**, escreva **Salesforce Sandbox**.

    ![Galeria de aplicação] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galeria de aplicação")

6.  No painel de resultados, selecione **Salesforce Sandbox**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Salesforce Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Salesforce com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração do **Salesforce Sandbox** aplicação, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Sandbox do Salesforce** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Salesforce Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **URL no início de sessão** , escreva o URL da sua utilizando o padrão seguinte `http://company.my.salesforce.com`e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurar o URL da aplicação")

4. Se já tiver configurado serviço single sign-on para outra instância do Salesforce Sandbox no seu diretório, em seguida, também tem de configurar o **identificador** de ter o mesmo valor que **Inicie sessão no URL**. O campo de **identificador** pode ser encontrado ao selecionar a caixa de verificação **Mostrar definições avançadas** na página **Configurar o URL da aplicação** da caixa de diálogo.

4.  Na página **Configurar serviço single sign-on em Sandbox do Salesforce** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão na sua sandbox Salesforce como administrador.

6.  No menu no canto superior, clique em **configuração**.

    ![Programa de configuração] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Programa de configuração")

7.  No painel de navegação à esquerda, clique em **Controlos de segurança**e, em seguida, clique em **Definições de Single Sign-On**.

    ![Definições de início de sessão únicos] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Definições de início de sessão únicos")

8.  Na secção definições de Single Sign-On, efetue os seguintes passos:

    ![Definições de início de sessão únicos] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Definições de início de sessão únicos")

    um.  Selecione **SAML ativado**.
    
    b.  Clique em **Novo**.

9.  Na secção SAML única definições Sign-On, efetue os seguintes passos:

    ![SAML única definições Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML única definições Sign-On")

    um.  Na caixa de texto nome, escreva o nome da configuração do (por exemplo: *SPSSOWAAD\_teste*).
    
    b.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Sandbox do Salesforce** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor** .
    
    c.  Na caixa de texto **O Id da entidade** , escreva **https://test.salesforce.com** se esta é a primeira instância do Salesforce Sandbox que está a adicionar ao seu diretório. Se já tiver adicionado uma instância do Salesforce Sandbox, em seguida, para o tipo de **ID da entidade** no **Início de sessão no URL**, que devem ser neste formato:`http://company.my.salesforce.com`
    
    d.  Clique em **Procurar** para carregar o certificado transferido.
    
    "e".  Como **O tipo de identidade do SAML**, selecione **afirmação contém o ID de Federação do objeto do utilizador**.
    
    f.  Como **Localização de identidade do SAML**, selecione a **identidade está no elemento de NameIdentifier da instrução assunto**.
    
    g.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em Sandbox do Salesforce** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de identidade** .
    
    h.  SFDC não suporta SAML termine a sessão.  Como solução, cole 'https://login.windows.net/common/wsfederation?wa=wsignout1.0'-lo para a caixa de texto do **URL de termine a sessão do fornecedor de identidade** .
    
    posso.  Como **Serviço fornecedor iniciados por pedir encadernação**, selecione **HTTP POST**.
    
    j. Clique em **Guardar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurar o Single Sign-On")

##<a name="enabling-your-domain"></a>Ativar o seu domínio
  
Esta secção assume que já criou um domínio.  
Para obter mais detalhes, consulte [Definir o nome do domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###<a name="to-enable-your-domain-perform-the-following-steps"></a>Para ativar o seu domínio, execute os seguintes passos:

1.  No painel de navegação esquerdo, clique em **Gestão de domínio**e, em seguida, clique em **meu domínio.**

    ![Meu domínio] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Meu domínio")

    >[AZURE.NOTE]Certifique-se de que o seu domínio ter sido configurado corretamente.

2.  Na secção **Definições da página de início de sessão** , clique em **Editar**, em seguida, como de **Serviço de autenticação**, selecione o nome SAML único início de sessão da definição da secção anterior e finalmente clique em **Guardar**.

    ![Meu domínio] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Meu domínio")
  
Assim que tiver um domínio configurado, os utilizadores devem utilizar o URL do domínio para o início de sessão para a equipa de vendas sandbox.  
Para obter o valor do URL, clique no perfil SSO que criou na secção anterior.
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
O objectivo desta secção é destacar como ativar o utilizador aprovisionamento de contas de utilizador do Active Directory para Salesforce Sandbox.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  No portal do Salesforce, na barra de navegação superior, selecione o seu nome para expandir o menu de utilizador:

    ![Minhas definições] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Minhas definições")

2.  O menu de utilizador, selecione **Definições do meu** para abrir a página de **Definições do meu** .

3.  No painel esquerdo, clique em **pessoal** para expandir a secção pessoal e, em seguida, clique em **Repor a minha Token de segurança**:

    ![Minhas definições] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Minhas definições")

4.  Na página **Repor a minha Token de segurança** , clique em **Repor o Token de segurança** para pedir um e-mail que contém o token de segurança Salesforce.com.

    ![Novo Token] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Novo Token")

5.  Verifique a sua caixa de entrada de e-mail para um e-mail a partir do Salesforce.com com "**confirmação de segurança Salesforce.com**" como assunto.

6.  Reveja esta mensagem de correio electrónico e copie o valor de token de segurança.

7.  No portal clássico Azure, na página de integração do **salesforce Sandbox** aplicação, clique em **Configurar aprovisionamento de utilizador** para abrir a caixa de diálogo **Configurar aprovisionamento de utilizador** .

    ![Configurar o aprovisionamento de utilizador] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configurar o aprovisionamento de utilizador")

8.  Na página **introduzir as suas credenciais do Salesforce Sandbox para ativar o aprovisionamento de utilizador automática** , forneça as seguintes definições de configuração:

    ![Salesforce Sandbox] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    um.  Na caixa de texto **Nome de utilizador do Salesforce Sandbox administrador** , escreva um nome de conta do Salesforce sandbox que tem o perfil de **Administrador do sistema** no Salesforce.com atribuídas.

    b.  Na caixa de texto **Palavra-passe de administrador do Salesforce Sandbox** , escreva a palavra-passe para esta conta.

    c.  Na caixa de texto **Token de segurança do utilizador** , cole o valor de token de segurança.

    d.  Clique em **Validar** para confirmar a sua configuração.

    "e".  Clique no botão **seguinte** para abrir a página de **confirmação** .

9.  Na página de **confirmação** , clique em **concluído** para guardar a configuração.
##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>Para atribuir utilizadores a equipa de vendas Sandbox, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração do **Salesforce Sandbox **aplicação, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Sim")
  
Agora deverá aguardar 10 minutos e certifique-se de que a conta foi sincronizada para Salesforce Sandbox.
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](https://msdn.microsoft.com/library/dn308586).
