<properties 
    pageTitle="Mensagens de serviço Bus amostras descrição geral | Microsoft Azure"
    description="Categoriza e descreve Bus de serviço de mensagens amostras com ligações para cada um."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/07/2016"
    ms.author="sethm" />

# <a name="service-bus-messaging-samples"></a>Exemplos de mensagens do serviço Bus

Os exemplos de mensagens do serviço Bus demonstram das principais funcionalidades do [Bus de serviço de mensagens](https://azure.microsoft.com/services/service-bus/) (serviço na nuvem) e o [Serviço Bus para o Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Este artigo categoriza e descreve as amostras disponíveis, com ligações para cada um.

>[AZURE.NOTE] Exemplos de serviço Bus não são instalados com o SDK. Para obter as amostras, visite a [página de amostras Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Para além disso, existe um conjunto actualizado de serviço Bus mensagens amostras [aqui](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (partir este escrita, estes não são descritos neste artigo).  

Para obter exemplos de reencaminhamento, consulte o artigo [amostras de reencaminhamento de Bus de serviço](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Bus de serviço de mensagens

Os exemplos seguintes ilustram como escrever as aplicações que utilizam Bus de serviço de mensagens.

Note que os exemplos de mensagens requerem uma cadeia de ligação para aceder ao seu espaço de nomes de serviço Bus.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Para obter uma cadeia de ligação para o Azure Service Bus

1. Inicie sessão no [portal do Azure](http://portal.azure.com).

1. Na coluna da esquerda, clique em **Serviço Bus**.

1. Clique no nome do espaço de nomes na lista.

3. Na pá espaço de nomes, clique em **partilhado as políticas de acesso**.

4. Na pá **políticas de acesso partilhado** , clique em **RootManageSharedAccessKey**.

6. Copie a cadeia de ligação para a área de transferência.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Para obter uma cadeia de ligação para o serviço Bus para o Windows Server

1. Execute o seguinte cmdlet do PowerShell:

    ```
    get-sbClientConfiguration
    ```

2. Cole a cadeia de ligação no ficheiro App da amostra.

### <a name="getting-started-samples"></a>Introdução amostras de introdução

Estes exemplos descrevem funcionalidades básicas de mensagens.

|Nome de exemplo|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Introdução: Mensagens com filas](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Demonstra como utilizar o Microsoft Azure Service Bus para enviar e receber mensagens de uma fila.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Introdução: Mensagens com tópicos](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Demonstra como utilizar o Microsoft Azure Service Bus para enviar e receber mensagens de um tópico com múltiplas subscrições.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|

### <a name="exploring-features"></a>Explorar funcionalidades

Os exemplos seguintes demonstram várias funcionalidades do serviço Bus.

|Nome de exemplo|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Fornecedores de Token de HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Demonstra as diferentes maneiras de um cliente HTTP/resto com Bus de serviço de autenticação.|2.1|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Cliente do serviço de Bus HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Demonstra como enviar mensagens para e receber mensagens de serviço Bus através de HTTP/restantes.|2.3|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Serviço Bus Autoforwarding](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Demonstra como reencaminhar automaticamente mensagens de uma fila de espera, a subscrição ou a fila de mensagens não entregues para outra fila ou tópico. Também demonstra como enviar uma mensagem para uma fila ou tópico através de uma fila de transferência.|2.3|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: Exemplo de sessão de canal de WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Demonstra como utilizar o Microsoft Azure Service Bus através de canais de Windows Communication Foundation (WCF). O exemplo mostra a utilização de canais WCF para enviar e receber mensagens através de uma fila Bus de serviço. O exemplo mostra sessão e comunicações que não sejam sessão sobre o serviço Bus.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Controlada mensagens: transações](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Demonstra como utilizar o Microsoft Azure Service Bus mensagens funcionalidades dentro de um âmbito da transação para garantir que as secções de mensagens operações são consolidadas atomicamente.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Mensagens juntamente: Operações de gestão utilizar o resto](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Demonstra como efetuar operações de gestão nos Bus serviço utilizando restantes.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Fornecedor de recursos REST APIs](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Demonstra como utilizar as novas REST APIs da serviço Bus RDFE para gerir espaços de nomes e entidades de mensagens.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: Exemplo de sessão de serviço WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Demonstra como utilizar o Microsoft Azure Service Bus utilizando o modelo de serviço WCF. O exemplo mostra a utilização do modelo de serviço WCF para efetuar a comunicação com base em sessão através de uma fila Bus de serviço.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: Pedido resposta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Demonstra como utilizar o Bus de serviço do Microsoft Azure e a funcionalidade de pedido/resposta. O exemplo mostra os clientes simples e servidores comunicar através de uma fila Bus de serviço.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: fila de entregues](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Demonstra como utilizar Bus de serviço do Microsoft Azure e a funcionalidade de "entregues fila" mensagens. O exemplo mostra um remetente simple e o destinatário comunicar através de uma fila Bus de serviço.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Mensagens juntamente: Adiada mensagens](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Demonstra como utilizar a funcionalidade de suspensão de mensagem do Microsoft Azure Service Bus. O exemplo mostra um remetente simple e o destinatário comunicar através de uma fila Bus de serviço.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Mensagens juntamente: Mensagens de sessão](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Demonstra como utilizar Bus de serviço do Microsoft Azure e a funcionalidade de sessão de mensagens. O exemplo mostra simples remetentes e destinatários comunicar através de uma fila Bus de serviço.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: Tópico de resposta de pedido](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Demonstra como implementar o padrão de pedido/resposta com tópicos Bus de serviço do Microsoft Azure e subscrições. O exemplo mostra os clientes simples e servidores comunicar através de um tópico Bus de serviço.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: Fila de resposta do pedido](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Demonstra como utilizar Bus de serviço do Microsoft Azure e a funcionalidade de pedido/resposta. O exemplo mostra os clientes simples e servidores comunicar através de duas filas Bus de serviço.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: Deteção de duplicados](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Demonstra como utilizar a deteção de mensagem duplicado do Microsoft Azure Service Bus com filas. Cria duas filas, uma com a deteção de duplicados ativada e outros uma sem deteção de duplicados.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Juntamente mensagens: Mensagens de assíncrona](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Demonstra como utilizar o Microsoft Azure Service Bus para enviar e receber mensagens modo assíncrono a partir de uma fila. Fila de espera fornece desacoplada, assíncrona comunicação entre um remetente e qualquer número de destinatários (neste caso uma única recetor).|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Mensagens juntamente: Filtros avançados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Demonstra como utilizar o Microsoft Azure Service Bus publicar/subscrever filtros avançados. Cria um tópico e 3 subscrições com definições de filtro diferentes, envia mensagens para o tópico e recebe todas as mensagens de subscrições.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Mensagens juntamente: Obtenção prévia de mensagens](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Demonstra como utilizar a funcionalidade de obtenção prévia de mensagens do Microsoft Azure Service Bus. -Demonstra como utilizar a funcionalidade de obtenção prévia mensagens relativamente a receber.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|

## <a name="service-bus-reference-tools"></a>Ferramentas de referência Bus de serviço

Os exemplos seguintes demonstram várias outras funcionalidades do serviço.

|Nome de exemplo|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Serviço Bus Explorer](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|O Explorador de Bus serviço permite aos utilizadores ligar para um espaço de nomes de serviço do serviço Bus e gerir mensagens entidades de uma forma fácil. A ferramenta fornece funcionalidades avançadas como a funcionalidade de importar/exportar e, a capacidade de testar entidades de mensagens e serviços de reencaminhamento.|1.8|Bus de serviço do Microsoft Azure; Bus de serviço para o Windows Server|
|[Autorização: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Este exemplo demonstra como criar e gerir identidades de serviço no Microsoft Azure Active Directory controlo de acesso (também conhecido como serviço de controlo de acesso ou ACS) para utilização com o serviço Bus.|N/D|Bus de serviço do Microsoft Azure|

## <a name="next-steps"></a>Próximos passos

Consulte os tópicos seguintes para obter descrições gerais conceptuais do serviço Bus.

- [Descrição geral do serviço Bus messaging](service-bus-messaging-overview.md)
- [Arquitetura de Bus de serviço](service-bus-architecture.md)
- [Conceitos básicos da Bus de serviço](service-bus-fundamentals-hybrid-solutions.md)
