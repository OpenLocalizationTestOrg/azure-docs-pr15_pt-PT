<properties
    pageTitle="Ver as atribuições de acesso de recursos Azure | Microsoft Azure"
    description="Ver e gerir todas as atribuições de controlo de acesso baseado em funções para qualquer utilizador ou grupo no portal do Azure"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="jeffsta"/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Ver as atribuições de acesso de utilizadores e grupos no portal do Azure - pré-visualização do público

> [AZURE.SELECTOR]
- [Gerir o acesso ao utilizador ou grupo](role-based-access-control-manage-assignments.md)
- [Gerir o acesso por recurso](role-based-access-control-configure.md)

Com baseado em funções acesso controlo RBCA () na pré-visualização do Azure Active Directory, pode gerir o acesso aos recursos Azure. [Qual é a pré-visualização?](active-directory-preview-explainer.md)

Acesso atribuído com RBAC é extensivamente porque podem restringir as permissões de duas formas:

- **Âmbito:** Atribuições de funções RBAC estão limitadas para uma subscrição específico, grupo de recursos ou recurso. Um utilizador concedido acesso a um único recurso não consegue aceder a quaisquer outros recursos na mesma subscrição.
- **Função:** No âmbito da atribuição, o access é reduzido ainda mais detalhada ao atribuir uma função. Podem ser funções de alto nível, como proprietário ou específico, como o leitor de máquina virtual.

Funções apenas podem ser atribuídas a partir do recurso que é o âmbito para a atribuição, do grupo de recursos ou a subscrição. Mas pode ver todas as atribuições de acesso para um determinado utilizador ou grupo num único local.

Obter mais informações sobre como [atribuições de funções de utilização para gerir o acesso aos recursos da subscrição Azure](role-based-access-control-configure.md).

##  <a name="view-access-assignments"></a>Ver atribuições de acesso

Para pesquisar as atribuições do access para um único utilizador ou grupo, inicie no Azure Active Directory no [portal do Azure](http://portal.azure.com).

1. Selecione o **Azure Active Directory**. Se esta opção não estiver visível na sua lista de navegação, selecione **Mais serviços** e, em seguida, desloque para baixo até localizar **Azure Active Directory**.
2. Selecione **utilizadores e grupos**e, em seguida, em **todos os utilizadores** ou **todos os grupos**. Neste exemplo, vamos concentrar-nos utilizadores individuais.
    ![Gerir utilizadores e grupos no Azure Active Directory - captura de ecrã](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Procure o utilizador por nome ou o nome de utilizador.
4. Selecione **recursos Azure** pá o utilizador. Todas as atribuições de acesso para esse utilizador são apresentados.

### <a name="read-permissions-to-view-assignments"></a>Permissões de leitura para ver as atribuições

Esta página mostra apenas as atribuições de acesso que tiver permissão de leitura. Por exemplo, ter acesso de leitura a subscrição A e ir para o pá Azure recursos para verificar as atribuições de um utilizador. Pode ver os respetivos atribuições de acesso para a subscrição A, mas não verá que ela também tem acesso atribuições na subscrição B.

## <a name="delete-access-assignments"></a>Eliminar as atribuições do access

A partir deste pá, pode eliminar as atribuições de acesso que foram atribuídas diretamente para um utilizador ou grupo. Se foi herdada a atribuição de acesso de um grupo principal, é necessário ir para o recurso ou da subscrição e gerir a atribuição aí.

1. A partir da lista de todas as atribuições de acesso para um utilizador ou grupo, selecione aquele que pretende eliminar.
2. Selecione **Remover** e, em seguida, **Sim** para confirmar.
    ![Remover atribuição do access - captura de ecrã](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="related-topics"></a>Tópicos relacionados

- Introdução ao controlo de acesso baseado em funções para [atribuições de funções de utilização para gerir o acesso aos recursos da subscrição Azure](role-based-access-control-configure.md)
- Consulte as [funções incorporadas RBAC](role-based-access-built-in-roles.md)
