<properties
    pageTitle="Atribuir um utilizador ou grupo a uma aplicação do enterprise na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Como selecionar uma aplicação do enterprise para atribuir um utilizador ou grupo ao mesmo Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Atribuir um utilizador ou grupo a uma aplicação do enterprise na pré-visualização do Azure Active Directory

É fácil atribuir um utilizador ou um grupo para as suas aplicações de empresa na pré-visualização do Azure Active Directory (Azure AD). [Qual é a pré-visualização?](active-directory-preview-explainer.md) Tem de ter as permissões adequadas para gerir a aplicação de empresa. Na pré-visualização atual, tem de ser administrador global para o diretório.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Como posso atribuir acesso do utilizador para uma aplicação do enterprise?

1. Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2. Selecione **mais serviços**, introduza o Azure Active Directory na caixa de texto e, em seguida, selecione **Enter**.

3. No * *Azure Active Directory - *Nome_do_directório* ** pá (ou seja, o Azure AD pá para o directório está a gerir), selecione **Enterprise aplicações * *.

    ![Aplicações empresariais de abertura](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. No pá **aplicações empresariais** , selecione **todas as aplicações**. Verá uma lista de aplicações, pode gerir.

5. No pá **aplicações empresariais - todas as aplicações** , selecione uma aplicação.

6. No pá de ***nome*** (ou seja, o pá com o nome da aplicação selecionado no título), selecione **utilizadores e grupos**.

    ![Selecionar o comando aplicações tudo](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. Sobre o ***nome*** **-utilizador e grupo atribuição** pá, selecione o comando **Adicionar** .

8. No pá **Adicionar atribuição** , selecione **utilizadores e grupos**.

    ![Atribuir um utilizador ou grupo para a aplicação](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. No pá **utilizadores e grupos** , selecione um ou mais utilizadores ou grupos a partir da lista e, em seguida, selecione o botão **Selecione** na parte inferior da pá.

10. No pá **Adicionar atribuição** , selecione a **função**. Em seguida, no pá **Selecione função** , selecione uma função para aplicar aos utilizadores selecionados ou grupos e, em seguida, selecione o botão **OK** na parte inferior da pá.

11. No pá **Adicionar atribuição** , selecione o botão **atribuir** na parte inferior da pá. Os utilizadores atribuídos ou grupos terá as permissões definidas pela função selecionada para esta aplicação empresarial.

## <a name="next-steps"></a>Próximos passos

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Desativar suplementos utilizador de início de sessão para uma aplicação do enterprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Alterar o nome ou o logótipo de uma aplicação empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
