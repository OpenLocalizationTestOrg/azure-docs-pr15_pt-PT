<properties
   pageTitle="Exceções da aplicações lógica | Microsoft Azure"
   description="Saiba padrões de erro e exceção processamento com aplicações do Azure lógica"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-error-and-exception-handling"></a>Erro de aplicações de lógica e processamento de exceção

Lógica aplicações fornece um conjunto avançado de ferramentas e padrões para ajudar a garantir a sua integrações são robusta e flexível contra falhas.  Uma dos desafios com qualquer arquitectura de integração de é garantir que tempo de inatividade ou problemas dos sistemas dependentes são processados corretamente.  Lógica aplicações torna o processamento de erros de uma experiência primeira classe, que lhe dá as ferramentas que precisa para agir em exceções e erros numa seus fluxos de trabalho.

## <a name="retry-policies"></a>Repetir políticas

O tipo de exceção e processamento de erros mais básico é uma política de repetir.  Esta política define se a ação deve ser repetida se pedido inicial excedeu ou Ocorreu uma falha (qualquer pedido que resultou num 429 ou resposta 5xx).  Por predefinição, todas as ações repetir 4 vezes adicionais sobre intervalos de 20 segundo.  Por isso, se o pedido primeiro recebido um `500 Internal Server Error` resposta, o motor de fluxo de trabalho interrompe para 20 segundos e tentativa do pedido novamente.  Se após várias tentativas de todos os a resposta ainda uma exceção ou falha, o fluxo de trabalho irá continuar e marcar o estado de ação como `Failed`.

Pode configurar as políticas de repetir nas **entradas do tipo** de uma determinada ação.  Uma política de repetir pode ser configurada para experimentar até 4 vezes mais de 1 hora intervalos.  Detalhes completos sobre as propriedades de entrada podem ser [encontrado no MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Se pretendia que a ação de HTTP para repetir o 4 horas e aguarde 10 minutos entre cada tentativa teria a seguinte definição:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Para obter mais detalhes sobre sintaxe suportado, visualize a [secção de política de repetir no MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Propriedade RunAfter para chamar falhas

Cada ação da aplicação de lógica declara as ações de precisa de concluir antes da ação que será iniciado.  Poderá pensar tal como a ordenação dos passos no seu fluxo de trabalho.  Esta ordenação é conhecido como o `runAfter` propriedade na definição de ação.  É um objeto que descreve quais ações e os Estados de ação seriam executar a ação.  Por predefinição, todas as ações adicionadas através da opção o designer estão definidas para `runAfter` o passo anterior, se o passo anterior foi `Succeeded`.  No entanto, pode personalizar este valor para despoletado ações quando são ações anterior `Failed`, `Skipped`, ou um conjunto destes valores possível.  Se quiser adicionar um item a um tópico Service Bus designado após uma acção específica `Insert_Row` falhar, que vai utilizar os seguintes `runAfter` configuração:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Aviso de `runAfter` propriedade estiver definida para fogo se a `Insert_Row` é uma ação `Failed`.  Para executar a ação, se o estado de ação estiver `Succeeded`, `Failed`, ou `Skipped` a sintaxe da seria:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Ações que execute depois de ter falhado uma acção anterior e concluída com êxito, serão marcadas como `Succeeded`.  Este comportamento significa que se que com êxito capturas todas as falhas num fluxo de trabalho a executar propriamente dito é assinalada como `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Âmbitos e resultados para avaliar ações

Semelhante ao modo como pode executar após ações individuais, pode também ações de grupo em conjunto no interior de um [âmbito](app-service-logic-loops-and-scopes.md) - que funcionam como um agrupamento lógico de ações.  Âmbitos são úteis para organizar as suas ações de aplicação lógica tanto para realizar agregação avaliações no estado de um âmbito.  O âmbito propriamente dito irão receber um Estado após tem concluído todas as ações dentro de um âmbito.  O estado do âmbito é determinado com os mesmos critérios como uma executar – se for a ação final um ramo de execução `Failed` ou `Aborted` o estado é `Failed`.

Pode `runAfter` um âmbito foi marcado `Failed` seja acionada ações específicas para qualquer falhas que tenham ocorrido no âmbito.  Em execução depois de um âmbito falha permite-lhe criar uma ação única para chamar falhas se *as* ações no âmbito falharem.

### <a name="getting-the-context-of-failures-with-results"></a>Obter o contexto de insucessos com resultados

Captura de falhas a partir de um âmbito é muito útil, mas também poderá querer o contexto para compreender exatamente as ações de falhou e quaisquer erros ou códigos de estado que foram devolvidos.  O `@result()` função de fluxo de trabalho fornece contexto para o resultado de todas as ações dentro de um âmbito.

`@result()`leva-o até um único parâmetro, o nome do âmbito e devolve uma matriz de todos os ação resultados a partir desse âmbito.  Estes objectos ação incluem os mesmos atributos que o `@actions()` exporta objecto, incluindo a hora de início de ação, hora de fim de ação, estado de ação, entradas de ação, correlação ação IDs e ação.  Pode facilmente emparelhar uma `@result()` funcione com um `runAfter` para enviar o contexto das ações que falhou dentro de um âmbito.

Se pretender executar uma ação *para cada* ação num âmbito que `Failed`, pode emparelhar o `@result()` com uma ação de **[Matriz de filtro](../connectors/connectors-native-query.md)** e um ciclo **[ForEach](app-service-logic-loops-and-scopes.md)** .  Esta opção permite-lhe filtrar a matriz de resultados para as ações que falhou.  Pode tomar a matriz de resultado filtrado e executar uma ação para cada falha utilizando o ciclo de **ForEach** .  Eis um exemplo abaixo, seguido de uma explicação detalhada.  Este exemplo irá enviar um pedido de HTTP POST com o corpo da resposta de quaisquer ações que ocorreu uma falha no âmbito `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Eis obter instruções detalhadas sobre o que se passa:

1. Ação de **Matriz de filtro** para filtrar o `@result('My_Scope')` para obter o resultado de todas as ações dentro`My_Scope`
1. Condição da **Matriz de filtro** é qualquer `@result()` item com o Estado igual a `Failed`.  Isto irá filtrar a matriz de todos os resultados de ação a partir do `My_Scope` para apenas uma matriz de resultados de falha de ação.
1. Efectue uma acção **para cada** saídas de **Matriz filtrados** .  Isto irá efetuar um ação *para cada uma* falha na ação resultado podemos filtrados acima.
    - Se tiver havido uma ação única no âmbito que falhou, as ações na `foreach` seria executado apenas uma vez.  Várias acções falhadas fará uma ação por falha.
1. Enviar uma mensagem de HTTP na `foreach` item corpo de resposta, ou `@item()['outputs']['body']`.  O `@result()` forma item é igual a `@actions()` da forma e, pode ser analisado da mesma forma.
1. Também incluído dois cabeçalhos personalizados com o nome de ação falha `@item()['name']` e executa a falha cliente ID rastreio `@item()['clientTrackingId']`.

Para sua referência, eis um exemplo de um único `@result()` item.  Pode ver o `name`, `body`, e `clientTrackingId` propriedades analisado no exemplo acima.  Deve notar que fora de um `foreach`, `@result()` devolve uma matriz destes objetos.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Pode utilizar as expressões acima para efetuar exceção diferentes padrões de processamento.  Pode optar por executar uma única exceção processamento ação fora do âmbito que aceita a matriz inteira filtrada de falhas e remover a `foreach`.  Também pode incluir as outras propriedades útil a partir do `@result()` resposta mostrada acima.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos do Azure e telemetria

Os padrões de acima são perfeitos processar erros e exceções dentro de uma executar, mas também é possível identificar e responder a erros independentes da própria executar.  [Diagnósticos do Azure](app-service-logic-monitor-your-logic-apps.md) fornece uma forma simple para enviar todos os eventos de fluxo de trabalho (incluindo todos os Estados use e ação) para uma conta de armazenamento do Windows Azure ou um concentrador de evento Azure.  Pode monitorizar os registos e métricas ou publicá-las para qualquer ferramenta monitorização que preferir, para avaliar Estados executar.  Uma das opções potencial é transmitir em fluxo todos os eventos através do Azure concentrador de evento para [A análise da cadeia](https://azure.microsoft.com/services/stream-analytics/).  Em sequência Analytics pode escrever consultas direto fora de qualquer anomalias, médias ou falhas de registos de diagnóstico.  Análise da cadeia pode facilmente de saída para outras origens de dados como filas, tópicos, SQL, DocumentDB e do Power BI.

## <a name="next-steps"></a>Próximos passos
- [Consulte o artigo como um cliente criadas com aplicações de lógica de processamento de erros robusto](app-service-logic-scenario-error-and-exception-handling.md)
- [Localizar mais aplicações lógica exemplos e cenários](app-service-logic-examples-and-scenarios.md)
- [Saiba como criar implementações automatizadas de aplicações de lógica](app-service-logic-create-deploy-template.md)
- [Estruturar e implementar lógica aplicações a partir do Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9