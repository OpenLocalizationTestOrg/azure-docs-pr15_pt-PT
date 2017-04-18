<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Lynda.com | Microsoft Azure" 
    description="Saiba como utilizar Lynda.com com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Tutorial: Integração do Azure Active Directory com Lynda.com
  
Este tutorial objectivo Mostrar a integração do Azure e Lynda.com.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um inquilino Lynda.com
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a Lynda.com poderão para o início de sessão único para a aplicação no site da sua empresa Lynda.com (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Lynda.com
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-lynda-tutorial/IC781046.png "Cenário")
##<a name="enabling-the-application-integration-for-lyndacom"></a>Ativar a integração da aplicação para Lynda.com
  
Objectivo de nesta secção é como ativar a integração da aplicação para Lynda.com de destaque.

###<a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Para ativar a integração da aplicação para Lynda.com, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-lynda-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-lynda-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-lynda-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-lynda-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Lynda.com**.

    ![Galeria de aplicação] (./media/active-directory-saas-lynda-tutorial/IC777524.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Lynda.com**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Lynda.com] (./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Lynda.com com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.

>[AZURE.IMPORTANT]Para poder configurar o início de sessão único no seu inquilino Lynda.com, tem primeiro contactar o suporte técnico Lynda.com para aceder esta funcionalidade activada.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Lynda.com** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777526.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão Lynda.com** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777527.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Lynda.com URL início de sessão** , escreva o seu URL do inquilino Lynda.com (por exemplo: *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*) e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na Lynda.com** , para transferir os seus metadados de, clique em **transferir metadados**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777529.png "Configurar serviço single sign-on")

5.  Envie o ficheiro de metadados transferido para a equipa de suporte Lynda.com. A equipa de suporte Lynda.com é que a configuração de início de sessão para si.

6.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777530.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Não existe nenhum item de ação que pode configurar Lynda.com do aprovisionamento de utilizador.  
Quando um utilizador atribuído tenta que inicie sessão no Lynda.com utilizando o painel de acesso, Lynda.com verifica se o utilizador existir.  
Se existe nenhuma conta de utilizador ainda estiver disponível, é criado automaticamente pelo Lynda.com.

>[AZURE.NOTE]Pode utilizar quaisquer outros Lynda.com utilizador conta ferramentas para a criação ou APIs fornecida pela Lynda.com às contas de utilizador AAD disposição.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Para atribuir utilizadores a Lynda.com, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Lynda.com **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-lynda-tutorial/IC777531.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-lynda-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).