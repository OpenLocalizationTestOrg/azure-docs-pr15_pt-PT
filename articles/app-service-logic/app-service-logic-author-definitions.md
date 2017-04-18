<properties 
    pageTitle="Definições de aplicação de lógica do autor | Microsoft Azure" 
    description="Saiba como escrever a definição de JSON para as aplicações de lógica" 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="author-logic-app-definitions"></a>Definições de aplicação de lógica de autor
Este tópico demonstra como utilizar as definições de [Azure lógica de aplicações](app-service-logic-what-are-logic-apps.md) , que é um idioma JSON simple, declarativa. Se ainda não o feito ainda, consulte o artigo [como criar uma nova aplicação de lógica](app-service-logic-create-a-logic-app.md) pela primeira vez. Também pode ler o [material de referência completo do idioma definição no MSDN](http://aka.ms/logicappsdocs).

## <a name="several-steps-that-repeat-over-a-list"></a>Vários passos que se repetem através de uma lista

Pode tirar partido o [tipo de foreach](app-service-logic-loops-and-scopes.md) para repetir através de uma matriz de até 10 k itens e executar uma ação para cada um.

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>Um passo de processamento de falha se algo correr mal

Pretende conseguir escrever um *passo de remediação* frequentemente — algumas lógica que executa, se **e apenas se**, uma ou mais das suas chamadas falharam. Neste exemplo, vamos são obter dados a partir de várias localizações, mas se a chamada falhar, quiser publicar uma mensagem algures, de modo que pode encontrar esse falha mais tarde:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "postToErrorMessageQueue": {
            "type": "ApiConnection",
            "inputs": "...",
            "runAfter": {
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

Pode tornar utilização da `runAfter` propriedade para especificar o `postToErrorMessageQueue` só deve executar `readData` é **falhou**.  Isto também pode ser uma lista de valores possíveis, por isso, `runAfter` dever `["Succeeded", "Failed"]`.

Por fim, dado que tem agora processados o erro, podemos já não marcar a execução como **falhou**. Como pode ver aqui, o execução deste é **bem sucedida** , de apesar de um passo falhou, porque que escreveu no passo para processar esta falha.

## <a name="two-or-more-steps-that-execute-in-parallel"></a>Duas (ou mais) passos que executem em paralelo

Para ter várias execução de ações em paralelo, o `runAfter` propriedade tem de ser equivalente o tempo de execução. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            }
        },
        "branch1": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

Como pode ver no exemplo acima, ambos `branch1` e `branch2` estão definidos para executar após `readData`. Como resultado, ambos estes ramificações serão executadas em paralelo:

![Paralelo](./media/app-service-logic-author-definitions/parallel.png)

Pode ver que o carimbo de hora para ambas as ramificações é idêntico. 

## <a name="join-two-parallel-branches"></a>Participar em duas ramificações paralelas

Pode participar duas ações que foram definidas para executar em paralelo ao adicionar itens para o `runAfter` propriedade semelhante ao acima.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "branch1": [
                    "Succeeded"
                ],
                "branch2": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![Paralelo](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>Mapeamento de itens numa lista para alguns configuração diferente

Seguinte, suponhamos pretende obter conteúdo completamente diferente consoante um valor de uma propriedade. Vamos pode criar um mapa de valores para os destinos como um parâmetro:  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/en-us/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

Neste caso, podemos pela primeira vez obter uma lista de artigos de e, em seguida, o segundo passo procura num mapa, com base na categoria que tenha sido definida como um parâmetro, o URL a obter o conteúdo a partir de. 

Dois itens para pagar a atenção aqui: o [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) função é utilizada para verificar se a categoria corresponde a uma das categorias conhecidas definidas. Em segundo lugar, assim que recebemos na categoria, podemos pode separar o item do mapa utilizando entre parênteses Retos: `parameters[...]`. 

## <a name="working-with-strings"></a>Trabalhar com cadeias

Existem várias funções que podem ser utilizados para manipular cadeia. Vamos um exemplo onde temos uma cadeia que pretendemos para passar para um sistema, mas não temos a certeza de que codificação de caracteres será efetuada corretamente. Uma das opções é para base64 codificar esta cadeia. No entanto, para evitar a perda de um URL vamos para substituir alguns carateres. 

Queremos também uma subcadeia da a ordem atribuir um nome a vez que não são utilizados os primeiros 5 carateres.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Trabalhar a partir de dentro para fora:

1. Obter o [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) do nome do autor da encomenda, isto novamente devolve o número total de carateres

2. Subtrair 5 (uma vez que irá queremos uma cadeia mais curta)

3. Tomar realmente a [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Vamos começar no índice `5` e aceda o resto da cadeia.

4. Converter este subcadeia para um [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) cadeia

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)todas as `+` carateres com`-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)todas as `/` carateres com`_`

## <a name="working-with-date-times"></a>Trabalhar com os tempos de data

Data vezes pode ser útil, especialmente quando está a tentar importar dados a partir de uma origem de dados que transmitem não suporta **Accionadores**.  Também pode utilizar datas horas descobrir quanto tempo vários passos estão a demorar. 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "actions" {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
                },
                "runAfter": {}
            }
            "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
        }
    },
    "outputs": {}
}
```

Neste exemplo, vamos extrair a `startTime` do passo anterior. Em seguida, podemos são introdução a hora atual e subtrair um segundo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (pode utilizar outras unidades de tempo tais como `minutes` ou `hours`). Por fim, vamos pode comparar estes dois valores. Se o primeiro é menor que o segundo, em seguida, isto significa mais do que um segundo decorrido desde a ordem foi primeira colocado. 

Tenha também em atenção que pode utilizamos formatters cadeia para formatar datas: na cadeia de consulta utilizo [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) para obter o RFC1123. Todas as data formatação [é documentado no MSDN](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). 

## <a name="using-deployment-time-parameters-for-different-environments"></a>Utilizar parâmetros de tempo de implementação para diferentes ambientes

É comum ter um ciclo de vida de implementação onde tem um ambiente de desenvolvimento, ambiente de teste e, em seguida, num ambiente de produção. Em todos os dos seguintes procedimentos poderá pretender que a mesma definição, mas utilizar bases de dados diferentes, por exemplo. Da mesma forma, poderá pretender utilizar a mesma definição nos muitos regiões diferentes para elevada disponibilidade, mas pretende que cada instância da aplicação de lógica falar com a base de dados nesse região. 

Tenha em atenção que este é diferente a partir tendo diferentes parâmetros no *runtime*, para que deve utilizar o `trigger()` funcionar como realçado acima. 

Pode começar com uma definição de simplista como este:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

Em seguida, o valor real na `PUT` pedir para a aplicação de lógica pode fornecer o parâmetro `uri`. Tenha em atenção como já não existe um valor predefinido, este parâmetro é exigido a carga de útil de aplicação de lógica:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Em cada ambiente, em seguida, pode fornecer um valor diferente para o `connection` parâmetro. 

Consulte a [documentação REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx) para todas as opções para criar e gerir aplicações de lógica. 
