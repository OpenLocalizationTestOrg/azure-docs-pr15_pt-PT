<properties 
    pageTitle="Como utilizar filas de serviço Bus com PHP | Microsoft Azure" 
    description="Saiba como utilizar filas de serviço Bus no Azure. Exemplos de código escritos PHP." 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Como utilizar filas Bus de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia mostra-lhe como utilizar filas Bus de serviço. Os exemplos são gravados no PHP e utilizam o [SDK do Azure para PHP](../php-download-sdk.md). Os cenários abrangidos incluem **criar filas**, **Enviar e receber mensagens**e **Eliminar filas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação de PHP

O requisito só para criar uma aplicação de PHP acede ao serviço de Blobs do Azure é referenciar de classes no [Azure SDK para PHP](../php-download-sdk.md) a partir do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a sua aplicação ou bloco de notas.

> [AZURE.NOTE] A instalação do PHP também tem de ter a [extensão de OpenSSL](http://php.net/openssl) instalada e ativada.

Neste guia, irá utilizar funcionalidades de serviço que podem ser chamadas de dentro de uma aplicação PHP localmente ou no código em execução no interior de uma função Azure web, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obter o cliente do Azure bibliotecas

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Para utilizar na fila de serviço Bus APIs, faça o seguinte:

1. Referenciar o ficheiro de Carregador automático utilizando o [require_once] [ require_once] declaração.
2. Fazer referência a qualquer classes que poderá utilizar.

O exemplo seguinte mostra como incluir o arquivo automático e a classe de **ServicesBuilder** de referência.

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) assume que instalou as bibliotecas do cliente PHP para Azure através do compositor. Se tiver instalado as bibliotecas manualmente ou como um pacote de PEREIRAS, tem de referenciar o ficheiro de Carregador automático **WindowsAzure.php** .

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos abaixo, o `require_once` declaração sempre será apresentada, mas só as classes necessárias para o exemplo para executar referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de serviço Bus

Para criar uma instância de um cliente de serviço Bus, tem de ter uma cadeia de ligação válida neste formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Onde **ponto final** é normalmente do formato `[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente do serviço Azure tem de utilizar a classe de **ServicesBuilder** . Pode:

* Passe a cadeia de ligação diretamente à mesma.
* Utilize o **CloudConfigurationManager (CCM)** para verificar a várias origens externas para a cadeia de ligação:
    * Por predefinição vem com o suporte para uma origem externa - variáveis de ambiente
    * Pode adicionar novas fontes através do prolongamento a classe de **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de ligação será transmitida diretamente.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Como: criar uma fila

Pode executar operações de gestão para filas Bus de serviço através da classe de **ServiceBusRestProxy** . Um objeto de **ServiceBusRestProxy** fórmula foi criado através do método de fábrica do mesmo **ServicesBuilder::createServiceBusService** com uma cadeia de ligação apropriada que contém as tokens permissões para geri-lo.

O exemplo seguinte mostra como criar uma instância de um **ServiceBusRestProxy** e ligar **ServiceBusRestProxy -> createQueue** para criar uma fila denominada `myqueue` dentro de uma `MySBNamespace` espaço de nomes de serviço:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] Pode utilizar o `listQueues` método no `ServiceBusRestProxy` objetos para verificar se já existe uma fila com um nome especificado dentro de um espaço de nomes.

## <a name="how-to-send-messages-to-a-queue"></a>Como: enviar mensagens para uma fila

Para enviar uma mensagem para uma fila Bus de serviço, a sua aplicação chama o método **ServiceBusRestProxy -> sendQueueMessage** . O código seguinte mostra como enviar uma mensagem para a `myqueue` fila anteriormente criada dentro de `MySBNamespace` espaço de nomes de serviço.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Mensagens enviadas para (e recebidas a partir do) filas de serviço Bus são instâncias da classe **BrokeredMessage** . Objetos de **BrokeredMessage** têm um conjunto de métodos padrão (como **getLabel**, **getTimeToLive**, **setLabel**e **setTimeToLive**) e as propriedades que são utilizadas para propriedades personalizadas de específicos da aplicação, espera um corpo dos dados da aplicação arbitrário.

Serviço Bus filas suportam a um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos numa fila mas há um remate no tamanho total das mensagens contidos por uma fila. Este limite superior de tamanho da fila é de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila

A melhor forma de receber mensagens de uma fila é utilizar um método **ServiceBusRestProxy -> receiveQueueMessage** . Mensagens podem ser recebidas em dois modos diferentes: **ReceiveAndDelete** (a predefinição) e **PeekLock**.

Quando utilizar o modo **ReceiveAndDelete** , receber é uma operação de captura único - isto é, quando o serviço Bus recebe um pedido de leitura para uma mensagem numa fila,-marca a mensagem como sendo consumidas e devolve-o para a aplicação. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

No modo de **PeekLock** , recebe uma mensagem torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que os outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção, passando mensagem recebida para **ServiceBusRestProxy -> deleteMessage**. Quando o serviço Bus vê **deleteMessage** chamada, irá marcar a mensagem como sendo consumidas e removê-la da fila de espera.

O exemplo seguinte mostra como uma mensagem pode ser recebida e processadas a utilizar o modo de **PeekLock** (não o modo de predefinido).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: processar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método de **unlockMessage** mensagem recebida (em vez do método **deleteMessage** ). Isto vai originar Bus de serviço para desbloquear a mensagem na fila de espera e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro de fila de espera e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus serão desbloquear automaticamente a mensagem e torná-lo disponível para ser recebidos novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do pedido de **deleteMessage** é emitido, em seguida, a mensagem vai ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**; Isto é, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, adicionar lógica adicional às aplicações para processar entrega de mensagens duplicados é recomendado. Muitas vezes fazê-utilizando o método de **getMessageId** da mensagem permanece constante ao longo de tentativas de entrega.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos filas Bus de serviço, consulte o artigo [filas, tópicos e subscrições][] para obter mais informações.

Para mais informações, consulte também o [Centro de programadores do PHP](/develop/php/).

[Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
