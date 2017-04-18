<properties
   pageTitle="Cenário de aplicação de lógica: criar um acionador de Azure funções serviço Bus | Microsoft Azure"
   description="Utilizar funções de Azure para criar um accionador Bus de serviço para uma aplicação de lógica"
   services="logic-apps,functions"
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
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Cenário de aplicação de lógica: criar um acionador de Azure Service Bus utilizando funções do Azure

Pode utilizar funções Azure para criar um accionador para uma aplicação de lógica quando precisar de implementar um execução longa escuta ou tarefa. Por exemplo, pode criar uma função que irá Oiça de uma fila de espera e, em seguida, fire imediatamente de uma aplicação de lógica como um accionador push.

## <a name="build-the-logic-app"></a>Criar a aplicação de lógica

Neste exemplo, tem de uma função executar para cada aplicação lógica que precisa de ser acionou. Primeiro, crie uma aplicação de lógica que tem um acionador de pedido HTTP. A função chamadas esse ponto final sempre que é recebida uma mensagem de fila de espera.  

1. Criar uma nova aplicação de lógica; Selecione o accionador **Manual - quando um pedido HTTP for recebida** .  
   Opcionalmente, pode especificar um esquema JSON para utilizar com a mensagem de fila de espera, utilizando uma ferramenta como [jsonschema.net](http://jsonschema.net). Cole o esquema ao accionador. Isto ajuda-o estruturador de compreender a forma dos dados e mais facilmente um fluxo de propriedades através do fluxo de trabalho.
1. Adicione quaisquer passos adicionais que pretende ocorrer depois é recebida uma mensagem de fila de espera. Por exemplo, envie uma mensagem de e-mail através do Office 365.  
1. Guarde a aplicação de lógica para gerar o URL de chamada de retorno para o accionador para esta aplicação lógica. O URL é apresentada no cartão de accionador.

![A chamada de retorno URL é apresentada no cartão de accionador][1]

## <a name="build-the-function"></a>Criar a função

Em seguida, tem de criar uma função que irá funcionar como o accionador e ouvir fila de espera.

1. No [portal do Azure funções](https://functions.azure.com/signin), selecione a **Nova função**e, em seguida, selecione o modelo de **ServiceBusQueueTrigger - c#** .

    ![Azure portal de funções][2]

2. Configurar a ligação para a fila de serviço Bus (que irá utilizar o SDK do Azure Service Bus `OnMessageReceive()` escuta).
3. Escreva uma função para ligar o ponto final de aplicação lógica (criado anteriormente) utilizando a mensagem de fila como um accionador para simple. Eis um exemplo de uma função completo. O exemplo que utiliza uma `application/json` tipo de conteúdo da mensagem, mas pode alterar caso seja necessário.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Para testar, adicione uma mensagem de fila através de uma ferramenta como [Serviço Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Ver a aplicação de lógica fogo imediatamente após a função recebe a mensagem.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG
