<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com PolicyStat | Microsoft Azure" 
    description="Saiba como utilizar PolicyStat com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração com o Azure Active Directory com PolicyStat
  
Este tutorial objectivo Mostrar a integração do Azure e PolicyStat.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino PolicyStat
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a PolicyStat poderão para o início de sessão único para a aplicação no site da sua empresa PolicyStat (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para PolicyStat
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-policystat-tutorial/IC808662.png "Cenário")
##<a name="enabling-the-application-integration-for-policystat"></a>Ativar a integração da aplicação para PolicyStat
  
Objectivo de nesta secção é como ativar a integração da aplicação para PolicyStat de destaque.

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>Para ativar a integração da aplicação para PolicyStat, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-policystat-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-policystat-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-policystat-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-policystat-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **PolicyStat**.

    ![Galeria de aplicação] (./media/active-directory-saas-policystat-tutorial/IC808627.png "Galeria de aplicação")

7.  No painel de resultados, selecione **PolicyStat**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![PolicyStat] (./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para PolicyStat com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
A aplicação PolicyStat espera as declarações SAML num formato específico, requer que adicione mapeamentos de atributos personalizados à configuração do **saml token atributos** .  
A captura de ecrã seguinte mostra um exemplo para esta situação.

![Atributos] (./media/active-directory-saas-policystat-tutorial/IC808628.png "Atributos")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **PolicyStat** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores a iniciar sessão PolicyStat** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurar o Single Sign-On")

3.  Na página **Configurar definições de aplicação** , na caixa de texto **URL no início de sessão** , escreva o URL utilizado pelos seus utilizadores para o início de sessão à aplicação PolicyStat de URL (por exemplo: *"https://demo-azure.policystat.com"*) e, em seguida, clique em **seguinte**.

    ![Configurar definições de aplicação] (./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurar definições de aplicação")

4.  Na página **Configurar serviço single sign-on na PolicyStat** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa PolicyStat como administrador.

6.  Clique no separador **administrador** e, em seguida, clique em **Configuração de início de sessão único** no painel de navegação do lado esquerdo.

    ![Menu Administrador] (./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu Administrador")

7.  Na secção **configuração** , selecione **Ativar única integração de início de sessão**.

    ![Configuração de início de sessão único] (./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuração de início de sessão único")

8.  Clique em **Configurar atributos**e, em seguida, na secção **Configurar atributos** , execute os seguintes passos:

    ![Configuração de início de sessão único] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuração de início de sessão único")

    1.  Na caixa de texto **Atributo Username** , escreva **uid**.
    2.  Na caixa de texto **Nome próprio atributo** , escreva **NomePróprio**.
    3.  Na caixa de texto **Último nome atributo** , escreva **Apelido**.
    4.  Na caixa de **Correio eletrónico atributo** texto, escreva **emailaddress**.
    5.  Clique em **Guardar alterações**.

9.  Clique em **Seu IDP metadados**e, em seguida, na secção do **Seu IDP metadados** , execute os seguintes passos:

    ![Configuração de início de sessão único] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuração de início de sessão único")

    1.  Abra o seu ficheiro transferido metadados, copie o conteúdo e, em seguida, colá-la a caixa de texto do **Seu metadados de fornecedor de identidade**
    2.  Clique em **Guardar alterações**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurar o Single Sign-On")

11. 12. No menu no canto superior, clique em **atributos** para abrir a caixa de diálogo **SAML Token atributos** .

    ![Atributos] (./media/active-directory-saas-policystat-tutorial/IC795920.png "Atributos")

13. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    ![Atributos] (./media/active-directory-saas-policystat-tutorial/IC804823.png "Atributos")

    1.  Clique em **Adicionar utilizador atributo**.
    2.  Na caixa de texto **Nome do atributo** , escreva **uid**.
    3.  Na caixa de texto **Valor do atributo** , selecione **ExtractMailPrefix()**.
    4.  A partir da lista de **correio** , selecione **User.mail**.
    5.  Clique em **concluído**.
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no PolicyStat, tem de ser aprovisionadas para PolicyStat.  
PolicyStat suporta apenas nos aprovisionamento de utilizador do tempo. Isto significa que, não necessita de adicionar os utilizadores manualmente PolicyStat.  
Os utilizadores serão adicionados automaticamente no respetivo primeiro início de sessão através de início de sessão único no.

>[AZURE.NOTE]Pode utilizar quaisquer outros PolicyStat utilizador conta ferramentas para a criação ou APIs fornecida pela PolicyStat às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>Para atribuir utilizadores a PolicyStat, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **PolicyStat **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-policystat-tutorial/IC808636.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-policystat-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).