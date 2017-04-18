<properties 
    pageTitle="Como utilizar AMQP 1.0 com a API do Java serviço Bus | Microsoft Azure" 
    description="Como utilizar o serviço de mensagem Java (JMS) com o Azure Service Bus e avançadas mensagem colocação Protodol (AMQP) 1.0." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Como utilizar o Java mensagem serviço (JMS) API com Bus de serviço e AMQP 1.0

O avançadas mensagem colocação Protocol (AMQP) 1.0 é um eficaz, fiável fio nível mensagens protocolo que pode utilizar para criar aplicações de mensagens robustas e em diferentes plataformas.

Suporte para AMQP 1.0 no serviço Bus significa que pode utilizar a colocação em fila e publicar/subscrever juntamente funcionalidades mensagens a partir de um intervalo de plataformas de utilizando um protocolo binário eficiente. Além disso, pode criar aplicações composto por componentes construídas utilizando uma mistura de idiomas, quadros e sistemas operativos.

Este artigo explica como utilizar o serviço Bus mensagens funcionalidades (filas e publicar/subscrever tópicos) a partir de aplicações Java utilizando o populares Java mensagem serviço (JMS) API padrão. Existe um [artigo acompanha](service-bus-dotnet-advanced-message-queuing.md) que explica como pode fazer o mesmo com a API do serviço Bus .NET. Pode utilizar estas duas guias em conjunto para saber mais acerca das mensagens em diferentes plataformas utilizando AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Introdução ao serviço Bus

Este guia assume que já tem um espaço de nomes de serviço Bus que contém uma fila denominada **Fila1**. Se não o fizer, em seguida, pode [criar espaço de nomes e fila](service-bus-create-namespace-portal.md) utilizando o [Azure portal](https://portal.azure.com). Para mais informações sobre como criar espaços de nomes de serviço Bus e filas, consulte o artigo [como utilizar o serviço Bus filas](service-bus-dotnet-get-started-with-queues.md).

> [AZURE.NOTE] Filas com partições e tópicos também suportam AMQP. Para mais informações, consulte o artigo [Partitioned mensagens entidades](service-bus-partitioning.md) e [AMQP 1.0 suporte para o serviço Bus divididos filas e tópicos](service-bus-partitioned-queues-and-topics-amqp-overview.md).

## <a name="downloading-the-amqp-10-jms-client-library"></a>Transferência da biblioteca de cliente AMQP 1.0 JMS

Para obter informações sobre onde transferir a versão mais recente da biblioteca de cliente Apache Qpid JMS AMQP 1.0, visite [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Tem de adicionar os seguintes ficheiros para caixa quatro a partir do arquivo de distribuição Apache Qpid JMS AMQP 1.0 a CLASSPATH Java quando criação e execução de JMS aplicações com Bus de serviço:

- geronimo jms\_1.1\_1.0.jar especificação
- qpid-amqp-1-0-Client-[Version].JAR
- qpid-amqp-1-0-Client-jms-[Version].JAR
- qpid-amqp-1-0-Common-[Version].JAR

## <a name="coding-java-applications"></a>Codificação aplicações Java

### <a name="java-naming-and-directory-interface-jndi"></a>Atribuir nomes a Java e a Interface de diretório (JNDI)

JMS utiliza o Java nomenclatura e a Interface de diretório (JNDI) para criar uma separação entre os nomes lógicos e nomes físicos. Dois tipos de objetos JMS são resolvidos utilizando JNDI: ConnectionFactory e de destino. JNDI utiliza um modelo de fornecedor nas quais pode ligar serviços de directório diferente para processar direitos de resolução do nome. Formatar do Apache Qpid JMS AMQP 1.0 biblioteca vem com uma simples propriedades JNDI fornecedor com base no ficheiro que está configurado com um ficheiro de propriedades dos seguintes procedimentos:

```
# servicebus.properties - sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurar o ConnectionFactory

A entrada utilizada para definir um **ConnectionFactory** no fornecedor de JNDI Qpid propriedades do ficheiro é o seguinte formato:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Onde **[jndi_name]** e **[ConnectionURL]** têm os seguintes significados:

- **[jndi_name]**: O nome lógico do ConnectionFactory. Este é o nome que vai ser resolvido na aplicação Java utilizando o método de JNDI IntialContext.lookup().
- **[ConnectionURL]**: um URL que fornece a biblioteca JMS com as informações necessárias para o corretor AMQP.

O formato da **ConnectionURL** é da seguinte forma:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Onde o **[espaço de nome]**, **[SASPolicyName]** e **[SASPolicyKey]** têm os seguintes significados:

- **[espaço de nome]**: O serviço Bus espaço de nomes.
- **[SASPolicyName]**: nome da política de assinatura de acesso partilhado a fila de espera.
- **[SASPolicyKey]**: chave de política de assinatura de acesso partilhado a fila de espera.

> [AZURE.NOTE] Tem de codificação de URL a palavra-passe manualmente. Um utilitário de codificação de URL útil está disponível em [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### <a name="configure-destinations"></a>Configurar destinos

A entrada de utilizada para definir um destino no fornecedor de JNDI Qpid propriedades do ficheiro é o seguinte formato:

```
queue.[jndi_name] = [physical_name]
```

ou

```
topic.[jndi_name] = [physical_name]
```

Onde **[jndi\_nome]** e **[física\_nome]** têm os seguintes significados:

- **[jndi_name]**: O nome lógico de destino. Este é o nome que vai ser resolvido na aplicação Java utilizando o método de JNDI IntialContext.lookup().
- **[physical_name]**: O nome da entidade Bus de serviço para o qual a aplicação envia ou recebe mensagens.

> [AZURE.NOTE] Quando receber a partir de uma subscrição de tópico Bus de serviço, o nome físico especificado no JNDI deve ser o nome do tópico. O nome da subscrição é fornecido quando a subscrição resistente é criada no código da aplicação JMS. O [serviço Bus AMQP 1.0 programador do guia](service-bus-amqp-dotnet.md) fornece mais detalhes sobre como trabalhar com subscrições do tópico Bus de serviço a partir do JMS.

### <a name="write-the-jms-application"></a>Escrever a aplicação JMS

Não existem opções necessários ao utilizar JMS com o serviço Bus ou APIs especial. No entanto, existem algumas restrições que serão cobertas mais tarde. Como com qualquer aplicação JMS, primeiro elemento obrigatório é configuração do ambiente JNDI, para conseguir resolver um **ConnectionFactory** e destinos.

#### <a name="configure-the-jndi-initialcontext"></a>Configurar o InitialContext JNDI

O ambiente de JNDI está configurado, passando um hashtable de informações de configuração para o construtor de classe javax.naming.InitialContext. As duas necessário elementos a hashtable são o nome de classe da fábrica de contexto inicial e o URL do fornecedor. O seguinte código mostra como configurar o ambiente de JNDI para utilizar o ficheiro de propriedades Qpid com base JNDI fornecedor com um ficheiro de propriedades com o nome **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Uma aplicação de JMS simple utilizando uma fila Bus de serviço

O programa de exemplo seguinte envia JMS TextMessages para uma fila de serviço Bus com o nome de lógica JNDI da fila de ESPERA e recebe as mensagens novamente.

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Executar a aplicação

Executar a aplicação produz uma saída do formulário:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Em diferentes plataformas de mensagens entre JMS e .NET

Este guia mostrava como enviar e receber mensagens de serviço Bus e para utilizar JMS. No entanto, uma das principais vantagens do AMQP 1.0 é permite que as aplicações criadas a partir de componentes de escritos em idiomas diferentes, com as mensagens trocadas sujeito e em fidelidade completa.

Utilizar a aplicação de JMS exemplo descrito acima e uma aplicação do .NET semelhante tirado de um guia suplementar, [como utilizar AMQP 1.0 com a API do .NET serviço Bus .NET](service-bus-dotnet-advanced-message-queuing.md), pode trocar mensagens entre .NET e Java. 

Para mais informações sobre os detalhes de mensagens com o serviço Bus e AMQP 1.0 em diferentes plataformas, consulte o [Guia de serviço Bus AMQP 1.0 do programador](service-bus-amqp-dotnet.md).

### <a name="jms-to-net"></a>JMS para .NET

Para demonstrar JMS ao .NET messaging:

* Inicie a aplicação de exemplo .NET sem quaisquer argumentos da linha de comandos.
* Inicie a aplicação de exemplo Java com o argumento da linha de comandos "sendonly". Neste modo, a aplicação não irá receber mensagens de fila de espera, irão apenas enviar.
* Prima **Enter** algumas vezes na consola de aplicação de Java, provocará mensagens sejam enviadas.
* Estas mensagens são recebidas pela aplicação .NET.

#### <a name="output-from-jms-application"></a>Saída a partir da aplicação JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Saída a partir da aplicação .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET para JMS

Para demonstrar .NET para JMS mensagens:

* Inicie a aplicação de exemplo .NET com o argumento da linha de comandos "sendonly". Neste modo, a aplicação não irá receber mensagens de fila de espera, irão apenas enviar.
* Inicie a aplicação de exemplo Java sem quaisquer argumentos da linha de comandos.
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

As seguintes restrições existirem quando usar JMS AMQP 1.0 com Bus de serviço, nomeadamente:

* Apenas uma **MessageProducer** ou **MessageConsumer** é permitido por **sessão**. Se necessitar de criar várias **MessageProducers** ou **MessageConsumers** numa aplicação, crie uma dedicada **sessão** para cada um deles.
* Subscrições de tópico voláteis atualmente não são suportadas.
* **MessageSelectors** atualmente não são suportadas.
* Temporários destinos, ou seja, **TemporaryQueue**, **TemporaryTopic** não são atualmente suportados, juntamente com o **QueueRequestor** e **TopicRequestor** APIs utilizá-los.
* Sessões transaccionadas e transações distribuídas não são suportadas.

## <a name="summary"></a>Resumo

Este guia de procedimentos mostrava como utilizar o serviço Bus controlada funcionalidades de mensagens (filas e publicar/subscrever tópicos) a partir do Java utilizando o populares JMS API e AMQP 1.0.

Também pode utilizar o serviço Bus AMQP 1.0 a partir de outros idiomas, incluindo .NET, C, Python e PHP. Componentes construídas utilizando estes idiomas diferentes podem trocar mensagens sujeito e na fidelidade completa utilizando o 1.0 AMQP suporte no serviço Bus. Para obter mais informações, consulte o [Guia de serviço Bus AMQP 1.0 programador do](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Próximos passos

* [Suporte de AMQP 1.0 no Azure Service Bus](service-bus-amqp-overview.md)
* [Como utilizar AMQP 1.0 com a API do serviço Bus .NET](service-bus-dotnet-advanced-message-queuing.md)
* [Serviço Bus AMQP 1.0 Guia do Programador](service-bus-amqp-dotnet.md)
* [Como utilizar filas Bus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Centro de programadores do Java](/develop/java/).



 
