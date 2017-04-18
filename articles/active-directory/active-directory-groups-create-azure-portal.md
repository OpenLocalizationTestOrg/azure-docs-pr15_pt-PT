<properties
    pageTitle="Criar um novo grupo na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Como criar um grupo no Azure Active Directory e adicionar utilizadores (membros) ao grupo"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Criar um novo grupo na pré-visualização do Azure Active Directory

> [AZURE.SELECTOR]
- [Portal do Azure](active-directory-groups-create-azure-portal.md)
- [Azure portal clássico](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

Este artigo explica como criar e preencher um novo grupo no pré-visualização do Azure Active Directory (Azure AD). [Qual é a pré-visualização?](active-directory-preview-explainer.md) Utilize um grupo para efetuar tarefas de gestão, tais como atribuir permissões de licenças ou a um número de utilizadores ou dispositivos ao mesmo tempo.

## <a name="how-do-i-create-a-group"></a>Como posso criar um grupo?

1. Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2. Selecione **mais serviços**, introduza o **utilizador e grupos** na caixa de texto e, em seguida, selecione **Enter**.

  ![Gestão de utilizadores de abertura](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. No pá **utilizadores e grupos** , selecione **todos os grupos**.

  ![Abrir o pá de grupos](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. No pá **utilizadores e grupos - todos os grupos** , selecione o comando **Adicionar** .

  ![Selecionar o comando Adicionar](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. No pá **grupo** , adicione um nome e descrição para o grupo.

6. Para selecionar os membros de adicionar ao grupo, selecione **atribuído** na caixa **tipo de associação** e, em seguida, selecione **Membros**. Para mais informações sobre como gerir dinamicamente a associação de um grupo, consulte o artigo [atributos de utilizar para criar regras avançadas para membros do grupo](active-directory-groups-dynamic-membership-azure-portal.md).

  ![Selecionar Membros para adicionar](./media/active-directory-groups-create-azure-portal/select-members.png)

5. No pá **Membros** , selecione um ou mais utilizadores ou dispositivos para adicionar ao grupo e selecione o botão **Selecione** na parte inferior da pá adicioná-los ao grupo. Caixa de **utilizador** filtra a apresentação com base na sua entrada a qualquer parte de um nome de utilizador ou dispositivo de correspondência. São aceites sem carateres universais na caixa de diálogo.

6. Quando terminar de adicionar membros ao grupo, selecione **Criar** no pá **grupo** .    

  ![Criar a confirmação de grupo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Consulte o artigo grupos existentes](active-directory-groups-view-azure-portal.md)
* [Gerir definições de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerir membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerir membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas para utilizadores num grupo](active-directory-groups-dynamic-membership-azure-portal.md)
