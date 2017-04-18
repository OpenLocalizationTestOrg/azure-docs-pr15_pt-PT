<properties 
    pageTitle="Como utilizar AMQP 1.0 com a API do .NET serviço Bus | Microsoft Azure" 
    description="Saiba como utilizar avançadas mensagem colocação Protodol (AMQP) 1.0 com a API do Azure .NET serviço Bus." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>Como utilizar AMQP 1.0 com a API do serviço Bus .NET

O avançadas mensagem colocação Protocol (AMQP) 1.0 é um eficaz, fiável fio nível mensagens protocolo que pode utilizar para criar robusta, em diferentes plataformas, aplicações de mensagens.

Suporte para AMQP 1.0 no serviço Bus significa que pode utilizar a colocação em fila e publicar/subscrever juntamente funcionalidades mensagens a partir de um intervalo de plataformas de utilizando um protocolo binário eficiente. Além disso, pode criar aplicações composto por componentes construídas utilizando uma mistura de idiomas, quadros e sistemas operativos.

Este artigo explica como utilizar as funcionalidades de mensagens da Bus serviço controlada (filas e publicar/subscrever tópicos) da partir de aplicações .NET utilizando a API do serviço Bus .NET. Existe um [artigo acompanha](service-bus-java-how-to-use-jms-api-amqp.md) que explica como fazer a mesma utilizando a API do serviço de mensagem Java (JMS) padrão. Pode utilizar estas duas guias em conjunto para saber mais acerca das mensagens em diferentes plataformas utilizando AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introdução ao serviço Bus

Este artigo assume que já tem um espaço de nomes de serviço Bus que contém uma fila com o nome "Fila1." Se não o fizer, em seguida, pode criar espaço de nomes e fila utilizando o [Azure portal][]. Para mais informações sobre como criar espaços de nomes de serviço Bus e filas, consulte o artigo [Introdução ao serviço Bus filas](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Transferir o serviço Bus SDK

Suporte de AMQP 1.0 está disponível no serviço Bus SDK versão 2.1 ou posterior. Pode transferir os bits mais recentes do NuGet na [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Aplicações do .NET código

Por predefinição, a biblioteca de cliente do serviço Bus .NET comunica com o serviço do serviço Bus utilizando um protocolo SOAP baseado dedicado. Utilizar AMQP 1.0 em vez da predefinição protocolo exige configuração explícita numa cadeia de ligação de serviço Bus conforme descrito na secção seguinte. Diferente desta alteração, o código da aplicação permanece inalterado que mostra basicamente ao utilizar AMQP 1.0.

Na versão atual, existem algumas funcionalidades de API que não são suportadas quando utilizar AMQP. Estas funcionalidades não suportadas estão listadas, mais tarde, na secção [restrições e funcionalidades não suportadas](#unsupported-features-and-restrictions). Algumas das definições de configuração avançadas também têm um significado diferente ao utilizar AMQP. Nenhuma destas definições são utilizadas neste artigo, mas estão disponíveis na [Descrição geral do serviço Bus AMQP](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)mais detalhes.

### <a name="configure-via-appconfig"></a>Configurar através da App

É uma prática recomendada que as aplicações utilizar o ficheiro de configuração da App para armazenar as definições. Para as aplicações de serviço Bus, pode utilizar App para armazenar serviço Bus **ConnectionString**. Esta aplicação exemplo também utiliza App para armazenar o nome do serviço Bus mensagens entidade que utiliza.

Um ficheiro de App exemplo é mostrado aqui:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Configurar a cadeia de ligação Bus de serviço

O valor da definição **Microsoft.ServiceBus.ConnectionString** é a cadeia de ligação de Bus do serviço é utilizada para configurar a ligação ao serviço Bus. É o formato da seguinte forma:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Onde `[namespace]` e `[SAS key]` são obtidos a partir do [Azure portal][]. Para obter mais informações, consulte o artigo [como utilizar filas Bus de serviço] [].

Quando utilizar AMQP, a cadeia de ligação é acrescentada com `;TransportType=Amqp`, que lhe indica a biblioteca do cliente para efetuar a ligação ao serviço Bus utilizando AMQP 1.0.

### <a name="configure-the-entity-name"></a>Configurar o nome da entidade

Esta aplicação exemplo utiliza a `EntityName` na secção **appSettings** do ficheiro App a definição para configurar o nome da fila com o qual a aplicação de intercâmbio de mensagens.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Uma aplicação do .NET simple utilizando uma fila Bus de serviço

O exemplo seguinte efetua e recebe mensagens e para uma fila Bus de serviço.

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Executar a aplicação

Executar a aplicação produz uma saída do formulário:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Em diferentes plataformas de mensagens entre JMS e .NET

Este tópico mostrava como enviar mensagens para Bus serviço utilizando o .NET e também como receber essas mensagens utilizando o .NET. No entanto, uma das principais vantagens do AMQP 1.0 é permite que as aplicações criadas a partir de componentes de escritos em idiomas diferentes, com as mensagens trocadas sujeito e em fidelidade completa.

Utilizar a aplicação de .NET exemplo descrito acima e uma aplicação Java semelhante tirado de um guia suplementar, [como utilizar o Java mensagem serviço (JMS) API com o serviço Bus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), é possível para mensagens entre .NET e Java do exchange. 

Para mais informações sobre os detalhes de mensagens com o serviço Bus e AMQP 1.0 em diferentes plataformas, consulte o artigo [Descrição geral do serviço Bus AMQP 1.0](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>JMS para .NET

Para demonstrar JMS ao .NET messaging:

* Inicie a aplicação de exemplo .NET sem quaisquer argumentos da linha de comandos.
* Inicie a aplicação de exemplo Java com o argumento da linha de comandos "sendonly". Neste modo, a aplicação não irá receber mensagens de fila de espera, irão apenas enviar.
* Prima **Enter** algumas vezes na consola de aplicação de Java, provocará mensagens sejam enviadas.
* Estas mensagens são recebidas pela aplicação .NET.

### <a name="output-from-jms-application"></a>Saída a partir da aplicação JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Saída a partir da aplicação .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET para JMS

Para demonstrar .NET para JMS mensagens:

* Inicie a aplicação de exemplo .NET com o argumento de linha de comandos "sendonly". Neste modo, a aplicação não irá receber mensagens de fila de espera, irão apenas enviar.
* Inicie a aplicação de exemplo Java sem quaisquer argumentos de linha de comandos.
* Prima **Enter** algumas vezes consola de aplicação do .NET, que irão provocar mensagens sejam enviadas.
* Estas mensagens são recebidas por aplicação Java.

#### <a name="output-from-net-application"></a>Saída a partir da aplicação .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Saída a partir da aplicação JMS

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Restrições e funcionalidades não suportadas

As seguintes funcionalidades do serviço .NET Bus API não são atualmente suportadas quando utilizar AMQP:

 * Transações
 * Enviar por destino de transferência

Para mais informações, consulte o artigo [funcionalidades não suportadas, restrições e diferenças comportamentais](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Resumo

Este artigo mostrava como aceder às funcionalidades de mensagens do serviço Bus controlada (filas e publicar/subscrever tópicos) a partir do .NET utilizando AMQP 1.0 e a API do serviço Bus .NET.

Também pode utilizar o serviço Bus AMQP 1.0 de outros idiomas incluindo Java, C, Python e PHP. Componentes construídas utilizando os seguintes idiomas podem trocar mensagens de forma fiável e na fidelidade completa utilizar AMQP 1.0 no serviço Bus. Para mais informações, consulte o artigo [Descrição geral do serviço Bus AMQP](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Próximos passos

Agora que já ler uma descrição geral do serviço Bus e AMQP com .NET, consulte as ligações seguintes para obter mais informações.

* [Suporte de AMQP 1.0 no Azure Service Bus](service-bus-amqp-overview.md)
* [Como utilizar o Java mensagem serviço (JMS) API com o serviço Bus & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Como utilizar filas Bus de serviço](service-bus-dotnet-get-started-with-queues.md)
 
[Portal do Azure]: https://portal.azure.com
