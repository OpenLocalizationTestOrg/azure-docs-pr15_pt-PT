<properties 
    pageTitle="Como utilizar tópicos serviço Bus com Python | Microsoft Azure" 
    description="Saiba como utilizar a subscrições do Python e tópicos Azure Service Bus." 
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
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como utilizar a subscrições e tópicos de Bus de serviço

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como utilizar a subscrições e tópicos de Bus de serviço. Os exemplos são gravados no Python e utilizam o [pacote de Python Azure][]. Os cenários abrangidos incluem **criar tópicos e subscrições**, **criar filtros de subscrição**, **enviar mensagens a um tópico**, **receber mensagens de uma subscrição**e **Eliminar tópicos e subscrições**. Para mais informações sobre tópicos e subscrições, consulte a secção [Os passos seguintes](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Nota:** Se precisar de instalar o Python ou o [pacote de Python Azure][], consulte o [Guia de instalação Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Criar um tópico

O objeto **ServiceBusService** permite-lhe trabalhar com tópicos. Adicione o seguinte na parte superior de qualquer ficheiro Python na qual pretende através de programação aceder ao serviço Bus:

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

O código seguinte cria um objeto de **ServiceBusService** . Substituir `mynamespace`, `sharedaccesskeyname`, e `sharedaccesskey` com o seu espaço de nomes real, assinatura partilhadas do Access (SA) chave, o valor nome e chave.

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Pode obter os valores para o nome da chave SA e valor a partir do [Azure portal][].

```
bus_service.create_topic('mytopic')
```

**criar\_tópico** também suporta opções adicionais, que permitem-lhe substituir as predefinições de tópico como o tempo de mensagem para live ou o tamanho máximo de tópico. O exemplo seguinte define o tamanho máximo de tópico 5 GB e uma hora para live o valor (TTL) de 1 minuto:

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Criar subscrições

Subscrições para tópicos também são criadas com o objeto **ServiceBusService** . Subscrições são com nome e podem ter um filtro opcional que restringe o conjunto de mensagens entregues fila de espera virtual da subscrição.

> [AZURE.NOTE] Subscrições são persistentes e irão continuar a existir até, tanto o tópico para o qual subscrito, são eliminados.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro predefinido (MatchAll)

O filtro de **MatchAll** é o filtro predefinido que é utilizado se não for especificado nenhum filtro quando é criada uma nova subscrição. Quando é utilizado o filtro de **MatchAll** , todas as mensagens publicadas para o tópico são colocadas na fila de espera virtual da subscrição. O exemplo seguinte cria uma subscrição denominada 'AllMessages' e utiliza o filtro de **MatchAll** predefinido.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com os filtros

Também pode definir filtros que permitem-lhe especificar quais as mensagens enviadas para um tópico devem ser apresentada dentro de uma subscrição de tópico específico.

O tipo de filtro suportado em subscrições mais flexível é uma **SqlFilter**, que implementa um subconjunto de SQL92. Trabalhar com os filtros SQL nas propriedades das mensagens que são publicadas para o tópico. Para mais informações sobre as expressões que podem ser utilizados com um filtro SQL, consulte a sintaxe de [SqlFilter.SqlExpression][] .

Pode adicionar filtros para uma subscrição utilizando o **criar\_regra** método do objeto **ServiceBusService** . Este método permite-lhe adicionar novos filtros numa subscrição existente.

> [AZURE.NOTE] Uma vez que o filtro predefinido é aplicado automaticamente a todas as subscrições de novas, primeiro tem de remover o filtro predefinido ou o **MatchAll** substituirão os outros filtros de que pode especificar. Pode remover a regra predefinida utilizando o **Eliminar\_regra** método do objeto **ServiceBusService** .

O exemplo seguinte cria uma subscrição denominada `HighMessages` com **SqlFilter** que apenas seleciona todas as mensagens com uma propriedade personalizada **messagenumber** maior do que 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Do mesmo modo, o exemplo seguinte cria uma subscrição denominada `LowMessages` com **SqlFilter** que apenas seleciona todas as mensagens com uma **messagenumber** propriedade menor ou igual a 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Agora, quando uma mensagem é enviada para `mytopic` sempre seja entregue destinatários subscrito para a subscrição de tópico **AllMessages** e seletivamente entregues destinatários subscritos para as subscrições de tópico **HighMessages** e **LowMessages** (consoante o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem a um tópico de serviço Bus, tem de utilizar a aplicação a **Enviar\_tópico\_mensagem** método do objeto **ServiceBusService** .

O exemplo seguinte demonstra como enviar mensagens para de cinco teste `mytopic`. Tenha em atenção que o valor da propriedade **messagenumber** de cada mensagem varia iteração do ciclo (Isto determina quais as subscrições recebem-la):

```
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Tópicos de serviço Bus suportam um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos num tópico mas há um remate no tamanho total das mensagens contidos por um tópico. Tamanho este tópico é definido no momento de criação, com um limite máximo de 5 GB. Para mais informações acerca de quotas, consulte o artigo [quotas Bus de serviço][].

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

As mensagens são recebidas de uma subscrição utilizando o **receber\_subscrição\_mensagem** método no objeto **ServiceBusService** :

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

As mensagens são eliminadas da subscrição, tal como estes são lidos quando o parâmetro **pré-visualização\_bloquear** estiver definida como **False**. Pode ler (pré-visualização) e bloquear a mensagem sem eliminá-la a partir da fila definindo o parâmetro **pré-visualização\_bloquear** **Verdadeiro**.

O comportamento de leitura e eliminar a mensagem como parte da operação de receção é o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

Se o **pré-visualização\_bloquear** parâmetro estiver definido como **Verdadeiro**, a receber torna-se uma operação de duas fases, que torna possível para suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), conclui a segunda fase do processo de receção ao contactar o método de **Eliminar** o objeto de **mensagem** . O método **Eliminar** marca a mensagem como sendo consumidas e remove-a da subscrição.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação recetor for não é possível processar a mensagem por algum motivo,-pode ligar o método **Desbloquear** o objeto de **mensagem** . Isto vai originar Bus de serviço para desbloquear a mensagem no prazo da subscrição e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro da subscrição e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus desbloqueia automaticamente a mensagem e torna disponível na receber novamente.

Se que a aplicação falha após a transformação a mensagem, mas antes do método **delete** chama-se, em seguida, a mensagem irá ser reenviada para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**, ou seja, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes fazê-utilizando a propriedade **MessageId** da mensagem, será que permaneçam constante ao longo de tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Tópicos e subscrições são persistentes e têm de ser explicitamente eliminadas através do [Azure portal][] ou através de programação. O exemplo seguinte mostra como eliminar o tópico denominado `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Eliminar um tópico também elimina quaisquer subscrições que estão registadas com o tópico. Subscrições também podem ser eliminadas forma independente. O código seguinte mostra como eliminar uma subscrição denominada `HighMessages` a partir do `mytopic` tópico:

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para tópicos Bus de serviço, siga estas ligações para mais informações.

-   Consulte o artigo [filas, tópicos e subscrições][].
-   Referência para [SqlFilter.SqlExpression][].

[Portal do Azure]: https://portal.azure.com
[Pacote Python Azure]: https://pypi.python.org/pypi/azure  
[Filas, tópicos e subscrições]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Quotas de Bus de serviço]: service-bus-quotas.md 
