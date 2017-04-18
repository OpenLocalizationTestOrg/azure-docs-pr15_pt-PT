<properties 
    pageTitle="Novo esquema versão 2015-08-01-pré-visualização" 
    description="Saiba como escrever a definição de JSON para a versão mais recente das aplicações de lógica" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>
    
# <a name="new-schema-version-2015-08-01-preview"></a>Novo esquema versão 2015-08-01-pré-visualização

O nova esquema e a API versão para as aplicações de lógica tem um número de melhoramentos de melhorar a fiabilidade e facilidade de utilização de aplicações de lógica. Existem diferenças fundamentais 4:

1. O tipo de ação **APIApp** foi atualizado para um novo tipo de ação **APIConnection** .
2. **Repita** foi mudado para **Foreach**.
3. A aplicação de API **Escuta HTTP** já não é necessária.
4. Chamar fluxos de trabalho subordinados utiliza um novo esquema.

## <a name="1-moving-to-api-connections"></a>1. a mover para ligações de API

A alteração maior é que já não precisar implementar aplicações de API para a sua subscrição do Azure para utilizar a API do. Existem maneiras de utilizar APIs 2:
* API gerido do
* A API Web personalizado

Cada um destes é processada de forma ligeiramente diferente, porque a sua gestão e modelos de alojamento são diferentes. Uma neste modelo vantagens é que já não está restringido a recursos que são implementados no seu grupo de recursos. 

### <a name="managed-apis"></a>API gerido

Existem várias da API que é geridas pelo Microsoft em seu nome, como o Office 365, Salesforce, Twitter, FTP etc... Alguns destes API gerida podem ser utilizados como-for, como o Bing traduzir, enquanto outras necessitam de configuração. Esta configuração chama-se uma *ligação*.

Por exemplo, quando utiliza o Office 365, tem de criar uma ligação que contém o token de início de sessão no Office 365. Este token será segura armazenado e atualizado para que a sua aplicação de lógica sempre pode chamar a API do Office 365. Em alternativa, se pretender ligar ao seu servidor SQL ou FTP, terá de criar uma ligação que tem a cadeia de ligação. 

Dentro da definição estas ações são chamadas `APIConnection`. Eis um exemplo de uma ligação que liga para Office 365 para enviar uma mensagem de e-mail:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

A parte das entradas que seja exclusiva para ligações de API é o `host` objeto. Esta contém duas partes: `api` e `connection`.

O `api` tem o tempo de execução do URL do onde que API gerida do está alojado. Pode ver todas as API geridas disponíveis para ao contactar o suporte `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Quando utiliza uma API, poderá ou poderá não ter qualquer **parâmetros de ligação** definidos. Se não tiver nenhuma **ligação** é necessário. Caso isso aconteça, terá de criar uma ligação. Quando criar essa ligação que vai ter o nome que escolher e, em seguida, pode que na referência a `connection` dentro do objeto a `host` objeto. Para criar uma ligação num grupo de recursos, contacte:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Com o corpo seguinte:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Implementar gerido APIs num modelo de Gestor de recursos do Azure

Pode criar uma aplicação completa num modelo de processador desde que não necessite de interativo iniciar sessão. Se necessitar de iniciar sessão, pode configurar tudo com o modelo de processador, mas continuará a ter a visitar o portal para autorizar as ligações. 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

Pode ver neste exemplo que as ligações são apenas normais recursos que live no seu grupo de recursos. Que fazem referência a managedAPIs disponíveis para si na sua subscrição.

### <a name="your-custom-web-apis"></a>O APIs Web personalizado

Se utiliza o seu próprio API (especificamente, não gerido Microsoft aqueles), deve utilizar a ação de **HTTP** incorporada para lhes chamar. Para ter uma experiência ideal, deverá expor um ponto final de swagger para sua API. Isto irá activar o estruturador de aplicação de lógica para compor entradas e saídas para sua API. Sem um swagger, o estruturador apenas poderão mostrar as entradas e saídas como objectos JSON opacas.

Eis um exemplo que apresenta os novos `metadata.apiDefinitionUrl` propriedade:
```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Se aloja os seus API Web na **Aplicação de serviço** , em seguida, é provável automaticamente aparecer na lista de ações disponíveis no designer. Caso contrário, terá de copie o URL diretamente. O ponto final swagger tem de ser não autenticado para poder ser utilizada dentro de estruturador de aplicações de lógica (embora poderá segura API com qualquer métodos são suportados na Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>Utilizar as aplicações de API já implementadas com 2015-08-01-pré-visualização

Se uma aplicação de API implementado anteriormente, pode ligá-lo através da ação de **HTTP** .

Por exemplo, se utilizar o Dropbox para ficheiros de lista, pode ter este aspeto na sua definição de versão do esquema de **2014-12-01-pré-visualização** :

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Pode construir a ação de HTTP equivalente como abaixo (a secção de parâmetros de permanece de definição de aplicação lógica inalterada):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Observar estas propriedades um a um:

| Propriedade de ação |  Descrição |
| --------------- | -----------  |
| `type` | `Http`em vez de`APIapp` |
| `metadata.apiDefinitionUrl` | Se pretender utilizar esta ação no estruturador de aplicações de lógica, irá querer incluir o ponto final de metadados. Esta fórmula foi criada a partir de:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Esta fórmula foi criada a partir de:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre`POST` |
| `inputs.body` | Idêntico aos parâmetros de aplicação api | 
| `inputs.authentication` | Idêntico à autenticação de aplicação api |

Esta abordagem deverá funcionar para todas as ações de aplicação API. No entanto, tenha em atenção que estas aplicações de API anterior já não são suportadas e deverá mover para uma das duas outras opções acima (uma API gerida ou que aloja a sua personalizado a API do Web).

## <a name="2-repeat-renamed-to-foreach"></a>2. repetir o nome alterado para Foreach

Para a versão anterior do esquema recebemos muitas comentários dos clientes que **repita** foi confusa e não corretamente capturar que foi realmente um para cada loop. Como resultado, podemos ter mudado nome-lo para **Foreach**. Por exemplo:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Agora deverá ser escrita como:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Anteriormente a função `@repeatItem()` foi utilizado para fazer referência a ser sujeita a iteração ao longo do item atual. Este foi simplificado bastará `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Referenciar saídas do Foreach
Para simplificar a ainda mais, saídas das ações **Foreach** não irão ser moldadas de um objeto denominado **repeatItems**. Isto significa que, Considerando que foram saídas de repetir o acima:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Agora irá estar:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Ao referenciar estas saídas, para chegar ao corpo da ação seria tem de fazer:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Agora que pode fazer em vez disso:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Com estas alterações, as funções `@repeatItem()`, `@repeatBody()` e `@repeatOutputs()` são removidos.

## <a name="3-native-http-listener"></a>3. nativa escuta HTTP 
As capacidades de escuta HTTP estão incorporadas, pelo que já não necessita de implementar uma aplicação de HTTP escuta API. Leia sobre [os detalhes na totalidade como fazer a sua lógica aplicação ponto final passível aqui](app-service-logic-http-endpoint.md). 

Com estas alterações, a função `@accessKeys()` é removida e foi substituído pela `@listCallbackURL()` função para fins de introdução o ponto final (quando necessário). Além disso, agora tem de definir pelo menos um accionador na sua aplicação de lógica agora. Se pretender `/run` o fluxo de trabalho, terá de ter uma de um `manual`, `apiConnectionWebhook` ou `httpWebhook` accionadores. 

## <a name="4-calling-child-workflows"></a>4. fluxos de trabalho de subordinados de chamada

Anteriormente, chamar fluxos de trabalho subordinados necessário aceder a esse fluxo de trabalho, introdução o token de acesso e, em seguida, colar que para a definição da aplicação lógica ao qual pretende ligar essa subordinado. Com a nova versão do esquema, o motor de aplicações lógica gera automaticamente uma SA o tempo de execução do fluxo de trabalho subordinado, que significa que não tem que colar qualquer segredos a definição.  Eis um exemplo:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Uma segunda melhoria é que recomendamos irá ser fornecendo fluxos de trabalho subordinados acesso total ao pedido de entrada. Isto significa que pode passar parâmetros na secção de *consultas* e no objeto *cabeçalhos* e que pode definir totalmente corpo todo.

Por fim, existem alterações necessárias para o fluxo de trabalho subordinado. Considerando que antes pode apenas chamar um fluxo de trabalho subordinado diretamente; Agora, terá de definir um ponto final de accionador no fluxo de trabalho para principal ligar. Normalmente, isto significa que irá adicionar um acionador de tipo **manual** e, em seguida, utilizar que na definição do principal. Tenha em atenção que a `host` propriedade especificamente tem um `triggerName`, uma vez que tem de especificar sempre que accionador que está a invocar.

## <a name="other-changes"></a>Outras alterações

### <a name="new-queries-property"></a>Nova propriedade de consultas
Todos os tipos de ação suportam agora uma nova entrada denominada **consultas**. Isto pode ser um objeto estruturado em vez de que está a ter reunir manualmente a cadeia.

### <a name="parse-function-renamed"></a>função Parse() cujo nome foi mudada
À medida que vai mais cedo adicionar mais tipos de conteúdo, o `parse()` função foi mudada para `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Brevemente: APIs de integração de empresa
Neste ponto específico, recomendamos ainda não tiver gerido versões das APIs de integração do Enterprise disponíveis (por exemplo, AS2). Estas irão ser brevemente como descrito no [mapa](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Entretanto, pode utilizar a API BizTalk implementado existentes através da ação HTTP, como descrito acima em "utilizar as aplicações de API já implementadas."
