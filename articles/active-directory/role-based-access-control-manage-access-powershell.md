<properties
    pageTitle="Gerir o controlo de acesso baseado em funções (RBCA) com o Azure PowerShell | Microsoft Azure"
    description="Como gerir RBAC com PowerShell do Azure, incluindo listar funções, atribuir funções e eliminar atribuições de funções."
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

# <a name="manage-role-based-access-control-with-azure-powershell"></a>Gerir o controlo de acesso baseado em funções com o Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Clip Azure](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)


Pode utilizar o controlo de acesso baseado em funções RBCA () no portal do Azure e API de gestão de recursos do Azure para gerir o acesso à sua subscrição de um nível extensivamente. Com esta funcionalidade, pode conceder acesso a utilizadores, grupos ou principais de serviço do Active Directory ao atribuir funções de algumas aos mesmos a um determinado âmbito.

Antes de poder utilizar o PowerShell para gerir RBAC, tem de ter o seguinte procedimento:

- Azure PowerShell versão 0.8.8 ou posterior. Para instalar a versão mais recente e associá-la com a sua subscrição Azure, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

- Cmdlets de Gestor de recursos do Azure. Instale os [cmdlets do Gestor de recursos do Azure](https://msdn.microsoft.com/library/mt125356.aspx) no PowerShell.

## <a name="list-roles"></a>Funções de lista

### <a name="list-all-available-roles"></a>Lista todas as funções disponíveis
A funções RBAC de lista que estão disponíveis para a atribuição e para inspecionar as operações à qual possam conceder acesso, utilizam `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Ações de lista de uma função
Para listar as ações para uma função específica, utilize `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition para uma função específica-](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Ver quem tem acesso
Para atribuições de acesso RBAC da lista, utilize `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Lista de atribuições de funções num âmbito específico
Pode ver todas as atribuições de acesso para um recurso, grupo de recursos ou subscrição especificada. Por exemplo, para ver as todas as ativas atribuições para um grupo de recursos, utilize `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleAssignment para um grupo de recursos-](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Funções de lista atribuídas a um utilizador
Para listar todas as funções atribuídas a um utilizador especificado e as funções que são atribuídas a grupos a que pertence o utilizador, utilize `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleAssignment para um utilizador-](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Lista de atribuições de funções de administrador e coadmin serviço clássico
Para atribuições de acesso de lista para o administrador de subscrição clássica e coadministrators, utilize:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Conceder acesso
### <a name="search-for-object-ids"></a>Pesquisa de ID de objecto
Para atribuir uma função, é necessário identificar o objeto (utilizador, grupo ou aplicação) e o âmbito.

Se não souber o ID da subscrição, pode encontrar-na pá **subscrições** no portal do Azure. Para saber como consultar o ID da subscrição, consulte o artigo [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) no MSDN.

Para obter o ID do objeto para um grupo do Azure AD, utilize:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Para obter o ID do objeto para uma principal de serviço do Azure AD ou uma aplicação, utilize:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Atribuir uma função a uma aplicação do âmbito da subscrição
Para conceder acesso a uma aplicação no âmbito da subscrição, utilize:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![Captura de ecrã do RBAC PowerShell-novo AzureRmRoleAssignment](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Atribuir uma função a um utilizador no âmbito do grupo de recursos
Para conceder acesso a um utilizador no âmbito do grupo de recursos, utilize:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![Captura de ecrã do RBAC PowerShell-novo AzureRmRoleAssignment](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Atribuir uma função a um grupo no âmbito de recursos
Para conceder acesso a um grupo no âmbito de recursos, utilize:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![Captura de ecrã do RBAC PowerShell-novo AzureRmRoleAssignment](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Remover o acesso
Para remover o acesso de utilizadores, grupos e aplicações, utilize:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![Captura de ecrã do RBAC PowerShell-remover AzureRmRoleAssignment](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Crie uma função personalizada
Para criar uma função personalizada, utilize o `New-AzureRmRoleDefinition` comando.

Quando cria uma função personalizada utilizando o PowerShell, tem de iniciar com uma das [funções incorporadas](role-based-access-built-in-roles.md). Editar os atributos de adicionar as *ações*, *notActions*ou *âmbitos* que pretende e, em seguida, guarde as alterações como uma nova função.

O exemplo seguinte começa com a função *Contribuinte Máquina Virtual* e utiliza que para criar uma função personalizada denominada *Operador de Máquina Virtual*. A nova função concede acesso a todas as operações de leitura dos fornecedores de recurso *Microsoft.Compute*, *Microsoft.Storage*e *Microsoft.Network* concede acesso para começar, reinicie e monitorizar máquinas virtuais. A função personalizada pode ser utilizada nas duas subscrições.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## <a name="modify-a-custom-role"></a>Modificar uma função personalizada
Para modificar uma função personalizada, em primeiro lugar, utilize o `Get-AzureRmRoleDefinition` comando para recuperar a definição de função. Em segundo lugar, efetue as alterações desejadas à definição de função. Por fim, utilize o `Set-AzureRmRoleDefinition` comando para guardar a definição de função modificados.

O exemplo seguinte adiciona o `Microsoft.Insights/diagnosticSettings/*` operação à função personalizada *Operador de Máquina Virtual* .

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![Captura de ecrã do RBAC PowerShell-configurar AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

O exemplo seguinte adiciona uma subscrição do Azure aos atribuível âmbitos da função personalizado *Operador de Máquina Virtual* .

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2"
Set-AzureRmRoleDefinition -Role $role)
```

![Captura de ecrã do RBAC PowerShell-configurar AzureRmRoleDefinition-](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Para eliminar uma função personalizada, utilize o `Remove-AzureRmRoleDefinition` comando.

O exemplo seguinte remove a função personalizada *Operador de Máquina Virtual* .

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![Captura de ecrã do RBAC PowerShell-remover AzureRmRoleDefinition](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Lista de funções personalizadas
Para listar as funções que estão disponíveis para a atribuição num âmbito, utilize o `Get-AzureRmRoleDefinition` comando.

O exemplo seguinte apresenta todas as funções que estão disponíveis para a atribuição na subscrição seleccionada.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

No seguinte exemplo, a função personalizada *Operador de Máquina Virtual* não está disponível na subscrição *Production4* porque essa subscrição não estiver na **AssignableScopes** da função.

![Captura de ecrã do RBAC PowerShell-Get AzureRmRoleDefinition](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Consulte também
- [Utilizar o Azure PowerShell com o Gestor de recursos Azure](../powershell-azure-resource-manager.md)
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
