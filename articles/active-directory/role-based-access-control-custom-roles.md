<properties
    pageTitle="Funções personalizadas no Azure RBAC | Microsoft Azure"
    description="Saiba como definir funções personalizadas com o controlo de acesso Azure Role-Based mais precisa para gestão de identidades na sua subscrição do Azure."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="kgremban"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="07/25/2016"
    ms.author="kgremban"/>


# <a name="custom-roles-in-azure-rbac"></a>Funções personalizadas no Azure RBAC


Crie uma função personalizada no controlo de acesso de Azure Role-Based (RBAC) se nenhuma das funções incorporadas corresponder às suas necessidades de acesso específico. Funções personalizadas podem ser criadas através do [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [Interface de comandos do Azure](role-based-access-control-manage-access-azure-cli.md) (CLI) e a [REST API](role-based-access-control-manage-access-rest.md). Tal como faria com funções incorporadas, é possível atribuir funções personalizadas para utilizadores, grupos e aplicações na subscrição, no grupo de recursos e âmbitos de recursos. Funções personalizadas são armazenadas num inquilino Azure AD e podem ser partilhadas por todas as subscrições que utilizam esse inquilino como do diretório do Azure AD para a subscrição dos.

Segue-se um exemplo de uma função para a monitorização e reiniciar máquinas virtuais personalizada:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Ações
A propriedade de **ações** de uma função personalizada especifica as operações Azure à qual a função concede acesso. É uma coleção de cadeias de operação que identificam as operações passíveis de serem protegidas dos fornecedores de recurso Azure. Cadeias de operação que contêm carateres universais (\*) conceder acesso a todas as operações que correspondem a cadeia de operação. Por exemplo:

-   `*/read`concede de acesso para ler operações para todos os tipos de recursos de todos os fornecedores de recurso Azure.
-   `Microsoft.Network/*/read`concede de acesso para ler operações para todos os tipos de recursos no fornecedor de recurso Microsoft.Network do Azure.
-   `Microsoft.Compute/virtualMachines/*`concede acesso a todas as operações de máquinas virtuais e o seu filho tipos de recursos.
-   `Microsoft.Web/sites/restart/Action`concede de acesso para reiniciar a Web sites.

Utilizar `Get-AzureRmProviderOperation` (no PowerShell) ou `azure provider operations show` (no Azure CLI) para operações de lista de fornecedores de recurso Azure. Também pode utilizar estes comandos para verificar se uma cadeia de operação é válida e para expandir universais cadeias de operação.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Captura de ecrã do PowerShell - obter AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | Operação FT, OperationName](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure apresentação de operações de fornecedor a captura de ecrã - azure de clip "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Utilize a propriedade **NotActions** se o conjunto de operações que pretende permitir mais facilmente definido pelo excluindo as operações restritas. O acesso concedido por uma função personalizada é calculado subtraindo as operações de **NotActions** operações de **ações** .

> [AZURE.NOTE] Se um utilizador é atribuído uma função que exclua numa operação de em **NotActions**e é atribuída uma função segunda que concede acesso para a mesma operação, o utilizador terá autorização para executar essa operação. **NotActions** não é uma regra de negar – é simplesmente um meio cómodo como para criar um conjunto de operações permitidas quando precisa de ser excluídas operações específicas.

## <a name="assignablescopes"></a>AssignableScopes
A propriedade **AssignableScopes** da função personalizada Especifica os âmbitos (subscrições, grupos de recursos ou recursos) dentro do qual a função personalizada está disponível para atribuição. Pode disponibilizar a função personalizada para atribuição apenas as subscrições ou grupos de recursos que requerem-lo e não correio secundário experiência do utilizador para o resto das subscrições ou grupos de recursos.

Exemplos de âmbitos atribuível válidos incluem:

-   "/ subscrições/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/ subscrições/e91d47c4-76f3-4271-a796-21b4ecfe3624" - disponibiliza a função para atribuição nas duas subscrições.
-   "/ subscrições/c276fc76-9cd4-44c9-99a7-4fd71546436e" - disponibiliza a função de atribuição de numa única subscrição.
-  "/ subscrições/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/rede" - disponibiliza a função para atribuição apenas no grupo de recursos de rede.

> [AZURE.NOTE] Tem de utilizar, pelo menos, uma subscrição, grupo de recursos ou ID do recurso.

## <a name="custom-roles-access-control"></a>Controlo de acesso de funções personalizadas
A propriedade **AssignableScopes** da função personalizada também controla quem pode ver, modificar e eliminar a função.

- Quem pode criar uma função personalizada?
    Os proprietários (e administradores de acesso de utilizador) de subscrições, grupos de recursos e de recursos podem criar funções personalizadas para utilização nesses âmbitos.
    Criar a função de utilizador tem de conseguir executar `Microsoft.Authorization/roleDefinition/write` operação todos os **AssignableScopes** da função.

- Quem pode modificar uma função personalizada?
    Os proprietários (e administradores de acesso de utilizador) de subscrições, grupos de recursos e recursos, podem modificar as funções personalizadas nesses âmbitos. Os utilizadores têm de ser possível executar a `Microsoft.Authorization/roleDefinition/write` operação todos os **AssignableScopes** de uma função personalizada.

- Quem pode ver funções personalizadas?
    Todas as funções incorporadas no Azure RBAC permitem a visualização de funções que estão disponíveis para a atribuição. Os utilizadores que podem desempenhar o `Microsoft.Authorization/roleDefinition/read` operação de um âmbito pode ver as funções RBAC que estão disponíveis para a atribuição desse âmbito.

## <a name="see-also"></a>Consulte também
- [Função de controlo de acesso com base](role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
- Saiba como gerir o acesso com:
    - [PowerShell](role-based-access-control-manage-access-powershell.md)
    - [Clip Azure](role-based-access-control-manage-access-azure-cli.md)
    - [REST API](role-based-access-control-manage-access-rest.md)
- [Funções incorporadas](role-based-access-built-in-roles.md): obter detalhes sobre as funções que vêm padrão no RBAC.
