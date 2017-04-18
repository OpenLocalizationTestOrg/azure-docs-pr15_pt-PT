<properties
    pageTitle="Como utilizar o serviço Bus tópicos (Rubi) | Microsoft Azure"
    description="Saiba como utilizar a subscrições e tópicos de serviço Bus no Azure. Exemplos de código são escritos para aplicações rubi."
    services="service-bus"
    documentationCenter="ruby"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topicssubscriptions"></a>Como utilizar o serviço Bus tópicos/subscrições

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como utilizar a subscrições a partir de aplicações Rubi e tópicos de Bus de serviço. Os cenários abrangidos incluem a **criação de tópicos e subscrições, criar filtros de subscrição, enviar mensagens de** um tópico, **receber mensagens de uma subscrição**e **Eliminar tópicos e subscrições**. Para mais informações sobre tópicos e subscrições, consulte a secção [Os passos seguintes](#next-steps) .

## <a name="service-bus-topics-and-subscriptions"></a>Tópicos de serviço Bus e subscrições

Tópicos de serviço Bus subscrições suportam e um *Publicar/subscreva o* modelo de comunicação de mensagens. Quando utilizar tópicos e subscrições, componentes de uma aplicação distribuída não comunicarem diretamente com outro; em vez disso, eles trocarem mensagens através de um tópico que funciona como um intermédio.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrariamente ao filas Bus de serviço, onde cada mensagem é processada por um único consumidor, tópicos e subscrições fornecem um formulário **um-para-muitos** de comunicação, utilizando um padrão de publicar/subscrever. É possível registar múltiplas subscrições a um tópico. Quando uma mensagem é enviada para um tópico, em seguida, é tornado disponíveis para cada subscrição para processar de forma independente.

Uma subscrição de tópico é semelhante a uma fila de espera virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, pode registar regras de filtro para um tópico por subscrição base em, que permite-lhe filtro/restringir um tópico que mensagens são recebidas por quais as subscrições tópico.

Tópicos de serviço Bus e subscrições permitem-lhe dimensionar para processar um grande número de mensagens através de um grande número de utilizadores e aplicações.

## <a name="create-a-namespace"></a>Criar um espaço de nomes

Para começar a utilizar filas Bus de serviço no Azure, primeiro tem de criar um espaço de nomes. Um espaço de nomes fornece um âmbito contentor para endereçar recursos de serviço Bus na aplicação. Tem de criar o espaço de nomes através da interface da linha de comandos porque o [Azure portal][] não cria o espaço de nomes com ligação ACS.

Para criar um espaço de nomes:

1. Abra uma janela de consola do Azure Powershell.

2. Escreva o seguinte comando para criar um espaço de nomes. Fornecer o seu próprio valor espaço de nomes e especificar a mesma região como a aplicação.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```

    ![Criar espaço de nomes](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Obter predefinido gestão as credenciais para o espaço de nomes

Para realizar operações de gestão, tal como criar uma fila no novo espaço de nomes, terá de obter as credenciais de gestão para o espaço de nomes.

O cmdlet do PowerShell que executou para criar o espaço de nomes de serviço Bus apresenta a chave que pode utilizar para gerir o espaço de nomes. Copie o valor de **DefaultKey** . Irá utilizar este valor no seu código posteriormente neste tutorial.

![Copiar chave](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]
> Também pode encontrar esta tecla se inicie a sessão [portal do Azure][] e navegue para as informações de ligação para o espaço de nomes.

## <a name="create-a-ruby-application"></a>Criar uma aplicação Rubi

Para obter instruções, consulte o artigo [criar uma aplicação de Rubi no Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilização serviço Bus

Para utilizar o serviço Bus, transferir e utilizar o pacote de Azure Rubi, que inclui um conjunto de bibliotecas de conveniência que comunicar com os serviços de resto de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote

1. Utilize uma interface da linha de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix).

2. Escreva "azure de instalação gem" na janela de comandos para instalar o gem e dependências.

### <a name="import-the-package"></a>Importar o pacote

Utilizar o editor de texto Favoritos, adicione o seguinte para a parte superior do ficheiro Rubi em que pretende utilizar armazenamento:

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de serviço Bus

O módulo Azure lê as variáveis de ambiente **AZURE\_SERVICEBUS\_espaço de nomes** e **AZURE\_SERVICEBUS\_acesso\_chave** para obter informações necessárias para ligar ao seu espaço de nomes. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações de espaço de nomes antes de utilizar **Azure::ServiceBusService** com o seguinte código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Defina o valor de espaço de nomes para o valor que criou em vez do URL completo. Por exemplo, utilize **"yourexamplenamespace"**, não "yourexamplenamespace.servicebus.windows.net".

## <a name="create-a-topic"></a>Criar um tópico

O objeto **Azure::ServiceBusService** permite-lhe trabalhar com tópicos. O código seguinte cria um objeto de **Azure::ServiceBusService** . Para criar um tópico, utilize o **criar\_topic()** método. O exemplo seguinte cria um tópico ou impresso os erros, se existirem qualquer.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Também pode passar um objeto de **Azure::ServiceBus::Topic** com opções adicionais, que permitem-lhe substituir as predefinições da tópico como o tempo de mensagem para live ou o tamanho máximo da fila. O exemplo seguinte mostra a definir o tamanho máximo da fila para 5GB e a hora live minuto 1:

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Criar subscrições

Subscrições de tópico também são criadas com o objeto **Azure::ServiceBusService** . Subscrições são com nome e podem ter um filtro opcional que restringe o conjunto de mensagens entregues fila de espera virtual da subscrição.

Subscrições são persistentes e irão continuar a existir até a ambos estes ou o tópico estão associados com, é eliminado. Se a sua aplicação contiver lógica para criar uma subscrição, deverá primeiro verificar se a subscrição já existe utilizando o método de getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma subscrição com o filtro predefinido (MatchAll)

O filtro de **MatchAll** é o filtro predefinido que é utilizado se não for especificado nenhum filtro quando é criada uma nova subscrição. Quando é utilizado o filtro de **MatchAll** , todas as mensagens publicadas para o tópico são colocadas na fila de espera virtual da subscrição. O exemplo seguinte cria uma subscrição denominada "todas as mensagens" e utiliza o filtro de **MatchAll** predefinido.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Criar subscrições com os filtros

Também pode definir filtros que permitem-lhe especificar quais as mensagens enviadas para um tópico devem ser apresentada dentro de uma subscrição específica.

O tipo de filtro suportado em subscrições mais flexível é o **Azure::ServiceBus::SqlFilter**, que implementa um subconjunto de SQL92. Trabalhar com os filtros SQL nas propriedades das mensagens que são publicadas para o tópico. Para obter mais detalhes sobre as expressões que podem ser utilizadas com um filtro SQL, reveja a sintaxe de [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) .

Pode adicionar filtros para uma subscrição utilizando o **criar\_rule()** método do objeto **Azure::ServiceBusService** . Este método permite-lhe adicionar novos filtros numa subscrição existente.

Uma vez que o filtro predefinido é aplicado automaticamente a todas as subscrições de novas, primeiro tem de remover o filtro predefinido ou o **MatchAll** substituirão os outros filtros de que pode especificar. Pode remover a regra predefinida utilizando o **Eliminar\_rule()** método no objeto **Azure::ServiceBusService** .

O exemplo seguinte cria uma subscrição denominada "alta-mensagens" com um **Azure::ServiceBus::SqlFilter** apenas seleciona todas as mensagens com um personalizado **mensagem\_número** propriedade maior do que 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Do mesmo modo, o exemplo seguinte cria uma subscrição denominada "baixa-mensagens" com um **Azure::ServiceBus::SqlFilter** apenas seleciona todas as mensagens com uma **message_number** propriedade menor ou igual a 3:

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Quando uma mensagem ser enviada agora para "tópico de teste", é sempre entregue para destinatários subscrito para a subscrição de tópico "todas as mensagens" e seletivamente entregues destinatários subscritos para as subscrições de tópico "alta-mensagens" e "mensagens de baixa" (consoante o conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico

Para enviar uma mensagem a um tópico de serviço Bus, tem de utilizar a aplicação a **Enviar\_tópico\_message()** método no objeto **Azure::ServiceBusService** . As mensagens enviadas para tópicos do serviço Bus são instâncias dos objetos **Azure::ServiceBus::BrokeredMessage** . Objetos de **Azure::ServiceBus::BrokeredMessage** têm um conjunto de propriedades padrão (tal como **etiqueta** e **tempo\_para\_live**), um dicionário que é utilizado para colocar em espera propriedades específicas da aplicação personalizada, um e corpo de dados de cadeia. Uma aplicação pode definir o corpo da mensagem, passando um valor de cadeia para o **Enviar\_tópico\_message()** método e quaisquer obrigatório propriedades padrão serão preenchidas por valores predefinidos.

O exemplo seguinte demonstra como enviar mensagens para "tópico de teste" de cinco teste. Tenha em atenção que o valor da propriedade personalizada **message_number** de cada mensagem varia iteração do ciclo (Isto determina qual a subscrição difere):

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Tópicos de serviço Bus suportam um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos num tópico mas há um remate no tamanho total das mensagens contidos por um tópico. Tamanho este tópico é definido no momento de criação, com um limite máximo de 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma subscrição

As mensagens são recebidas de uma subscrição utilizando o **receber\_subscrição\_message()** método no objeto **Azure::ServiceBusService** . Por predefinição, as mensagens são read(peak) e bloqueado sem eliminá-la da subscrição. Pode ler e eliminar a mensagem da subscrição, definindo a **pré-visualização\_bloquear** opção para **Falso**.

O comportamento predefinido facilita a leitura e eliminar uma operação de duas fases, que também torna possível suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), que é concluído a segunda fase do processo de receção ao contactar o suporte **Eliminar\_subscrição\_message()** método e fornecer a mensagem a ser eliminada como um parâmetro. O **Eliminar\_subscrição\_message()** método serão marcar a mensagem como sendo consumidas e removê-la da subscrição.

Se o **: pré-visualização\_bloquear** parâmetro estiver definido como **Falso**, de leitura e eliminar a mensagem torna-se o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Porque Bus serviço será tiver marcado a mensagem como consumida, em seguida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha.

O exemplo seguinte demonstra como mensagens podem ser recebidas e utilizar transformados **receber\_subscrição\_message()**. O exemplo recebe e elimina uma mensagem da subscrição "mensagens de baixa" utilizando **: pré-visualização\_bloquear** definido como **Falso**, em seguida, -recebe outra mensagem a partir de "como sendo de alta-mensagens" e, em seguida, elimina a mensagem utilizando **Eliminar\_subscrição\_message()**:

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Alça de falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação do destinatário não consegue processar a mensagem por algum motivo, em seguida, pode ligar a **Desbloquear\_subscrição\_message()** método no objeto **Azure::ServiceBusService** . Isto faz com que o serviço Bus desbloquear a mensagem no prazo da subscrição e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro da subscrição e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus serão desbloquear automaticamente a mensagem e torná-lo disponível para ser recebidos novamente.

Se que a aplicação falha após a transformação a mensagem mas antes de **Eliminar\_subscrição\_message()** método é chamado, em seguida, a mensagem é sido novamente entregues para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**; Isto é, cada mensagem será processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Esta lógica com frequência é obtida utilizando o **mensagem\_id** propriedade da mensagem, será que permaneçam constante ao longo de tentativas de entrega.

## <a name="delete-topics-and-subscriptions"></a>Eliminar tópicos e subscrições

Tópicos e subscrições são persistentes e têm de ser explicitamente eliminadas através do [Azure portal][] ou através de programação. O exemplo abaixo demonstra como eliminar o tópico com o nome "tópico de teste".

```
azure_service_bus_service.delete_topic("test-topic")
```

Eliminar um tópico também elimina quaisquer subscrições que estão registadas com o tópico. Subscrições também podem ser eliminadas forma independente. O código seguinte demonstra como eliminar a subscrição com o nome "alta mensagens" a partir do tópico "tópico de teste":

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para tópicos Bus de serviço, siga estas ligações para mais informações.

- Consulte o artigo [filas, tópicos e subscrições](service-bus-queues-topics-subscriptions.md).
- Referência de API para [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
- Visite o repositório [Azure SDK para Rubi](https://github.com/Azure/azure-sdk-for-ruby) no GitHub.
 
[Portal do Azure]: https://portal.azure.com
