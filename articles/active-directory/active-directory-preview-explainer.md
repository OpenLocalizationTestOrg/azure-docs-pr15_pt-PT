<properties
    pageTitle="Explainer de pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Um tópico que explica as diferenças entre o Azure Active Directory no portal do clássico e pré-visualização do Azure Active Directory no portal do Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Pré-visualização da experiência de gestão do Azure Active Directory no portal do Azure

A experiência de gestão do Azure Active Directory (Azure AD) é na pré-visualização no portal do Azure. Que pode experimentar-out ao iniciar sessão no [portal do Azure](https://portal.azure.com) como um administrador global do seu diretório. Em seguida, selecione Azure Active Directory na lista de serviços se estiver visível, ou selecione **mais serviços** para ver a lista de todos os serviços. Não necessita de uma subscrição do Azure para utilizar o Azure uma experiência de gestão de AD no portal do Azure.


## <a name="capabilities-of-the-preview-experience"></a>Capacidades da experiência do pré-visualização

A experiência de pré-visualização permite-lhe gerir muitos diretório recursos, tais como os utilizadores, grupos e aplicações, bem como as definições de diretório, no portal do Azure. Estamos a melhorar esta experiência para incluir todas as funcionalidades que existam no Azure uma experiência de gestão de AD no [Azure portal clássica](https://manage.windowsazure.com). Até lá, existem algumas gestão de diretório concluir tarefas que ainda tem estiver no portal do clássico.

## <a name="manage-the-same-azure-ad-tenants"></a>Gerir os mesmo inquilinos do Azure AD

A experiência de pré-visualização lê e escreve ao mesmo inquilino Azure Active Directory como o portal clássico e o Centro de administração do Office 365. As alterações efetuadas em qualquer um dos seguintes portais refletem-se em todas as outras pessoas.

## <a name="use-the-same-authorization-logic"></a>Utilizar a mesma lógica de autorização

A experiência de pré-visualização utiliza a mesma lógica de autorização como clientes do Active Directory existentes. Os utilizadores estejam autorizados a efetuar alterações a recursos de diretório com base em função do diretório, tais como administrador global, o administrador do utilizador, o administrador de palavra-passe. Está a ter uma função no Azure recursos ou uma subscrição do Azure não autoriza um utilizador para gerir os recursos de diretório. Para obter mais informações funções de gestão do Azure AD, consulte o artigo [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md). 

A experiência de pré-visualização está optimizada para os administradores globais. Se utilizar a experiência de pré-visualização enquanto a sessão iniciada como um utilizador que não seja um administrador global, poderá ter uma experiência degradada. Por exemplo, poderá ser possível selecionar um botão que permite-lhe iniciar uma tarefa que não conseguir concluir no diretório. Estamos são melhorar esta experiência mais rapidamente.
 
## <a name="tell-us-what-you-think"></a>Dê-nos opinião

Pode fornecer comentários sobre a experiência de pré-visualizar na secção portal de administração do [Fórum de comentários do Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).
