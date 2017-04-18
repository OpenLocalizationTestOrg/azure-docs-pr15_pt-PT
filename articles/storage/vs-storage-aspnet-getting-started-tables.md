<properties
    pageTitle="Introdução ao armazenamento de tabela e do Visual Studio ligado serviços (ASP.NET) | Microsoft Azure"
    description="Como começar a utilizar o armazenamento de tabela do Azure num projeto de ASP.NET no Visual Studio após ligar a uma conta de armazenamento utilizando o Visual Studio ligado serviços"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="tarcher"/>

# <a name="get-started-with-table-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao armazenamento de tabela e do Visual Studio ligado serviços (ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o armazenamento de tabela do Azure no Visual Studio depois de ter criado ou referenciada uma conta de armazenamento Azure num projeto ASP.NET utilizando a caixa de diálogo do Visual Studio **Adicionar serviços ligados** . Este artigo mostra-lhe como realizar tarefas comuns no Azure tabelas, incluindo criar e eliminar uma tabela, bem como trabalhar com entidades de tabela. Os exemplos estão escritos em C\# código e utilizar a [Biblioteca de cliente do Microsoft Azure armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para mais informações gerais sobre como utilizar o Azure armazenamento de tabela, consulte o artigo [Introdução ao armazenamento de tabela do Azure utilizando o .NET](storage-dotnet-how-to-use-tables.md).

Armazenamento de tabela do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados de NoSQL aceita autenticadas chamadas a partir do dentro e fora da nuvem Azure. Tabelas Azure são ideais para armazenar dados estruturados e não relacionais.


## <a name="access-tables-in-code"></a>Tabelas do Access no código

1. Certifique-se que as declarações de espaço de nomes na parte superior do ficheiro c# incluem estas instruções **utilizar** .

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obter um objeto de **CloudStorageAccount** que representa as informações da conta de armazenamento. Utilize o seguinte código para obter a sua cadeia de ligação de armazenamento e informações de conta de armazenamento da configuração do serviço Azure.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **Nota** - utilizar todo o código acima à frente o código nos exemplos seguintes.

3. Obter um objeto de **CloudTableClient** para os objetos de tabela na sua conta de armazenamento de referência.  

        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obter um objeto de referência **CloudTable** para fazer referência a uma tabela específica e entidades.

        // Get a reference to a table named "peopleTable"
        CloudTable table = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Criar uma tabela no código

Para criar a tabela Azure, basta adicione uma chamada para **CreateIfNotExistsAsync()** o código anterior.

    // Create the CloudTable if it does not exist
    await table.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela é criar uma classe que define as propriedades da sua entidade. O código seguinte define uma classe de entidade denominada **CustomerEntity** que utiliza o nome do cliente primeiro como a chave de linha e o apelido como chave partição.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Operações de tabela que envolvam entidades são concluído utilizando o **CloudTable** objeto que criou anteriormente no "Tabelas do Access no código". O objecto **TableOperation** representa a operação de ser executadas. Exemplo de código que se segue mostra como criar um objeto de **CloudTable** e um objeto de **CustomerEntity** . Para preparar a operação, é criada uma **TableOperation** para inserir a entidade cliente na tabela. Por fim, a operação seja executada ao contactar o suporte CloudTable.ExecuteAsync.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Pode inserir várias entidades para uma tabela numa operação de escrita única. O exemplo seguinte de código cria duas objectos da entidade ("Paula Silva" e "Carlos Silva"), adiciona-os para um objeto de **TableBatchOperation** utilizando o método de inserir e, em seguida, inicia o funcionamento ao contactar o suporte **CloudTable.ExecuteBatchAsync**.

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";
    customer1.PhoneNumber = "425-555-0104";

    // Create another customer entity and add it to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";

    // Add both customer entities to the batch insert operation.
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);

    // Execute the batch operation.
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades numa partição
Para uma tabela para todas as entidades numa partição de consulta, utilize um objeto de **TableQuery** . Exemplo de código seguinte especifica um filtro para entidades onde 'Santos' são a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta à consola.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## <a name="get-a-single-entity"></a>Obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O código seguinte utiliza um objeto de **TableOperation** para especificar um cliente chamado 'Carlos Santos'. Este método devolve apenas uma entidade, em vez de uma colecção de e o valor devolvido no **TableResult.Result** é um objeto de **CustomerEntity** . Especificar teclas partição e linha numa consulta é a forma mais rápida para obter uma única entidade a partir do serviço de tabela.

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
    
    // Print the phone number of the result.
    if (retrievedResult.Result != null)
        Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode eliminar uma entidade depois de o encontrar. O seguinte código de procura de uma entidade de cliente com o nome "Carlos Silva" e se encontrá-lo, elimina-lo.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
