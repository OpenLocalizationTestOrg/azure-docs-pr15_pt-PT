<properties 
    pageTitle="Serviço Bus e Python com AMQP 1.0 | Microsoft Azure"
    description="Utilizar o serviço Bus a partir do Python com AMQP."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-python-with-amqp-10"></a>Utilizar o serviço Bus a partir do Python com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Protão Python é uma associação de idioma Python para protão-C; Isto é, protão Python é implementada como um dispositivo de moldagem à volta de um motor implementado C.

## <a name="download-the-proton-client-library"></a>Transferir a biblioteca do cliente protão

Pode transferir protão-C e respectivos enlaces associados (incluindo Python) do [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). A transferência estiver no formulário de código de origem. Para criar o código, siga as instruções contidas dentro do pacote transferido.

Tenha em atenção que no momento deste escrita, o suporte SSL no protão-C só está disponível para sistemas operativos Linux. Porque Azure Service Bus necessita da utilização de SSL, C protão (e as ligações de idioma) apenas podem utilizadas para aceder ao serviço Bus a partir do Linux neste momento. Trabalho para ativar protão-C com SSL no Windows está verificação decorrer por isso, com frequência para obter atualizações.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Trabalhar com filas, tópicos e as subscrições do Python Bus de serviço

O código seguinte mostra como enviar e receber mensagens de um serviço Bus mensagens entidade.

### <a name="send-messages-using-proton-python"></a>Enviar mensagens utilizando protão Python

O código seguinte mostra como enviar uma mensagem para um serviço Bus mensagens entidade.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Receber mensagens de correio protão Python

O código seguinte mostra como receber uma mensagem a partir de um serviço Bus mensagens entidade.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Mensagens entre .NET e protão Python

### <a name="application-properties"></a>Propriedades da aplicação

#### <a name="proton-python-to-service-bus-net-apis"></a>Protão-Python para servir Bus .NET APIs

Propriedades da aplicação dos seguintes tipos de suporte de mensagens protão Python: **int**, **longo**, **flutuante**, **uuid**, **Booleano**, **cadeia**. O código de Python seguinte mostra como definir as propriedades de uma mensagem ao utilizar cada um destes tipos de propriedade.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

No serviço Bus .NET API, as propriedades da aplicação são realizadas na coleção **Propriedades** do [BrokeredMessage][]. O código seguinte mostra como ler as propriedades da aplicação de uma mensagem recebida a partir de um cliente Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

A tabela seguinte mapas os tipos de propriedade Python para os tipos de propriedade .NET.

| Tipo de propriedade Python | Tipo de propriedade .NET |
|----------------------|--------------------|
| Int                  | Int                |
| flutuante                | dupla             |
| longo                 | Int64              |
| UUID                 | GUID               |
| Booleano                 | Booleano               |
| cadeia               | cadeia             |

#### <a name="service-bus-net-apis-to-proton-python"></a>Serviço Bus .NET APIs para protão Python

O tipo de [BrokeredMessage][] suporta dos seguintes tipos de propriedades da aplicação: **byte**, **sbyte**, **char**, **curta**, **ushort**, **int**, **uint**, **longo**, **ulong**, **flutuante**, **duplo**, **decimal**, **Booleano**, **Guid**, **cadeia**, **Uri**, **DateTime**, **DateTimeOffset**e **TimeSpan**. O código de .NET seguinte mostra como definir as propriedades num objeto [BrokeredMessage][] utilizar cada uma das seguintes tipos de propriedade.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

O código de Python seguinte mostra como ler as propriedades da aplicação de uma mensagem recebida a partir de um cliente de serviço Bus .NET.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

A tabela seguinte mapas os tipos de propriedade .NET para os tipos de propriedade Python.

| Tipo de propriedade .NET | Tipo de propriedade Python | Notas                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | Int                  | -                                                                                                                                                                     |
| SByte              | Int                  | -                                                                                                                                                                     |
| CARÁCT               | CARÁCT                 | Classe protão Python                                                                                                                                                 |
| breve              | Int                  | -                                                                                                                                                                     |
| ushort             | Int                  | -                                                                                                                                                                     |
| Int                | Int                  | -                                                                                                                                                                     |
| UInt               | Int                  | -                                                                                                                                                                     |
| longo               | Int                  | -                                                                                                                                                                     |
| ulong              | longo                 | Classe protão Python                                                                                                                                                 |
| flutuante              | flutuante                | -                                                                                                                                                                     |
| dupla             | flutuante                | -                                                                                                                                                                     |
| decimal            | Cadeia               | Atualmente, não é suportada decimal com protão.                                                                                                                     |
| Booleano               | Booleano                 | -                                                                                                                                                                     |
| GUID               | UUID                 | Classe protão Python                                                                                                                                                 |
| cadeia             | cadeia               | -                                                                                                                                                                     |
| Data/hora           | data/hora            | Classe protão Python                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | DateTimeOffset.UtcTicks mapeado para o tipo de AMQP:<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType        | Timespan.Ticks mapeado para o tipo de AMQP:<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType        | Uri.AbsoluteUri mapeado para o tipo de AMQP:<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Propriedades padrão

As tabelas seguintes mostram o mapeamento entre as propriedades da mensagem padrão protão Python e as propriedades da mensagem padrão [BrokeredMessage][] .

#### <a name="proton-python-to-service-bus-net-apis"></a>Protão-Python para servir Bus .NET APIs

| Protão Python        | Serviço Bus .NET         | Notas                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| resistentes              | n/d                      | Serviço Bus suporta apenas resistentes mensagens.               |
| prioridade             | n/d                      | Serviço Bus suporta apenas uma única mensagem prioridade.      |
| TTL                  | Message.TimeToLive       | Conversão, protão Python TTL é definido em milissegundos. |
| primeira\_Adquirente      | n/d                      | -                                                           |
| entrega\_contagem      | n/d                      | -                                                           |
| ID                   | Message.MessageID        | -                                                           |
| utilizador\_id             | n/d                      | -                                                           |
| endereço              | Message.To               | -                                                           |
| assunto              | Message.Label            | -                                                           |
| resposta\_para            | Message.ReplyTo          | -                                                           |
| correlação\_id      | Message.CorrelationID    | -                                                           |
| conteúdo\_tipo        | Message.ContentType      | -                                                           |
| conteúdo\_codificação    | n/d                      | -                                                           |
| termo\_tempo         | n/d                      | -                                                           |
| criação\_tempo       | n/d                      | -                                                           |
| grupo\_id            | Message.SessionId        | -                                                           |
| grupo\_sequência      | n/d                      | -                                                           |
| resposta\_para\_grupo\_id | Message.ReplyToSessionId | -                                                           |
| Formatar               | n/d                      | -                                                           |

| Serviço Bus .NET        | Protão                       | Notas                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.Content\_tipo        | -                                                           |
| CorrelationId           | Message.Correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | n/d                          | -                                                           |
| Etiqueta                   | Message.Subject              | -                                                           |
| MessageId               | Message.ID                   | -                                                           |
| ReplyTo                 | Message.Reply\_para            | -                                                           |
| ReplyToSessionId        | Message.Reply\_para\_grupo\_id | -                                                           |
| ScheduledEnqueueTimeUtc | n/d                          | -                                                           |
| ID de sessão               | Message.Group\_id            | -                                                           |
| TimeToLive              | Message.TTL                  | Conversão, protão Python TTL é definido em milissegundos. |
| Para                      | Message.address              | -                                                           |

## <a name="next-steps"></a>Próximos passos

Está pronto para saber mais? Visite as ligações seguintes:

- [Descrição geral do serviço Bus AMQP]
- [AMQP no serviço Bus para o Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP no serviço Bus para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Descrição geral do serviço Bus AMQP]: service-bus-amqp-overview.md
