<properties 
    pageTitle="Como utilizar filas de serviço Bus com Python | Microsoft Azure" 
    description="Saiba como utilizar Azure Service Bus filas de Python." 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="sethm;lmazuel"/>


# <a name="how-to-use-service-bus-queues"></a>Como utilizar filas Bus de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este artigo descreve como utilizar filas Bus de serviço. Os exemplos são gravados no Python e utilizam o [pacote de Python Azure Service Bus][]. Os cenários abrangidos incluem **criar filas, enviar e receber mensagens**e **Eliminar filas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Para instalar Python ou o [pacote de Python Azure Service Bus][], consulte o [Guia de instalação Python](../python-how-to-install.md).

## <a name="create-a-queue"></a>Criar uma fila

O objeto **ServiceBusService** permite-lhe trabalhar com filas. Adicione o seguinte código na parte superior de qualquer ficheiro Python na qual que pretende aceder programaticamente Bus de serviço:

```
from azure.servicebus import ServiceBusService, Message, Queue
```

O código seguinte cria um objeto de **ServiceBusService** . Substituir `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com o seu espaço de nomes, o nome da chave acesso partilhado assinatura (SA) e o valor.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Os valores para o nome da chave SA e valor podem encontrar as informações de ligação do [Azure portal clássico][] ou no painel do Visual Studio **Propriedades** Quando seleciona o espaço de nomes de serviço Bus no Explorador do servidor (como mostrado na secção anterior).

```
bus_service.create_queue('taskqueue')
```

**create_queue** também suporta opções adicionais, que permitem-lhe substituir as predefinições de fila como o tempo de mensagem para live (TTL) ou o tamanho máximo da fila. O exemplo seguinte define o tamanho máximo da fila 5GB e o valor TTL minuto 1:

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens para uma fila

Para enviar uma mensagem para uma fila Bus de serviço, as chamadas de aplicação a **Enviar\_fila\_mensagem** método no objeto **ServiceBusService** .

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila denominada *taskqueue utilizando* **Enviar\_fila\_mensagem**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Serviço Bus filas suportam a um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos numa fila mas há um remate no tamanho total das mensagens contidos por uma fila. Este tamanho da fila é definido a hora de criação, com um limite máximo de 5 GB. Para mais informações acerca de quotas, consulte o artigo [quotas Bus de serviço][].

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila

As mensagens são recebidas de uma fila utilizando o **receber\_fila\_mensagem** método no objeto **ServiceBusService** :

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

As mensagens são eliminadas da fila, tal como estes são lidos quando o parâmetro **pré-visualização\_bloquear** estiver definida como **False**. Pode ler (pré-visualização) e bloquear a mensagem sem eliminá-la a partir da fila definindo o parâmetro **pré-visualização\_bloquear** **Verdadeiro**.

O comportamento de leitura e eliminar a mensagem como parte da operação de receção é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

Se o **pré-visualização\_bloquear** parâmetro estiver definido como **Verdadeiro**, a receber torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao contactar o método de **Eliminar** no objeto **mensagem** . O método **delete** irá marcar a mensagem como sendo consumidas e removê-la da fila de espera.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método **Desbloquear** o objeto de **mensagem** . Isto vai originar Bus de serviço para desbloquear a mensagem na fila de espera e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro de fila de espera e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus serão desbloquear automaticamente a mensagem e torná-lo disponível para ser recebidos novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do método **delete** chama-se, em seguida, a mensagem irá ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando a propriedade **MessageId** da mensagem, será que permaneçam constante ao longo de tentativas de entrega.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos filas Bus de serviço, siga estas ligações para mais informações.

-   Consulte o artigo [filas, tópicos e subscrições][].

[Azure portal clássico]: https://manage.windowsazure.com
[Pacote Python Azure Service Bus]: https://pypi.python.org/pypi/azure-servicebus  
[Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
[Quotas de Bus de serviço]: service-bus-quotas.md
 
