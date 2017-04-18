<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com Work.com | Microsoft Azure" 
    description="Saiba como utilizar Work.com com o Azure Active Directory para permitir o início de sessão único, automatizado aprovisionamento e mais!." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Azure Active Directory com Work.com
  
Este tutorial objectivo Mostrar a integração do Azure e Work.com.  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um Work.com serviço single sign-on com capacidade de subscrição
  
Depois de concluir este tutorial, os utilizadores AAD para quem tem atribuem Work.com acesso será capaz de único início de sessão para a aplicação no site da sua empresa Work.com (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
  
O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para Work.com
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-work-com-tutorial/IC794105.png "Cenário")

##<a name="enabling-the-application-integration-for-workcom"></a>Ativar a integração da aplicação para Work.com
  
Objectivo de nesta secção é como ativar a integração da aplicação para Work.com de destaque.

###<a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Para ativar a integração da aplicação para Work.com, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-work-com-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-work-com-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-work-com-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-work-com-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **Work.com**.

    ![Galeria de aplicação] (./media/active-directory-saas-work-com-tutorial/IC794106.png "Galeria de aplicação")

7.  No painel de resultados, selecione **Work.com**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![Work.com] (./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único
  
Objectivo de nesta secção é como permitir que os utilizadores autenticados para Work.com com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, é necessário para carregar um certificado para Work.com.com.

>[AZURE.NOTE] Para configurar o início de sessão único, precisa de configurar um nome de domínio personalizado Work.com ainda. Tem de definir, pelo menos, um nome de domínio, teste o seu nome de domínio e implementá-lo a toda a organização.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  Inicie sessão no seu inquilino Work.com como administrador.

2.  Aceda a **configuração**.

    ![Programa de configuração] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programa de configuração")

3.  No painel de navegação à esquerda, na secção **administrar** , clique em **Gestão de domínios** para expandir a secção relacionada e, em seguida, clique no **Meu domínio** para abrir a página do **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-work-com-tutorial/IC767825.png "Meu domínio")

4.  Para verificar que o domínio foi configuração corretamente, certifique-se de que é "o**Passo 4 implementado para os utilizadores**" e reveja as "**Definições do meu domínio**".

    ![Domínio implementado utilizador] (./media/active-directory-saas-work-com-tutorial/IC784377.png "Domínio implementado utilizador")

5.  Numa janela do browser web diferente, inicie sessão no seu portal clássica Azure.

6.  Na página de integração de aplicação **Work.com **, clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurar o Single Sign-On")

7.  Na página **como pretende utilizadores a iniciar sessão Work.com** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurar o Single Sign-On")

8.  Na página **Configurar o URL da aplicação** , na caixa de texto **Work.com início de sessão no URL** , escreva o URL utilizado pelos utilizadores para iniciar sessão sua aplicação Work.com (por exemplo: " *http://company.my.salesforce.com*") e, em seguida, clique em **seguinte**: 

    ![Configurar o URL da aplicação] (./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurar o URL da aplicação")

9.  Na página **Configurar serviço single sign-on na Work.com** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar o Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurar o Single Sign-On")

10. Inicie sessão no seu inquilino Work.com.

11. Aceda a **configuração**.

    ![Programa de configuração] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programa de configuração")

12. Expandir o menu de **Controlos de segurança** e, em seguida, clique em **Definições de Single Sign-On**.

    ![Definições de início de sessão únicos] (./media/active-directory-saas-work-com-tutorial/IC794113.png "Definições de início de sessão únicos")

13. Na página de diálogo **Definições de Single Sign-On** , execute os seguintes passos:

    ![SAML activado] (./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML activado")

    1.  Selecione **SAML ativado**.
    2.  Clique em **Novo**.

14. Na secção **SAML única definições Sign-On** , execute os seguintes passos:

    ![SAML única início de sessão definição] (./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML única início de sessão definição")

    1.  Na caixa de texto **nome** , escreva um nome para a configuração.  

        >[AZURE.NOTE] Fornecer um valor para **nome** povoar automaticamente a caixa de texto **Nome API** .

    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Work.com** , copie o valor de **Emissor URL** e, em seguida, colá-la a caixa de texto **emissor** .
    3.  Para carregar o certificado transferido, clique em **Procurar**.
    4.  Na caixa de texto **Entidade Id** , escreva **https://salesforce-work.com**.
    5.  Como **O tipo de identidade do SAML**, selecione **afirmação contém o ID de Federação do objeto do utilizador**.
    6.  Como **Localização de identidade do SAML**, selecione a **identidade está no elemento de NameIdentfier da instrução assunto**.
    7.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Work.com** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão do fornecedor de identidade** .
    8.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na Work.com** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto do **URL de termine a sessão do fornecedor de identidade** .
    9.  Como **Serviço fornecedor iniciados por pedir encadernação**, selecione **HTTP Post**.
    10. Clique em **Guardar**.

15. No seu portal clássica Work.com, no painel de navegação esquerdo, clique em **Gestão de domínios** para expandir a secção relacionada e, em seguida, clique no **Meu domínio** para abrir a página do **Meu domínio** . 

    ![Meu domínio] (./media/active-directory-saas-work-com-tutorial/IC794115.png "Meu domínio")

16. Na página **Meu domínio** , na secção de **Personalização da página de início de sessão** , clique em **Editar**.

    ![Página de início de sessão de imagem corporativa] (./media/active-directory-saas-work-com-tutorial/IC767826.png "Página de início de sessão de imagem corporativa")

17. Na página **Imagem corporativa página de início de sessão** , na secção de **Serviço de autenticação** , o nome das suas **Definições de SSO SAML** é apresentado. Selecione-o e, em seguida, clique em **Guardar**.

    ![Página de início de sessão de imagem corporativa] (./media/active-directory-saas-work-com-tutorial/IC784366.png "Página de início de sessão de imagem corporativa")

18. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar o Single Sign-On] (./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurar o Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador
  
Para o Azure Active Directory os utilizadores possam iniciar sessão, tem de ser aprovisionadas para Work.com.  
No caso de Work.com, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa Work.com como administrador.

2.  Aceda a **configuração**.

    ![Programa de configuração] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programa de configuração")

3.  Aceda a **Gerir utilizadores \> utilizadores**.

    ![Gerir utilizadores] (./media/active-directory-saas-work-com-tutorial/IC784369.png "Gerir utilizadores")

4.  Clique em **novo utilizador**.

    ![Todos os utilizadores] (./media/active-directory-saas-work-com-tutorial/IC794117.png "Todos os utilizadores")

5.  Na secção Editar do utilizador, execute os seguintes passos:

    ![O utilizador editar] (./media/active-directory-saas-work-com-tutorial/IC794118.png "O utilizador editar")

    1.  Escreva o **Apelido**, **Alias**, **E-Mail**, **nome de utilizador** e **Alcunha** atributos de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas.
    2.  Selecione a **função**, **licença de utilizador** e **perfil**.
    3.  Clique em **Guardar**.  

        >[AZURE.NOTE] Titular da conta Azure Active Directory receberão um e-mail que inclua uma ligação para confirmar a conta antes de se tornar ativa.

>[AZURE.NOTE] Pode utilizar quaisquer outros Work.com utilizador conta ferramentas para a criação ou APIs fornecida pela Work.com às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores
  
Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Para atribuir utilizadores a Work.com, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação Work.com, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-work-com-tutorial/IC794119.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-work-com-tutorial/IC767830.png "Sim")
  
Agora que deve esperar 10 minutos e certifique-se de que a conta foi sincronizada para Work.com.com.
  
Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).