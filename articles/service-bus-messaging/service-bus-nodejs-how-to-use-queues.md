<properties 
    pageTitle="Como utilizar filas Bus de serviço no Node.js | Microsoft Azure" 
    description="Saiba como utilizar filas Bus de serviço no Azure através de uma aplicação de Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Como utilizar filas Bus de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar filas Bus de serviço no Node.js. Os exemplos são gravados no JavaScript e utilizam o módulo Node.js Azure. Os cenários abrangidos incluem **criar filas**, **Enviar e receber mensagens**e **Eliminar filas**. Para mais informações sobre filas, consulte a secção [os passos seguintes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação de Node.js

Crie uma aplicação de Node.js em branco. Para obter instruções sobre como criar uma aplicação de Node.js, consulte o artigo [criar e implementar uma aplicação de Node.js para um Web site Azure][], ou [Serviço em nuvem Node.js][] através do Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Para utilizar Azure Service Bus, transferir e utilizar o pacote de Node.js Azure. Este pacote inclui um conjunto de bibliotecas comunicar com os serviços de serviço Bus restantes.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilizar o Gestor de pacote nó (NPM) para obter o pacote

1. Utilizar a janela de comando do **Windows PowerShell para Node.js** para navegar para o **c:\\nó\\sbqueues\\WebRole1** pasta na qual que criou a sua aplicação de exemplo.

2. Escreva **npm instalar azure** na janela de comando, deverá resultar numa saída semelhante ao seguinte:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3. Pode executar o comando **ls** para verificar que manualmente uma **nó\_módulos** pasta foi criada. Nessa pasta localize o pacote do **azure** , que contém as bibliotecas que precisa de aceder a filas Bus de serviço.

### <a name="import-the-module"></a>Importar o módulo

Utilizar o bloco de notas ou outro editor de texto, adicione o seguinte para a parte superior do ficheiro **server.js** da aplicação:

```
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma ligação do Azure Service Bus

O módulo Azure lê as variáveis de ambiente AZURE\_SERVICEBUS\_espaço de nomes e AZURE\_SERVICEBUS\_acesso\_chave para obter as informações necessárias para ligar ao serviço Bus. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações de conta quando chamar **createServiceBusService**.

Para obter um exemplo de definir as variáveis de ambiente num ficheiro de configuração de um serviço de nuvem Azure, consulte o artigo [Node.js serviço em nuvem com armazenamento][].

Para obter um exemplo de definir as variáveis de ambiente no [portal clássica Azure][] para um Web site Azure, consulte o artigo [Aplicação da Web Node.js com armazenamento][].

## <a name="create-a-queue"></a>Criar uma fila

O objeto **ServiceBusService** permite-lhe trabalhar com filas Bus de serviço. O código seguinte cria um objeto de **ServiceBusService** . Adicione-o na parte superior do ficheiro **server.js** , depois da instrução para importar o módulo Azure:

```
var serviceBusService = azure.createServiceBusService();
```

Ao contactar o suporte **createQueueIfNotExists** no objeto **ServiceBusService** , da fila especificada é devolvida (se existir) ou, é criada uma nova fila com o nome especificado. O código seguinte utiliza **createQueueIfNotExists** para criar ou ligar para a fila denominada `myqueue`:

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** também suporta opções adicionais, que permitem-lhe substituir as predefinições de fila como o tempo de mensagem para o tamanho da fila live ou máximo. O exemplo seguinte define o tamanho máximo da fila para 5 GB e uma hora para live o valor (TTL) de 1 minuto:

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filtros

Operações de filtragem opcionais podem ser aplicadas a operações realizadas utilizando **ServiceBusService**. Operações de filtragem pode incluir registo, automaticamente a repetir, etc. Filtros são objetos que implementam um método com a assinatura:

```
function handle (requestOptions, next)
```

Após fazer a sua pré-processamento no menu Opções de pedido, o método tem de chamar `next`, prisma uma chamada de retorno com a assinatura seguinte:

```
function (returnObject, finalCallback, next)
```

Esta chamada de retorno e depois processamento **returnObject** (a resposta do pedido de servidor), a chamada de retorno quer terá de invocar `next` se existir para continuar a outros filtros de processamento ou simplesmente invocar `finalCallback`, que termina a invocação de serviço.

Dois filtros implementam lógica de repetição são incluídos com o SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguinte cria um objeto de **ServiceBusService** que utiliza o **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila Bus de serviço, a sua aplicação chama o método de **sendQueueMessage** no objeto **ServiceBusService** . Mensagens enviadas para (e recebidas a partir do) filas de serviço Bus **BrokeredMessage** objetos são e tem um conjunto de propriedades padrão (como **etiqueta** e **TimeToLive**), um dicionário que é utilizado para colocar em espera propriedades personalizadas de específicos da aplicação bem como um corpo dos dados da aplicação arbitrário. Uma aplicação pode definir o corpo da mensagem, passando uma cadeia que a mensagem. As propriedades necessárias padrão são preenchidas com valores predefinidos.

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila denominada `myqueue` utilizando **sendQueueMessage**:

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Serviço Bus filas suportam a um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos numa fila mas há um remate no tamanho total das mensagens contidos por uma fila. Este tamanho da fila é definido a hora de criação, com um limite máximo de 5 GB. Para mais informações acerca de quotas, consulte o artigo [quotas Bus de serviço][].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

As mensagens são recebidas de uma fila utilizando o método de **receiveQueueMessage** no objeto **ServiceBusService** . Por predefinição, as mensagens são eliminadas da fila, tal como estes são lidos; No entanto, pode ler (pré-visualização) e bloquear a mensagem sem eliminá-la a partir da fila de definindo o parâmetro opcional **isPeekLock** **Verdadeiro**.

O comportamento predefinido da leitura e eliminar a mensagem como parte da operação de receção é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

Se o parâmetro **isPeekLock** estiver definido como **Verdadeiro**, a receber torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao chamar o método de **deleteMessage** e fornecer a mensagem a ser eliminada como um parâmetro. O método de **deleteMessage** irá marcar a mensagem como sendo consumidas e removê-la da fila de espera.

O exemplo seguinte demonstra como receber e processar mensagens utilizando **receiveQueueMessage**. O exemplo pela primeira vez recebe e elimina uma mensagem e, em seguida, recebe uma mensagem com o **isPeekLock** definida como **Verdadeiro**, em seguida, elimina a mensagem utilizando **deleteMessage**:

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método de **unlockMessage** o objeto **ServiceBusService** . Isto vai originar Bus de serviço para desbloquear a mensagem na fila de espera e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro de fila de espera e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus serão desbloquear automaticamente a mensagem e torná-lo disponível para ser recebidos novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do método **deleteMessage** é chamado, em seguida, a mensagem irá ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando a propriedade **MessageId** da mensagem, será que permaneçam constante ao longo de tentativas de entrega.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre filas, consulte os seguintes recursos.

-   [Filas, tópicos e subscrições][]
-   Repositório de [Azure SDK para nó][] no GitHub
-   [Centro de programadores do node.js](/develop/nodejs/)

  [Azure SDK para nó]: https://github.com/Azure/azure-sdk-for-node
  [Azure portal clássico]: http://manage.windowsazure.com
  
  [Serviço em nuvem node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
  [Criar e implementar uma aplicação de Node.js para um site do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Serviço em nuvem node.js com armazenamento]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Aplicação Web do node.js com armazenamento]: ../storage/storage-nodejs-how-to-use-table-storage.md
  [Quotas de Bus de serviço]: service-bus-quotas.md
 
