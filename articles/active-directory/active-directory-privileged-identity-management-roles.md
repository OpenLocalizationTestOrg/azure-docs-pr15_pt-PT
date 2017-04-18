<properties
   pageTitle="Funções no PIM | Microsoft Azure"
   description="Saiba quais as funções são utilizadas para identidades privilegiadas com a extensão de gestão de identidades privilegiados Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Funções de gestão de identidades do Azure AD privilegiados

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Pode atribuir utilizadores na sua organização para diferentes funções administrativas no Azure AD. Estas atribuições de funções controlam quais as tarefas, tal como adicionar ou remover utilizadores ou alterar as definições de serviço, os utilizadores podem executar no Azure AD, Office 365 e outros serviços Online da Microsoft e aplicações ligadas.  

Um administrador global pode atualizar os utilizadores que são **permanentemente** atribuídas a funções no Azure AD, tais como a utilizar os cmdlets do PowerShell `Add-MsolRoleMember` e `Remove-MsolRoleMember`, ou através do portal do clássico como descrito em [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

Gestão de identidades do Azure AD privilegiados (PIM) gere políticas de acesso privilegiado para utilizadores no Azure AD. PIM atribui utilizadores a um ou mais funções no Azure AD e pode atribuir a alguém a ser permanentemente no papel, ou elegível para a função. Quando um utilizador permanentemente está atribuído a uma função ou activa uma atribuição de funções elegível, em seguida, pode gerir Azure Active Directory, Office 365 e outras aplicações com as permissões atribuídas a suas funções.

Não existe nenhuma diferença no access atribuído a alguém com permanente versus uma atribuição de funções elegível. A única diferença é que algumas pessoas não precisam de que o access sempre. São efetuadas elegíveis para a função e pode a ativar e desativar sempre que necessário.

## <a name="roles-managed-in-pim"></a>Funções geridas no PIM

Gestão de identidades privilegiados permite-lhe atribuir utilizadores a funções de administrador comuns, incluindo:


- **Administrador global** (também conhecido como administrador da empresa) tem acesso a todas as funcionalidades administrativas. Pode ter mais do que um administrador global na sua organização. A pessoa que se inscreve para comprar o Office 365 automaticamente torna-se um administrador global.
- **Função privilegiados administrador** gere Azure AD PIM e atualiza as atribuições de funções para outros utilizadores.  
- **Administrador de faturação:** efetua compras, gere subscrições, gere pedidos de suporte e monitoriza o funcionamento de serviço.
- **Administrador de palavra-passe** repõe as palavras-passe, gere pedidos de serviço e monitoriza o funcionamento de serviço. Administradores de palavra-passe estão limitados a repor palavras-passe para os utilizadores.
- **Administrador de serviços** gere os pedidos de serviço e estado de funcionamento do serviço de monitores.

  > [AZURE.NOTE] Se estiver a utilizar o Office 365, em seguida, antes de atribuir a função de administrador do serviço a um utilizador, pela primeira vez atribua ao utilizador permissões administrativas para um serviço, como o Exchange Online.

- **Administrador de gestão de utilizadores** repõe as palavras-passe, monitoriza o funcionamento de serviço e gere pedidos de serviço, grupos de utilizadores e contas de utilizador. O administrador de gestão de utilizadores não é possível eliminar um administrador global, criar outras funções de administrador ou repor palavras-passe e de faturação, global, administradores de serviços.
- **Administrador do Exchange** tem acesso administrativo ao Exchange Online através do Centro de administração do Exchange (EAC) e pode executar quase todas as tarefas no Exchange Online.
- **Administrador do SharePoint** tem acesso administrativo ao SharePoint Online através do Centro de administração do SharePoint Online e pode executar quase todas as tarefas no SharePoint Online.
- **Administrador de negócio do Skype para** tem acesso administrativo ao Skype para empresas através do Skype para o Centro de administração empresas e pode executar quase todas as tarefas no Skype para empresas Online.

Consultar estes artigos para obter mais detalhes sobre como [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md) e [atribuir funções de administrador no Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


A partir de PIM, pode [atribuir estas funções de administrador a um utilizador](active-directory-privileged-identity-management-how-to-add-role-to-user.md) para que o utilizador pode [Ativar a função quando for necessário](active-directory-privileged-identity-management-how-to-activate-role.md).

Se pretende conceder acesso do utilizador outro para gerir a PIM propriamente dito, as funções que PIM requer o utilizador tenha estão ainda mais descritas no [artigo como dar acesso aos PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Funções não geridas no PIM

Funções no Exchange Online ou do SharePoint Online, exceto os mencionados acima, não são representadas no Azure AD e por isso, não são visíveis no PIM. Para mais informações sobre como alterar atribuições de funções extensivamente nestes serviços do Office 365, consulte o artigo [permissões no Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Subscrições do Azure e grupos de recursos também são representados não no Azure AD. Para gerir subscrições Azure, consulte o artigo [como adicionar ou alterar funções de administrador Azure](../billing-add-change-azure-subscription-administrator.md) e para obter mais informações sobre Azure RBAC consulte [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Funções de utilizador e iniciar sessão
Para algumas aplicações e serviços da Microsoft, atribuir um utilizador a uma função poderão não ser suficiente para permitir que esse utilizador de ser um administrador.

Aceder ao portal clássico Azure requer que o utilizador ser um administrador de serviços ou administrador cocriação numa subscrição do Azure, mesmo se o utilizador não necessárias para gerir as subscrições Azure.  Por exemplo, para gerir as definições de configuração para Azure AD no portal do clássico, um utilizador tem de ser administrador global no Azure AD e um administrador de cocriação de subscrição numa subscrição do Azure.  Para saber como adicionar utilizadores ao Azure subscrições, consulte o artigo [como adicionar ou alterar funções de administrador Azure](../billing-add-change-azure-subscription-administrator.md).

Acesso ao Microsoft Online Services precisem do utilizador também ser atribuída uma licença antes de poderem abra o portal do serviço ou desempenhar tarefas administrativas.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Atribuir uma licença a um utilizador no Azure AD

1. Inicie sessão no [portal Azure clássico] (http://manage.windowsazure.com) com uma conta de administrador global ou de uma conta de administrador cocriação.
2. Selecione **Todos os itens** a partir do menu principal.
3. Selecione a pasta que pretende trabalhar e que tem licenças associadas.
4. Selecione **licenças**. A lista de licenças disponíveis irão aparecer.
5. Selecione o plano de licenças que contém as licenças que pretende distribuir.
6. Selecione **atribuir aos utilizadores**.
7. Selecione o utilizador ao qual pretende atribuir a licença.
8. Clique no botão **atribuir** .  Agora, o utilizador pode iniciar sessão para Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
