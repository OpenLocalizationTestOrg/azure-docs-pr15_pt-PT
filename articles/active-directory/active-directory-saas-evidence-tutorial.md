<properties
    pageTitle="Tutorial: Integração do Azure Active Directory com Evidence.com | Microsoft Azure"
    description="Saiba como configurar o serviço single sign-on entre o Azure Active Directory e Evidence.com."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Tutorial: Integração do Azure Active Directory com Evidence.com

Este tutorial objectivo Mostrar como configurar o serviço single sign-on entre o Azure Active Directory (AAD) e Evidence.com. O cenário descrito neste tutorial assume que já tem os seguintes itens:
    
* Uma subscrição do Microsoft Azure válida
* Uma subscrição do Evidence.com com o single sign-On ativado (e-mail earlyaccess@evidence.com se baseadas em SAML single sign-on não estiver ativado)

Depois de concluir este tutorial, os utilizadores AAD a quem tenha atribuído Evidence.com acesso poderão para o início de sessão único para a aplicação utilizando o painel de acesso AAD.

## <a name="add-evidencecom-to-your-directory"></a>Adicionar Evidence.com ao seu diretório

Esta secção descreve como adicionar Evidence.com como uma aplicação integrada no Azure Active Directory.

**Para ativar a integração da aplicação para provas:**

1.  No [portal clássica Azure](https://manage.windowsazure.com), no painel de navegação esquerdo, clique em **Do Active Directory**.

2.  A partir da lista de **diretório** , selecione o diretório para o qual pretende ativar a integração de diretórios.

3.  Para abrir a vista de aplicações, na vista de diretório, clique em **aplicações** no menu superior.

4.  Para abrir a Galeria de aplicação, clique em **Adicionar**e, em seguida, clique em **Adicionar uma aplicação a partir da Galeria**.

5.  Na caixa de pesquisa, escreva **Evidence.com**.

6.  No painel de resultados, selecione **Evidence.com**e, em seguida, clique em **Concluir** para adicionar a aplicação.


## <a name="configuring-single-sign-on"></a>Configurar o início de sessão único

Esta secção descreve como permitir que os utilizadores autenticar Evidence.com com a sua conta no Azure Active Directory, utilizando a Federação com base no protocolo SAML.

**Para configurar o início de sessão único, execute os seguintes passos:**

1.  Depois de adicionar Evidence.com no portal do Azure clássico, clique em **Configurar Single Sign-On**. 
 
2.  No ecrã seguinte, selecione **Azure AD Single Sign-On**e, em seguida, clique em **seguinte**.

3.  No ecrã Configurar o URL da aplicação, introduza o URL onde os utilizadores irão inicie sessão no seu URL do inquilino Evidence.com (exemplo: https://yourtenant.evidence.com e, em seguida, clique em **seguinte**. 

4.  Clique na ligação de **Transferir o certificado** e guardá-lo para a sua unidade local. Este certificado e os URLs de metadados (ID da entidade, SSO URL início de sessão e URL de saída de início de sessão) serão utilizados para configurar o SSO no Evidence.com site. 

5.  Numa janela do browser web em separado, iniciar sessão no seu Evidence.com como administrador de inquilinos e navegue até ao separador **administrador**
      
6.  Clique no **Agência Single Sign-On**
 
7.  Selecione **SAML com base início de sessão único**
 
8.  Copie o **URL de emissor**, valores de **Início de sessão** e **Único terminar sessão** apresentados no portal clássico do Azure e para os campos correspondentes Evidence.com.

9.  Abrir o certificado transferido no passo 4 utilizando um editor de texto como Notepad.exe e copiar e colar os conteúdos na caixa de **Certificado de segurança** . 

10. Guarde a configuração no Evidence.com.
 
11. No portal do Azure clássico, selecione **Confirmar que configurou serviço single sign-on como é descrito acima**. Esta verificação permitirá que o certificado atual para começar a trabalhar para esta caixa de verificação de aplicação.
 
12. Na página de confirmação de início de sessão único, clique em **Concluir**.  


## <a name="creating-an-evidencecom-test-user"></a>Criação de um utilizador de teste Evidence.com

Para utilizadores do Azure AD possam iniciar sessão, tem de ser aprovisionadas para acesso dentro da aplicação Evidence.com. Esta secção descreve como criar contas de utilizador do Azure AD dentro Evidence.com

**Aprovisionamento de uma conta de utilizador no Evidence.com:**

1.  Numa janela do browser web, inicie sessão no site da sua empresa Evidence.com como administrador.

2.  Navegue até ao separador **administrador** .

3.  Clique em **Adicionar utilizador**.

4.  Clique no botão **Adicionar** .

5.  O **Endereço de E-Mail** do utilizador adicionado tem de corresponder o nome de utilizador dos utilizadores no Azure AD quem pretende conceder acesso. Se o nome de utilizador e endereço de e-mail não estiverem o mesmo valor na sua organização, pode utilizar o **Evidence.com > atributos > Single Sign-On** secção do portal clássica Azure para alterar nameidenitifer enviada para Evidence.com para ser o endereço de e-mail.


## <a name="assigning-users-to-evidencecom"></a>Atribuir utilizadores a Evidence.com

Para aprovisionada AAD os utilizadores consigam ver Evidence.com os respetivos de texto, estas têm atribuídas acesso dentro do Azure portal clássico.

**Para atribuir utilizadores a Evidence.com:**

1.  Na página de guia de introdução para Evidence.com no portal do Azure clássico, clique em **atribuir aos utilizadores Evidence.com**.
 
2.  No menu **Mostrar** , selecione se pretende atribuir um utilizador ou um grupo a Evidence.com e clique no botão de marca de verificação.
 
3.  Na lista de **utilizadores** , selecione os utilizadores para agrupar para quem pretende atribuir Evidence.com.
 
4.  No rodapé da página, clique no botão **atribuir** .

