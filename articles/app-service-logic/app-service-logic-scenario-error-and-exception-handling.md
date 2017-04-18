<properties
    pageTitle="Registo e processamento de erros no lógica aplicações | Microsoft Azure"
    description="Ver um caso de utilização de vida real de erro avançado processamento e registo com aplicações de lógica"
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager="anneta"
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# <a name="logging-and-error-handling-in-logic-apps"></a>Registo e processamento de erros no aplicações de lógica

Este artigo descreve como pode expandir uma aplicação de lógica para suportar melhor exceções. Se for um caso de utilização de vida real e os nossos resposta à pergunta de "Lógica aplicações suporta exceção e processamento de erros?"

>[AZURE.NOTE]A versão atual da funcionalidade de lógica aplicações Azure aplicação do serviço da Microsoft fornece um modelo padrão para as respostas de ação.
>Isto inclui validação interna e respostas de erro devolvidas a partir de uma aplicação de API.

## <a name="overview-of-the-use-case-and-scenario"></a>Descrição geral de casos de utilização e cenário

O bloco seguinte é o caso de utilização deste artigo.
Uma organização cuidados de saúde famoso interessada-nos para desenvolver uma solução do Azure que pretende criar um portal de paciente utilizando o Microsoft Dynamics CRM Online. São necessitavam para enviar registos de compromisso entre o portal paciente Dynamics CRM Online e a equipa de vendas.  Vamos foram-lhe pedidos para utilizar o padrão de [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) para todos os registos pacientes.

O projeto teve dois requisitos principais:  

 -  Um método para iniciar sessão registos enviados a partir do portal do Dynamics CRM Online
 -  Uma forma de ver erros que ocorrido dentro do fluxo de trabalho


## <a name="how-we-solved-the-problem"></a>Como é resolvido o problema

>[AZURE.TIP] Pode ver um vídeo de alto nível do projeto o [Grupo de utilizadores de integração de](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integração com o grupo de utilizadores").

Optamos por [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "Azure DocumentDB") como um repositório para que os registos de erro e registo (DocumentDB refere-se aos registos como documentos). Porque lógica aplicações tem um modelo de padrão para todas as respostas, teria não temos que criar um esquema personalizado. Vamos podia criar uma aplicação de API para **Inserir** e **consulta** para registos de erro e inicie sessão. Também podemos poderia definir um esquema para cada da aplicação de API.  

Outro requisito foi remover registos após uma determinada data. DocumentDB tem uma propriedade denominada [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Time to Live") (TTL), que permitido-nos definir um valor de **Time to Live** para cada registo ou a coleção. Isto eliminado a necessidade de eliminar registos no DocumentDB manualmente.

### <a name="creation-of-the-logic-app"></a>Criação da aplicação de lógica

O primeiro passo é criar a aplicação de lógica e carregá-lo no estruturador de. Neste exemplo, estamos a utilizar aplicações de lógica de elemento principal-subordinado. Imaginemos que já criou o elemento principal e ir para criar uma aplicação de lógica subordinado.

Uma vez que recomendamos irão ser registo o registo a chegar fora do Dynamics CRM Online, vamos começar na parte superior. Precisamos de utilize um acionador de pedido de uma vez que a aplicação de lógica principal accionadores este elemento subordinado.

> [AZURE.IMPORTANT] Para concluir este tutorial, terá de criar uma base de dados DocumentDB e duas colecções (registo e erros).

### <a name="logic-app-trigger"></a>Acionador de aplicação de lógica

Estamos a utilizar um pedido de accionador conforme mostrado no seguinte exemplo.

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### <a name="steps"></a>Passos

É necessário iniciar sessão a origem (pedido) do registo paciente a partir do portal do Dynamics CRM Online.

1. Precisamos de obter um novo registo de compromisso do Dynamics CRM Online.
    O accionador provenientes do CRM fornece-nos com o **CRM PatentId**, **tipo de registo**, **novo ou actualizado registo** (novo ou atualizar valor booleano) e **SalesforceId**. O **SalesforceId** pode ser nulo porque são utilizada apenas para uma atualização.
    Vamos irá obter o registo CRM utilizando o CRM **PatientID** e o **Tipo de registo**.
1. Em seguida, precisamos de adicionar a nossa aplicação DocumentDB API **InsertLogEntry** operação conforme apresentado nos seguintes valores.


#### <a name="insert-log-entry-designer-view"></a>Inserir a vista do estruturador de entrada registo

![Inserir a entrada de registo](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### <a name="insert-error-entry-designer-view"></a>Inserir a vista do estruturador de entrada erro
![Inserir a entrada de registo](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### <a name="check-for-create-record-failure"></a>Verificar existência de criar falha de registo

![Condição](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## <a name="logic-app-source-code"></a>Código de origem de aplicação de lógica

>[AZURE.NOTE]  Seguem-se apenas amostras. Uma vez que este tutorial baseia-se uma implementação atualmente em produção, o valor de um **Nó de origem** não podem ser apresentadas de propriedades que estão relacionados com agendar um compromisso.

### <a name="logging"></a>Funcionalidade de registo
O exemplo de código de aplicação lógica seguinte mostra como processar o registo.

#### <a name="log-entry"></a>Entrada de registo
Este é o código de origem da aplicação de lógica para inserir uma entrada de registo.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Pedido de registo

Esta é a mensagem de pedido de registo registada para a aplicação de API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}"
        }
    }

```


#### <a name="log-response"></a>Registar resposta

Esta é a mensagem de resposta do registo a partir da aplicação de API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "\"0400fc2f-0000-0000-0000-575b3ff00000\"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{\"Pragma\":\"no-cache\",\"x-ms-request-id\":\"e750c9a9-bd48-44c4-bbba-1688b6f8a132\",\"OData-Version\":\"4.0\",\"Cache-Control\":\"no-cache\",\"Date\":\"Fri, 10 Jun 2016 22:31:56 GMT\",\"Set-Cookie\":\"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1\",\"Server\":\"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0\",\"X-AspNet-Version\":\"4.0.30319\",\"X-Powered-By\":\"ASP.NET\",\"Content-Length\":\"1935\",\"Content-Type\":\"application/json; odata.metadata=minimal; odata.streaming=true\",\"Expires\":\"-1\"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Agora vamos ver os passos de processamento de erros.


### <a name="error-handling"></a>Processamento de erros

O exemplo seguinte de código lógica aplicações mostra como implementar o processamento de erros.

#### <a name="create-error-record"></a>Criar o registo de erros

Este é o código de origem lógica aplicações para criar um registo de erros.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}          
```

#### <a name="insert-error-into-documentdb--request"></a>Erro ao inserir para DocumentDB – pedir

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MasterID_mp__c\":\"\",\"C_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"ONY_ID__c\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-documentdb--response"></a>Inserir erro DocumentDB – resposta


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "\"0c00eaac-0000-0000-0000-575b3fdc0000\"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{\"Account_Class_vod__c\":\"PRAC\",\"Account_Status_MED__c\":\"I\",\"CRM_HUB_ID__c\":\"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0\",\"Credentials_vod__c\":\"DO - Degree level is DO\",\"DTC_ID_MED__c\":\"\",\"Fax\":\"\",\"FirstName\":\"A\",\"Gender_vod__c\":\"\",\"IMS_ID__c\":\"\",\"LastName\":\"BAILEY\",\"MterID_mp__c\":\"\",\"Medicis_ID_MED__c\":\"851588\",\"Middle_vod__c\":\"\",\"NPI_vod__c\":\"\",\"PDRP_MED__c\":false,\"PersonDoNotCall\":false,\"PersonEmail\":\"\",\"PersonHasOptedOutOfEmail\":false,\"PersonHasOptedOutOfFax\":false,\"PersonMobilePhone\":\"\",\"Phone\":\"\",\"Practicing_Specialty__c\":\"FM - FAMILY MEDICINE\",\"Primary_City__c\":\"\",\"Primary_State__c\":\"\",\"Primary_Street_Line2__c\":\"\",\"Primary_Street__c\":\"\",\"Primary_Zip__c\":\"\",\"RecordTypeId\":\"012U0000000JaPWIA0\",\"Request_Date__c\":\"2016-06-10T22:31:55.9647467Z\",\"XXXXXXX\":\"\",\"Specialty_1_vod__c\":\"\",\"Suffix_vod__c\":\"\",\"Website\":\"\"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Resposta de erro do Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="returning-the-response-back-to-the-parent-logic-app"></a>Voltar a resposta novamente para a aplicação de lógica principal

Depois de ter a resposta, é possível passar novamente para a aplicação de lógica principal.

#### <a name="return-success-response-to-the-parent-logic-app"></a>Devolver sucesso resposta à aplicação de lógica principal

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "   Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-the-parent-logic-app"></a>Erro de retorno resposta à aplicação de lógica principal

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="documentdb-repository-and-portal"></a>Repositório de DocumentDB e portal

A nossa solução adicionados capacidades adicionais com [DocumentDB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Portal de gestão de erro

Para ver os erros, pode criar uma aplicação web do MVC para apresentar os registos de erro de DocumentDB. Operações de **lista**, **Detalhes**, **Editar**e **Eliminar** estão incluídas na versão atual.

> [AZURE.NOTE]Editar operação: DocumentDB faz uma substituir de todo o documento.
> Os registos apresentados na **lista** e vistas de **detalhe** são amostras apenas. Não são registos compromisso paciente real.

Seguem-se exemplos de detalhes da aplicação nosso MVC criados com a abordagem descrita anteriormente.

#### <a name="error-management-list"></a>Lista de gestão de erro

![Lista de erros](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Vista de detalhes de gestão de erro

![Detalhes do erro](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal de gestão de registo

Para ver os registos de início, criámos também uma aplicação web do MVC.  Seguem-se exemplos de detalhes da aplicação nosso MVC criados com a abordagem descrita anteriormente.

#### <a name="sample-log-detail-view"></a>Vista de detalhes de registo de exemplo

![Vista de detalhes de registo](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Detalhes da aplicação API

#### <a name="logic-apps-exception-management-api"></a>API de gestão de exceção de aplicações do lógica

Nossa aplicação de API de gestão do abrir origem lógica aplicações exceção fornece as seguintes funcionalidades.

Existem dois controladores:

- **ErrorController** insere um registo de erro (documento) numa coleção de DocumentDB.
- **LogController** Insere um registo (documento) numa coleção de DocumentDB.

> [AZURE.TIP] Ambos os controladores utilizam `async Task<dynamic>` operações. Esta opção permite-operações para ser resolvido o tempo de execução, para que possam criar o esquema de DocumentDB no corpo da operação de.

Todos os documentos na DocumentDB tem de ter um ID exclusivo. Estamos a utilizar `PatientId` e adicionar um carimbo de data/hora que é convertido num valor de data/hora Unix (duplo). Vamos truncá-lo para remover o valor fraccionário.

Pode ver o código fonte da nossa controlador de erro API [do GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Chamamos API através de uma aplicação de lógica utilizando a seguinte sintaxe.

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

A expressão no exemplo anterior código está a verificar o estado de *Create_NewPatientRecord* de **falhou**.

## <a name="summary"></a>Resumo

- Pode implementar facilmente a funcionalidade de registo e processamento de erros numa aplicação de lógica.
- Pode utilizar DocumentDB como o repositório de registos de erro e registo (documentos).
- Pode utilizar MVC para criar um portal para apresentar os registos de erro e registo.

### <a name="source-code"></a>Código fonte
O código de origem para a gestão de exceção lógica aplicações aplicação API está disponível nesta [GitHub repositório](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de gestão de exceção de aplicação de lógica").


## <a name="next-steps"></a>Próximos passos
- [Ver mais exemplos de aplicações de lógica e aos cenários](app-service-logic-examples-and-scenarios.md)
- [Saiba mais sobre aplicações de lógica de ferramentas de monitorização](app-service-logic-monitor-your-logic-apps.md)
- [Criar um modelo de implementação automatizada da aplicação de lógica](app-service-logic-create-deploy-template.md)
