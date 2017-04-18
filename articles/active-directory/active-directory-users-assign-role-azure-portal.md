<properties
    pageTitle="Atribuir um utilizador para funções de administrador na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Explica como alterar as informações do utilizador administrativas no Azure Active Directory"
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

# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory-preview"></a>Atribuir um utilizador para funções de administrador na pré-visualização do Azure Active Directory

Este artigo explica como atribuir uma função administrativa a um utilizador na pré-visualização do Azure Active Directory (Azure AD). [Qual é a pré-visualização?](active-directory-preview-explainer.md) Para obter informações sobre a adição de novos utilizadores na sua organização, consulte o artigo [Adicionar novos utilizadores para o Azure Active Directory](active-directory-users-create-azure-portal.md). Utilizadores adicionados não tem permissões de administrador por predefinição, mas pode atribuir funções aos mesmos em qualquer altura.

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um utilizador

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

    ![Gestão de utilizadores de abertura](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3.  No pá **utilizadores e grupos** , selecione **todos os utilizadores**.

    ![Abrir a todos os pá de utilizadores](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)

4. No pá **utilizadores e grupos - todos os utilizadores** , selecione um utilizador a partir da lista.

5. No pá para o utilizador selecionado, selecione **a função de diretório**e, em seguida, atribuir o utilizador a uma função a partir da lista de **funções de diretório** . Para mais informações sobre as funções de utilizador e administrador, consulte [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

      ![Atribuir um utilizador a uma função](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

6. Selecione **Guardar**.


## <a name="whats-next"></a>O que se segue

- [Adicionar um utilizador](active-directory-users-create-azure-portal.md)
- [Repor palavra-passe de um utilizador no novo portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Alterar as informações de trabalho de um utilizador](active-directory-users-work-info-azure-portal.md)
- [Gerir perfis de utilizador](active-directory-users-profile-azure-portal.md)
- [Eliminar um utilizador no seu Azure AD](active-directory-users-delete-user-azure-portal.md)
