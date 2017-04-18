<properties 
    pageTitle="Serviço Bus e PHP com AMQP 1.0 | Microsoft Azure"
    description="Utilizar o serviço Bus a partir do PHP com AMQP."
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

# <a name="using-service-bus-from-php-with-amqp-10"></a>Utilizar o serviço Bus a partir do PHP com AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Protão PHP é uma associação de idioma PHP para protão-C; Isto é, protão PHP é implementada como um dispositivo de moldagem à volta de um motor implementado C.

## <a name="downloading-the-proton-client-library"></a>Transferência da biblioteca de cliente protão

Pode transferir protão-C e respectivos enlaces associados (incluindo PHP) do [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). A transferência estiver no formulário de código de origem. Para criar o código, siga as instruções contidas na embalagem transferida.

> [AZURE.IMPORTANT] No momento deste escrita, o suporte SSL no protão-C só está disponível para sistemas operativos Linux. Porque Azure Service Bus necessita da utilização de SSL, C protão (e as ligações de idioma) apenas podem utilizadas para aceder ao serviço Bus a partir do Linux neste momento. Trabalho para ativar protão-C com SSL no Windows esteja a decorrer, por isso, verificação com frequência para obter atualizações.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>Trabalhar com filas, tópicos e as subscrições do PHP Bus de serviço

O código seguinte mostra como enviar e receber mensagens de um serviço Bus mensagens entidade.

### <a name="sending-messages-using-proton-php"></a>Enviar mensagens utilizando protão PHP

O código seguinte mostra como enviar uma mensagem para um serviço Bus mensagens entidade.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Receber mensagens utilizando protão PHP

O código seguinte mostra como receber uma mensagem a partir de um serviço Bus mensagens entidade.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>Mensagens entre .NET e protão PHP

### <a name="application-properties"></a>Propriedades da aplicação

#### <a name="protonphp-to-service-bus-net-apis"></a>ProtonPHP para servir Bus .NET APIs

Propriedades da aplicação dos seguintes tipos de suporte de mensagens protão PHP: **número inteiro**, **duplo**, **Booleano**, **cadeia**e **objeto**. O código PHP seguinte mostra como definir as propriedades de uma mensagem ao utilizar cada um destes tipos de propriedade.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

No API do serviço Bus .NET, as propriedades da aplicação são realizadas na coleção **Propriedades** do [BrokeredMessage][]. O código seguinte mostra como ler as propriedades da aplicação de uma mensagem recebida a partir de um cliente PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

A tabela seguinte mapas os tipos de propriedade PHP para os tipos de propriedade .NET.

| Tipo de propriedade PHP | Tipo de propriedade .NET |
|-------------------|--------------------|
| número inteiro           | Int                |
| dupla            | dupla             |
| Booleano           | Booleano               |
| cadeia            | cadeia             |
| objecto            | Objecto             |

#### <a name="service-bus-net-apis-to-php"></a>Serviço Bus .NET APIs para PHP

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
```

O código PHP seguinte mostra como ler as propriedades da aplicação de uma mensagem recebida a partir de um cliente de serviço Bus .NET.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

A tabela seguinte mapas os tipos de propriedade .NET para os tipos de propriedade PHP.

| Tipo de propriedade .NET | Tipo de propriedade PHP | Notas                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte               | número inteiro           | -                                                                                                                                                                     |
| SByte              | número inteiro           | -                                                                                                                                                                     |
| CARÁCT               | CARÁCT              | Classe protão PHP                                                                                                                                                    |
| breve              | número inteiro           | -                                                                                                                                                                     |
| ushort             | número inteiro           | -                                                                                                                                                                     |
| Int                | número inteiro           | -                                                                                                                                                                     |
| UInt               | Número inteiro           | -                                                                                                                                                                     |
| longo               | número inteiro           | -                                                                                                                                                                     |
| ulong              | número inteiro           | -                                                                                                                                                                     |
| flutuante              | dupla            | -                                                                                                                                                                     |
| dupla             | dupla            | -                                                                                                                                                                     |
| decimal            | cadeia            | Atualmente, não é suportada decimal com protão.                                                                                                                     |
| Booleano               | Booleano           | -                                                                                                                                                                     |
| GUID               | UUID              | Classe protão PHP                                                                                                                                                    |
| cadeia             | cadeia            | -                                                                                                                                                                     |
| Data/hora           | número inteiro           | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks mapeado para o tipo de AMQP:<type name="datetime-offset" class=restricted source="long"><descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan           | DescribedType     | Timespan.Ticks mapeado para o tipo de AMQP:<type name="timespan" class=restricted source="long"><descriptor name="com.microsoft:timespan" /></type>                        |
| URI                | DescribedType     | Uri.AbsoluteUri mapeado para o tipo de AMQP:<type name="uri" class=restricted source="string"><descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>Propriedades padrão

As tabelas seguintes mostram o mapeamento entre as propriedades da mensagem padrão protão PHP e as propriedades da mensagem padrão [BrokeredMessage][] .

| Protão PHP           | Serviço Bus .NET         | Notas                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| Resistentes              | n/d                      | Serviço Bus suporta apenas resistentes mensagens.          |
| Prioridade             | n/d                      | Serviço Bus suporta apenas uma única mensagem prioridade. |
| TTL                  | Message.TimeToLive       | Conversão, protão PHP TTL é definido em milissegundos.   |
| primeira\_Adquirente      | -                          | -                                                          |
| entrega\_contagem      | -                          | -                                                          |
| ID                   | Message.Id               | -                                                          |
| utilizador\_id             | -                          | -                                                          |
| Endereço              | Message.To               | -                                                          |
| Assunto              | Message.Label            | -                                                          |
| resposta\_para            | Message.ReplyTo          | -                                                          |
| correlação\_id      | Message.CorrelationId    | -                                                          |
| conteúdo\_tipo        | Message.ContentType      | -                                                          |
| conteúdo\_codificação    | n/d                      | -                                                          |
| termo\_tempo         | Message.ExpiresAtUTC     | -                                                          |
| criação\_tempo       | n/d                      | -                                                          |
| grupo\_id            | Message.SessionId        | -                                                          |
| grupo\_sequência      | -                          | -                                                          |
| resposta\_para\_grupo\_id | Message.ReplyToSessionId | -                                                          |
| Formatar               | n/d                      | -

#### <a name="service-bus-net-apis-to-proton-php"></a>Serviço Bus .NET APIs para protão PHP

| Serviço Bus .NET        | Protão PHP                                             | Notas                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | Mensagem -\>conteúdo\_tipo                                | -                                                        |
| CorrelationId           | Mensagem -\>correlação\_id                              | -                                                        |
| EnqueuedTimeUtc         | Mensagem -\>anotações [x-optar ativamente por participar-colocado em fila-time]             | -                                                        |
| Etiqueta                   | Mensagem -\>assunto                                      | -                                                        |
| MessageId               | Mensagem -\>id                                           | -                                                        |
| ReplyTo                 | Mensagem -\>resposta\_para                                    | -                                                        |
| ReplyToSessionId        | Mensagem -\>resposta\_para\_grupo\_id                         | -                                                        |
| ScheduledEnqueueTimeUtc | Mensagem -\>anotações ["x-optar ativamente por participar-agendada-colocar-hora"] | -                                                        |
| ID de sessão               | Mensagem -\>grupo\_id                                    | -                                                        |
| TimeToLive              | Mensagem -\>ttl                                          | Conversão, protão PHP TTL é definido em milissegundos. |
| Para                      | Mensagem -\>endereço                                      | -                                                        |

## <a name="next-steps"></a>Próximos passos

Está pronto para saber mais? Visite as ligações seguintes:

- [Descrição geral do serviço Bus AMQP]
- [AMQP no serviço Bus para o Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP no serviço Bus para o Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Descrição geral do serviço Bus AMQP]: service-bus-amqp-overview.md
