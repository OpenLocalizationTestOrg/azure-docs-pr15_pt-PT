<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com DIATRABALHO | Microsoft Azure" 
    description="Saiba como utilizar DIATRABALHO com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integração do Azure Active Directory com DIATRABALHO
  
Este tutorial objectivo Mostrar a integração do Azure e DIATRABALHO. O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino no dia de trabalho
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o dia de trabalho
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Configurar o aprovisionamento de utilizador

![Cenário] (./media/active-directory-saas-workday-tutorial/IC782919.png "Cenário")

##<a name="enabling-the-application-integration-for-workday"></a>Ativar a integração de aplicação para o dia de trabalho
  
O objectivo desta secção é destacar como ativar a integração de aplicação para a equipa de vendas.

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Para ativar a integração da aplicação para DIATRABALHO, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-workday-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-workday-tutorial/IC700994.png "Aplicações")

4.  Para abrir a **Galeria de aplicação**, clique em **Adicionar uma aplicação**e, em seguida, clique em **Adicionar uma aplicação para a minha organização utilizar**.

    ![o que pretende fazer?] (./media/active-directory-saas-workday-tutorial/IC700995.png "o que pretende fazer?")

5.  Na **caixa de pesquisa**, escreva **DIATRABALHO**.

    ![DIATRABALHO] (./media/active-directory-saas-workday-tutorial/IC701021.png "DIATRABALHO")

6.  No painel de resultados, selecione **DIATRABALHO**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![DIATRABALHO] (./media/active-directory-saas-workday-tutorial/IC701022.png "DIATRABALHO")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para DIATRABALHO com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  Na página de integração de aplicação **DIATRABALHO** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-workday-tutorial/IC782920.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão dia de trabalho** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-workday-tutorial/IC782921.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , execute os seguintes passos e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-workday-tutorial/IC782957.png "Configurar o URL da aplicação")

    um. Na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para iniciar sessão no DIATRABALHO utilizando o padrão seguinte:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b.  Na caixa de texto **DIATRABALHO resposta URL** , escreva o URL de resposta DIATRABALHO utilizando o padrão seguinte:`https://impl.workday.com/<tenant>/login-saml.htmld`

    >[AZURE.NOTE]O URL da sua resposta tem de ter um subdomínio (por exemplo: www, wd2, wd3, wd3 impl, wd5, wd5 impl). 
    >Utilizar algo parecido com "*http://www.myworkday.com*" funciona mas não "*http://myworkday.com*". 
 
4.  Na página **Configurar serviço single sign-on no dia de trabalho** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-workday-tutorial/IC782922.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa DIATRABALHO como administrador.

6.  Aceda a **Menu \> estar**.

    ![Estar] (./media/active-directory-saas-workday-tutorial/IC782923.png "Estar")

7.  Ir para a **Administração de conta**.

    ![Administração da conta] (./media/active-directory-saas-workday-tutorial/IC782924.png "Administração da conta")

8.  Vá para **Editar o programa de configuração do inquilino – segurança**.

    ![Editar a segurança de inquilino] (./media/active-directory-saas-workday-tutorial/IC782925.png "Editar a segurança de inquilino")

9.  Na secção de **Redirecionamento de URLs** , execute os seguintes passos:

    ![URLs de redirecionamento] (./media/active-directory-saas-workday-tutorial/IC7829581.png "URLs de redirecionamento")

    um. Clique em **Adicionar linha**.

    b. Na caixa de texto de **Início de sessão redirecionar URL** e a caixa de texto **Mobile redirecionar URL** , escreva o **URL de inquilino DIATRABALHO** que introduziu na página **Configurar o URL da aplicação** do portal do Azure clássica.
    
    c. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na DIATRABALHO** , copie o **URL do serviço Sign-Out único**e, em seguida, colá-la a caixa de texto do **URL de redirecionar termine a sessão** .

    d.  Na caixa de texto do **ambiente** , escreva o nome do ambiente.  


    >[AZURE.NOTE] O valor do atributo ambiente está associado ao valor do URL inquilino:
    >
    >-   Se o nome de domínio do inquilino DIATRABALHO URL é iniciado com impl (por exemplo: *https://impl.workday.com/\<inquilino\>/login-saml2.htmld*), o atributo **ambiente** tem de estar definido para implementação.
    >-   Se o nome do domínio for iniciado com outra coisa, terá de contactar DIATRABALHO para obter o valor de **ambiente** correspondente.

10. Na secção **Configuração SAML** , execute os seguintes passos:

    ![Programa de configuração do SAML] (./media/active-directory-saas-workday-tutorial/IC782926.png "Programa de configuração do SAML")

    um.  Selecione **Ativar a autenticação do SAML**.

    b.  Clique em **Adicionar linha**.

11. Na secção fornecedores de identidade do SAML, execute os seguintes passos:

    ![Fornecedores de identidade do SAML] (./media/active-directory-saas-workday-tutorial/IC7829271.png "Fornecedores de identidade do SAML")

    um. Na caixa de texto nome do fornecedor de identidade, escreva um nome de fornecedor (por exemplo: *SPInitiatedSSO*).

    b. No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na DIATRABALHO** , copie o valor de **ID do fornecedor de identidade** e, em seguida, colá-la a caixa de texto **emissor** .

    c. Selecione **Ativar o dia a dia Initialted termine a sessão**.

    d. No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na DIATRABALHO** , copie o **URL do serviço Sign-Out único** valor e, em seguida, colá-la a caixa de texto do **URL de pedir termine a sessão** .


    "e". Clique em **Certificado de chave pública fornecedor de identidade**e, em seguida, clique em **Criar**. 

    ![Criar] (./media/active-directory-saas-workday-tutorial/IC782928.png "Criar")

    f. Clique em **Criar x509 chave pública**. 
        
    ![Criar] (./media/active-directory-saas-workday-tutorial/IC782929.png "Criar")


1. Na secção **Chave pública vista x509** , execute os seguintes passos: 

    ![Chave pública vista x509] (./media/active-directory-saas-workday-tutorial/IC782930.png "Chave pública vista x509") 

    um. Na caixa de texto **nome** , escreva um nome para o seu certificado (por exemplo: *EPI\_SP*).
        
    b. Na **Válido de** caixa de texto, escreva válido, a partir do valor do atributo do seu certificado.
    
    c.  Na **Válido para** caixa de texto, escreva válido para o valor do atributo do seu certificado.
        
    >[AZURE.NOTE] Pode obter válido de data e a válida até à data a partir do certificado transferido fazendo duplo clique na mesma. As datas são listadas no separador **Detalhes** .

    d. Crie um ficheiro de **Base-64 codificado** a partir do seu certificado transferido.  

    >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    "e".  Abra o seu certificado codificado base-64 no bloco de notas e, em seguida, copie o conteúdo da mesma.
    
    f.  Na caixa de texto **certificado** , cole o conteúdo da sua área de transferência.
    
    g.  Clique em **OK**.

12.  Execute os seguintes passos: 

    ![Configuração de SSO] (./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuração de SSO")

    um.  Ativar o **x509 par de chave privada**.

    b.  Na caixa de texto **ID do fornecedor de serviço** , escreva **http://www.workday.com**.

    c.  Selecione **Ativar SP iniciados por SAML autenticação**.

    d.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na DIATRABALHO** , copie o valor **Único início de sessão URL do serviço de** e, em seguida, colá-la a caixa de texto do **URL do serviço de SSO IdP** .
     
    "e". Selecione **não esvaziar a pedido de autenticação iniciados por SP**.

    f. **Pedido de assinatura método de autenticação de**, selecione **SHA256**. 
        
    ![Método de assinatura do pedido de autenticação] (./media/active-directory-saas-workday-tutorial/IC782932.png "Método de assinatura do pedido de autenticação") 
 
    g. Clique em **OK**. 
        
    ![OK] (./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. No portal clássico Azure, na página **Configurar serviço single sign-on no dia de trabalho** , clique em **seguinte**. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-workday-tutorial/IC782934.png "Configurar serviço single sign-on")

13. Na página de **confirmação de início de sessão única** , clique em **Concluir**. 

    ![Configurar serviço single sign-on] (./media/active-directory-saas-workday-tutorial/IC782935111.png "Configurar serviço single sign-on")



##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para obter um utilizador de teste aprovisionado para o dia de trabalho, tem de contactar a equipa de suporte do dia de trabalho.  
A equipa de suporte DIATRABALHO irá criar o utilizador por si.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-workday-perform-the-following-steps"></a>Para atribuir utilizadores a DIATRABALHO, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **DIATRABALHO **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-workday-tutorial/IC782935.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-workday-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).