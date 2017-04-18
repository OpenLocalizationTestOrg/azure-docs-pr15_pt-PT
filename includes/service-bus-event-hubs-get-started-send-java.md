## <a name="send-messages-to-event-hubs"></a>Enviar mensagens para concentradores de evento

A biblioteca do cliente Java para evento concentradores está disponível para utilizar em projetos Maven a partir do [Repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)e pode ser referenciada utilizando a seguinte declaração de dependência dentro de ficheiro do projeto avançou:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Para diferentes tipos de compilação ambientes, pode obter explicitamente os mais recentes para caixa ficheiros a partir do [Repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) ou do [ponto de distribuição de lançamento no GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Para o publisher um evento simples, importe o pacote de *com.microsoft.azure.eventhubs* para as classes de cliente do evento concentradores e o pacote de *com.microsoft.azure.servicebus* para aulas utilitário como exceções comuns que são partilhadas com o cliente de mensagens do Azure Service Bus. 

Para o exemplo seguinte, crie um novo projeto de Maven para uma aplicação de consola/shell no seu ambiente de desenvolvimento Java favorito. A classe será chamada ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Substitua o espaço de nomes e os nomes de concentrador de evento com os valores que utilizou quando criou o concentrador de evento.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Em seguida, crie um evento no singular ao torne uma cadeia em respectiva codificação byte UTF-8. Em seguida, podemos criar uma nova instância de cliente do evento concentradores da cadeia de ligação e envie a mensagem.   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 
