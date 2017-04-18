<properties 
    pageTitle="Tutorial: Integração com o Azure Active Directory com consola de administração Mimecast | Microsoft Azure" 
    description="Saiba como utilizar consola de administração Mimecast com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Tutorial: Integração com o Azure Active Directory com Mimecast consola de administração
  
Este tutorial objectivo Mostrar a integração do Azure e Mimecast consola de administração.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Uma consola de administração Mimecast serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a consola de administração Mimecast poderão para o início de sessão único para a aplicação no site da sua empresa Mimecast consola de administrador (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração de aplicação para Mimecast consola de administração
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Cenário")
##<a name="enabling-the-application-integration-for-mimecast-admin-console"></a>Ativar a integração de aplicação para Mimecast consola de administração
  
Objectivo de nesta secção é como ativar a integração de aplicação para Mimecast consola de administração de destaque.

###<a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>Para ativar a integração de aplicação para Mimecast consola de administração, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Mimecast consola de administração**.

    ![Galeria de aplicação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Mimecast consola de administrador**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Consola de administração de Mimecast] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Consola de administração de Mimecast")
##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados consola de administração Mimecast com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **Mimecast consola de administração** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configurar o Single Sign-On")

2.  Na página **como pretende utilizadores para iniciar sessão consola de administração Mimecast** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configurar o Single Sign-On")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **Mimecast administração consola de início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão sua aplicação Mimecast consola de administração (por exemplo: "https://webmail-uk.mimecast.com" ou "https://webmail-us.mimecast.com") e, em seguida, clique em **seguinte**.

    >[AZURE.NOTE] O URL de início de sessão é região específico.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na consola do administrador de Mimecast** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configurar o Single Sign-On")

5.  Numa janela do browser web diferente, inicie sessão na sua consola de administração Mimecast como administrador.

6.  Aceda a **serviços \> aplicação**.

    ![Serviços] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Serviços")

7.  Clique em **perfis de autenticação**.

    ![Perfis de autenticação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Perfis de autenticação")

8.  Clique em **novo perfil de autenticação**.

    ![Novos perfis de autenticação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Novos perfis de autenticação")

9.  Na secção de **Perfil de autenticação** , execute os seguintes passos:

    ![Perfil de autenticação] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Perfil de autenticação")

    1.  Na caixa de texto **Descrição** , escreva um nome para a configuração.
    2.  Selecione **impor a autenticação SAML para consola de administração de Mimecast**.
    3.  Como **fornecedor**, selecione o **Azure Active Directory**.
    4.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na consola do administrador de Mimecast** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **Emissor URL** .
    5.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na consola do administrador de Mimecast** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão** .
    6.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na consola do administrador de Mimecast** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de terminar sessão** .  

        >[AZURE.NOTE]O valor de URL de início de sessão e o valor de URL termine a sessão são para consola de administração do Mimecast os mesmos.

    7.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP]Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o).

    8.  Abrir o certificado codificado base-64 no bloco de notas, remover a primeira linha ("*--*") e a última linha ("*--*"), copie o restante conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto do **Certificado de fornecedor de identidade (metadados)** .
    9.  Selecione **Permitir o início de sessão único no**.
    10. Clique em **Guardar**.

10. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configurar o Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para permitir que os utilizadores Azure AD inicie sessão na consola do administrador de Mimecast, tem de ser aprovisionadas para Mimecast consola de administração.  
No caso de Mimecast consola de administração, aprovisionamento é uma tarefa manual.
  
Tem de registar um domínio, antes de poder criar utilizadores.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão sua **Consola de administração Mimecast** como administrador.

2.  Aceda a **directórios \> interno**.

    ![Diretórios] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Diretórios")

3.  Clique em **Registar novo domínio**.

    ![Registe-se novo domínio] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Registe-se novo domínio")

4.  Depois de ter sido criado o seu novo domínio, clique em **Novo endereço**.

    ![Novo endereço] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Novo endereço")

5.  Na caixa de diálogo novo do endereço, execute os seguintes passos:

    ![Guardar] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Guardar")

    1.  Escreva os atributos de **Endereço de E-Mail**, **Nome Global**, **palavra-passe** e **Confirmar palavra-passe** de uma conta AAD válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Clique em **Guardar**.

>[AZURE.NOTE]Pode utilizar outras ferramentas de criação de conta de utilizador da consola de administração Mimecast ou APIs fornecidos pela consola de administração Mimecast às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>Para atribuir utilizadores a Mimecast consola de administração, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **Mimecast consola de administração **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Sim")
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).