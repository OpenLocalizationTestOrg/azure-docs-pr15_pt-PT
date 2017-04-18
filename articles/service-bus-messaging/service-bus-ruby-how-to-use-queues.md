<properties
    pageTitle="Como utilizar filas de serviço Bus com Rubi | Microsoft Azure"
    description="Saiba como utilizar filas de serviço Bus no Azure. Exemplos de código escritos rubi."
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

# <a name="how-to-use-service-bus-queues"></a>Como utilizar filas Bus de serviço

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia descreve como utilizar filas Bus de serviço. Os exemplos estão escritos em Rubi e utilizam a Azure gem. Os cenários abrangidos incluem **criar filas, enviar e receber mensagens**e **Eliminar filas**. Para mais informações sobre filas Bus de serviço, consulte a secção [Os passos seguintes](#next-steps) .

## <a name="what-are-service-bus-queues"></a>O que são filas Bus de serviço?

Serviço Bus filas suportam a um modelo de comunicação *juntamente mensagens* . Com filas, componentes de uma aplicação distribuída não comunicarem diretamente com outro; em vez disso, eles trocarem mensagens através de uma fila, que age como um intermédio. Um produtor de mensagem (remetente) mãos desativar uma mensagem para a fila e, em seguida, continua a sua processamento.
Forma assíncrona, um consumidor mensagem (recetor) obtém a mensagem da fila de espera e processa-lo. Produtor não tem de aguardar uma resposta do consumidor para poder continuar a processar e ainda mais enviar mensagens. Filas oferecem a entrega de mensagens **do primeiro, primeiro saída (FIFO)** para um ou mais consumidores concorrentes. Isto é, as mensagens são recebidas normalmente e processadas pelos destinatários pela ordem em que foram adicionadas à fila de espera e cada mensagem é recebida e processada pelos consumidores de apenas uma mensagem.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Serviço Bus filas são uma tecnologia uso geral que pode ser utilizada para uma grande variedade de cenários:

-   Comunicação entre funções web e trabalhador num [várias camadas aplicação Azure](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   Comunicação entre aplicações no local e o Azure alojado aplicações numa [solução híbrido](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Comunicação entre os componentes de uma aplicação distribuída em execução no local em organizações diferentes ou os departamentos de uma organização.

Utilizar filas pode permitem-lhe dimensionar terminar as suas aplicações melhor e ativar RDP mais a sua arquitetura.

## <a name="create-a-namespace"></a>Criar um espaço de nomes

Para começar a utilizar filas Bus de serviço no Azure, primeiro tem de criar um espaço de nomes. Um espaço de nomes fornece um âmbito contentor para endereçar recursos de serviço Bus na aplicação. Tem de criar o espaço de nomes através da interface da linha de comandos porque o portal do Azure não cria o espaço de nomes com ligação ACS.

Para criar um espaço de nomes:

1. Abra uma consola do Azure Powershell.

2. Escreva o seguinte comando para criar um espaço de nomes de serviço Bus. Fornecer o seu próprio valor espaço de nomes e especificar a mesma região como a aplicação.

    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Obter gestão as credenciais para o espaço de nomes

Para realizar operações de gestão, tal como criar uma fila no novo espaço de nomes, terá de obter as credenciais de gestão para o espaço de nomes.

O cmdlet do PowerShell que executou para criar o espaço de nomes do serviço Azure bus apresenta a chave que pode utilizar para gerir o espaço de nomes. Copie o valor de **DefaultKey** . Irá utilizar este valor no seu código posteriormente neste tutorial.

![Copiar chave](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE] Também pode encontrar esta tecla se inicie a sessão [portal do Azure](https://portal.azure.com/) e navegue para as informações de ligação para o seu espaço de nomes de serviço Bus.

## <a name="create-a-ruby-application"></a>Criar uma aplicação Rubi

Crie uma aplicação rubi. Para obter instruções, consulte o artigo [criar uma aplicação de Rubi no Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a aplicação para utilizar o serviço Bus

Para utilizar Azure Service Bus, transferir e utilizar o pacote de Azure Rubi, que inclui um conjunto de bibliotecas de conveniência que comunicar com os serviços de resto de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote

1. Utilize uma interface da linha de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix).

2. Escreva "azure de instalação gem" na janela de comandos para instalar o gem e dependências.

### <a name="import-the-package"></a>Importar o pacote

Utilizar o editor de texto Favoritos, adicione o seguinte para a parte superior do ficheiro Rubi onde tenciona utilizar o armazenamento:

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma ligação do Azure Service Bus

O módulo Azure lê as variáveis de ambiente **AZURE\_SERVICEBUS\_espaço de nomes** e **AZURE\_SERVICEBUS\_ACCESS_KEY** para obter informações necessárias para estabelecer ligação ao serviço Bus espaço de nomes. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações de espaço de nomes antes de utilizar **Azure::ServiceBusService** com o seguinte código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Defina o valor de espaço de nomes para o valor que criou, em vez do URL completo. Por exemplo, utilize **"yourexamplenamespace"**, não "yourexamplenamespace.servicebus.windows.net".

## <a name="how-to-create-a-queue"></a>Como criar uma fila

O objeto **Azure::ServiceBusService** permite-lhe trabalhar com filas. Para criar uma fila de espera, utilize o método de **create_queue()** . O exemplo seguinte cria uma fila ou impresso erros.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Também pode passar um objeto de **Azure::ServiceBus::Queue** com as opções adicionais, que lhe permite substituir as definições de fila de espera predefinido, como o tempo de mensagem para live ou o tamanho máximo da fila. O exemplo seguinte mostra como configurar o tamanho máximo da fila para 5GB e a hora live minuto 1:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila

Para enviar uma mensagem para uma fila Bus de serviço, chamadas de aplicações o **Enviar\_fila\_message()** método no objeto **Azure::ServiceBusService** . Mensagens filas de serviço Bus enviadas para (e recebidas a partir do) são **Azure::ServiceBus::BrokeredMessage** objetos e tem um conjunto de propriedades padrão (tal como **etiqueta** e **tempo\_para\_live**), um dicionário que é utilizado para colocar em espera propriedades específicas da aplicação personalizada bem como um corpo dos dados da aplicação arbitrário. Uma aplicação pode definir o corpo da mensagem, passando um valor de cadeia como a mensagem e as propriedades necessárias padrão serão preenchidas com valores predefinidos.

O exemplo seguinte demonstra como enviar uma mensagem de teste para a fila com o nome "fila de teste" utilizar **Enviar\_fila\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Serviço Bus filas suportam a um tamanho máximo da mensagem de 256 KB na [camada padrão](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicação padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não existe nenhum limite o número de mensagens contidos numa fila mas há um remate no tamanho total das mensagens contidos por uma fila. Este tamanho da fila é definido a hora de criação, com um limite máximo de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila

As mensagens são recebidas de uma fila utilizando o **receber\_fila\_message()** método no objeto **Azure::ServiceBusService** . Por predefinição, as mensagens são ler e bloqueadas sem a ser eliminado da fila de espera. No entanto, pode eliminar mensagens de fila de espera tal como estes são lidos definindo a **: peek_lock** opção para **Falso**.

O comportamento predefinido facilita a leitura e eliminar uma operação de duas fases, que também torna possível suportar aplicações que não poder permitir mensagens em falta. Quando o serviço Bus recebe um pedido, localiza a próxima mensagem para ser consumida, bloqueios-la para impedir que outros consumidores recebê-la e, em seguida, devolve-o para a aplicação. Depois da aplicação de concluir o processamento a mensagem (ou armazena sujeito para processamento futuro), que é concluído a segunda fase do processo de receção ao contactar o suporte **Eliminar\_fila\_message()** método e fornecer a mensagem a ser eliminada como um parâmetro. O **Eliminar\_fila\_message()** método serão marcar a mensagem como sendo consumidas e removê-la da fila de espera.

Se o **: pré-visualização\_bloquear** parâmetro estiver definido como **Falso**, de leitura e eliminar a mensagem torna-se o modelo mais simples e funciona melhor para cenários em que uma aplicação pode tolerar não processamento de uma mensagem em caso de falha. Para compreender isto, considere um cenário em que o consumidor emite o pedido de receção e, em seguida, falha antes de processá-la. Uma vez que o serviço Bus irá tiver marcado a mensagem como consumida, quando a aplicação reinicia e começa consumir mensagens novamente,-lo será não ter visto a mensagem que foi consumida antes da falha de sistema.

O exemplo seguinte demonstra como receber e processar mensagens utilizando **receber\_fila\_message()**. O exemplo recebe e elimina uma mensagem utilizando **: pré-visualização\_bloquear** definido como **Falso**, em seguida, -recebe outra mensagem e, em seguida, elimina a mensagem utilizando **Eliminar\_fila\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como lidar falhas de aplicações e legível mensagens

Serviço Bus fornece funcionalidade para ajudá-lo a recuperar correctamente de erros na sua aplicação ou dificuldades processamento de uma mensagem. Se uma aplicação do destinatário não consegue processar a mensagem por algum motivo, em seguida, pode ligar a **Desbloquear\_fila\_message()** método no objeto **Azure::ServiceBusService** . Isto faz com que o serviço Bus desbloquear a mensagem na fila de espera e torná-lo disponível para ser recebidos novamente, pela mesma aplicação consome ou por outra aplicação consome.

Também existe um limite de tempo associado a uma mensagem bloqueada dentro de fila de espera e, se a aplicação falhar processar a mensagem antes do tempo limite de bloqueio expira (por exemplo, se a aplicação falha), em seguida, o serviço Bus desbloqueia automaticamente a mensagem e torna disponível na receber novamente.

Se que a aplicação falha após a transformação a mensagem mas antes de **Eliminar\_fila\_message()** método é chamado, em seguida, vai ser reenviada a mensagem para a aplicação quando é reiniciado. Esta opção é geralmente denominada **, Pelo menos, uma vez processamento**; Isto é, cada mensagem é processada pelo menos uma vez, mas em determinadas situações a mesma mensagem pode ser sido novamente entregues. Se o cenário de não poder permitir o processamento de duplicados, em seguida, os programadores de aplicações devem adicionar lógica adicional para a sua aplicação para processar entrega de mensagens duplicados. Muitas vezes pode fazê-utilizar o **mensagem\_id** propriedade da mensagem permanece constante ao longo de tentativas de entrega.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos filas Bus de serviço, siga estas ligações para mais informações.

-   Descrição geral das [subscrições, tópicos e filas](service-bus-queues-topics-subscriptions.md).
-   Visite o repositório [Azure SDK para Rubi](https://github.com/Azure/azure-sdk-for-ruby) no GitHub.

Para uma comparação entre as filas Azure Service Bus outros fabricantes referidos neste artigo e filas Azure abordado no artigo [como utilizar o armazenamento de fila de Rubi](../storage/storage-ruby-how-to-use-queue-storage.md) , consulte o artigo [Azure filas e Azure Service Bus filas - comparados e Contrasted](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 
