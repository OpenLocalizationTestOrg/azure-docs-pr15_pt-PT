<properties
    pageTitle="Análise de registo sessão pesquisa REST API | Microsoft Azure"
    description="Este guia fornece um tutorial básico que descreve como pode utilizar a pesquisa de análise de registo REST API no conjunto de aplicações de gestão de operações (OMS) e fornece exemplos que lhe mostram como utilizar os comandos."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="log-analytics-log-search-rest-api"></a>Pesquisa de registo de análise do registo REST API

Este guia fornece um tutorial básico que descreve como pode utilizar o registo de análise de pesquisa REST API no conjunto de aplicações de gestão de operações (OMS) e fornece exemplos que lhe mostram como utilizar os comandos. Alguns dos exemplos neste artigo de referência informações operacionais avançados, que é o nome da versão anterior do registo de análise.

## <a name="overview-of-the-log-search-rest-api"></a>Descrição geral da pesquisa registo REST API

Registo de análise de pesquisa REST API é RESTful e pode ser acedido através da API do Gestor de recursos do Azure. Neste documento irá encontrar exemplos onde a API é acedida através de [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comandos de abrir origem simplifica invocar a API do Gestor de recursos do Azure. A utilização de ARMClient e PowerShell é uma das várias opções para aceder a API do registo de análise de pesquisa. Outra opção é a utilizar o módulo Azure PowerShell para OperationalInsights que inclui cmdlets para aceder a pesquisa. Pode utilizar a API do Gestor de recursos do Azure RESTful para efetuar chamadas para áreas de trabalho OMS e efetuar comandos de pesquisa nelas com estas ferramentas. API será de saída de resultados de pesquisa formato JSON, permitindo-lhe utilizar os resultados da pesquisa de muitas maneiras diferentes através de programação.

O Gestor de recursos do Azure podem ser utilizado através de uma [biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) , bem como uma [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx). Reveja as páginas web ligada para obter mais informações.

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Tutorial do registo Analytics pesquisa REST API básica

### <a name="to-use-the-arm-client"></a>Para utilizar o cliente de processador

1. Instalar [Chocolatey](https://chocolatey.org/), que é um Gestor de pacote origem abrir para Windows. Abra uma janela de linha de comandos como administrador e, em seguida, execute o seguinte comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Instale o ARMClient executando o seguinte comando:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>Para efetuar uma procura simple utilizando o ARMClient

1. Iniciar sessão na sua conta Microsoft ou OrgID:

    ```
    armclient login
    ```

    Um início de sessão bem sucedido lista todas as subscrições associadas à conta determinada:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obter as áreas de trabalho do conjunto de aplicações de gestão de operações:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Uma chamada efetuada com êxito de obter seria saída todas as áreas de trabalho associadas à subscrição:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Crie a variável de pesquisa:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Procure utilizando a sua nova variável de pesquisa:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Inicie sessão exemplos de referência de análise pesquisa REST API
Os exemplos seguintes mostram como pode utilizar a API de pesquisa.

### <a name="search---actionread"></a>Procura - ação/lidas

**Url de exemplo:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Pedido:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
A tabela seguinte descreve as propriedades que estão disponíveis.

|**Propriedade**|**Descrição**|
|---|---|
|início|O número máximo de resultados a devolver.|
|Realce|Contém parâmetros pre e registar, normalmente utilizados para realçar os campos correspondentes|
|tipo de ficheiro|Prefixos a cadeia determinada para os campos correspondentes.|
|publicar|Acrescenta a cadeia determinada para os campos correspondentes.|
|consulta|A consulta de pesquisa utilizada para recolher e devolver os resultados.|
|Iniciar|Início da janela de tempo que pretende resultados para não é possível encontrar.|
|fim|Fim da janela de tempo que pretende resultados para não é possível encontrar.|


**Resposta:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Procurar / {ID} - ação/lidas

**Pedir o conteúdo de uma pesquisa guardado:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Se a pesquisa devolve com o estado de 'Pendente', os resultados actualizados de consulta, em seguida, pode ser feita através esta API. Após 6 mínimo, o resultado da pesquisa será ignorado da cache de e desaparece HTTP será devolvido. Se o pedido de pesquisa inicial devolvido um Estado 'Efetuada com êxito' imediatamente, não será adicionado à cache a causar este API devolver HTTP desaparece se consultado. Os conteúdos de um resultado de HTTP 200 será o mesmo formato, como o pedido de pesquisa inicial apenas com valores atualizados.

### <a name="saved-searches---rest-only"></a>Pesquisas - guardadas apenas resto

**Pedido de lista de pesquisas guardadas:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Métodos suportados: GET colocar eliminar

Métodos de coleções de sites suportados: obter

A tabela seguinte descreve as propriedades que estão disponíveis.

|Propriedade|Descrição|
|---|---|
|ID|O identificador exclusivo.|
|ETag|**Necessário para patches**. Actualizados pelo servidor no cada escrita. Valor tem de ser igual ao valor armazenado atual ou ' *' para atualizar. 409 devolvido para valores antigo/inválido.|
|Properties.Query|**Obrigatório**. A consulta de pesquisa.|
|properties.displayName|**Obrigatório**. O nome a apresentar definido utilizador da consulta. Se considerado um recurso Azure, isto seria uma etiqueta.|
|Properties.Category|**Obrigatório**. Os definidos pelo utilizador categoria da consulta. Se considerado um recurso Azure isto seria uma etiqueta.|

>[AZURE.NOTE] A API de pesquisa de análise de registo atualmente devolve criados pelo utilizador pesquisas quando consultado para pesquisas guardadas numa área de trabalho guardadas. API não irá devolver procuras guardadas fornecidas pela soluções neste momento. Esta funcionalidade será adicionada a uma data posterior.

### <a name="create-saved-searches"></a>Criar procuras guardadas

**Pedido:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Eliminar guardado pesquisas

**Pedido:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Pesquisas de atualização guardada

 **Pedido:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadados - JSON apenas

Eis uma forma para ver os campos para todos os tipos de registo para os dados recolhidos na área de trabalho. Por exemplo, se pretender que sabe se o tipo de evento tem um computador com o nome de campo, em seguida, esta é uma forma de procurar e confirme.

**Pedido para campos:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Resposta:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

A tabela seguinte descreve as propriedades que estão disponíveis.

|**Propriedade**|**Descrição**|
|---|---|
|nome|Nome do campo.|
|DisplayName em grupos|O nome a apresentar do campo.|
|tipo|O tipo do valor do campo.|
|facetable|Combinação de corrente 'indexado', ' armazenados ' e 'faceta' Propriedades.|
|apresentar|Propriedade 'apresentar atual. TRUE se o campo estiver visível na pesquisa.|
|ownerType|Reduzido apenas tipos de que pertencem a do onboarded IP.|


## <a name="optional-parameters"></a>Parâmetros opcionais
As informações seguintes descrevem os parâmetros opcionais disponíveis.

### <a name="highlighting"></a>Realce

O parâmetro de "Realce" é um parâmetro opcional que pode utilizar para pedir a subsystem de pesquisa incluem um conjunto de marcadores na sua resposta.

Estes marcadores indicam o início e de fim texto realçado que corresponde aos termos fornecidos na sua consulta de pesquisa.
Pode especificar os marcadores de início e de fim que serão utilizados pela pesquisa para moldar o termo realçado.

**Consulta de pesquisa de exemplo**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Resultado do exemplo:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Repare que o resultado acima contém uma mensagem de erro que tenha sido o prefixo e acrescentada.

## <a name="computer-groups"></a>Grupos de computador

Os grupos de computador são pesquisas guardadas especiais que devolvem um conjunto de computadores.  Pode utilizar um grupo de computador nas outras consultas para limitar os resultados para os computadores no grupo.  Um grupo de computador é implementado como uma procura guardada com uma etiqueta de grupo com um valor de computador.

Segue-se uma resposta de exemplo para um grupo de computador.

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>Obter grupos de computador

Utilize o método de introdução com o ID de grupo para obter um grupo de computador.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Criar ou actualizar um grupo de computador

Utilize o método de colocar com um único guardados ID de pesquisa para criar um novo grupo de computador. Se utilizar um ID de grupo já existente do computador, em seguida, que um será modificado. Quando cria um grupo de computador na consola do OMS, o ID é criado a partir do grupo e o nome.

A consulta utilizada para a definição do grupo tem de devolver um conjunto de computadores para o grupo funcionar corretamente.  É recomendado que terminar a consulta com *| Computador distinto* para garantir que os dados corretos são devolvidos.

A definição da procura guardada tem de incluir uma etiqueta denominada grupo com um valor de computador para a pesquisa para ser classificados como um grupo de computador.

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>Eliminar grupos de computador

Utilize o método de eliminar com o ID de grupo para eliminar um grupo de computador.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Próximos passos

- Saiba [as pesquisas de registo](log-analytics-log-searches.md) construir consultas através de campos personalizados no critério.
