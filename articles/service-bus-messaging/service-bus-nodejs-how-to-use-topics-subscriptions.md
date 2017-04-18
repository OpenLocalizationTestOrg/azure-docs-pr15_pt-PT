<properties 
    pageTitle="Como utilizar tópicos serviço Bus com Node.js | Microsoft Azure" 
    description="Saiba como utilizar subscrições e tópicos de serviço Bus no Azure através de uma aplicação de Node.js." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como tópicos Bus de serviço de utilização e subscrições

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este guia descreve como utilizar a subscrições a partir de aplicações Node.js e tópicos de serviço Bus. Os cenários abrangidos incluem **criar tópicos e subscrições**, **criar filtros de subscrição**, **enviar mensagens de** um tópico, **receber mensagens de uma subscrição**e **Eliminar tópicos e subscrições**. Para mais informações sobre tópicos e subscrições, consulte a secção [os passos seguintes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação de Node.js

Crie uma aplicação de Node.js em branco. Para obter instruções sobre como criar uma aplicação de Node.js, consulte o artigo [criar e implementar uma aplicação de Node.js para um Web Site do Azure], [Node.js serviço em nuvem][] de utilizar o Windows PowerShell, ou o Web Site com WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Para utilizar o serviço Bus, transfira o pacote de Node.js Azure. Este pacote inclui um conjunto de bibliotecas comunicar com os serviços de serviço Bus restantes.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilizar o Gestor de pacote nó (NPM) para obter o pacote

1.  Utilizar uma interface da linha de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix), navegue para a pasta onde a criou a aplicação de exemplo.

2.  Escreva **npm instalar azure** na janela do comando deve ocorrer o seguinte resultado:

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

3.  Pode executar o comando **ls** para verificar que manualmente uma **nó\_módulos** pasta foi criada. Nessa pasta localize o pacote do **azure** , que contém as bibliotecas que precisa de aceder a tópicos Bus de serviço.

### <a name="import-the-module"></a>Importar o módulo

Utilizar o bloco de notas ou outro editor de texto, adicione o seguinte para a parte superior do ficheiro **server.js** da aplicação:

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de serviço Bus

O módulo Azure lê as variáveis de ambiente AZURE\_SERVICEBUS\_espaço de nomes e AZURE\_SERVICEBUS\_acesso\_chave para obter informações necessárias para ligar ao serviço Bus. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações de conta quando chamar **createServiceBusService**.

Para obter um exemplo de definir as variáveis de ambiente num ficheiro de configuração de um serviço de nuvem Azure, consulte o artigo [Node.js serviço em nuvem com armazenamento][].

Para obter um exemplo de definir as variáveis de ambiente no [portal clássica Azure][] para um Web site Azure, consulte o artigo [Aplicação da Web Node.js com armazenamento][].

## <a name="create-a-topic"></a>Criar um tópico

O objeto **ServiceBusService** permite-lhe trabalhar com tópicos. O código seguinte cria um objeto de **ServiceBusService** . Adicione-o na parte superior do ficheiro **server.js** , depois da instrução para importar o módulo azure:

```
var serviceBusService = azure.createServiceBusService();
```

Ao contactar o suporte **createTopicIfNotExists** no objeto **ServiceBusService** , será devolvido o tópico especificado (se existir) ou, será criado um novo tópico com o nome especificado. O código seguinte utiliza **createTopicIfNotExists** para criar ou ligar para o tópico denominado 'MyTopic':

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** também suporta opções adicionais, que permitem-lhe substituir as predefinições de tópico como o tempo de mensagem para live ou o tamanho máximo de tópico. O exemplo seguinte define o tamanho máximo de tópico para 5GB com uma hora para a live de 1 minuto:

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtros

Operações de filtragem opcionais podem ser aplicadas a operações realizadas utilizando **ServiceBusService**. Operações de filtragem pode incluir registo, automaticamente a repetir, etc. Filtros são objetos que implementam um método com a assinatura:

```
function handle (requestOptions, next)
```

Após efetuar pré-processamento no menu Opções de pedido, o método chamadas `next` prisma uma chamada de retorno com a assinatura seguinte:

```
function (returnObject, finalCallback, next)
```

Esta chamada de retorno e depois processamento **returnObject** (a resposta do pedido de servidor), as necessidades de chamada de retorno para quer invocar seguinte se existir para continuar a outros filtros de processamento ou simplesmente invocar **finalCallback** caso contrário, para fazer a invocação de serviço.

Dois filtros implementam lógica de repetição são incluídos com o SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguinte cria um objeto de **ServiceBusService** que utiliza o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Criar subscrições

Subscrições de tópico também são criadas com o objeto **ServiceBusService** . Subscrições são com nome e podem ter um filtro opcional que restringe o conjunto de mensagens entregues fila de espera virtual da subscrição.

> [AZURE.NOTE] Subscrições são persistentes e irão continuar a existir até a ambos estes ou o tópico estão associados com, é eliminado. Se a sua aplicação contiver lógica para criar uma subscrição, deverá primeiro verificar se a subscrição já existe utilizando o método de **getSubscription** .

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro predefinido (MatchAll)

O filtro de **MatchAll** é o filtro predefinido que é utilizado se não for especificado nenhum filtro quando é criada uma nova subscrição. Quando é utilizado o filtro de **MatchAll** , todas as mensagens publicadas para o tópico são colocadas na fila de espera virtual da subscrição. O exemplo seguinte cria uma subscrição denominada 'AllMessages' e utiliza o filtro de **MatchAll** predefinido.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com os filtros

Também pode criar filtros que permitam que ao âmbito do que as mensagens enviadas para um tópico deve ser apresentada dentro de uma subscrição de tópico específico.

O tipo de filtro suportado em subscrições mais flexível é o **SqlFilter**, que implementa um subconjunto de SQL92. Trabalhar com os filtros SQL nas propriedades das mensagens que são publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser utilizadas com um filtro SQL, reveja a [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] sintaxe.

Filtros podem ser adicionados a uma subscrição utilizando o método de **createRule** do objeto **ServiceBusService** . Este método permite-lhe adicionar novos filtros numa subscrição existente.

> [AZURE.NOTE] Uma vez que o filtro predefinido é aplicado automaticamente a todas as subscrições de novas, primeiro tem de remover o filtro predefinido ou o **MatchAll** substituirão os outros filtros de que pode especificar. Pode remover a regra predefinida, utilizando o método de **deleteRule** do objeto **ServiceBusService** .

O exemplo seguinte cria uma subscrição denominada `HighMessages` com **SqlFilter** que apenas seleciona todas as mensagens com uma propriedade personalizada **messagenumber** maior do que 3:

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Do mesmo modo, o exemplo seguinte cria uma subscrição denominada `LowMessages` com **SqlFilter** que apenas seleciona todas as mensagens com uma **messagenumber** propriedade menor ou igual a 3:

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Quando uma mensagem agora é enviada para `MyTopic`, sempre será enviado para destinatários subscritos o `AllMessages` subscrição do tópico e seletivamente entregue para destinatários subscritos o `HighMessages` e `LowMessages` subscrições tópico (consoante o conteúdo da mensagem).

## <a name="how-to-send-messages-to-a-topic"></a>Como enviar mensagens para um tópico

Para enviar uma mensagem a um tópico de serviço Bus, a aplicação tem de utilizar o método de **sendTopicMessage** do objeto **ServiceBusService** .
As mensagens enviadas para tópicos do serviço Bus são **BrokeredMessage** objectos.
Objectos de **BrokeredMessage** têm um conjunto de propriedades padrão (como **etiqueta** e **TimeToLive**), um dicionário que é utilizado para colocar em espera propriedades específicas da aplicação personalizada, um e corpo de dados de cadeia. Uma aplicação pode definir o corpo da mensagem, passando um valor de cadeia para o **sendTopicMessage** e as propriedades necessárias padrão serão preenchidas por valores predefinidos.

O exemplo seguinte demonstra como enviar mensagens para 'MyTopic' de cinco teste. Tenha em atenção que o valor da propriedade **messagenumber** de cada mensagem varia iteração do ciclo (Isto irá determinar quais as subscrições recebem-la):

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Tópicos de serviço Bus suportam um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos num tópico mas há um remate no tamanho total das mensagens contidos por um tópico. Tamanho este tópico é definido no momento de criação, com um limite máximo de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

As mensagens são recebidas de uma subscrição utilizando o método de **receiveSubscriptionMessage** no objeto **ServiceBusService** . Por predefinição, as mensagens são eliminadas da subscrição, tal como estes são lidos; No entanto, pode ler (pré-visualização) e bloquear a mensagem sem eliminá-la da subscrição ao definir o parâmetro opcional **isPeekLock** **Verdadeiro**.

O comportamento predefinido da leitura e eliminar a mensagem como parte da operação de receção é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

Se o parâmetro **isPeekLock** estiver definido como **Verdadeiro**, a receber torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação.
Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao chamar o método de **deleteMessage** e fornecer a mensagem a ser eliminada como um parâmetro. O método de **deleteMessage** irá marcar a mensagem como sendo consumidas e removê-la da subscrição.

O exemplo seguinte demonstra como mensagens podem ser recebidas e processados utilizando **receiveSubscriptionMessage**. O exemplo pela primeira vez recebe elimina uma mensagem da subscrição 'LowMessages' e, em seguida, recebe uma mensagem da subscrição 'HighMessages' utilizando **isPeekLock** definida como verdadeiro. Em seguida, elimina a mensagem utilizando **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método de **unlockMessage** o objeto **ServiceBusService** . Isto vai originar Bus de serviço para desbloquear a mensagem no prazo da subscrição e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro da subscrição e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus desbloqueia automaticamente a mensagem e torna disponível na receber novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do método **deleteMessage** é chamado, em seguida, a mensagem irá ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando a propriedade **MessageId** da mensagem, será que permaneçam constante ao longo de tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Tópicos e subscrições são persistentes e têm de ser explicitamente eliminadas através do [portal clássica Azure][] ou através de programação.
O exemplo seguinte demonstra como eliminar o tópico denominado `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Eliminar um tópico também irá eliminar quaisquer subscrições que estão registadas com o tópico. Subscrições também podem ser eliminadas forma independente. O exemplo seguinte mostra como eliminar uma subscrição denominada `HighMessages` a partir do `MyTopic` tópico:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para tópicos Bus de serviço, siga estas ligações para mais informações.

-   Consulte o artigo [filas, tópicos e subscrições][].
-   Referência de API para [SqlFilter][].
-   Visite o repositório [Azure SDK para nó][] no GitHub.

  [Azure SDK para nó]: https://github.com/Azure/azure-sdk-for-node
  [Azure portal clássico]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Serviço em nuvem node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Criar e implementar uma aplicação de Node.js para um Web Site do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Serviço em nuvem node.js com armazenamento]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Aplicação Web do node.js com armazenamento]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
