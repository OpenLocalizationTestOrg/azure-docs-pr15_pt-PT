<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Salesforce | Microsoft Azure"
    description="Saiba como utilizar Salesforce com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>Tutorial: Como integrar o Salesforce com o Azure Active Directory

Neste tutorial mostrar-lhe como ligar o seu ambiente do Salesforce ao Azure Active Directory. Irá obter informações sobre como configurar o serviço single sign-on para Salesforce, como ativar o aprovisionamento automatizado de utilizador e como atribuir aos utilizadores acesso a equipa de vendas.

##<a name="prerequisites"></a>Pré-requisitos

1. Para aceder ao Azure Active Directory através do [portal clássica Azure](https://manage.windowsazure.com), primeiro tem de ter uma subscrição do Azure válida.

2. Tem de ter um inquilino válido no [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Se estiver a utilizar uma conta de **avaliação** Salesforce.com, em seguida, não será possível configurar o aprovisionamento automatizado do utilizador. Contas de avaliação não possui o acesso de API necessário ativado até que sejam comprados.
> 
> Pode aceder à volta esta limitação utilizando uma [conta de programador gratuito](https://developer.salesforce.com/signup) para concluir este tutorial.

Se estiver a utilizar um ambiente do Salesforce Sandbox, consulte o artigo o [tutorial de integração de Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Tutoriais do vídeos

Poderá siga este tutorial utilizando os vídeos abaixo.

**Defina uma parte: como ativar o Single Sign-On**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Parte do vídeo de demonstração dois: Como automatizar aprovisionamento de utilizador**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>Passo 1: Adicionar Salesforce ao diretório da sua

1. No [portal clássica Azure](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Selecione do Active Directory a partir do painel de navegação à esquerda.][0]

2. A partir da lista de **diretório** , selecione o diretório que pretender para adicionar Salesforce para.

3. Clique em **aplicações** no menu superior.

    ![Clique em aplicações.][1]

4. Clique em **Adicionar** na parte inferior da página.

    ![Clique em Adicionar para adicionar uma nova aplicação.][2]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Clique em Adicionar uma aplicação a partir da galeria.][3]

6. Na **caixa de pesquisa**, escreva **Salesforce**. Em seguida, selecione **Salesforce** nos resultados de e clique em **Concluir** para adicionar a aplicação.

    ![Adicione a equipa de vendas.][4]

7. Agora deverá ver a página de início rápido para a equipa de vendas:

    ![Página de início rápido do Salesforce no Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Passo 2: Ativar o início de sessão único

1. Pode configurar o início de sessão único, tem de configurar e implementar um domínio personalizado para o ambiente do Salesforce. Para obter instruções sobre como fazê-lo, consulte o artigo [Configurar o nome de domínio um](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Em Guia página do Salesforce do Azure AD, clique no botão **Configurar serviço single sign-on** .

    ![O único início de sessão botão Configurar][6]

3. Uma caixa de diálogo será aberto e verá um ecrã de pede "Seria como os utilizadores para iniciar sessão Salesforce?" Selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Selecione Azure AD Single Sign-On][7]

    > [AZURE.NOTE] Para obter mais informações sobre as diferentes únicos início de sessão opções, [clique aqui](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. Na página **Configurar definições de aplicação** , preencha o **Início de sessão no URL** ao escrever no seu URL do domínio Salesforce utilizando o seguinte formato:
 - Conta da empresa:`https://<domain>.my.salesforce.com`
 - Conta de programador:`https://<domain>-dev-ed.my.salesforce.com` 

    ![Escreva o URL de início de sessão][8]

5. Na página **Configurar serviço single sign-on na Salesforce** , clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Transferir o certificado][9]

6. Abra um novo separador no seu browser e início de sessão à sua conta de administrador do Salesforce.

7. No painel de navegação de **administrador** , clique em **Controlos de segurança** para expandir a secção relacionada. Em seguida, clique em **Definições de início de início de sessão único**.

    ![Clique em definições de início de sessão únicos em controlos de segurança][10]

8. Na página **Definições de Single Sign-On** , clique no botão **Editar** .

    ![Clique no botão Editar][11]

    > [AZURE.NOTE] Se não conseguir activar Single Sign-On definições para a sua conta do Salesforce, poderá ter de contactar o suporte do Salesforce para ter a funcionalidade de permissão para utilizar.

9. Selecione **SAML com permissão para**e, em seguida, clique em **Guardar**.

    ![Selecione SAML activado][12]

10. Configure as suas SAML únicos início de sessão definições, clique em **Novo**.

    ![Selecione SAML activado][13]

11. Na página **SAML único início de sessão definição Editar** , certifique configurações que se seguem:

    ![Captura de ecrã das configurações que deve efetuar][14]

 - Para o campo **nome** , escreva um nome amigável para esta configuração. Fornecer um valor para **nome** povoar automaticamente a caixa de texto **Nome API** .

 - No Azure AD, copie o valor de **Emissor URL** e, em seguida, colá-la campo **emissor** Salesforce.

 - Em **Id da entidade de caixa de texto**, escreva o seu nome de domínio de Salesforce utilizando o padrão seguinte:
     - Conta da empresa:`https://<domain>.my.salesforce.com`
     - Conta de programador:`https://<domain>-dev-ed.my.salesforce.com`

 - Clique em **Procurar** ou **Escolher ficheiro** para abrir a caixa de diálogo **Escolher ficheiro a carregar** , selecione o certificado de Salesforce e, em seguida, clique em **Abrir** para carregar o certificado.

 - Para **Tipo de identidade do SAML**, selecione **afirmação contém o nome de utilizador do utilizador salesforce.com**.

 - Para a **Localização de identidade do SAML**, selecione a **identidade está no elemento de NameIdentifier da instrução assunto**

 - No Azure AD, copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la campo **URL de início de sessão do fornecedor de identidade** Salesforce.

 - Para **Pedir-lhe-iniciados por vincular serviço fornecedor**, selecione **Redirecionar HTTP**.

 - Por fim, clique em **Guardar** para aplicar as SAML únicos início de sessão definições.

12. No painel de navegação à esquerda na Salesforce, clique em **Gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **Meu domínio**.

    ![Clique no meu domínio][15]

13. Desloque para baixo até à secção de **Configuração de autenticação** e clique no botão **Editar** .

    ![Clique no botão Editar][16]

14. Na secção **Serviço de autenticação** , selecione o nome amigável da sua configuração SAML SSO e, em seguida, clique em **Guardar**.

    ![Selecione a sua configuração SSO][17]

    > [AZURE.NOTE] Se mais do que um serviço de autenticação estiver selecionado, em seguida, quando os utilizadores tentarem iniciar sessão único para o ambiente do Salesforce, será pedido para selecionar qual que gostaria de iniciar sessão com o serviço de autenticação. Se não quiser que este deverá acontecer, em seguida, deverá **Deixar todos os outros serviços de autenticação desmarcados**.

15. No Azure AD, selecione a caixa de verificação de confirmação de configuração de início de sessão único para ativar o certificado que carregou para a equipa de vendas. Em seguida, clique em **seguinte**.

    ![Selecione a caixa de verificação de confirmação][18]

16. Na página final da caixa de diálogo, escreva um endereço de e-mail se pretender para receber notificações de correio eletrónico para erros e avisos relacionados com a manutenção desta configuração de início de sessão única. 

    ![Escreva o seu endereço de e-mail.][19]

17. Clique em **Concluir** para fechar a caixa de diálogo. Para testar a sua configuração, consulte a secção abaixo intitulada [Atribuir utilizadores a equipa de vendas](#step-4-assign-users-to-salesforce).

##<a name="step-3-enable-automated-user-provisioning"></a>Passo 3: Ativar aprovisionamento automatizado do utilizador

1. Na página de Azure AD guia de introdução do Salesforce, clique no botão **Configurar aprovisionamento de utilizador** .

    ![Clique no botão de configurar o aprovisionamento de utilizador][20]

2. Na caixa de diálogo **Configurar aprovisionamento de utilizador** , escreva o nome de utilizador do Salesforce administrador e a palavra-passe.

    ![Escreva no seu nome de utilizador do administrador ou palavra-passe][21]

    > [AZURE.NOTE] Se estiver a configurar o ambiente de produção, é a melhor prática criar uma nova conta de administrador no Salesforce especificamente para este passo. Esta conta tem de ter o perfil de **Administrador do sistema** atribuído à mesma no Salesforce.

3. Para obter a segurança do Salesforce token, abra um novo separador e inicie sessão na conta de administrador do mesmo Salesforce. No canto superior direito da página, clique no seu nome e, em seguida, clique em **Definições do meu**.

    ![Clique no seu nome, em seguida, clique em definições do meu][22]

4. No painel de navegação esquerdo, clique no **pessoal** para expandir a secção relacionada e, em seguida, clique em **Repor a minha Token de segurança**.

    ![Clique no seu nome, em seguida, clique em definições do meu][23]

5. Na página **Repor a minha Token de segurança** , clique no botão **Repor Token de segurança** .

    ![Leia os avisos.][24]

6. Selecione a caixa de entrada de e-mail associada esta conta de administrador. Procure uma mensagem de e-mail a partir do Salesforce.com que contém o novo token de segurança.

7. Copie o token, aceda à sua janela do Azure AD e colá-lo para o campo **Token de segurança do utilizador** . Em seguida, clique em **seguinte**.

    ![Cole o token de segurança][25]

8. Na página de confirmação, pode optar por receber notificações de correio eletrónico para quando ocorrem falhas de aprovisionamento. Clique em **Concluir** para fechar a caixa de diálogo.

    ![Escreva o seu endereço de e-mail para receber notificações][26]

##<a name="step-4-assign-users-to-salesforce"></a>Passo 4: Atribuir utilizadores a equipa de vendas

1. Para testar a sua configuração, comece por criar uma nova conta de teste no diretório.

2. Na página de início rápido do Salesforce, clique no botão **Atribuir utilizadores** .

    ![Clique em atribuir utilizadores][27]

3. Selecione o utilizador de teste e clique no botão **atribuir** na parte inferior do ecrã:

 - Se ainda não o ativar Aprovisiona o utilizador automatizado, em seguida, verá o pedido seguinte para confirmar:

        ![Confirm the assignment.][28]

 - Se ativou o utilizador automatizadas de aprovisionamento, em seguida, verá um pedido para definir qual o tipo de perfil do Salesforce o utilizador deve ter. Utilizadores recentemente aprovisionados deverão aparecer no seu ambiente do Salesforce depois de alguns minutos.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Se são Aprovisiona para um ambiente do **Programador** Salesforce, terá de um número de licenças disponíveis para cada perfil muito limitado. Por isso, é melhor o aprovisionamento de utilizadores para o perfil de **Utilizador gratuito Chatter** , que possui 4,999 licenças disponíveis.

4. Para testar as suas definições de início de sessão únicos, abra o painel de acesso no [https://myapps.microsoft.com](https://myapps.microsoft.com/), em seguida, inicie sessão na conta de teste e clique no **Salesforce**.

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Lista de tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png
