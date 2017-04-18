<properties
   pageTitle="Gestor de recursos REST APIs | Microsoft Azure"
   description="Uma descrição geral dos exemplos de autenticação e a utilização do Gestor de recursos REST APIs"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>Gestor de recursos REST APIs

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Clip Azure](xplat-cli-azure-resource-manager.md)
- [Portal](./azure-portal/resource-group-portal.md) 
- [REST API](resource-manager-rest-api.md)

Atrás de cada chamada para o Gestor de recursos do Azure, atrás de cada modelo implementado, atrás de todas as contas de armazenamento configurado não existe uma ou várias chamadas para o Gestor de recursos de Azure RESTful API. Este tópico é dedicado para esses APIs e como pode ligá-los sem utilizar qualquer SDK de todo. Isto pode ser muito útil se pretender que o controlo total de todos os pedidos para Azure ou se o SDK para o idioma preferido não está disponível ou não suporta as operações que pretende efetuar.

Este artigo não vai ficar através de cada API que é apresentado quando se clica no Azure, mas irá preferir utilizar alguns como exemplo como prosseguir e ligar aos mesmos. Se compreender as noções básicas, em seguida, pode prosseguir e ler a [Referência da API do Azure Gestor de recursos do resto](https://msdn.microsoft.com/library/azure/dn790568.aspx) para encontrar informações detalhadas sobre como utilizar o resto das APIs.

## <a name="authentication"></a>Autenticação
Autenticação para processador é processada ao Azure Active Directory (AD). Para poder ligar a qualquer API tem primeiro para autenticar com Azure AD para receber um token de autenticação que o utilizador pode passar a todos os pedidos. Como podemos são descrever uma chamada pura diretamente para o resto APIs, podemos será também partem do princípio de que não pretende que autenticar com uma palavra-passe de nome de utilizador normal onde um pop-valorização ecrã poderá pedir-lhe por nome de utilizador e palavra-passe e talvez até mesmo outras mecanismos de autenticação utilizados em dois cenários de autenticação de fator. Por conseguinte, vamos criar o que é denominado uma aplicação do Azure AD e um Principal de serviço que será utilizada para iniciar sessão com. Mas lembre-se de que o Azure AD suporta vários procedimentos de autenticação e todos eles podem ser utilizados para obter esse token de autenticação precisamos para pedidos de API subsequentes.
Siga [Criar Azure AD aplicação e o princípio do serviço](./resource-group-create-service-principal-portal.md) para obter instruções passo a passo.

### <a name="generating-an-access-token"></a>Gerar um Token de acesso 
Autenticação contra Azure AD é efetuada ao ligar para o Azure AD, que se encontra no login.microsoftonline.com. Para autenticar o utilizador tem de ter as seguintes informações:

* ID de inquilino do Azure AD (o nome desse Azure AD estiver a utilizar para iniciar sessão, muitas vezes a mesma como a sua empresa, mas não é necessário)
* ID da aplicação (tomada durante o passo de criação de aplicação do Azure AD)
* Palavra-passe (que selecionou ao criar a aplicação do Azure AD)

Na seguir o pedido de HTTP certificar-se de que substitua os valores corretos "ID de inquilino do Azure AD", "ID da aplicação" e "Palavra-passe".

**Pedido de HTTP genérico:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

… irá (se a autenticação é concluída com êxito) resultar numa resposta semelhante ao seguinte:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(Access_token na resposta acima foram abreviados para aumentar a legibilidade)

**Gerar token de acesso de festa a utilizar:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Gerar token de acesso através do PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

A resposta contém um Token de acesso, informações sobre quanto tempo esse token do é válida e informações sobre qual o recurso pode utilizar esse token para.
Token de acesso que recebeu na chamada HTTP anterior deve ser passado para pedido de todos os à API processador como um cabeçalho denominado "Autorização" com o valor "Portadores YOUR_ACCESS_TOKEN". Tenha em atenção o espaço entre "Portadores" e o Token de acesso.

Como pode ver a partir do resultado de HTTP acima, o token é válido para um determinado período de tempo durante o qual deve em cache e reutilização esse mesmo token. Mesmo se for possível autenticar Azure AD para cada chamada API, seria altamente ineficaz.

## <a name="calling-arm-rest-apis"></a>Chamar processador REST APIs

[Azure Gestor de recursos REST APIs encontram-se documentados aqui](https://msdn.microsoft.com/library/azure/dn790568.aspx) e -lo do fora do âmbito deste tutorial para ficar a utilização de cada e todas do documento. Esta documentação irá utilizar apenas alguns APIs para explicar a utilização da básica das APIs e depois que recomendamos referem-se para a documentação oficial.

### <a name="list-all-subscriptions"></a>Lista todas as subscrições

Uma das operações mais simples que pode fazer consiste em listar as subscrições disponíveis que pode aceder. Na abaixo pedido pode ver como o Token de acesso é transmitido no como um cabeçalho.

(Substitua YOUR_ACCESS_TOKEN seu real Token de acesso.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

… e como resultado, irá obter uma lista de subscrições que é permitido este Principal do serviço para aceder a

(IDs de subscrição abaixo foram abreviados para legibilidade)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Listar todos os grupos de recurso uma subscrição específica

Todos os recursos disponíveis com a API processador são aninhados dentro de um grupo de recursos. Vamos a consulta processador para grupos de recursos existentes no nosso subscrição utilizando o abaixo pedido HTTP GET. Repare como o ID da subscrição é transmitido no como parte do URL este período de tempo.

(Substitua YOUR_ACCESS_TOKEN e ID_SUBSCRIÇÃO com o seu real Token de acesso e ID da subscrição)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

A resposta obtém irá dependem de se tiver qualquer grupos de recursos definidos e em caso afirmativo, quantas.

(IDs de subscrição abaixo foram abreviados para legibilidade)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Até ao momento podemos tenha apenas foi consultar as APIs processador para obter informações, é hora podemos criar alguns recursos em vez disso e vamos começar por mais simples de-los a todos, um grupo de recursos. O pedido de HTTP seguinte cria um novo grupo de recursos numa região localização da sua preferência e adiciona-lhe um ou mais etiquetas (exemplo abaixo realmente apenas adiciona uma tag).

(Substitua YOUR_ACCESS_TOKEN, ID_SUBSCRIÇÃO, RESOURCE_GROUP_NAME seu real Token de acesso, ID da subscrição e o nome do grupo de recursos que pretende criar)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Se tiver êxito, irá obter uma resposta semelhante a esta

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Que criou com êxito um grupo de recursos no Azure. Parabéns!

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Implementar recursos a um grupo de recursos utilizando um modelo de processador

Com braço, pode implementar os recursos da utilização de modelos de processador. Um modelo de processador define vários recursos e respectivas dependências. Para esta secção podemos irá apenas assumir estiver familiarizado com os modelos de processador e iremos apenas mostrar-lhe como tornar a chamada à API para começar a implementação de um. Uma documentação detalhada dos modelos de processador pode ser encontrada aqui.

Implementação de um modelo de processador não diferem muito para como ligar a outras APIs. Um aspecto importante é que a implementação de um modelo pode demorar algum tempo, consoante o que é dentro do modelo e a chamada à API apenas irá devolver e é como programador para consultar o estado da implementação para poder saber para quando concluir o processo de implementação.

Neste exemplo, vamos utilizar um modelo de processador expostas publicamente disponível no [GitHub](https://github.com/Azure/azure-quickstart-templates). O modelo que vamos utilizar irá implementar uma VM Linux à região de EUA Ocidental. Apesar deste modelo terão o modelo disponível num repositório público como GitHub, também pode selecionar para passar o modelo completo como parte do pedido. Tenha em atenção que fornecemos valores de parâmetros como parte do pedido que será utilizada dentro do modelo utilizado.

(Substitua ID_SUBSCRIÇÃO, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD e DNS_NAME_FOR_PUBLIC_IP valores adequados o pedido)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

A resposta JSON bastante comprida para este pedido foram omitidos para melhorar a legibilidade desta documentação. A resposta irá conter informações sobre a implementação transformada em modelo que acabou de criar.

