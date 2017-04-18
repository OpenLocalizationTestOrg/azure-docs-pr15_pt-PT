<properties
    pageTitle="Adicionar utilizadores a partir de outros directórios ou empresas na pré-visualização do Azure Active Directory do parceiro | Microsoft Azure"
    description="Explica como adicionar utilizadores ou alterar as informações de utilizador no Azure Active Directory, incluindo os utilizadores externos e de convidado."
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

# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory-preview"></a>Adicionar utilizadores a partir de outras empresas de parceiro na pré-visualização do Azure Active Directory ou directórios

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-users-create-external-azure-portal.md)
- [Azure portal clássico](active-directory-create-users-external.md)

Este artigo explica como adicionar utilizadores a partir de outros directórios na pré-visualização do Azure Active Directory (Azure AD) ou de empresas de parceiro. [Qual é a pré-visualização?](active-directory-preview-explainer.md) Para obter informações sobre a adição de novos utilizadores na sua organização e a adição de utilizadores que tenham contas do Microsoft, consulte o artigo [Adicionar novos utilizadores para o Azure Active Directory](active-directory-users-create-azure-portal.md). Utilizadores adicionados não tem permissões de administrador por predefinição, mas pode atribuir funções aos mesmos em qualquer altura.

## <a name="add-a-user"></a>Adicionar um utilizador

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

    ![Gestão de utilizadores de abertura](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  No pá **utilizadores e grupos** , selecione **utilizadores**e, em seguida, selecione **Adicionar**.

    ![Selecionar o comando Adicionar](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. Na pá **utilizador** , forneça um nome a apresentar no **nome** ou o nome do utilizador iniciar sessão em **nome de utilizador**.

5. Copie ou anote caso contrário, a palavra-passe de utilizador gerado para que pode fornecê-lo ao utilizador após concluir este processo.

6. Em alternativa, selecione o **perfil** para adicionar os utilizadores primeiro e último nome, um cargo e um nome de departamento.
    
    ![Abrir o perfil de utilizador](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

    - Selecione **grupos** para adicionar o utilizador a um ou mais grupos.

        ![Adicionar um utilizador a grupos](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

    - Selecione a **função organizacional** para atribuir ao utilizador para uma função a partir da lista de **funções** . Para mais informações sobre as funções de utilizador e administrador, consulte [atribuir funções de administrador no Azure AD](active-directory-assign-admin-roles.md).

        ![Atribuir um utilizador a uma função](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. Selecione **Criar**.

8. Em segurança distribua a palavra-passe gerada para o novo utilizador para que o utilizador pode iniciar sessão.

> [AZURE.IMPORTANT] Se a sua organização utiliza mais do que um domínio, que devem saber sobre os seguintes problemas ao adicionar uma conta de utilizador:
>
> - Para adicionar contas de utilizador com o mesmo nome principal de utilizador (UPN) em domínios, **primeiro** adicionar, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido** geoffgrisso@contoso.com.
> - **Não** adicionar geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Por esta ordem é importante e pode ser complicado para anular.

Se alterar as informações para um utilizador cuja identidade é sincronizada com o seu serviço de Active Directory no local, não pode alterar as informações do utilizador no portal do Azure clássica. Para alterar as informações do utilizador, utilize as ferramentas de gestão de Active Directory no local.


## <a name="whats-next"></a>O que se segue

- [Adicionar um utilizador](active-directory-users-create-azure-portal.md)
- [Repor palavra-passe de um utilizador no novo portal do Azure](active-directory-users-reset-password-azure-portal.md)
- [Atribuir um utilizador a uma função no seu Azure AD](active-directory-users-assign-role-azure-portal.md)
- [Alterar as informações de trabalho de um utilizador](active-directory-users-work-info-azure-portal.md)
- [Gerir perfis de utilizador](active-directory-users-profile-azure-portal.md)
- [Eliminar um utilizador no seu Azure AD](active-directory-users-delete-user-azure-portal.md)
