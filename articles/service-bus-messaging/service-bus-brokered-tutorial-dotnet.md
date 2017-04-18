<properties 
    pageTitle="Serviço Bus controlada mensagens tutorial de .NET | Microsoft Azure"
    description="Tutorial .NET mensagens juntamente."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-net-tutorial"></a>Serviço Bus controlada mensagens .NET tutorial

Azure Service Bus fornece duas soluções mensagens abrangentes – um, através de um serviço de centralizada "reencaminhamento" executar na nuvem que suporta uma variedade de diferentes protocolos de transporte e Web serviços normas, incluindo SOAP, WS-* e coloque o PONTEIRO. O cliente não necessita de uma ligação direta para o serviço no local nem necessitam saber onde reside o serviço e o serviço no local não precisam de qualquer portas de entrada aberta na firewall.

A segunda solução mensagens permite "juntamente" capacidades de mensagens. Estes podem ser consideradas como assíncrona ou desacoplado funcionalidades mensagens que suporte publicar-subscrever, desacoplamento temporal e cenários utilizando a infraestrutura de mensagens do serviço Bus de balanceamento de carga. Comunicação desacoplada tem muitas vantagens; Por exemplo, clientes e servidores podem ligar conforme necessário e executar as operações de uma forma assíncrona.

Neste tutorial destina-se que lhe fornece uma descrição geral e obter experiência com filas, uma dos componentes principais do serviço Bus controlada mensagens. Depois de trabalhar através de sequência de tópicos neste tutorial, terá uma aplicação que preenche uma lista de mensagens, cria uma fila e envia mensagens para essa fila. Por fim, a aplicação recebe e apresenta as mensagens a partir de fila de espera, em seguida, limpa a sua e saídas de recursos. Para obter um tutorial correspondente que descreve como criar uma aplicação que utiliza o reencaminhamento de Bus de serviço, consulte o artigo [serviço Bus reencaminhado tutorial mensagens](../service-bus-relay/service-bus-relay-tutorial.md).

## <a name="introduction-and-prerequisites"></a>Introdução e pré-requisitos

Filas oferecem do primeiro entrega de mensagens do primeiro saída (FIFO) para um ou mais consumidores concorrentes. FIFO significa que as mensagens são normalmente esperadas para ser recebido e processadas pelos destinatários pela ordem temporal terem sido colocado em fila, em que cada mensagem será recebida e processada pelos consumidores de apenas uma mensagem. É dos principais benefícios da utilização filas alcançar *desacoplamento temporal* de componentes de aplicações: por outras palavras, produtor e consumidores é necessário enviar e receber mensagens ao mesmo tempo, uma vez que as mensagens são armazenadas durável na fila de espera. Das vantagens relacionada é *nivelamento de carga*, que permite aos produtor e consumidores para enviar e receber mensagens com taxas diferentes.

Seguem-se alguns passos administrativos e pré-requisito que deve seguir antes de iniciar o tutorial. O primeiro é para criar um espaço de nomes de serviço e para obter uma chave de assinatura (SA) acesso partilhado. Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através de Bus de serviço. Uma chave de SA é gerada automaticamente pelo sistema quando é criado um espaço de nomes de serviço. A combinação de espaço de nomes de serviço e chave SA fornece uma credencial com o qual o serviço Bus autentica acesso a uma aplicação.

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>Criar um espaço de nomes de serviço e obter uma chave de SA

O primeiro passo é para criar um espaço de nomes de serviço e para obter uma chave de [Assinatura de acesso partilhados](service-bus-sas-overview.md) (SA). Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através de Bus de serviço. Uma chave de SA é gerada automaticamente pelo sistema quando é criado um espaço de nomes de serviço. A combinação de espaço de nomes de serviço e chave SA fornece uma credencial para Bus de serviço para autenticar o acesso a uma aplicação.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

O passo seguinte é criar um projeto do Visual Studio e escrever duas funções de helper carregar uma lista delimitado por vírgulas de mensagens para um objeto de .NET [lista](https://msdn.microsoft.com/library/6sh2ey19.aspx) [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) vivamente escreveu.

### <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Abrir o Visual Studio como administrador ao clicar o programa no menu Iniciar e clicar em **Executar como administrador**.

1. Crie um novo projeto de aplicação de consola. Clique no menu **ficheiro** e selecione **Novo**e, em seguida, clique em **projeto**. Na caixa de diálogo **Novo projeto** , clique em **Visual c#** (se **Visual c#** não aparecer, procure em **Idiomas de outras**), clique no modelo de **Aplicação de consola** e o nome **QueueSample**. Utilize a predefinição de **localização**. Clique em **OK** para criar o projeto.

1. Utilize o Gestor de pacote NuGet para adicionar as bibliotecas de serviço Bus ao seu projeto:
    1. No Explorador de solução, com o botão direito do projecto **QueueSample** e, em seguida, clique em **Gerir pacotes de NuGet**.
    2. Na caixa de diálogo **Gerir pacotes de Nuget** , clique no separador **Procurar** e procurar **Azure Service Bus**, em seguida, clique em **instalar**.
<br />
1. No Explorador de solução, faça duplo clique no ficheiro de Program.cs para abri-lo no editor do Visual Studio. Alterar o nome do espaço de nomes a partir do seu nome de grupo predefinido `QueueSample` para `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Modificar a `using` declarações conforme mostrado no seguinte código.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Criar um ficheiro de texto com o nome Data.csv e copiar no seguinte texto delimitado por vírgulas.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Guarde e feche o ficheiro Data.csv e lembre-se a localização na qual o guardou.

1. No Explorador de solução, com o botão direito no nome do seu projeto (neste exemplo, **QueueSample**), clique em **Adicionar**, em seguida, clique em **Item existente**.

1. Procure o ficheiro de Data.csv que criou no passo 6. Clique no ficheiro e, em seguida, clique em **Adicionar**. Certifique-se de que * *todos os ficheiros (*.*) * * está selecionada na lista de tipos de ficheiro.

### <a name="create-a-method-that-parses-a-list-of-messages"></a>Criar um método que analisa uma lista de mensagens

1. No `Program` classe antes do `Main()` método, declarar duas variáveis: um dos tipos de **DataTable**, para conter a lista de mensagens em Data.csv. A outra deve ser do tipo de objeto de lista, vivamente escrito para [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). O último é a lista de mensagens juntamente que irão utilizar passos subsequentes no tutorial.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Fora `Main()`, definir uma `ParseCSV()` método que analisa a lista de mensagens no Data.csv e carrega as mensagens para uma tabela de [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) , conforme mostrado aqui. O método devolve um objeto de **DataTable** .

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. No `Main()` método, adicione uma instrução que liga a `ParseCSVFile()` método:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>Criar um método que carrega o na lista de mensagens

1. Fora `Main()`, definir uma `GenerateMessages()` método leva-o até o objecto **DataTable** devolvido por `ParseCSVFile()` e carrega a tabela para uma lista vivamente escreveu das mensagens juntamente. O método, em seguida, devolve o objeto de **lista** , tal como no exemplo seguinte. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. No `Main()`, imediatamente a seguir a chamada para `ParseCSVFile()`, adicione uma instrução que liga a `GenerateMessages()` método com o valor de retorno do `ParseCSVFile()` como um argumento:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>Obter as credenciais de utilizador

1. Primeiro, crie três variáveis de cadeia global para colocar em espera estes valores. Declarar estas variáveis imediatamente a seguir as declarações de variáveis anterior; Por exemplo:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Em seguida, crie uma função que aceita e armazena a chave de SA e o espaço de nomes de serviço. Adicionar este método fora da `Main()`. Por exemplo: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. No `Main()`, imediatamente a seguir a chamada para `GenerateMessages()`, adicione uma instrução que liga a `CollectUserInput()` método:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>Criar a solução

No menu **construir** no Visual Studio, pode clique em **Criar solução** ou prima **Ctrl + Shift + B** para confirmar a precisão do seu trabalho até ao momento.

## <a name="create-management-credentials"></a>Criar de credenciais de gestão

Neste passo, pode definir as operações de gestão que irá utilizar para criar acesso partilhado com o qual será autorizada a aplicação de credenciais de assinatura (SA).

1. Para clareza, este tutorial coloca todas as operações de fila de espera um método em separado. Criar uma assíncrona `Queue()` método na `Program` classe, após o `Main()` método. Por exemplo:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. O passo seguinte é criar uma credencial de SA utilizando um objeto de [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) . O método de criação leva-o até o nome da chave SA e valor obtido o `CollectUserInput()` método. Adicione o seguinte código para o `Queue()` método:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Crie um novo objeto de gestão do espaço de nomes, com um URI que contém o nome do espaço de nomes e as credenciais de gestão obtidas no passo anterior, como argumentos. Adicione este código diretamente a seguir ao código adicionado no passo anterior. Certifique-se substituir `<yourNamespace>` com o nome do seu espaço de nomes de serviço:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>Exemplo

Neste momento, o seu código deve ser semelhante ao seguinte:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>Enviar mensagens para fila de espera

Neste passo, pode cria uma fila, em seguida, envia as mensagens contidas na lista de mensagens juntamente para essa fila.

### <a name="create-queue-and-send-messages-to-the-queue"></a>Fila de criar e enviar mensagens para fila de espera

1. Primeiro, crie fila de espera. Por exemplo, atribua- `myQueue`e declará-lo diretamente a seguir as operações de gestão que adicionou na `Queue()` método no último passo:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. No `Queue()` método, crie um objecto de fábrica do mesmo mensagens com um recentemente criado Bus URI do serviço de como um argumento. Adicione o seguinte código imediatamente a seguir as operações de gestão que adicionou no último passo. Certifique-se substituir `<yourNamespace>` com o nome do seu espaço de nomes de serviço:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Em seguida, crie o objeto de fila utilizando a classe de [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) . Adicione o seguinte código diretamente a seguir ao código que adicionou no último passo:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Em seguida, adicione o código que ciclos pela lista de mensagens juntamente que criou anteriormente, cada um deles para a fila de enviar. Adicione o seguinte código diretamente após a `CreateQueueClient()` declaração no passo anterior:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>Receber mensagens de fila de espera

Neste passo, obtenha a lista de mensagens da fila que criou no passo anterior.

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>Criar um recetor e receber mensagens de fila de espera

No `Queue()` método, iteração fila de espera e receber mensagens utilizando o método de [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) , impressão de cada mensagem na consola. Adicione o seguinte código diretamente a seguir ao código que adicionou no passo anterior:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Tenha em atenção que a `Thread.Sleep` só é utilizado para simular a mensagem de processamento e provavelmente não seria precisa numa aplicação real mensagens.

### <a name="end-the-queue-method-and-clean-up-resources"></a>Terminar o método de fila de espera e limpar recursos

Imediatamente a seguir ao código anterior, adicione o seguinte código para limpar os recursos de fábrica do mesmo e fila de mensagem:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>Chamar o método de fila de espera

O último passo é adicionar uma instrução que liga a `Queue()` método de `Main()`. Adicione a linha seguinte realçada de código no final da Main():
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### <a name="example"></a>Exemplo

O código seguinte contém a aplicação de **QueueSample** concluída.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>Criar e executar a aplicação QueueSample

Agora que concluiu os passos anteriores, pode criar e executar a aplicação **QueueSample** .

### <a name="build-the-queuesample-application"></a>Criar a aplicação QueueSample

No Visual Studio, no menu de **Criar** , clique em **Criar solução**ou prima **Ctrl + Shift + B**. Se encontrar erros, verifique que o seu código está correto com base no exemplo completo apresentado no final do passo anterior.

## <a name="next-steps"></a>Próximos passos

Neste tutorial mostrava como construir um cliente de serviço Bus aplicação de serviço e utilizando as capacidades de mensagens do serviço Bus controlada. Para obter um tutorial semelhante que utiliza o serviço Bus [reencaminhamento](service-bus-messaging-overview.md#Relayed-messaging), consulte o artigo [serviço Bus reencaminhado tutorial mensagens](../service-bus-relay/service-bus-relay-tutorial.md).

Para saber mais sobre [Bus de serviço](https://azure.microsoft.com/services/service-bus/), consulte os tópicos seguintes.

- [Descrição geral do serviço Bus messaging](service-bus-messaging-overview.md)
- [Conceitos básicos da Bus de serviço](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura de Bus de serviço](service-bus-architecture.md)

