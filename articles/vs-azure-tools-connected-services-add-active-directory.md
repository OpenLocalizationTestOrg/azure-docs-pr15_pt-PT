<properties 
   pageTitle="Adicionar um Azure Active Directory utilizando serviços ligados no Visual Studio | Microsoft Azure"
   description="Adicionar um Azure Active Directory utilizando a caixa de diálogo do Visual Studio adicionar serviços ligados"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="active-directory"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adicionar um Azure Active Directory utilizando serviços ligados no Visual Studio 

##<a name="overview"></a>Descrição geral
Ao utilizar o Azure Active Directory (Azure AD), pode suportar único início de sessão (SSO) para as aplicações web do ASP.NET MVC ou autenticação AD nos serviços Web API. Com a autenticação do Azure AD, os seus utilizadores podem utilizar as respetivas contas a partir do Azure AD para ligar para as suas aplicações web. As vantagens do Azure AD autenticação com a API do Web incluem a segurança de dados melhorada quando expor uma API a partir de uma aplicação web. Com o Azure AD, não possui que gerir um sistema de autenticação em separado com a sua própria gestão de conta e o utilizador.

## <a name="supported-project-types"></a>Tipos de projecto suportados

Pode utilizar a caixa de diálogo Serviços ligados para ligar ao Azure AD nos seguintes tipos de projeto.

- Projetos MVC do ASP.NET

- Projetos de Web do ASP.NET API


### <a name="connect-to-azure-ad-using-the-connected-services-dialog"></a>Ligar ao Azure AD utilizando a caixa de diálogo Serviços ligados

1. Certifique-se de que tem uma conta Azure. Se não tiver uma conta Azure, pode inscrever-se para uma [versão de avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).

1. No Visual Studio, abra o menu de atalho do nó **referências** no seu projeto e selecione **Adicionar serviços ligados**.
1. Selecione **Azure AD autenticação** e, em seguida, selecione **Configurar**.

    ![Selecione adicionar a autenticação do Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Na primeira página do **Configurar Azure AD autenticação**, selecione **configurar Single Sign-on utilizando Azure AD**.

    Se o seu projeto está configurado com outra configuração de autenticação, o assistente avisa-o de que continuar irá desactivar a configuração anterior.

    ![Configurar o Azure AD no Assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  Na segunda página, selecione um domínio a partir da lista pendente do **domínio** . Lista de domínios contém todos os domínios acessíveis por contas listadas na caixa de diálogo Definições da conta. Como alternativa, pode introduzir um nome de domínio, se não encontrar aquele que procura, tal como mydomain.onmicrosoft.com. Pode escolher a opção para criar uma nova aplicação do Azure AD ou utilize as definições de uma aplicação do Azure AD existente. 

    ![Configurar o Azure AD no Assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. Na terceira página do assistente, certifique-se de que a **Ler diretório dados** está selecionada. O assistente irá preencher o **segredo cliente**. 

    ![Configurar o Azure AD no Assistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Selecione o botão **Concluir** . A caixa de diálogo adiciona o código de configuração necessária e referências para ativar o seu projeto para a autenticação do Azure AD. Pode ver o domínio de AD no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Reveja a página Introdução ao que aparece no seu browser para obter ideias sobre os passos seguintes e, a página o que aconteceu para ver como o seu projeto foi modificado. Se pretender verificar a tudo funcionava, abra um dos ficheiros de configuração modificados e verifique se as definições de mencionadas no que aconteceu estão lá. Por exemplo, a Web. config principal num projeto ASP.NET MVC terão estas definições adicionadas:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## <a name="how-your-project-is-modified"></a>Como o seu projeto é modificado

Quando executa o assistente, o Visual Studio adiciona Azure AD e associados referências ao seu projeto. Ficheiros de configuração e ficheiros de código no seu projeto também são modificados para adicionar suporte para Azure AD. As modificações específicas que Visual Studio faz com que variam consoante o tipo de projecto. Para obter informações detalhadas sobre como projetos ASP.NET MVC são modificados, consulte o artigo [o que aconteceu – MVC projetos](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para os projectos Web API, consulte o artigo [o que aconteceu – Web API projetos](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##<a name="next-steps"></a>Próximos passos

Colocar questões e obter ajuda.

 - [Fórum MSDN: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentação do Azure AD](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Mensagem de blogue: Introdução ao Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

