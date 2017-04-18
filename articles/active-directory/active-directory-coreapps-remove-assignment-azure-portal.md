<properties
    pageTitle="Remover uma atribuição de utilizador ou grupo a partir de uma aplicação do enterprise na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Como remover a atribuição de acesso de um utilizador ou grupo a partir de uma aplicação do enterprise no Azure Active Directory"
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
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Remover um utilizador ou atribuição de grupo a partir de uma aplicação do enterprise na pré-visualização do Azure Active Directory

É fácil remover um utilizador ou um grupo de que está a ser atribuído o acesso a uma das suas aplicações de empresa na pré-visualização do Azure Active Directory (Azure AD). [Qual é a pré-visualização?](active-directory-preview-explainer.md) Tem de ter as permissões adequadas para gerir a aplicação de empresa. Na pré-visualização atual, tem de ser administrador global para o diretório.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Como remover um utilizador ou atribuição de grupo?

1. Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2. Selecione **mais serviços**, introduza o **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.

3. No * *Azure Active Directory - *Nome_do_directório* ** pá (ou seja, o Azure AD pá para o directório está a gerir), selecione **Enterprise aplicações * *.

    ![Aplicações empresariais de abertura](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)

4. No pá **aplicações empresariais** , selecione **todas as aplicações**. Verá uma lista de aplicações, pode gerir.

5. No pá **aplicações empresariais - todas as aplicações** , selecione uma aplicação.

6. No pá de ***nome*** (ou seja, o pá com o nome da aplicação selecionado no título), selecione **utilizadores e grupos**.

    ![Selecionar utilizadores ou grupos](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)

7. Sobre o ***nome*** **-utilizador e grupo atribuição** pá, selecione uma das mais utilizadores ou grupos e, em seguida, selecione o comando **Remover** . Confirme a sua decisão na linha de comandos.

    ![Selecionar o comando remover](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Próximos passos

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Desativar suplementos utilizador de início de sessão para uma aplicação do enterprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Alterar o nome ou o logótipo de uma aplicação empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
