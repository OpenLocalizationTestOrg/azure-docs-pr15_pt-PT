<properties
   pageTitle="Como conceder acesso a PIM | Microsoft Azure"
   description="Saiba como adicionar funções para utilizadores com a extensão do Azure Active Directory privilegiados gestão de identidades para que possam gerir PIM."
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
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>Como dar às acesso para gerir a gestão de identidades privilegiados do Azure AD

O administrador global, que ativa automaticamente o Azure AD privilegiados gestão de identidades (PIM) para uma organização obter acesso PIM e atribuições de funções. Ninguém obtém acesso de escrita por predefinição, no entanto, incluindo outros administradores globais. Outros administradores globais, administradores de segurança e leitores de segurança têm acesso só de leitura para Azure AD PIM. Para conceder acesso a PIM, o primeiro utilizador pode atribuir as outras pessoas para a função de **administrador de função privilegiados** . Desta atribuição deve ser feita dentro PIM propriamente dito e não pode ser alterada através do PowerShell ou outros portais.

> [AZURE.NOTE] Gerir Azure AD PIM requer o Azure MFA. Uma vez que não é possível registar contas do Microsoft Azure MFA, não é possível aceder a um utilizador que inicia sessão com uma conta Microsoft Azure AD PIM.

Certifique-se existirem sempre, pelo menos, dois utilizadores numa função de administrador função privilegiados, no caso de um utilizador está bloqueado ou respetiva conta é eliminada.

## <a name="give-another-user-access-to-manage-pim"></a>Conceder acesso de utilizador de outro para gerir PIM

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e selecione a aplicação de **Gestão de identidades do Azure AD privilegiados** no dashboard.
2. Selecione **Gerir funções privilegiadas** > **administrador função privilegiados** > **Adicionar**.

    ![Adicionar administradores de função privilegiados - captura de ecrã][1]

4. No pá de utilizadores geridos adicionar, o passo 1 já está concluído. Selecione o passo 2, **selecione utilizadores** e pesquisa para o utilizador que pretende adicionar.

    ![Selecione utilizadores - captura de ecrã][2]

6. Selecione o utilizador nos resultados da pesquisa e clique em **concluído**.
7. Clique em **OK** para guardar a sua seleção. O utilizador que tiver selecionado irão aparecer na lista de administradores de função privilegiados.

    - Sempre que atribuir a alguém uma nova função, estes são automaticamente configurados como elegíveis para ativar a função. Se quiser torná-las permanentes na função, clique no utilizador na lista. Selecione **fazer permissão** no menu de informações do utilizador.

8. Envie uma ligação ao utilizador para a [Introdução à gestão de identidades do Azure AD privilegiados](active-directory-privileged-identity-management-getting-started.md).


## <a name="remove-another-users-access-rights-for-managing-pim"></a>Remover direitos de acesso de outro utilizador para gerir PIM

Antes de remover alguém da função de administrador de função privilegiados, certifique-se sempre haverá ainda dois utilizadores atribuídos à mesma.

1. No dashboard de PIM, clique na função de **administrador de função privilegiados**.  Será apresentada a lista de utilizadores atualmente nessa função.
2. Clique no utilizador na lista de utilizadores.
3. Clique em **Remover**.  São apresentadas com uma mensagem de confirmação.
4. Clique em **Sim** para remover o utilizador da função.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
