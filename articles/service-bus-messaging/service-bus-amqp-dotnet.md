<properties 
    pageTitle="Serviço Bus com .NET e AMQP 1.0 | Microsoft Azure"
    description="Utilizar o serviço Bus a partir do .NET com AMQP"
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>Utilizar o serviço Bus a partir do .NET com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Transferir o serviço Bus SDK

Suporte de AMQP 1.0 está disponível no serviço Bus SDK versão 2.1 ou posterior. Pode Certifique-se de que tem a versão mais recente, transferindo os bits Bus de serviço a partir do [NuGet][].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurar aplicações de .NET para utilizar AMQP 1.0

Por predefinição, a biblioteca de cliente do serviço Bus .NET comunica com o serviço do serviço Bus utilizando um protocolo SOAP baseado dedicado. Utilizar AMQP 1.0 em vez da predefinição protocolo exige configuração explícita numa cadeia de ligação Bus de serviço, conforme descrito na secção seguinte. Diferente desta alteração, o código da aplicação permanece inalterado ao utilizar AMQP 1.0.

Na versão atual, existem algumas funcionalidades de API que não são suportadas quando utilizar AMQP. Estas funcionalidades não suportadas estão listadas, mais tarde, na secção de [funcionalidades não suportadas, restrições e diferenças comportamentais](#unsupported-features-restrictions-and-behavioral-differences). Algumas das definições de configuração avançadas também têm um significado diferente ao utilizar AMQP.

### <a name="configuration-using-appconfig"></a>Configuração utilizando App

É aconselhável utilizar o ficheiro de configuração da App para armazenar as definições de aplicações. Para as aplicações de serviço Bus, pode utilizar App para armazenar as definições para o valor de serviço Bus **ConnectionString** . Um exemplo de ficheiro App é da seguinte forma:

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

O valor da `Microsoft.ServiceBus.ConnectionString` definição está a cadeia de ligação de Bus do serviço é utilizada para configurar a ligação ao serviço Bus. É o formato da seguinte forma:

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Onde `[namespace]` e `SharedAccessKey` são obtidos a partir do [Azure portal][]. Para mais informações, consulte o artigo [Introdução ao serviço Bus filas][].

Ao utilizar AMQP, acrescentar a cadeia de ligação com `;TransportType=Amqp`. Este notation informa a biblioteca do cliente para efetuar a ligação ao serviço Bus utilizando AMQP 1.0.

## <a name="message-serialization"></a>Serialização de mensagem

Ao utilizar o protocolo predefinido, o comportamento de serialização predefinido da biblioteca de cliente do .NET é utilizar o tipo de [DataContractSerializer][] para serializar uma instância de [BrokeredMessage][] para transporte entre a biblioteca do cliente e o serviço do serviço Bus. Ao utilizar o modo de transporte AMQP, a biblioteca do cliente utiliza o sistema de tipo AMQP para serialização da [mensagem juntamente][BrokeredMessage] numa mensagem AMQP. Este serialização permite a mensagem ser recebido e interpretadas por uma aplicação de recepção potencialmente está a ser executado numa plataforma diferente, por exemplo, uma aplicação de Java que utiliza a API JMS para aceder ao serviço Bus.

Quando constrói uma instância de [BrokeredMessage][] , pode fornecer um objeto de .NET como um parâmetro para o construtor para servir como o corpo da mensagem. Para objetos que podem ser mapeados para tipos de primitivos AMQP, o corpo é serializado em tipos de dados AMQP. Se o objeto não pode ser mapeado diretamente para um tipo de primitivo AMQP; ou seja, um tipo de personalizado definido pela aplicação, em seguida, o objeto está serializado utilizando o [DataContractSerializer][]e os bytes série são enviadas numa mensagem de dados AMQP.

Para facilitar a interoperabilidade com os clientes de que não sejam .NET, utilize apenas os tipos de .NET que podem ser serializados diretamente para tipos AMQP para o corpo da mensagem. A tabela seguinte apresenta detalhes sobre esses tipos e o mapeamento correspondente ao sistema de tipo AMQP.

| Tipo de objeto do corpo .NET          | Tipo AMQP mapeadas                          | Tipo de secção AMQP corpo                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Booleano                           | Booleano                                   | Valor AMQP                                                                                                                                                |
| byte                           | ubyte                                     | Valor AMQP                                                                                                                                                |
| ushort                         | ushort                                    | Valor AMQP                                                                                                                                                |
| UInt                           | UInt                                      | Valor AMQP                                                                                                                                                |
| ulong                          | ulong                                     | Valor AMQP                                                                                                                                                |
| SByte                          | byte                                      | Valor AMQP                                                                                                                                                |
| breve                          | breve                                     | Valor AMQP                                                                                                                                                |
| Int                            | Int                                       | Valor AMQP                                                                                                                                                |
| longo                           | longo                                      | Valor AMQP                                                                                                                                                |
| flutuante                          | flutuante                                     | Valor AMQP                                                                                                                                                |
| dupla                         | dupla                                    | Valor AMQP                                                                                                                                                |
| decimal                        | decimal128                                | Valor AMQP                                                                                                                                                |
| CARÁCT                           | CARÁCT                                      | Valor AMQP                                                                                                                                                |
| Data/hora                       | data/hora                                 | Valor AMQP                                                                                                                                                |
| GUID                           | UUID                                      | Valor AMQP                                                                                                                                                |
| byte]                         | em binário                                    | Valor AMQP                                                                                                                                                |
| cadeia                         | cadeia                                    | Valor AMQP                                                                                                                                                |
| System.Collections.IList       | lista                                      | Valor AMQP: itens contidos na coleção de só podem ser aquelas que são definidos nesta tabela.                                                             |
| System. Array                   | matriz                                     | Valor AMQP: itens contidos na coleção de só podem ser aquelas que são definidos nesta tabela.                                                             |
| System.Collections.IDictionary | mapa                                       | Valor AMQP: itens contidos na coleção de só podem ser aquelas que são definidos nesta tabela. Nota: apenas as chaves de cadeia são suportadas.                        |
| URI                            | Descrito cadeia (consulte a tabela seguinte) | Valor AMQP                                                                                                                                                |
| DateTimeOffset                 | Descrito tempo (consulte a tabela seguinte)   | Valor AMQP                                                                                                                                                |
| TimeSpan                       | Descrito tempo (consulte o seguinte)         | Valor AMQP                                                                                                                                                |
| Sequência                         | em binário                                    | Dados de AMQP (pode estar vários). As secções de dados contenham os bytes observou ler a partir de um objecto de sequência.                                                           |
| Outro objeto                   | em binário                                    | Dados de AMQP (pode estar vários). Contém o ficheiro binário série do objeto que utiliza o DataContractSerializer ou um serializador fornecido pela aplicação. |

| Tipo de .NET      | AMQP mapeada descrito tipo                                                                                              | Notas                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Funcionalidades não suportadas, restrições e comportamentais diferenças

As seguintes funcionalidades da API do serviço Bus .NET não são atualmente suportadas quando utilizar AMQP:

-   Transações

-   Enviar por destino de transferência

Também existem algumas diferenças pequenas no comportamento da API do serviço Bus .NET ao utilizar AMQP, comparado com o protocolo predefinido:

-   A propriedade [OperationTimeout][] é ignorada.

-   `MessageReceiver.Receive(TimeSpan.Zero)`é implementada como `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

-   Concluir mensagens por tokens de bloqueio só pode ser feita pelos destinatários da mensagem que recebeu inicialmente as mensagens.

## <a name="controlling-amqp-protocol-settings"></a>Controlar as definições do protocolo AMQP

API do .NET expor várias definições para controlar o comportamento do protocolo AMQP:

-   **MessageReceiver.PrefetchCount**: controla o crédito inicial aplicado a uma ligação. A predefinição é 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: controlos o tamanho máximo da moldura AMQP oferecido durante a negociação ao estabelecer a ligação abra hora. A predefinição é 65.536 bytes.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: se as transferências são batchable, este valor determina o atraso máximo para enviar disposições. Herdado por remetentes/destinatários por predefinição. Remetente/recetor individual pode substituir o valor predefinido, que é 20 milissegundos.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: controla se são estabelecer ligações de AMQP através de uma ligação SSL. A predefinição é **Verdadeiro**.

## <a name="next-steps"></a>Próximos passos

Está pronto para saber mais? Visite as ligações seguintes:

- [Descrição geral do serviço Bus AMQP]
- [Suporte de AMQP 1.0 para serviço Bus divididos filas e tópicos]
- [AMQP no serviço Bus para o Windows Server]

  [Introdução ao filas Bus de serviço]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Portal do Azure]: https://portal.azure.com
[Descrição geral do serviço Bus AMQP]: service-bus-amqp-overview.md
[Suporte de AMQP 1.0 para serviço Bus divididos filas e tópicos]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP no serviço Bus para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
