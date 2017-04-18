<properties
    pageTitle="Desativar suplementos utilizador de início de sessão para uma aplicação do enterprise na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Como desativar uma aplicação empresarial para que os utilizadores não podem iniciar sessão para o mesmo no Azure Active Directory"
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
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Desativar suplementos utilizador de início de sessão para uma aplicação do enterprise na pré-visualização do Azure Active Directory

É fácil desativar uma aplicação empresarial para que os utilizadores não podem iniciar sessão para a mesma na pré-visualização do Azure Active Directory (Azure AD). [Qual é a pré-visualização?](active-directory-preview-explainer.md) Tem de ter as permissões adequadas para gerir a aplicação de empresa. Na pré-visualização atual, tem de ser administrador global para o diretório.

## <a name="how-do-i-disable-user-sign-ins"></a>Como desativo os suplementos de início de sessão do utilizador?

1. Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2. Selecione **mais serviços**, introduza o **Azure Active Directory** na caixa de texto e, em seguida, selecione **Enter**.

3. No * *Azure Active Directory - *Nome_do_directório* ** pá (ou seja, o Azure AD pá para o directório está a gerir), selecione **Enterprise aplicações * *.

    ![Aplicações empresariais de abertura](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. No pá **aplicações empresariais** , selecione **todas as aplicações**. Verá uma lista de aplicações, pode gerir.

5. No pá **aplicações empresariais - todas as aplicações** , selecione uma aplicação.

6. No pá de ***nome*** (ou seja, o pá com o nome da aplicação selecionado no título), selecione **Propriedades**.

    ![Selecionar o comando aplicações tudo](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. Sobre o ***nome*** **-Propriedades** pá, selecione **não** para **ativadas para os utilizadores iniciar sessão?**.

8. Selecione o comando **Guardar** .

## <a name="next-steps"></a>Próximos passos

- [Ver todos os meus grupos](active-directory-groups-view-azure-portal.md)
- [Atribuir um utilizador ou grupo a uma aplicação empresarial](active-directory-coreapps-assign-user-azure-portal.md)
- [Remover uma atribuição de utilizador ou grupo a partir de uma aplicação empresarial](active-directory-coreapps-remove-assignment-azure-portal.md)
- [Alterar o nome ou o logótipo de uma aplicação empresarial](active-directory-coreapps-change-app-logo-user-azure-portal.md)
