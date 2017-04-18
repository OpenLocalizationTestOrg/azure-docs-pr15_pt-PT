<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com NetSuite | Microsoft Azure"
    description="Saiba como utilizar NetSuite com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!"
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

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>Tutorial: Como integrar o NetSuite com o Azure Active Directory

Neste tutorial mostrar-lhe como ligar o seu ambiente NetSuite ao seu Azure Active Directory (Azure AD). Irá obter informações sobre como configurar o serviço single sign-on para NetSuite, como ativar o aprovisionamento automatizado de utilizador e como atribuir aos utilizadores têm acesso ao NetSuite. 

##<a name="prerequisites"></a>Pré-requisitos

1. Para aceder ao Azure Active Directory através do [portal clássica Azure](https://manage.windowsazure.com), primeiro tem de ter uma subscrição do Azure válida.

2. Tem de ter acesso do administrador para uma subscrição de [NetSuite](http://www.netsuite.com/portal/home.shtml) . Pode utilizar uma conta de avaliação gratuita ou serviço.

##<a name="step-1-add-netsuite-to-your-directory"></a>Passo 1: Adicionar NetSuite ao seu diretório

1. No [portal clássica Azure](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![Selecione do Active Directory a partir do painel de navegação à esquerda.][0]

2. A partir da lista de **diretório** , selecione o diretório que pretender para adicionar NetSuite para.

3. Clique em **aplicações** no menu superior.

    ![Clique em aplicações.][1]

4. Clique em **Adicionar** na parte inferior da página.

    ![Clique em Adicionar para adicionar uma nova aplicação.][2]

5. Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Clique em Adicionar uma aplicação a partir da galeria.][3]

6. Na **caixa de pesquisa**, escreva **NetSuite**. Em seguida, selecione **NetSuite** nos resultados de e clique em **Concluir** para adicionar a aplicação.

    ![Adicione NetSuite.][4]

7. Agora deverá ver a página de início rápido para NetSuite:

    ![Página de início rápido do NetSuite no Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Passo 2: Ativar Single Sign-On

1. No Azure AD, na página de início rápido para NetSuite, clique no botão **Configurar serviço single sign-on** .

    ![O único início de sessão botão Configurar][6]

2. Uma caixa de diálogo será aberto e verá um ecrã de pede "Seria como os utilizadores para iniciar sessão NetSuite?" Selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Selecione Azure AD Single Sign-On][7]

    > [AZURE.NOTE] Para obter mais informações sobre as diferentes únicos início de sessão opções, [clique aqui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Na página **Configurar definições de aplicação** , para o campo **Resposta URL** , escreva o URL de inquilino NetSuite utilizando um dos seguintes formatos:
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Escreva o URL do seu inquilino][8]

4. Na página **Configurar serviço single sign-on na NetSuite** , clique em **transferir metadados**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Transfira os metadados.][9]

5. Abrir um novo separador no seu browser e inicie sessão no site da sua empresa Netsuite como administrador.

6. Na barra de ferramentas na parte superior da página, clique em **configuração**, em seguida, clique em **Gestor de configuração**.

    ![Ir para o Gestor de configuração][10]

7. A partir da lista de **Tarefas de configuração** , selecione **integração**.

    ![Aceda à integração][11]

8. Na secção **Autenticação gerir** , clique em **SAML Single Sign-on**.

    ![Aceda a SAML Single Sign-on][12]

9. Na página **Configuração de SAML** , execute os seguintes passos:

    - No Azure Active Directory, copie o valor de **URL remoto de início de sessão** e colá-la o campo de **Página de início de sessão do fornecedor de identidade** no NetSuite.

        ![Página de configuração de SAML.][13]

    - Na NetSuite, selecione o **Método de autenticação principal**.

    - Para o campo denominado **Metadados de fornecedor de identidade SAMLV2**, selecione **Carregar o ficheiro de metadados IDP**. Em seguida, clique em **Procurar** para carregar o ficheiro de metadados que transferiu no passo #4.

        ![Carregar os metadados][16]

    - Clique em **Submeter**.

9. No Azure AD, selecione a caixa de verificação de confirmação de configuração de início de sessão único para ativar o certificado que carregou para NetSuite. Em seguida, clique em **seguinte**.

    ![Selecione a caixa de verificação de confirmação][14]

10. Na página final da caixa de diálogo, escreva um endereço de e-mail se pretender para receber notificações de correio eletrónico para erros e avisos relacionados com a manutenção desta configuração de início de sessão única. 

    ![Escreva o seu endereço de e-mail.][15]

11. Clique em **Concluir** para fechar a caixa de diálogo. Em seguida, clique em **atributos** na parte superior da página.

    ![Clique em atributos.][17]

12. Clique em **Adicionar utilizador atributo**.

    ![Clique em Adicionar utilizador atributo.][18]

13. Para o campo **Nome do atributo** , escreva na `account`. Para o campo de **Valor do atributo** , escreva o seu ID de conta NetSuite. Obter instruções sobre como localizar o seu ID de conta estão incluídas abaixo:

    ![Adicionar o seu ID de conta NetSuite.][19]

    - No NetSuite, clique no **programa de configuração** a partir do menu de navegação superior.
    - Em seguida, clique na secção de **Tarefas de configuração** do menu de navegação esquerdo, selecione a secção de **integração** e clique em **Preferências de serviços Web**.
    - Copie o seu ID de conta NetSuite e colá-la para o campo de **Valor do atributo** Azure AD.

        ![Obtenha o seu ID de conta][20]

14. No Azure AD, clique em **Concluir** para concluir a adição o atributo SAML. Em seguida, clique em **Aplicar alterações** no menu da parte inferior.

    ![Guarde as suas alterações.][21]

15. Antes dos utilizadores podem executar o início de sessão único para NetSuite, eles tem primeiro de ser atribuídos as permissões adequadas no NetSuite. Siga as instruções abaixo para atribuir estas permissões.

    - No menu de navegação superior, clique em **configuração**, em seguida, clique em **Gestor de configuração**.

        ![Ir para o Gestor de configuração][10]

    - No menu de navegação esquerdo, selecione **Utilizadores/funções**, em seguida, clique em **Gerir funções**.

        ![Ir para gerir funções][22]

    - Clique em **nova função**.

    - Escreva um **nome** para a função novo e selecione a caixa de **Único início de sessão apenas** verificação.

        ![Nome da nova função.][23]

    - Clique em **Guardar**.

    - No menu no canto superior, clique em **permissões**. Em seguida, clique em **configuração**.

        ![Aceda às permissões][24]

    - Selecione **definir o SAM Single Sign-on**e, em seguida, clique em **Adicionar**.

    - Clique em **Guardar**.

    - No menu de navegação superior, clique em **configuração**, em seguida, clique em **Gestor de configuração**.

        ![Ir para o Gestor de configuração][10]

    - No menu de navegação esquerdo, selecione **Utilizadores/funções**, em seguida, clique em **Gerir utilizadores**.

        ![Ir para gerir utilizadores][25]

    - Selecione um utilizador de teste. Em seguida, clique em **Editar**.

        ![Ir para gerir utilizadores][26]

    - Na caixa de diálogo Funções, selecione a função que criou e clique em **Adicionar**.

        ![Ir para gerir utilizadores][27]

    - Clique em **Guardar**.

19. Para testar a sua configuração, consulte a secção abaixo intitulada [Atribuir aos utilizadores NetSuite](#step-4-assign-users-to-netsuite).

##<a name="step-3-enable-automated-user-provisioning"></a>Passo 3: Ativar aprovisionamento automatizado do utilizador

> [AZURE.NOTE] Por predefinição, os utilizadores aprovisionados serão adicionados a uma filial raiz do seu ambiente NetSuite.

1. No Azure Active Directory, na página de início rápido para NetSuite, clique no **aprovisionamento de utilizador de configurar**.

    ![Configurar o aprovisionamento de utilizador][28]

2. Na caixa de diálogo que é aberta, escreva na suas credenciais de administrador para NetSuite, em seguida, clique em **seguinte**.

    ![Escreva na suas credenciais de administrador NetSuite.][29]

3. Na página de confirmação, escreva o endereço de e-mail para receber notificações de aprovisionamento falhas.

    ![Confirme.][30]

4. Clique em **Concluir** para fechar a caixa de diálogo.

##<a name="step-4-assign-users-to-netsuite"></a>Passo 4: Atribuir utilizadores a NetSuite

1. Para testar a sua configuração, comece a criar uma nova conta de teste no diretório.

2. Na página NetSuite guia de introdução, clique no botão **Atribuir utilizadores** .

    ![Clique em atribuir utilizadores][31]

3. Selecione o utilizador de teste e clique no botão **atribuir** na parte inferior do ecrã:

 - Se ainda não o ativar Aprovisiona o utilizador automatizado, em seguida, verá o pedido seguinte para confirmar:

        ![Confirm the assignment.][32]

 - Se ativou o utilizador automatizadas de aprovisionamento, em seguida, verá um pedido para definir qual o tipo de função o utilizador deve ter em NetSuite. Os utilizadores recentemente aprovisionados deverão aparecer no seu ambiente NetSuite depois de alguns minutos.

4. Para testar as suas definições de início de sessão únicos, abra o painel de acesso no [https://myapps.microsoft.com](https://myapps.microsoft.com/), inicie sessão na conta de teste e clique no **NetSuite**.

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Lista de tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png
