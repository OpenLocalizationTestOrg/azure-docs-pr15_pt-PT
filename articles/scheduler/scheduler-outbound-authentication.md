<properties
 pageTitle="Autenticação de saída de programador"
 description="Autenticação de saída de programador"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>Autenticação de saída de programador

Poderá ter do Programador de tarefas a chamada para serviços de que precisam de autenticação. Desta forma, um serviço chamado pode determinar se a tarefa de programador pode aceder aos seus recursos. Alguns destes serviços incluem outros serviços do Azure, Salesforce.com, Facebook e Web sites personalizados seguros.

## <a name="adding-and-removing-authentication"></a>Adicionar e remover autenticação

Adicionar a autenticação para uma tarefa de programador é simple – adicionar um elemento subordinado do JSON `authentication` para o `request` elemento quando criar ou atualizar uma tarefa. Segredos passaram para o serviço de programador num pedido de mensagem, PATCHES ou local – como parte da `authentication` objeto – nunca são devolvidos nas respostas. Em respostas, informações secretas são definidas como nulo ou poderá ter um token público que representa a entidade autenticada.

Para remover a autenticação, colocar ou correcção a tarefa explicitamente, definindo a `authentication` objeto nulo. Não verá as propriedades de autenticação novamente na resposta.

Neste momento, os tipos de autenticação suportados apenas são o `ClientCertificate` modelo (para utilizar os certificados SSL/TLS de cliente), o `Basic` modelo (para a autenticação básica) bem como a `ActiveDirectoryOAuth` modelo (para autenticação do Active Directory OAuth.)

## <a name="request-body-for-clientcertificate-authentication"></a>Corpo de pedidos de autenticação ClientCertificate

Ao adicionar a autenticação, utilizando o `ClientCertificate` do modelo, especifique os seguintes elementos adicionais no corpo do pedido.  

|Elemento|Descrição|
|:---|:---|
|_autenticação (elemento principal)_|Objeto de autenticação para utilizar um certificado de cliente SSL.|
|_tipo_|Obrigatório. Tipo de autenticação. Para certificados SSL de cliente, o valor deve ser `ClientCertificate`.|
|_PFX_|Obrigatório. Conteúdo do ficheiro PFX codificado em Base64.|
|_palavra-passe_|Obrigatório. Palavra-passe para aceder ao ficheiro PFX.|


## <a name="response-body-for-clientcertificate-authentication"></a>Corpo de resposta para ClientCertificate autenticação

Quando é enviado um pedido de com informações de autenticação, a resposta contém os seguintes elementos relacionados com autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento principal)_ |Objeto de autenticação para utilizar um certificado de cliente SSL.|
|_tipo_ |Tipo de autenticação. Para certificados SSL de cliente, o valor for `ClientCertificate`.|
|_certificateThumbprint_ |A impressão do certificado digital.|
|_certificateSubjectName_ |O nome distinto assunto do certificado.|
|_certificateExpiration_ |A data de validade do certificado.|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Exemplo de resto pedido para ClientCertificate autenticação

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Exemplo resto resposta para ClientCertificate autenticação

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Corpo do pedido para a autenticação básica

Ao adicionar a autenticação, utilizando o `Basic` do modelo, especifique os seguintes elementos adicionais no corpo do pedido.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento principal)_ |Objecto de autenticação para utilizando a autenticação básica.|
|_tipo_ |Obrigatório. Tipo de autenticação. Para a autenticação básica, o valor deve ser `Basic`.|
|_nome de utilizador_ |Obrigatório. Nome de utilizador para autenticar.|
|_palavra-passe_ |Obrigatório. Palavra-passe para autenticar.|

## <a name="response-body-for-basic-authentication"></a>Corpo de resposta para a autenticação básica

Quando é enviado um pedido de com informações de autenticação, a resposta contém os seguintes elementos relacionados com autenticação.

|Elemento|Descrição|
|:--|:--|
|_autenticação (elemento principal)_ |Objecto de autenticação para utilizando a autenticação básica.|
|_tipo_ |Tipo de autenticação. Para a autenticação básica, o valor for `Basic`.|
|_nome de utilizador_ |O nome de utilizador autenticado.|

## <a name="sample-rest-request-for-basic-authentication"></a>Exemplo de resto pedido para a autenticação básica

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Resposta do resto de exemplo para a autenticação básica

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Corpo de pedidos de autenticação de ActiveDirectoryOAuth

Ao adicionar a autenticação, utilizando o `ActiveDirectoryOAuth` do modelo, especifique os seguintes elementos adicionais no corpo do pedido.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento principal)_ |Objecto de autenticação para utilizando a autenticação do ActiveDirectoryOAuth.|
|_tipo_ |Obrigatório. Tipo de autenticação. Para a autenticação de ActiveDirectoryOAuth, o valor deve ser `ActiveDirectoryOAuth`.|
|_inquilino_ |Obrigatório. O identificador de inquilino para o inquilino do Azure AD.|
|_audiência_ |Obrigatório. Este é definido como https://management.core.windows.net/.|
|_clientId_ |Obrigatório. Fornece o identificador de cliente para a aplicação do Azure AD.|
|_secreta_ |Obrigatório. Secreta do cliente que está a pedir o token.|

### <a name="determining-your-tenant-identifier"></a>Para determinar o identificador de inquilino

Pode encontrar o identificador de inquilino para o inquilino do Azure AD executando `Get-AzureAccount` no Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Corpo de resposta para autenticação ActiveDirectoryOAuth

Quando é enviado um pedido de com informações de autenticação, a resposta contém os seguintes elementos relacionados com autenticação.

|Elemento |Descrição |
|:--|:--|
|_autenticação (elemento principal)_ |Objecto de autenticação para utilizando a autenticação do ActiveDirectoryOAuth.|
|_tipo_ |Tipo de autenticação. Para a autenticação de ActiveDirectoryOAuth, o valor for `ActiveDirectoryOAuth`.|
|_inquilino_ |O identificador de inquilino para o inquilino do Azure AD. |
|_audiência_ |Este é definido como https://management.core.windows.net/.|
|_clientId_ |O identificador de cliente para a aplicação do Azure AD.|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Exemplo de resto pedido para autenticação ActiveDirectoryOAuth

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Exemplo resto resposta para autenticação ActiveDirectoryOAuth

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Consulte também


 [O que é o Scheduler?](scheduler-intro.md)

 [Azure Scheduler conceitos, terminologia e hierarquia entidade](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Programador de tarefas](scheduler-plans-billing.md)

 [Referência de programador REST API Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure cmdlets do PowerShell de Programador de referência](scheduler-powershell-reference.md)

 [Azure Scheduler alta disponibilidade e fiabilidade](scheduler-high-availability-reliability.md)

 [Limites de programador Azure, predefinições e códigos de erro](scheduler-limits-defaults-errors.md)
