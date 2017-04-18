<properties 
    pageTitle="Como utilizar tópicos serviço Bus com PHP | Microsoft Azure" 
    description="Saiba como utilizar tópicos serviço Bus com PHP no Azure." 
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
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como utilizar a subscrições e tópicos de Bus de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo mostra-lhe como utilizar a subscrições e tópicos de Bus de serviço. Os exemplos são gravados no PHP e utilizam o [SDK do Azure para PHP](../php-download-sdk.md). Os cenários abrangidos incluem **criar tópicos e subscrições**, **criar filtros de subscrição**, **enviar mensagens a um tópico**, **receber mensagens de uma subscrição**e **Eliminar tópicos e subscrições**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação de PHP

É o único requisito para criar uma aplicação de PHP que acede ao serviço de Blobs do Azure fazer referência a classes no [Azure SDK para PHP](../php-download-sdk.md) a partir do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a sua aplicação ou bloco de notas.

> [AZURE.NOTE] A instalação do PHP também tem de ter a [extensão de OpenSSL](http://php.net/openssl) instalada e ativada.

Este artigo descreve como utilizar funcionalidades de serviço que podem ser chamadas dentro de uma aplicação PHP localmente ou no código em execução no interior de uma função Azure web, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obter o cliente do Azure bibliotecas

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Para utilizar a API Bus do serviço:

1. Referenciar o ficheiro de Carregador automático utilizando o [require_once] [ require-once] declaração.
2. Fazer referência a qualquer classes que poderá utilizar.

O exemplo seguinte mostra como incluir o arquivo automático e a classe de **ServiceBusService** de referência.

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) assume que instalou as bibliotecas do cliente PHP para Azure através do compositor. Se tiver instalado as bibliotecas manualmente ou como um pacote de PEREIRAS, tem de referenciar o ficheiro de Carregador automático **WindowsAzure.php** .

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nos exemplos seguintes, o `require_once` declaração sempre será apresentada, mas só as classes necessárias para o exemplo para executar referenciadas.

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de serviço Bus

Para criar uma instância de um cliente de serviço Bus tem de ter uma cadeia de ligação válida neste formato:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Onde `Endpoint` é normalmente do formato `https://[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente do serviço Azure tem de utilizar a classe de **ServicesBuilder** . Pode:

* Passe a cadeia de ligação diretamente à mesma.
* Utilize o **CloudConfigurationManager (CCM)** para verificar a várias origens externas para a cadeia de ligação:
    * Por predefinição inclui com o suporte para uma origem externa - ambientais variáveis.
    * Pode adicionar novas fontes através do prolongamento a classe de **ConnectionStringSource** .

Para os exemplos descritos aqui, a cadeia de ligação é transmitida diretamente.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Criar um tópico

Pode executar operações de gestão para tópicos Bus de serviço através da classe de **ServiceBusRestProxy** . Um objeto de **ServiceBusRestProxy** fórmula foi criado através do método de fábrica do mesmo **ServicesBuilder::createServiceBusService** com uma cadeia de ligação apropriada que contém as tokens permissões para geri-lo.

O exemplo seguinte mostra como criar uma instância de um **ServiceBusRestProxy** e ligar **ServiceBusRestProxy -> createTopic** para criar um tópico denominado `mytopic` dentro de uma `MySBNamespace` espaço de nomes:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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

> [AZURE.NOTE] Pode utilizar o `listTopics` método no `ServiceBusRestProxy` objetos para verificar se já existe um tópico com um nome especificado dentro de um espaço de nomes de serviço.

## <a name="create-a-subscription"></a>Criar uma subscrição

Subscrições de tópico também são criadas com o método **ServiceBusRestProxy -> createSubscription** . Subscrições são com nome e podem ter um filtro opcional que restringe o conjunto de mensagens passado para fila de espera virtual da subscrição.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro predefinido (MatchAll)

O filtro de **MatchAll** é o filtro predefinido que é utilizado se não for especificado nenhum filtro quando é criada uma nova subscrição. Quando é utilizado o filtro de **MatchAll** , todas as mensagens publicadas para o tópico são colocadas na fila de espera virtual da subscrição. O exemplo seguinte cria uma subscrição denominada 'mysubscription' e utiliza o filtro de **MatchAll** predefinido.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com os filtros

Também pode configurar filtros que permitem-lhe especificar quais as mensagens enviadas para um tópico deverão aparecer dentro de uma subscrição de tópico específico. O tipo de filtro suportado em subscrições mais flexível é o **SqlFilter**, que implementa um subconjunto de SQL92. Trabalhar com os filtros SQL nas propriedades das mensagens que são publicadas para o tópico. Para mais informações sobre SqlFilters, consulte o artigo [SqlFilter.SqlExpression propriedade][sqlfilter].

> [AZURE.NOTE] Cada regra de uma subscrição processa as mensagens a receber de forma independente, adicionar as suas mensagens de resultado para a subscrição. Além disso, cada nova subscrição tem um objeto de **regra** predefinida com um filtro que adiciona todas as mensagens do tópico para a subscrição. Para receber apenas as mensagens que correspondam seu filtro, tem de remover a regra predefinida. Pode remover a regra predefinida utilizando o `ServiceBusRestProxy->deleteRule` método.

O exemplo seguinte cria uma subscrição denominada **HighMessages** com **SqlFilter** que apenas seleciona todas as mensagens com maior do que 3 (consulte o artigo [enviar mensagens para um tópico](#send-messages-to-a-topic) para obter informações sobre a adição de propriedades personalizadas a mensagens de) uma propriedade de **MessageNumber** personalizada:

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Nota a que este código necessita da utilização de um espaço de nomes adicional: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Do mesmo modo, o exemplo seguinte cria uma subscrição denominada **LowMessages** com **SqlFilter** que apenas seleciona todas as mensagens com uma **MessageNumber** propriedade menor ou igual a 3:

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Agora, quando uma mensagem é enviada para o `mytopic` tópico, que é sempre entregue para destinatários subscritos o `mysubscription` subscrição e seletivamente entregue para destinatários subscritos o `HighMessages` e `LowMessages` subscrições (consoante o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem a um tópico de serviço Bus, a sua aplicação chama o método **ServiceBusRestProxy -> sendTopicMessage** . O código seguinte mostra como enviar uma mensagem para a `mytopic` criado anteriormente dentro do tópico de `MySBNamespace` espaço de nomes de serviço.

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
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

As mensagens enviadas para tópicos do serviço Bus são instâncias da classe **BrokeredMessage** . Objetos de **BrokeredMessage** tem um conjunto de propriedades padrão e métodos (tal como **getLabel**, **getTimeToLive**, **setLabel**e **setTimeToLive**), assim como as propriedades que podem ser utilizadas para colocar em espera propriedades personalizadas de específicos da aplicação. O exemplo seguinte mostra como enviar mensagens de teste 5 a `mytopic` tópico criado anteriormente. O método **DefinirPropriedade** é utilizado para adicionar uma propriedade personalizada (`MessageNumber`) a cada mensagem. Tenha em atenção que a `MessageNumber` o valor da propriedade varia em cada mensagem (que pode utilizar este valor para determinar quais as subscrições recebem-lo, conforme mostrado na secção [criar uma subscrição](#create-a-subscription) ):

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Tópicos de serviço Bus suportam um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos num tópico mas há um remate no tamanho total das mensagens contidos por um tópico. Este limite superior no tamanho do tópico é 5 GB. Para mais informações acerca de quotas, consulte o artigo [quotas Bus de serviço][].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

A melhor forma de receber mensagens de uma subscrição é utilizar um método **ServiceBusRestProxy -> receiveSubscriptionMessage** . Mensagens recebidas podem trabalhar em dois modos diferentes: **ReceiveAndDelete** (a predefinição) e **PeekLock**.

Quando utilizar o modo **ReceiveAndDelete** , receber é uma operação de captura único; Isto é, quando serviço Bus recebe um pedido de leitura para uma mensagem de uma subscrição, este marca a mensagem como sendo consumidas e devolve-o para a aplicação. Modo de **ReceiveAndDelete** é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

No modo de **PeekLock** , recebe uma mensagem torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção, passando mensagem recebida para **ServiceBusRestProxy -> deleteMessage**. Quando o serviço Bus vê **deleteMessage** chamada, irá marcar a mensagem como sendo consumidas e removê-la da fila de espera.

O exemplo seguinte mostra como receber e processar uma mensagem com o modo de **PeekLock** (não o modo de predefinição). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como: processar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método de **unlockMessage** mensagem recebida (em vez do método **deleteMessage** ). Isto vai originar Bus de serviço para desbloquear a mensagem na fila de espera e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro de fila de espera e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus serão desbloquear automaticamente a mensagem e torná-lo disponível para ser recebidos novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do pedido de **deleteMessage** é emitido, em seguida, a mensagem vai ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**; Isto é, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional a aplicações para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando o método de **getMessageId** da mensagem permanece constante ao longo de tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Para eliminar um tópico ou uma subscrição, utilize o **ServiceBusRestProxy -> deleteTopic** ou os métodos de **ServiceBusRestProxy -> deleteSubscripton** , respetivamente. Tenha em atenção que a eliminação de um tópico também elimina quaisquer subscrições que estão registadas com o tópico.

O exemplo seguinte mostra como eliminar um tópico denominado `mytopic` as suas subscrições registadas e.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Utilizando o método de **deleteSubscription** , pode eliminar uma subscrição de forma independente:

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos filas Bus de serviço, consulte o artigo [filas, tópicos e subscrições][] para obter mais informações.

[Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Quotas de Bus de serviço]: service-bus-quotas.md
