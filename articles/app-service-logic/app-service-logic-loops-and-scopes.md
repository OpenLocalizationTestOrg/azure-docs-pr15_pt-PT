<properties
   pageTitle="Aplicações de lógica ciclos, âmbitos e Debatching | Microsoft Azure"
   description="Ciclo de aplicação de lógica, âmbito e debatching conceitos"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/14/2016"
   ms.author="jehollan"/>
   
# <a name="logic-apps-loops-scopes-and-debatching"></a>Aplicações de lógica ciclos, âmbitos e Debatching
  
>[AZURE.NOTE] Esta versão do artigo aplica-se ao esquema de lógica aplicações-04-01-pré-visualização 2016 e versões posteriores.  Conceitos são semelhantes para esquemas mais antigos, mas âmbitos só estão disponíveis para este esquema e versões posteriores.
  
## <a name="foreach-loop-and-arrays"></a>Ciclo de ForEach e matrizes
  
Lógica de aplicações permite-lhe Repetir continuamente um conjunto de dados e executar uma ação de cada item.  Isto é possível através de `foreach` ação.  No estruturador de, pode especificar para adicionar um para cada loop.  Após selecionar a matriz que pretender iteramos sobre, pode começar a adicionar cações.  Atualmente são limitadas ao livro apenas uma ação por foreach ciclo, mas esta restrição vai ser retirada nas próximos semanas.  Uma vez dentro do ciclo pode começar a especificar o que deve ocorrer em cada valor da matriz.

Se utilizar a vista de código, pode especificar um para cada loop como abaixo.  Este é um exemplo de um para cada loop que envia uma mensagem de e-mail para cada endereço de e-mail que contém 'microsoft.com':

```
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  A `foreach` ação pode iteramos sobre matrizes até 5000 linhas.  Cada iteração pode ser executados em paralelo, pelo que poderão ser necessária para adicionar mensagens a uma fila se for necessário o controlo do fluxo.
  
## <a name="until-loop"></a>Até ciclo
  
  Pode executar uma acção ou série de acções até uma condição é cumprida.  O cenário mais comum para que esta está a chamar um ponto final até chegar a resposta que procura.  No estruturador de, pode especificar para adicionar um até que a ligação.  Depois de adicionar ações dentro do ciclo, pode definir a condição de sair, bem como o ciclo limites.  Existe um atraso de 1 minuto entre ciclo ciclos.
  
  Se utilizar a vista de código, pode especificar um até loop como abaixo.  Este é um exemplo de um ponto final HTTP a chamar até o corpo da resposta tem o valor 'Concluído'.  Irá concluir quando quer 
  
  * Resposta de HTTP tem o estado de 'Concluída'
  * Tem tentou para 1 hora
  * -Tem anelada 100 vezes
  
  ```
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed'),
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn e Debatching

Por vezes, um accionador poderá receber uma matriz de itens que pretende debatch e iniciar um fluxo de trabalho por produto.  Isto pode ser feito através de `spliton` comando.  Por predefinição, se o seu swagger accionador Especifica uma carga útil que é uma matriz, um `spliton` será adicionada e iniciar uma executar por produto.  SplitOn só pode ser adicionado a um accionador.  Isto pode ser manualmente configurado ou substituído na vista de código definição.  Atualmente SplitOn pode debatch matrizes até 5000 itens.  Não pode ter uma `spliton` e também implementar o padrão de resposta síncrono.  Qualquer fluxo de trabalho chamado que tem um `response` ação para além `spliton` vai executar asyncronously e enviar uma imediata `202 Accepted` resposta.  

SplitOn pode ser especificado na vista de código como o exemplo seguinte.  Este receberá uma matriz de itens e debatches em cada linha.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequency": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Âmbitos de

É possível agrupar uma série de acções em conjunto com um âmbito.  Isto é particularmente útil para a aplicação de exceções.  No estruturador de pode adicionar um novo âmbito e começar a adicionar as ações dentro-lo.  Pode definir âmbitos na vista de código como o seguinte:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
