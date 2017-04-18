<properties
   pageTitle="Como adicionar ou remover uma função de utilizador | Microsoft Azure"
   description="Saiba como adicionar funções a identidades privilegiadas com a aplicação do Azure Active Directory privilegiados identidade Management."
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
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD privilegiados gestão de identidades: Como adicionar ou remover uma função de utilizador

Com o Azure Active Directory (AD), um administrador global (ou administrador da empresa) pode atualizar os utilizadores que são **permanentemente** atribuídas a funções no Azure AD. Esta é executada com os cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`. Ou pode utilizar o portal clássico Azure tal como descrito em [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

A aplicação de gestão de identidades do Azure AD privilegiados permite aos administradores da função privilegiados tornar permanente as atribuições de funções, também. Para além disso, os administradores de função privilegiados podem fazer utilizadores **elegível** para funções de administrador. Um administrador elegível pode ativar a função quando precisam-lo e, em seguida, as respetivas permissões expirarem uma vez que terminar.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Gerir funções com PIM no portal do Azure

Na sua organização, pode atribuir utilizadores a diferentes funções administrativas no Azure AD, Office 365 e outros serviços Microsoft e aplicações.  Obter mais detalhes sobre as funções disponíveis podem ser encontrados em [funções no Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Para adicionar ou remover um utilizador numa função de utilizar a gestão de identidades privilegiados, aceda a dashboard PIM. Em seguida, clique no botão de **utilizadores no funções de administrador** ou selecione uma função específica (como Administrador Global) a partir da tabela de funções.

> [AZURE.NOTE] Se ainda não o tenha ativado PIM no portal do Azure ainda, aceda a [Introdução ao Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) para obter detalhes.

Se pretende conceder acesso do utilizador outra para PIM próprio, as funções que PIM requer o utilizador tenha estão ainda mais descritas no [artigo como dar acesso aos PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Adicionar um utilizador a uma função

1. No [portal do Azure](https://portal.azure.com/), selecione o mosaico do **Azure AD privilegiados gestão de identidades** no dashboard.
2. Selecione **Gerir funções privilegiadas**.
3. Na tabela de **função de resumo** , selecione a função que pretende gerir.
4. No pá função, selecione **Adicionar**.
5. Clique em pesquisa para o utilizador na pá **selecione utilizadores** e **selecione utilizadores** .  
6. Selecione o utilizador a partir da lista de resultados de pesquisa e clique em **concluído**.
4. Clique em **OK** para guardar a sua seleção. O utilizador que tiver selecionado irão aparecer na lista como elegível para a função.

> [AZURE.NOTE]
>Novos utilizadores em função só são elegíveis para a função por predefinição. Se quiser tornar a função permanentes, clique no utilizador na lista. As informações do utilizador serão apresentados na pá novo. Selecione **fazer permissão** no menu de informações do utilizador.  
>Se um utilizador não é possível registar para a autenticação do Azure Multifator (MFA) ou está a utilizar uma conta Microsoft (normalmente @outlook.com), precisa para torná-las permanentes em todos os seus direitos. Administradores elegíveis-lhe pedidos para se registar no MFA durante a ativação.

Agora que o utilizador for elegível para uma função, informe de que estes podem ativá-lo de acordo com as instruções no [artigo como ativar ou desativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Remover um utilizador a partir de uma função

Pode remover utilizadores de atribuições de funções elegível, mas certifique-se de é sempre pelo menos um utilizador que é um administrador global permanente.

Siga estes passos para remover um utilizador específico de uma função:

1. Navegue para a função na lista de funções ao selecionar uma função no dashboard do Azure AD PIM ou ao clicar no botão de **utilizadores no funções de administrador** .
2. Clique no utilizador na lista de utilizadores.
3. Clique em **Remover**. Uma mensagem irá pedir para confirmar.
4. Clique em **Sim** para remover a função do utilizador.

Se não tiver a certeza que os utilizadores ainda precisa das respetivas atribuições de funções, em seguida, pode [Iniciar uma revisão de acesso para a função](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
