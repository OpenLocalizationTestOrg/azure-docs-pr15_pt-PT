<properties 
    pageTitle="Tutorial: Integração do Azure Active Directory com AnswerHub | Microsoft Azure" 
    description="Saiba como utilizar AnswerHub com o Azure Active Directory para permitir o início de sessão único, aprovisionamento automatizado e mais!" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Tutorial: Integração do Azure Active Directory com AnswerHub

Este tutorial objectivo Mostrar a integração do Azure e [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
O cenário descrito neste tutorial assume que já tem os seguintes itens:

-   Uma subscrição do Azure válida
-   Um [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) serviço single sign-on com capacidade de subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD que ter atribuído a AnswerHub poderão para o início de sessão único para a aplicação no site da sua empresa AnswerHub (sinal de fornecedor iniciados por serviço no) ou utilizar a [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial é constituído pelos seguintes blocos modulares:

1.  Ativar a integração da aplicação para AnswerHub
2.  Configurar o início de sessão único
3.  Configurar o aprovisionamento de utilizador
4.  Atribuir utilizadores

![Cenário] (./media/active-directory-saas-answerhub-tutorial/IC785165.png "Cenário")

##<a name="enabling-the-application-integration-for-answerhub"></a>Ativar a integração da aplicação para AnswerHub

Objectivo de nesta secção é como ativar a integração da aplicação para AnswerHub de destaque.

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>Para ativar a integração da aplicação para AnswerHub, execute os seguintes passos:

1.  No portal clássico Azure, no painel de navegação esquerdo, clique em **Do Active Directory**.

    ![O Active Directory] (./media/active-directory-saas-answerhub-tutorial/IC700993.png "O Active Directory")

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

    ![Aplicações] (./media/active-directory-saas-answerhub-tutorial/IC700994.png "Aplicações")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicação] (./media/active-directory-saas-answerhub-tutorial/IC749321.png "Adicionar aplicação")

5.  Na caixa de diálogo **o que pretende fazer** , clique em **Adicionar uma aplicação a partir da Galeria**.

    ![Adicionar uma aplicação a partir do gallerry] (./media/active-directory-saas-answerhub-tutorial/IC749322.png "Adicionar uma aplicação a partir do gallerry")

6.  Na **caixa de pesquisa**, escreva **AnswerHub**.

    ![Galeria de aplicação] (./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galeria de aplicação")

7.  No painel de resultados, selecione **AnswerHub**e, em seguida, clique em **Concluir** para adicionar a aplicação.

    ![AnswerHub] (./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Objectivo de nesta secção é como permitir que os utilizadores autenticados para AnswerHub com a sua conta no Azure AD utilizando a Federação com base no protocolo SAML de destaque.  
Como parte deste procedimento, são necessários para criar um ficheiro de certificado codificado base-64.  
Se não estiver familiarizado com este procedimento, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o início de sessão único, execute os seguintes passos:

1.  No portal clássico Azure, na página de integração de aplicação **AnswerHub** , clique **Configurar serviço single sign-on** para abrir a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurar serviço single sign-on")

2.  Na página **como pretende utilizadores a iniciar sessão AnswerHub** , selecione **Microsoft Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurar serviço single sign-on")

3.  Na página **Configurar o URL da aplicação** , na caixa de texto **AnswerHub URL início de sessão** , escreva o seu URL utilizando o padrão seguinte "*https://company.answerhub.com*" e, em seguida, clique em **seguinte**.

    ![Configurar o URL da aplicação] (./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurar o URL da aplicação")

4.  Na página **Configurar serviço single sign-on na AnswerHub** , para transferir o certificado, clique em **Transferir o certificado**e, em seguida, guarde o ficheiro de certificado localmente no seu computador.

    ![Configurar serviço single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurar serviço single sign-on")

5.  Numa janela do browser web diferente, inicie sessão no site da sua empresa AnswerHub como administrador.
    >[AZURE.NOTE] Se precisar de ajuda a configurar AnswerHub, contacte [a equipa de suporte do AnswerHub](mailto:success@answerhub.com. ).








6.  Ir para a **Administração**.

7.  Clique no separador de **utilizador e grupo** .

8.  No painel de navegação no lado esquerdo, na secção **Definições de rede Social** , clique em **SAML configuração**.

9.  Clique em separador de **IDP Config** .

10. No separador **IDP Config** , execute os seguintes passos:

    ![Programa de configuração do SAML] (./media/active-directory-saas-answerhub-tutorial/IC785172.png "Programa de configuração do SAML")

    1.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na AnswerHub** , copie o valor de **URL remoto de início de sessão** e, em seguida, colá-la a caixa de texto do **URL de início de sessão IDP** .
    2.  No portal clássico Azure, na página de diálogo **Configurar serviço single sign-on na AnswerHub** , copie o valor **De URL remoto termine a sessão** e, em seguida, colá-la a caixa de texto **IDP termine a sessão URL** .
    3.  No Azure clássico portal do, na página de diálogo **Configurar serviço single sign-on na AnswerHub** , copie o valor de **Formato de nome do identificador** e, em seguida, colá-la a caixa de texto de **Formato de identificador IDP nome** .
    4.  Clique em **chaves e certificados**.

11. No separador chaves e certificados, execute os seguintes passos:

    ![Chaves e certificados] (./media/active-directory-saas-answerhub-tutorial/IC785173.png "Chaves e certificados")

    1.  Crie um ficheiro de **base-64 codificado** a partir do seu certificado transferido.  

        >[AZURE.TIP] Para obter mais detalhes, consulte o artigo [como converter um certificado binário para um ficheiro de texto](http://youtu.be/PlgrzUZ-Y1o)

    2.  Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo da mesma para a sua área de transferência e, em seguida, cole-a para a caixa de texto **IDP chave pública (x 509 formato)** .
    3.  Clique em **Guardar**.

12. No separador **IDP configuração** , clique em **Guardar**.

13. No portal do Azure clássico, selecione a confirmação de configuração de início de sessão único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar Single Sign On** .

    ![Configurar serviço single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurar serviço single sign-on")

##<a name="configuring-user-provisioning"></a>Configurar o aprovisionamento de utilizador

Para permitir que utilizadores do Azure AD que inicie sessão no AnswerHub, tem de ser aprovisionadas para AnswerHub.  
No caso de AnswerHub, aprovisionamento é uma tarefa manual.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizador, execute os seguintes passos:

1.  Inicie sessão no site da sua empresa **AnswerHub** como administrador.

2.  Ir para a **Administração**.

3.  Clique no separador de **utilizadores e grupos** .

4.  No painel de navegação no lado esquerdo, na secção **Gerir utilizadores** , clique em **criar ou importar utilizadores**.

    ![Utilizadores e grupos] (./media/active-directory-saas-answerhub-tutorial/IC785175.png "Utilizadores e grupos")

5.  Escreva o **endereço de E-Mail**, o **nome de utilizador** e a **palavra-passe** de uma conta do Azure Active Directory válida que pretende aprovisionar para as caixas de texto relacionadas e, em seguida, clique em **Guardar**.

>[AZURE.NOTE] Pode utilizar quaisquer outros AnswerHub utilizador conta ferramentas para a criação ou APIs fornecida pela AnswerHub às contas de utilizador do aprovisionar AAD.

##<a name="assigning-users"></a>Atribuir utilizadores

Para testar a sua configuração, tem de conceder os utilizadores do Azure AD que pretende permitir ao utilizar a sua aplicação acesso à mesma, atribuindo-lhes.

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Para atribuir utilizadores a AnswerHub, execute os seguintes passos:

1.  No portal do Azure clássico, crie uma conta de teste.

2.  Na página de integração de aplicação **AnswerHub **, clique em **atribuir aos utilizadores**.

    ![Atribuir aos utilizadores] (./media/active-directory-saas-answerhub-tutorial/IC785176.png "Atribuir aos utilizadores")

3.  Selecione o utilizador de teste, clique em **atribuir**e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim] (./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sim")

Se pretender testar as suas definições de início de sessão únicos, abra o painel de acesso. Para obter mais detalhes acerca do painel de acesso, consulte o artigo [Introdução ao painel do Access](active-directory-saas-access-panel-introduction.md).
