<properties
    pageTitle="Adicionar novos utilizadores a pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Explica como adicionar novos utilizadores ou alterar as informações de utilizador no Azure Active Directory."
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


# <a name="add-new-users-to-azure-active-directory-preview"></a>Adicionar novos utilizadores a pré-visualização do Azure Active Directory

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-users-create-azure-portal.md)
- [Azure portal clássico](active-directory-create-users.md)

Este artigo explica como adicionar novos utilizadores na sua organização na pré-visualização do Azure Active Direstory (Azure AD). [Qual é a pré-visualização?](active-directory-preview-explainer.md)

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

    ![Gestão de utilizadores de abertura](./media/active-directory-users-create-azure-portal/create-users-user-management.png)

3.  No pá **utilizadores e grupos** , selecione **todos os utilizadores**e, em seguida, selecione **Adicionar**.

    ![Selecionar o comando Adicionar](./media/active-directory-users-create-azure-portal/create-users-add-command.png)

4.  Introduza os detalhes do utilizador, como o **nome** e o **nome de utilizador**. A parte do nome de domínio do nome de utilizador tem de ser o nome de domínio do nome de domínio "foo.onmicrosoft.com" predefinido ou um nome de domínio não Federado de verificado, tal como "contoso.com."

5. Copie ou anote caso contrário, a palavra-passe de utilizador gerado para que pode fornecê-lo ao utilizador após concluir este processo.

6. Opcionalmente, pode abrir e preencha as informações no pá o **perfil** , pá a **grupos** ou a **função de diretório** pá para o utilizador. Para mais informações sobre as funções de utilizador e administrador, consulte [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

7.  No pá **utilizador** , selecione **Criar**.

8. Em segurança distribua a palavra-passe gerada para o novo utilizador para que o utilizador pode iniciar sessão.

## <a name="whats-next"></a>O que se segue

- [Adicionar um utilizador externo](active-directory-users-create-external-azure-portal.md)
- [Repor palavra-passe de um utilizador no novo portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Alterar as informações de trabalho de um utilizador](active-directory-users-work-info-azure-portal.md)
- [Gerir perfis de utilizador](active-directory-users-profile-azure-portal.md)
- [Eliminar um utilizador no seu Azure AD](active-directory-users-delete-user-azure-portal.md)
- [Atribuir um utilizador a uma função no seu Azure AD](active-directory-users-assign-role-azure-portal.md)
