<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com um Software de OfficeSpace | Microsoft Azure" 
    description="Saiba como utilizar o Software de OfficeSpace com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Tutorial: Integração do Azure Active Directory com um Software de OfficeSpace
  
Este tutorial objectivo Mostrar a integração do Azure e OfficeSpace Software.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Software de OfficeSpace serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a OfficeSpace Software poderão para o início de sessão único para a aplicação no site da sua empresa OfficeSpace Software (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para o Software de OfficeSpace
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Cenário")
##<a name="enabling-the-application-integration-for-officespace-software"></a>Ativar a integração de aplicação para o Software de OfficeSpace
  
Objectivo de nesta secção é como ativar a integração de aplicação para o Software de OfficeSpace de destaque.

###<a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>Para ativar a integração de aplicação para o Software de OfficeSpace, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-officespace-software-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **OfficeSpace Software**.

    ![Galeria de aplicação] (./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galeria de aplicação")

7.  No painel de resultados, selecione **OfficeSpace Software**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![OfficeSpace Software] (./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados ao OfficeSpace Software com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Configurar o serviço single sign-on para OfficeSpace Software requer que obter um valor de impressão digital a partir de um certificado.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação de **OfficeSpace Software** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o início de sessão único = no] (./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurar o início de sessão único = no")

2.  Na página **como pretende utilizadores a iniciar sessão OfficeSpace Software** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **OfficeSpace Software de início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão aplicação de OfficeSpace Software (por exemplo: "*https://company.officespacesoftware.com*") e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on em OfficeSpace Software** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa OfficeSpace Software como administrador.

6.  Aceda a **Administração \> conectores**.

    ![Admin] (./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Admin")

7.  Clique em **SAML autorização**.

    ![Conectores] (./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Conectores")

8.  Na secção **SAML autorização** , execute os seguintes passos:

    ![Configuração do SAML] (./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuração do SAML")

    1.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em OfficeSpace Software** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **url do fornecedor termine a sessão** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on em OfficeSpace Software** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **url de destino do cliente idp** .
    3.  Copie o valor de **impressão digital** a partir do certificado exportado e, em seguida, colá-la a caixa de texto de **identificação de orientação do diapositivo notas idp de cliente** .  

        >[AZURE.TIP]
        Para obter mais detalhes, consulte o artigo [como obter o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  Clique em **Guardar definições**.

9.  No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configurar serviço single sign-on")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que utilizadores do Azure AD que inicie sessão no OfficeSpace Software, tem de ser aprovisionadas para OfficeSpace Software. No caso de OfficeSpace Software, aprovisionamento é uma tarefa automatizada.  
Não existe nenhum item ação por si.  
Os utilizadores são criados automaticamente se for necessário durante a primeira única início de sessão tentativa.

>[AZURE.NOTE]Pode utilizar qualquer outras OfficeSpace Software utilizador conta ferramentas para a criação ou APIs fornecida pela OfficeSpace Software às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>Para atribuir utilizadores a OfficeSpace Software, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação de **OfficeSpace Software **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).