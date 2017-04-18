<properties 
    pageTitle="Descrição geral de amostras de reencaminhamento de serviço Bus | Microsoft Azure"
    description="Categoriza e descreve amostras de reencaminhamento de serviço Bus com ligações para cada um."
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

# <a name="service-bus-relay-samples"></a>Exemplos de reencaminhamento de Bus de serviço

Os exemplos de reencaminhamento de serviço Bus demonstram das principais funcionalidades do [serviço Bus reencaminhamento](https://azure.microsoft.com/services/service-bus/). Este artigo categoriza e descreve as amostras disponíveis, com ligações para cada um.

>[AZURE.NOTE] Exemplos de serviço Bus não são instalados com o SDK. Para obter as amostras, visite a [página de amostras Azure SDK](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
>
>Para além disso, existe um conjunto actualizado de amostras de reencaminhamento de serviço Bus [aqui](https://github.com/Azure-Samples/azure-servicebus-relay-samples) (partir este escrita, estes não são descritos neste artigo).  

Para obter exemplos de mensagens, consulte o artigo [Bus de serviço de mensagens amostras](../service-bus-messaging/service-bus-samples.md).

## <a name="service-bus-relay"></a>Reencaminhamento de Bus de serviço

Os exemplos seguintes ilustram como escrever as aplicações que utilizam o serviço de reencaminhamento de Bus de serviço.

Note que os exemplos de reencaminhamento requerem uma cadeia de ligação para aceder ao seu espaço de nomes de serviço Bus.

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

## <a name="service-bus-relay"></a>Reencaminhamento de Bus de serviço

Exemplos que demonstram o reencaminhamento de Bus de serviço.

### <a name="getting-started"></a>Introdução

|Nome de exemplo|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Reencaminhamento de mensagens: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Demonstra como executar um cliente de serviço Bus e o serviço no Azure. Este exemplo configura Bus de serviço através de programação. Informação de ambiente e segurança apenas é armazenada nos ficheiros de configuração.|1.8|Bus de serviço do Microsoft Azure|
|[Autenticação mensagens transmitida: Segredo partilhado](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Demonstra como utilizar um nome de emissor e secreta emissor para autenticar com Bus de serviço.|1.8|Bus de serviço do Microsoft Azure|
|[Reencaminhamento de mensagens de autenticação: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Demonstra como para expor um serviço HTTP não requer autenticação de utilizador do cliente.|1.8|Bus de serviço do Microsoft Azure|
|[Reencaminhado enlaces mensagens: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Demonstra como utilizar o enlace **WebHttpRelayBinding** para devolver dados binários utilizando a modelo de programação na Web.|1.8|Bus de serviço do Microsoft Azure|
|[Enlaces mensagens transmitidos: NetTcp reencaminhada](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Demonstra como utilizar o enlace **NetTcpRelayBinding** .|1.8|Bus de serviço do Microsoft Azure|

### <a name="exploring-features"></a>Explorar funcionalidades

Exemplos que demonstram várias funcionalidades de reencaminhamento de Bus de serviço.

|Nome de exemplo|Descrição|Versão mínima SDK|Disponibilidade|
|---|---|---|---|
|[Transmitida autenticação de mensagens: WebToken Simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Demonstra como utilizar uma credencial token web simples para autenticar com Bus de serviço. O exemplo é semelhante ao exemplo eco, com algumas alterações. Especificamente, este exemplo adiciona um comportamento nas aplicações de ChannelFactory (cliente) e ServiceHost (serviço).|1.8|Bus de serviço do Microsoft Azure|
|[Transmitido mensagens: Balanceamento de carga](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Demonstra como utilizar o Microsoft Azure Service Bus para encaminhar mensagens para vários destinatários. Que apresenta várias instâncias de um serviço simple comunicar com um cliente através do enlace **NetTcpRelayBinding**|1.8|Bus de serviço do Microsoft Azure|
|[Transmitidos enlaces mensagens: Evento líquido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Demonstra utilizando o enlace de **NetEventRelayBinding** no Microsoft Azure Service Bus.|1.8|Bus de serviço do Microsoft Azure|
|[Transmitidos enlaces mensagens: Sessões de WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Demonstra a utilizar o enlace **WS2007HttpRelayBinding** com sessões fiáveis ativados. Também mostra como especificar as credenciais de serviço Bus no ficheiro de configuração, em vez de através de programação.|1.8|Bus de serviço do Microsoft Azure|
|[Transmitidos enlaces mensagens: MsgSecCertificate de WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Demonstra como utilizar o enlace **WS2007HttpRelayBinding** com segurança da mensagem para proteger mensagens de ponto a ponto enquanto ainda que exige o clientes autenticar com Bus de serviço.|1.8|Bus de serviço do Microsoft Azure|
|[Mensagens transmitido: Troca de metadados](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Demonstra como para expor um ponto final de metadados que utiliza o enlace reencaminhamento. **MetadataExchange** é suportada nas seguintes ligações de reencaminhamento: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding**e **WS2007HttpRelayBinding**.|1.8|Bus de serviço do Microsoft Azure|
|[Enlaces mensagens transmitidos: NetTcp diretos](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Demonstra como configurar o enlace **NetTcpRelayBinding** para suportar o modo de ligação **híbrido** que pela primeira vez estabelece uma ligação transmitida e, se possível, muda automaticamente para uma ligação direta entre um cliente e um serviço.|1.8|Bus de serviço do Microsoft Azure|
|[Enlaces mensagens transmitidos: o nome de utilizador NetTcp MsgSec](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Demonstra como utilizar o enlace **NetTcpRelayBinding** com segurança da mensagem.|1.8|Bus de serviço do Microsoft Azure|
|[Transmitidos enlaces mensagens: Só líquida ida](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Demonstra como expor e consuma um ponto final de serviço utilizando o enlace **NetOnewayRelayBinding** .|1.8|Bus de serviço do Microsoft Azure|
|[Enlaces mensagens transmitidos: WS2007Http simples](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Demonstra utilizando o enlace **WS2007HttpRelayBinding** . Demonstra a um serviço simple que utiliza sem opções de segurança e não requer autenticar os clientes.|1.8|Bus de serviço do Microsoft Azure|

## <a name="next-steps"></a>Próximos passos

Consulte os tópicos seguintes para obter descrições gerais conceptuais do serviço Bus.

- [Descrição geral do reencaminhamento de Bus de serviço](service-bus-relay-overview.md)
- [Arquitetura de Bus de serviço](../service-bus-messaging/service-bus-architecture.md)
- [Conceitos básicos da Bus de serviço](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)