<properties
    pageTitle="Gerir o controlo de acesso baseado em funções (RBCA) com clip Azure | Microsoft Azure"
    description="Saiba como gerir baseado em funções controlo de acesso (RBAC) com a interface de comandos Azure listando funções e ações de função e por atribuir funções de aos âmbitos de subscrição e de aplicação."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/22/2016"
    ms.author="kgremban"/>

# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gerir o controlo de acesso baseado em funções com a interface da linha de comandos do Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Clip Azure](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

Pode utilizar o controlo de acesso baseado em funções RBCA () no portal do Azure e API do Gestor de recursos do Azure para gerir o acesso à sua subscrição e os recursos num nível de extensivamente. Com esta funcionalidade, pode conceder acesso a utilizadores, grupos ou principais de serviço do Active Directory ao atribuir funções de algumas aos mesmos a um determinado âmbito.

Antes de poder utilizar a interface de comandos Azure (CLI) para gerir RBAC, tem de ter o seguinte procedimento:

- Clip Azure versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la com a sua subscrição Azure, consulte o artigo [instalar e configurar o clip do Azure](../xplat-cli-install.md).
- Gestor de recursos do Azure no clip Azure. Aceda a [utilizar o clip do Azure com o Gestor de recursos](../xplat-cli-azure-resource-manager.md) para obter mais detalhes.

## <a name="list-roles"></a>Funções de lista

### <a name="list-all-available-roles"></a>Lista todas as funções disponíveis
Para listar todas as funções disponíveis, utilize:

        azure role list

O exemplo seguinte mostra a lista de *todas as funções disponíveis*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Captura de ecrã do RBAC Azure linha de comandos - lista de função azure-](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Ações de lista de uma função
Para listar as ações de uma função, utilize:

    azure role show "<role name>"

O exemplo seguinte mostra as ações das funções de *Contribuinte* e *Máquina Virtual contribuinte* .

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Captura de ecrã do RBAC Azure linha de comandos - Mostrar função azure-](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##  <a name="list-access"></a>Acesso de lista
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Lista as atribuições de funções efetiva num grupo de recursos
Para listar as atribuições de funções que existam num grupo de recursos, utilize:

    azure role assignment list --resource-group <resource group name>

O exemplo seguinte mostra as atribuições de funções no grupo *vendas-pharma-projecforcast* .

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Linha de comandos do RBAC Azure - lista de atribuição de funções azure por captura de ecrã de grupo](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Lista de atribuições de funções para um utilizador
Para listar as atribuições de funções para um utilizador específico e as atribuições que são atribuídas a grupos de um utilizador, utilize:

    azure role assignment list --signInName <user email>

Também pode ver as atribuições de funções que são herdadas a partir de grupos, alterando o comando:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

O exemplo seguinte mostra as atribuições de funções que são concedidas para o *sameert@aaddemo.com* utilizador. Isto inclui funções que são atribuídas diretamente para o utilizador e funções que são herdadas a partir de grupos.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Captura de ecrã do RBAC Azure linha de comandos - lista de atribuição de funções azure por utilizador-](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##  <a name="grant-access"></a>Conceder acesso
Para conceder acesso depois de ter identificado a função que pretende atribuir, utilize:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Atribuir uma função de grupo ao âmbito da subscrição
Para atribuir uma função para um grupo do âmbito da subscrição, utilize:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

O exemplo seguinte atribui a função de *leitor* à *equipa do Cristina Koch* no âmbito de *subscrição* .


![Linha de comandos Azure de RBAC - atribuição de funções azure crie ao captura de ecrã de grupo](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Atribuir uma função a uma aplicação do âmbito da subscrição
Para atribuir uma função para uma aplicação do âmbito da subscrição, utilize:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

O exemplo seguinte concede a função *Contribuinte* para uma aplicação do *Azure AD* da subscrição selecionado.

 ![Linha de comandos Azure de RBAC - atribuição de funções azure criar por aplicação](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Atribuir uma função a um utilizador no âmbito do grupo de recursos
Para atribuir uma função a um utilizador no âmbito do grupo de recursos, utilize:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

O exemplo seguinte concede a função *Contribuinte Máquina Virtual* para *samert@aaddemo.com* utilizador no âmbito *Pharma-vendas-ProjectForcast* do grupo de recursos.

![Linha de comandos Azure de RBAC - atribuição de funções azure crie ao captura de ecrã de utilizador](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Atribuir uma função a um grupo no âmbito de recursos
Para atribuir uma função para um grupo do âmbito de recursos, utilize:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

O exemplo seguinte atribui a função *Contribuinte Máquina Virtual* a um grupo do *Azure AD* numa *sub-rede*.

![Linha de comandos Azure de RBAC - atribuição de funções azure crie ao captura de ecrã de grupo](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##  <a name="remove-access"></a>Remover o acesso
Para remover uma atribuição de funções, utilize:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

O exemplo seguinte remove a atribuição de funções de *Máquina Virtual contribuinte* a partir do *sammert@aaddemo.com* utilizador num grupo de recursos de *Vendas de Pharma-ProjectForcast* .
O exemplo, em seguida, remove a atribuição de funções de um grupo da subscrição.

![Captura de ecrã do RBAC Azure linha de comandos - delete de atribuição de funções azure-](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Crie uma função personalizada
Para criar uma função personalizada, utilize:

    azure role create --inputfile <file path>

O exemplo seguinte cria uma função personalizada denominada *Operador de Máquina Virtual*. A função personalizada concede acesso a todas as operações de leitura dos fornecedores de recurso *Microsoft.Compute*, *Microsoft.Storage*e *Microsoft.Network* concede acesso para começar, reinicie e monitorizar máquinas virtuais. A função personalizada pode ser utilizada nas duas subscrições. Este exemplo utiliza um ficheiro JSON como uma entrada.

![Captura de ecrã JSON - definição de função personalizado-](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Linha de comandos Azure de RBAC - função azure criar - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Modificar uma função personalizada

Para modificar uma função personalizada, utilize o `azure role show` comando para recuperar a definição de função. Em segundo lugar, efetue as alterações pretendidas para o ficheiro de definição de função. Por fim, utilizar `azure role set` para guardar a definição de função modificadas.

    azure role set --inputfile <file path>

O exemplo seguinte adiciona a operação de *Microsoft.Insights/diagnosticSettings/* para as **ações**e uma subscrição do Azure para o **AssignableScopes** da função personalizada operador de Máquina Virtual.

![JSON - modificar a definição de função personalizado - captura de ecrã](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Captura de ecrã do RBAC Azure linha de comandos - função azure set-](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize o `azure role show` comando para determinar o **ID** da função. Em seguida, utilize o `azure role delete` comando para eliminar a função ao especificar o **ID**.

O exemplo seguinte remove a função personalizada *Operador de Máquina Virtual* .

![Captura de ecrã do RBAC Azure linha de comandos - função azure delete-](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Lista de funções personalizadas

Para listar as funções que estão disponíveis para a atribuição num âmbito, utilize o `azure role list` comando.

O exemplo seguinte apresenta todas as funções que estão disponíveis para a atribuição na subscrição seleccionada.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Captura de ecrã do RBAC Azure linha de comandos - lista de função azure-](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

No seguinte exemplo, a função personalizada *Operador de Máquina Virtual* não está disponível na subscrição *Production4* porque essa subscrição não estiver na **AssignableScopes** da função.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Captura de ecrã do RBAC Azure linha de comandos - lista de função azure para funções personalizadas-](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)





## <a name="rbac-topics"></a>Tópicos RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
