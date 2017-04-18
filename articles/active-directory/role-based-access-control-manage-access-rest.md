<properties
    pageTitle="Gerir o controlo de acesso baseado em funções com a API REST"
    description="Gerir o controlo de acesso baseado em funções com a API REST"
    services="active-directory"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="multiple"
    ms.tgt_pltfrm="rest-api"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="managing-role-based-access-control-with-the-rest-api"></a>Gerir o controlo de acesso baseado em funções com a API REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Clip Azure](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

Baseado em funções acesso controlo RBCA () no Portal do Azure e API do Gestor de recursos do Azure ajudam-na gerir o acesso à sua subscrição e os recursos num nível de extensivamente. Com esta funcionalidade, pode conceder acesso a utilizadores, grupos ou principais de serviço do Active Directory ao atribuir funções de algumas aos mesmos a um determinado âmbito.

## <a name="list-all-role-assignments"></a>Lista todas as atribuições de funções

Lista todas as atribuições de funções no âmbito especificado e subscopes.

Para atribuições de funções de lista, tem de ter acesso a `Microsoft.Authorization/roleAssignments/read` operação no âmbito. Todas as funções incorporadas são concedidas acesso a esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método de **Introdução** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* o âmbito para o qual pretende listar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{versão api}* 2015-07-01.

3. Substitua *{filtro}* com a condição que pretende aplicar a filtrar a lista de atribuição de funções:

  - Lista atribuições de funções apenas o âmbito especificado, excluindo as atribuições de funções na subscopes:`atScope()`    
  - Lista as atribuições de funções de uma aplicação, grupo ou utilizador específico:`principalId%20eq%20'{objectId of user, group, or service principal}'`  
  - Lista de atribuições de funções para um utilizador específico, incluindo as herdados a partir de grupos |`assignedTo('{objectId of user}')`

### <a name="response"></a>Resposta

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Obter informações sobre uma atribuição de funções

Obtém informações sobre uma atribuição de funções única especificada pelo identificador de atribuição de funções.

Para obter informações sobre uma atribuição de funções, tem de ter acesso a `Microsoft.Authorization/roleAssignments/read` operação. Todas as funções incorporadas são concedidas acesso a esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método de **Introdução** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* o âmbito para o qual pretende listar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id da atribuição função}* o identificador de GUID da atribuição de funções.

3. Substitua *{versão api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Criar uma atribuição de funções

Crie uma atribuição de funções no âmbito especificado para o capital especificado conceder a função especificada.

Para criar uma atribuição de funções, tem de ter acesso a `Microsoft.Authorization/roleAssignments/write` operação. Das funções incorporadas, apenas o *proprietário* e o *Administrador de acesso de utilizador* são concedido acesso para esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método **colocar** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* o âmbito no qual pretende criar as atribuições de funções. Quando cria uma atribuição de funções num âmbito de elemento principal, todos os âmbitos de subordinados herdam a mesma atribuição de funções. Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1   
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id da atribuição função}* um novo GUID, qual é o identificador de GUID da nova atribuição de funções.

3. Substitua *{versão api}* 2015-07-01.

Para o corpo do pedido, forneça os valores no seguinte formato:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nome do elemento     | Obrigatório | Tipo   | Descrição |
|------------------|----------|--------|-------------|
| roleDefinitionId | Sim      | Cadeia | Identificador da função. O formato do identificador é:`{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId      | Sim      | Cadeia | ID de objecto do Azure AD principal (utilizador, grupo ou principal do serviço) ao qual a função é atribuída. |

### <a name="response"></a>Resposta

Código de estado: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Eliminar uma atribuição de funções

Elimine uma atribuição de funções no âmbito especificado.

Para eliminar uma atribuição de funções, tem de ter acesso à `Microsoft.Authorization/roleAssignments/delete` operação. Das funções incorporadas, apenas o *proprietário* e o *Administrador de acesso de utilizador* são concedido acesso para esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método **Eliminar** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* o âmbito no qual pretende criar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua o id de atribuição de funções GUID *{id da atribuição função}* .

3. Substitua *{versão api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Lista todas as funções

Lista todas as funções que estão disponíveis para a atribuição no âmbito especificado.

A funções de lista, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/read` operação no âmbito. Todas as funções incorporadas são concedidas acesso a esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método de **Introdução** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua o âmbito para o qual pretende lista as funções *{âmbito}* . Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{versão api}* 2015-07-01.

3. Substitua *{filtro}* com a condição que pretende aplicar a filtrar a lista de funções:

  - Funções disponíveis para atribuição no âmbito especificado e qualquer dos seus âmbitos de subordinados da lista:`atScopeAndBelow()`
  - Procurar uma função utilizando o nome a apresentar exata: `roleName%20eq%20'{role-display-name}'`. Utilize o formulário de codificação de URL do nome da função exacto a apresentar. Por exemplo,`$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Resposta

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Obter informações sobre uma função

Obtém informações sobre uma única função especificada pelo identificador de definição de função. Para obter informações sobre uma única função utilizando o nome a apresentar, consulte a [lista de todas as funções](role-based-access-control-manage-access-rest.md#list-all-roles).

Para obter informações sobre uma função, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/read` operação. Todas as funções incorporadas são concedidas acesso a esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método de **Introdução** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* o âmbito para o qual pretende listar as atribuições de funções. Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* o identificador GUID da definição de função.

3. Substitua *{versão api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Crie uma função personalizada
Crie uma função personalizada.

Para criar uma função personalizada, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/write` operação em todas as o `AssignableScopes`. Das funções incorporadas, apenas o *proprietário* e o *Administrador de acesso de utilizador* são concedido acesso para esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método **colocar** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* a primeira *AssignableScope* da função personalizada. Os exemplos seguintes mostram como especificar o âmbito do níveis diferentes.

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* um novo GUID, qual é o identificador GUID do novo grupo personalizado.

3. Substitua *{versão api}* 2015-07-01.

Para o corpo do pedido, forneça os valores no seguinte formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome do elemento | Obrigatório | Tipo | Descrição |
|--------------|----------|------|-------------|
| nome         | Sim | Cadeia   | Identificador GUID da função personalizada.    |
| properties.roleName               | Sim | Cadeia   | Nome da função personalizada a apresentar. Carateres de tamanho máximo 128.                        |
| Properties.Description            | N  | Cadeia   | Descrição da função personalizada. Carateres de tamanho máximo 1024.                                               |
| Properties.Type                   | Sim | Cadeia   | Definido como "CustomRole."                                         |
| Properties.Permissions.Actions    | Sim | String] | Uma matriz de cadeias de ação especificando as operações concedidas pela função personalizada.             |
| properties.permissions.notActions | N  | String] | Uma matriz de cadeias de ação especificando as operações para excluir de operações concedidas pela função personalizada. |
| properties.assignableScopes       | Sim | String] | Uma matriz de âmbitos na qual a função personalizada pode ser utilizada.   |

### <a name="response"></a>Resposta

Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Modificar uma função personalizada.

Para modificar uma função personalizada, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/write` operação em todas as o `AssignableScopes`. Das funções incorporadas, apenas o *proprietário* e o *Administrador de acesso de utilizador* são concedido acesso para esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método **colocar** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* a primeira *AssignableScope* da função personalizada. Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* o identificador GUID da função personalizado.

3. Substitua *{versão api}* 2015-07-01.

Para o corpo do pedido, forneça os valores no seguinte formato:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nome do elemento | Obrigatório | Tipo | Descrição |
|--------------|----------|------|-------------|
| nome         | Sim      | Cadeia | Identificador GUID da função personalizada. |
| properties.roleName | Sim | Cadeia | Nome a apresentar da função personalizada atualizada. |
| Properties.Description | N | Cadeia | Descrição da função personalizada atualizada. |
| Properties.Type | Sim | Cadeia | Definido como "CustomRole." |
| Properties.Permissions.Actions | Sim | String] | Uma matriz de cadeias de ação especificando as operações para o qual a função personalizada actualizada concede acesso. |
| properties.permissions.notActions | N | String] | Uma matriz de cadeias de ação especificando as operações para excluir a partir de operações de que a função personalizada actualizada concede. |
| properties.assignableScopes | Sim | String] | Uma matriz de âmbitos na qual a função personalizada actualizada pode ser utilizada. |

### <a name="response"></a>Resposta

Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Eliminar uma função personalizada

Elimine uma função personalizada.

Para eliminar uma função personalizada, tem de ter acesso a `Microsoft.Authorization/roleDefinitions/delete` operação em todas as o `AssignableScopes`. Das funções incorporadas, apenas o *proprietário* e o *Administrador de acesso de utilizador* são concedido acesso para esta operação. Para mais informações sobre as atribuições de funções e gerir acesso para recursos Azure, consulte o artigo [Azure Role-Based o controlo de acesso](role-based-access-control-configure.md).

### <a name="request"></a>Pedido

Utilize o método **Eliminar** com o URI seguinte:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro de URI, efetue as seguintes substituições para personalizar o seu pedido:

1. Substitua *{âmbito}* o âmbito no qual pretende eliminar a definição de função. Os exemplos seguintes mostram como especificar o âmbito para diferentes níveis:

  - Subscrição: /subscriptions/ {id da subscrição}  
  - Grupo de recursos: /subscriptions/ {id da subscrição} / resourceGroups/myresourcegroup1  
  - Recurso: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  

2. Substitua *{id de definição de função}* com o id de definição de função GUID da função personalizado.

3. Substitua *{versão api}* 2015-07-01.

### <a name="response"></a>Resposta

Código de estado: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```


[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
