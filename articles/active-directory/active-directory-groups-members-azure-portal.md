<properties
    pageTitle="Gerir membros de um grupo na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Como os utilizadores e dispositivos que são membros de um grupo no Azure Active Directory"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>Gerir membros de um grupo na pré-visualização do Azure Active Directory

Este artigo explica como gerir os membros de um grupo na pré-visualização do Azure Active Directory (Azure AD). [Qual é a pré-visualização?](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>Como encontrar os membros e geri-los?

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

  ![Gestão de utilizadores de abertura](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  No pá **utilizadores e grupos** , selecione **todos os grupos**.

  ![Abrir o pá de grupos](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. No pá **utilizadores e grupos - todos os grupos** , selecione um grupo.

5. No * *grupo - *nome de grupo* ** pá, selecione **membros * *.

  ![Abrir o pá membros](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Para adicionar membros ao grupo, no pá **grupo - Membros** , selecione **Adicionar membros**.

  ![Adicionar membros comando](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. No pá **Membros** , selecione um ou mais utilizadores ou dispositivos para adicionar ao grupo e selecione o botão **Selecione** na parte inferior da pá adicioná-los ao grupo. Caixa de **utilizador** filtra a apresentação com base na sua entrada a qualquer parte de um nome de utilizador ou dispositivo de correspondência. São aceites sem carateres universais na caixa de diálogo.

8. Para remover membros do grupo, no pá **grupo - Membros** , selecione um membro.

9. No pá ***Membro*** , selecione o comando **Remover** e confirmar a sua escolha na linha de comandos.

  ![remover o comando de membros](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Quando terminar de alterar os membros do grupo, selecione **Guardar**.


## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Consulte o artigo grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerir membros de um grupo](active-directory-groups-membership-azure-portal.md)
* [Gerir regras dinâmicas para utilizadores num grupo](active-directory-groups-dynamic-membership-azure-portal.md)
