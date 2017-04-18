<properties
    pageTitle="Azure acionador de temporizador funções | Microsoft Azure"
    description="Compreenda como utilizar accionadores temporizador no Azure funções."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="funções de Azure, funções, evento processamento, cluster dinâmico, sem servidor arquitetura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-timer-trigger"></a>Azure acionador de temporizador de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar accionadores temporizador no Azure funções. Temporizador de accionadores chamada funções com base numa agenda, uma vez ou periódico.  

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a name="functionjson-for-timer-trigger"></a>Function.JSON para accionador temporizador

O ficheiro *function.json* fornece uma expressão de agenda. Por exemplo, a seguinte agenda é executado a função minuto:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O accionador temporizador processa com várias instâncias escala-out automaticamente: apenas uma única ocorrência de uma função de temporizador determinado irá estar em execução em todas as instâncias.

## <a name="format-of-schedule-expression"></a>Formato de expressão de agenda

A expressão agenda é uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`. 

Tenha em atenção muitas das expressões cron que encontrar online omitem o campo {segundo}, para que se copiar a partir de um desses terá de ajustar para o campo extra. 

Eis algumas outras agenda exemplos de expressões:

Para acionam em 5 minutos:

```json
"schedule": "0 */5 * * * *"
```

Para acionar uma vez na parte superior de cada hora:

```json
"schedule": "0 0 * * * *",
```

Para acionar uma vez a cada duas horas:

```json
"schedule": "0 0 */2 * * *",
```

Para acionam cada hora a partir de 9 AM às 17: 00:

```json
"schedule": "0 0 9-17 * * *",
```

Acionado na 9:30 AM diariamente:

```json
"schedule": "0 30 9 * * *",
```

Acionado na 9:30 AM todos os dias úteis:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="timer-trigger-c-code-example"></a>Exemplo de código do temporizador accionador c#

Este exemplo de código c# escreve um único registo sempre que a função é acionada.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
