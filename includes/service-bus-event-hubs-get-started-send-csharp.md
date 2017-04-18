## <a name="send-messages-to-event-hubs"></a>Enviar mensagens para concentradores de evento

Nesta secção, irá escrever uma aplicação de consola do Windows que envia eventos ao seu centro de evento.

1. No Visual Studio, crie um novo projeto do Visual aplicação de ambiente de trabalho da c# utilizando o modelo de projeto de **Aplicação de consola** . Nome do projeto **remetente**.

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp1.png)

2. No Explorador de solução, a solução com o botão direito e, em seguida, clique em **Gerir pacotes de NuGet para solução**. 

3. Clique no separador **Procurar** , em seguida, procure `Microsoft Azure Service Bus`. Certifique-se de que o nome do projeto (**remetente**) está especificado na caixa de **versões** . Clique em **instalar**e aceite os termos de utilização. 

    ![](./media/service-bus-event-hubs-getstarted-send-csharp/create-sender-csharp2.png)

    Visual Studio transferências, instalações e adiciona uma referência para o [pacote do Azure Service Bus biblioteca NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Adicione o seguinte `using` declarações na parte superior do ficheiro **Program.cs** :

    ```
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Adicione os campos seguintes para a classe de **programa** , substituindo os valores de marcador de posição com o nome do centro do evento que criou na secção anterior e a cadeia de ligação de nível de espaço de nomes que guardou anteriormente.

    ```
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```

6. Adicione o seguinte método para a classe de **programa** :

    ```
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }

            Thread.Sleep(200);
        }
    }
    ```

    Este método envia continuamente eventos ao seu centro de evento com um atraso de 200 ms.

7. Por fim, adicione as seguintes linhas para o método **principais** :

    ```
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```
