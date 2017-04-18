<properties
    pageTitle="Política de Gestor de recursos do Azure | Microsoft Azure"
    description="Descreve como utilizar a política de Gestor de recursos do Azure para impedir que violações de diferentes âmbitos de como subscrição, grupos de recursos ou recursos individuais."
    services="azure-resource-manager"
    documentationCenter="na"
    authors="ravbhatnagar"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="gauravbh;tomfitz"/>

# <a name="use-policy-to-manage-resources-and-control-access"></a>Utilizar a política para gerir os recursos e controlar o acesso

Agora o Gestor de recursos do Azure permite-lhe controlar o acesso através das políticas de personalizado. Com as políticas, pode impedir que os utilizadores na sua organização a partir de força convenções que são necessários para gerir os recursos da sua organização. 

Criar definições de política que descrevem as ações ou recursos que são especificamente negados. Atribuir essas definições de política no âmbito pretendido, como a subscrição, grupo de recursos ou um recurso individual. 

Neste artigo, iremos explicar a estrutura básica da linguagem de definição de política que pode utilizar para criar políticas. Em seguida, podemos descrevem como pode aplicar estas políticas de diferentes âmbitos de.

## <a name="how-is-it-different-from-rbac"></a>Como é diferente do RBAC?

Existem algumas diferenças fundamentais entre política e controlo de acesso baseado em funções, mas a primeira coisa para compreender é que as políticas e RBAC funcionam em conjunto. Para utilizar as políticas, têm de ser autenticadas através de RBAC. Ao contrário RBAC, a política é uma predefinição de permitir e explícitos negar sistema. 

RBAC foca as acções que pode executar um **utilizador** em diferentes âmbitos de. Por exemplo, um determinado utilizador é adicionado à função de contribuinte para um grupo de recursos no âmbito pretendido, para que o utilizador pode efetuar alterações a esse grupo de recursos. 

Política foca-se em **recurso** ações em vários âmbitos. Por exemplo, através das políticas, pode controlar os tipos de recursos que podem ser aprovisionados ou restringir as localizações na qual os recursos podem ser aprovisionados.

## <a name="common-scenarios"></a>Cenários comuns

Um cenário comum é exigir departamentais etiquetas para encargos objetivo. Uma organização poderá pretender permitir operações apenas quando o Centro de custos adequado está associado; caso contrário, eles recusar o pedido. Esta política ajuda-los cobrar o Centro de custos adequadas para operações efetuadas.

Outro cenário comum é que a organização poderá pretender controlar as localizações onde são criados recursos. Ou às quais poderão pretender controlar o acesso aos recursos ao permitir apenas determinados tipos de recursos para ser aprovisionado.

Do mesmo modo, uma organização pode controlar o catálogo de serviço ou impor as convenções de nomenclatura pretendidas para os recursos.

Utilizar as políticas, estes cenários podem ser fácil.

## <a name="policy-definition-structure"></a>Estrutura de definição da política

Definição de política é criada utilizando JSON. É constituído por uma ou mais condições/os operadores lógicos que definem as ações e um efeito que indica o que acontece quando são preenchidas as condições. Esquema de é publicado no [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

Basicamente, uma política de contém os seguintes elementos:

**Operadores condição/lógica:** um conjunto de condições que pode ser manipulação através de um conjunto de operadores lógicos.

**Efeito:** o que acontece quando a condição é cumprida – em negar ou de auditoria. Um efeito de auditoria emite um registo de serviço de eventos de aviso. Por exemplo, um administrador pode criar uma política que faz com que um evento de auditoria se qualquer pessoa cria uma grande VM. O administrador pode rever os registos mais tarde.

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }
    
## <a name="policy-evaluation"></a>Avaliação da política

Políticas são avaliadas quando são criados recursos. Em caso de implementação do modelo, políticas são avaliadas durante a criação de cada recurso no modelo. 

> [AZURE.NOTE] Atualmente, a política não avaliar tipos de recursos que não suportam etiquetas, tipo e localização, como o tipo de recurso Microsoft.Resources/deployments. Este suporte será adicionado ao mesmo tempo futuro. Para evitar problemas de compatibilidade com versões anteriores, deve especificar explicitamente tipo durante a criação de políticas. Por exemplo, uma política de etiqueta que não especificar tipos é aplicada para todos os tipos. Nesse caso, uma implementação do modelo poderá falhar se existir um recurso aninhado que não suporta etiquetas e o tipo de implementação do recurso foi adicionado à avaliação da política. 

## <a name="logical-operators"></a>Operadores lógicos

Os operadores lógicos suportados juntamente com a sintaxe da são:

| Nome do operador     | Sintaxe         |
| :------------- | :------------- |
| Não            | "não": {&lt;condição ou operador &gt;}             |
| E           | "allOf": [{&lt;condição ou operador &gt;}, {&lt;condição ou operador &gt;}] |
| Ou                         | "anyOf": [{&lt;condição ou operador &gt;}, {&lt;condição ou operador &gt;}] |

Gestor de recursos permite-lhe especificar lógica complexa na sua política através de operadores aninhadas. Por exemplo, pode negar criação de recursos numa localização específica para um tipo de recurso especificado. Um exemplo de operadores aninhados é apresentado abaixo.

## <a name="conditions"></a>Condições

Uma condição devolver o valor se um **campo** ou **origem** cumprem determinado critério. Os nomes de condição suportados e sintaxe são:

| Nome da condição | Sintaxe                |
| :------------- | :------------- |
| É igual a             | "é igual a": "&lt;valor&gt;"               |
| Como                  | "gosto": "&lt;valor&gt;"                   |
| Contém          | "contém": "&lt;valor&gt;"|
| No                        | "em": ["&lt;valor1&gt;","&lt;valor2&gt;"]|
| ContainsKey    | "containsKey": "&lt;nome chave&gt;" |
| Existe     | "existe": "&lt;booleano&gt;" |

### <a name="fields"></a>Campos

Condições são formadas utilizando campos e origens. Um campo representa propriedades no payload pedido recurso que é utilizado para descrever o estado do recurso. Uma fonte representa características do pedido própria. 

São suportadas os seguintes campos e origens:

Campos de: **nome**, **tipo**, o **tipo**, **localização**, **etiquetas**, **etiquetas.** *, and * *alias de propriedade**. 

### <a name="property-aliases"></a>Aliases de propriedade 
Propriedade alias é um nome que pode ser utilizado numa definição de política para aceder às recurso tipo específico propriedades, tais como definições e skus. Funciona em todas as versões de API onde existe a propriedade. Podem ser obtidos aliases ao utilizar a API REST mostrado abaixo (Powershell suporte será adicionado no futuro):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
    
A definição de um alias de é apresentada abaixo. Como pode ver, um alias de define caminhos de versões diferentes do API, mesmo quando existe uma alteração de nome de propriedade. 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

Neste momento, os aliases suportados são:

| Nome de alias | Descrição |
| ---------- | ----------- |
| {resourceType}/sku.name | Tipos de recursos suportados são: Microsoft.Compute/virtualMachines,<br />Microsoft.Storage/storageAccounts,<br />Microsoft.Web/serverFarms,<br /> Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family | Tipo de recurso suportados é Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | Tipo de recurso suportados é Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher |  |
| Microsoft.Compute/virtualMachines/imageOffer  |  |
| Microsoft.Compute/virtualMachines/imageSku  |  |
| Microsoft.Compute/virtualMachines/imageVersion  |  |
| Microsoft.Cache/Redis/enableNonSslPort |  |
| Microsoft.Cache/Redis/shardCount |  |
| Microsoft.SQL/servers/version |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId |  |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName |  |
| Microsoft.SQL/servers/databases/edition |  |
| Microsoft.SQL/servers/databases/elasticPoolName |  |
| Microsoft.SQL/servers/elasticPools/dtu |  |
| Microsoft.SQL/servers/elasticPools/edition |  |

Atualmente, política apenas funciona com pedidos de colocar. 

## <a name="effect"></a>Efeito
A política de suporta três tipos de efeito - **Negar**, **Auditar**e **Acrescentar**. 

- Negar gera um evento no registo de auditoria e falha o pedido
- Auditoria gera um evento no registo de auditoria, mas não falhar o pedido
- Acrescentar adiciona o conjunto de campos definido para o pedido 

Para **Acrescentar**, terá de fornecer os seguintes detalhes:

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

O valor pode ser uma cadeia ou um objeto de formato JSON. 

## <a name="policy-definition-examples"></a>Exemplos de definição de política

Agora vamos ver como podemos definir a política para atingir os cenários anteriores.

### <a name="chargeback-require-departmental-tags"></a>Encargos: Exigir departamentais etiquetas

A seguinte política recusa pedidos que não têm uma etiqueta que contém a chave de "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

A seguinte política acrescenta costCenter etiqueta com um valor predefinido quando estão presentes sem etiquetas. 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }
    
A seguinte política acrescenta costCenter etiqueta com um valor predefinido quando não estiver presente na etiqueta de costCenter, mas se existirem outras etiquetas. 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]
    
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>Conformidade Geo: Certifique-se localizações recurso

O exemplo seguinte mostra uma política que recusa pedidos onde localização não é Europa Norte ou Europa Ocidental.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>Serviço Curation: Selecione o catálogo de serviço

O exemplo seguinte mostra uma política que permite ações apenas nos serviços do tipo Microsoft.Resources/\*, Microsoft.Compute/\*, Microsoft.Storage/\*, Microsoft.Network/\* são permitidos. Negado mais nada.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>Utilizar SKUs aprovados

O exemplo seguinte mostra a utilização de alias de propriedade para restringir SKUs. No exemplo, apenas Standard_LRS e Standard_GRS são aprovados para contas de armazenamento.

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
    

### <a name="naming-convention"></a>Convenção de nomenclatura

O exemplo seguinte mostra a utilização de caracteres universais, que são suportado pela condição "gostar". A condição afirma que, se o nome de corresponder o padrão de mencionadas (namePrefix\*nameSuffix), em seguida, recusar o pedido.

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### <a name="tag-requirement-just-for-storage-resources"></a>Requisitos de etiqueta apenas de recursos de armazenamento

O exemplo seguinte mostra como aninhar operadores lógicos para requerer uma tag de aplicação para apenas os recursos de armazenamento.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="policy-assignment"></a>Atribuição de políticas

Políticas são aplicadas ao diferentes âmbitos de como recursos individuais, grupos de recursos e subscrição. Políticas são herdadas por todos os recursos de subordinados. Por isso, se uma política é aplicada a um grupo de recursos, é aplicável a todos os recursos nesse grupo de recursos.

## <a name="creating-a-policy"></a>Criar uma política de

Esta secção fornece detalhes sobre como uma política pode ser criada utilizando REST API.

### <a name="create-policy-definition-with-rest-api"></a>Criar a definição de política com a REST API

Pode criar uma política com a [API REST para definições de política](https://msdn.microsoft.com/library/azure/mt588471.aspx). REST API permite-lhe criar e eliminar definições de política e obter informações sobre definições de existentes.

Para criar uma política, execute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Com o corpo do pedido semelhante ao exemplo seguinte:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }


Para a versão da api utilize *2016-04-01*. Para obter exemplos e obter mais detalhes, consulte o artigo [REST API para definições de política](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### <a name="create-policy-definition-using-powershell"></a>Criar a definição de política através do PowerShell

Pode criar uma definição de política utilizando o cmdlet AzureRmPolicyDefinition novo. O exemplo seguinte cria uma política para permitir que os recursos apenas no Norte e Europa Ocidental.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{  
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'          

O resultado da execução está armazenado no objeto $policy e pode ser utilizado mais tarde durante a atribuição de política. Para o parâmetro de política, o caminho para um ficheiro de .json que contenha a política de também pode ser fornecido em vez de especificando a política inline.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain    regions" -Policy "path-to-policy-json-on-disk"

### <a name="create-policy-definition-using-azure-cli"></a>Criar a definição de política utilizando o clip do Azure

Pode criar uma definição de política utilizando o clip azure com o comando de definição de política, conforme apresentado abaixo. O exemplo seguinte cria uma política para permitir que os recursos apenas no Norte e Europa Ocidental.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{   
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    
    

É possível especificar o caminho para um ficheiro de .json que contenha a política em vez de especificando o inline política, conforme apresentado abaixo.

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"


## <a name="applying-a-policy"></a>Aplicar uma política de

### <a name="policy-assignment-with-rest-api"></a>Política de atribuição com a REST API

Pode aplicar a definição de política no âmbito através da [REST API para atribuições de política](https://msdn.microsoft.com/library/azure/mt588466.aspx)pretendida. REST API permite-lhe criar e eliminar as atribuições de políticas e obter informações sobre atribuições existentes.

Para criar uma atribuição de política, execute:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{Política de atribuição} é o nome da atribuição política. Para a versão da api utilize *2016-04-01*. 

Com o corpo do pedido semelhante ao exemplo seguinte:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

Para obter exemplos e obter mais detalhes, consulte o artigo [REST API para atribuições de política](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### <a name="policy-assignment-using-powershell"></a>Atribuição de política através do PowerShell

Pode aplicar a política criada anteriormente através do PowerShell para o âmbito pretendido utilizando o cmdlet AzureRmPolicyAssignment novo:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
$Policy Eis o objeto de política que foi devolvido como resultado de executar o cmdlet novo AzureRmPolicyDefinition conforme mostrado acima. O âmbito aqui é o nome do grupo de recursos que especificar.

Se pretender remover a atribuição de política acima, pode fazê-lo da seguinte forma:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Pode obter, alterar ou remover as definições de política através de obter AzureRmPolicyDefinition, AzureRmPolicyDefinition conjunto e remover AzureRmPolicyDefinition cmdlets respetivamente.

Da mesma forma, pode obter, alterar ou remover as atribuições de políticas através do Get-AzureRmPolicyAssignment, AzureRmPolicyAssignment conjunto e remover AzureRmPolicyAssignment cmdlets respetivamente.

### <a name="policy-assignment-using-azure-cli"></a>Atribuição de política utilizando o clip do Azure

Pode aplicar a política criada anteriormente através do Azure clip para o âmbito pretendido, utilizando o comando de atribuição de política:

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
O âmbito aqui é o nome do grupo de recursos que especificar. Se o valor do parâmetro política-definição-id é desconhecido, é possível obtê-lo através do clip do Azure, conforme apresentado abaixo: 

    azure policy definition show <policy-name>

Se pretender remover a atribuição de política acima, pode fazê-lo da seguinte forma:

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Pode obter, alterar ou remover a política definições através da política definition Mostrar, definir e eliminar comandos respetivamente.

Da mesma forma, pode obter, alterar ou remover a política de atribuições através da atribuição de política mostram e eliminar comandos respetivamente.

##<a name="policy-audit-events"></a>Eventos de política de auditoria

Depois de ter aplicado a política de, vai começar a ver os eventos relacionados com a política. Pode quer ir para portal, utilize o PowerShell ou o clip do Azure para obter estes dados. 

### <a name="policy-audit-events-using-powershell"></a>Eventos de política de auditoria através do PowerShell

Para ver todos os eventos relacionados com para recusar o efeito, pode utilizar o seguinte comando PowerShell:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

Para ver todos os eventos relacionados com o efeito de auditoria, pode utilizar o seguinte comando:

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="policy-audit-events-using-azure-cli"></a>Eventos de política de auditoria utilizando o clip do Azure

Para ver todos os eventos a partir de um recurso de grupo que relacionada com a negar efeito, pode utilizar o seguinte comando clip:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

Para ver todos os eventos relacionados com o efeito de auditoria, pode utilizar o comando clip seguinte:

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"
