<properties
    pageTitle="Gerir grupos do seu grupo é um membro na pré-visualização do Azure Active Directory | Microsoft Azure"
    description="Os grupos podem conter outros grupos no Azure Active Directory. Eis como gerir esses membros."
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


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>Gerir grupos do que seu grupo é um membro na pré-visualização do Azure Active Directory

Os grupos podem conter outros grupos na pré-visualização do Azure Active Directory. [Qual é a pré-visualização?](active-directory-preview-explainer.md) Eis como gerir esses membros.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Como posso encontrar os grupos que meu grupo é membro?

1.  Inicie sessão no [Azure portal](https://portal.azure.com) com uma conta que é um administrador global para o diretório.

2.  Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

  ![Gestão de utilizadores de abertura](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  No pá **utilizadores e grupos** , selecione **todos os grupos**.

  ![Abrir o pá de grupos](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. No pá **utilizadores e grupos - todos os grupos** , selecione um grupo.

5. No * *grupo - *nome de grupo* ** pá, selecione **agrupe associações * *.

  ![Abrir o pá de membros do grupo](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. Para adicionar o seu grupo como um membro do grupo outro, no pá **grupo - os membros do grupo** , selecione o comando **Adicionar** .

7. Selecione um grupo a partir do pá **Seleccionar grupo** e, em seguida, selecione o botão **Selecione** na parte inferior da pá. Pode adicionar o seu grupo apenas a um grupo uma vez. Caixa de **utilizador** filtra a apresentação com base na sua entrada a qualquer parte de um nome de utilizador ou dispositivo de correspondência. São aceites sem carateres universais na caixa de diálogo.

  ![Adicionar uma associação a grupos](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. Para remover o seu grupo como um membro do grupo outro, no pá **grupo - os membros do grupo** , selecione um grupo.

9. No pá ***nome de grupo*** , selecione o comando **Remover** e confirmar a sua escolha na linha de comandos.

  ![remover o comando de associação](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. Quando terminar de alterar membros do grupo para o seu grupo, selecione **Guardar**.


## <a name="additional-information"></a>Informações adicionais

Estes artigos fornecem informações adicionais sobre o Azure Active Directory.

* [Consulte o artigo grupos existentes](active-directory-groups-view-azure-portal.md)
* [Criar um novo grupo e adicionar membros](active-directory-groups-create-azure-portal.md)
* [Gerir definições de um grupo](active-directory-groups-settings-azure-portal.md)
* [Gerir membros de um grupo](active-directory-groups-members-azure-portal.md)
* [Gerir regras dinâmicas para utilizadores num grupo](active-directory-groups-dynamic-membership-azure-portal.md)
