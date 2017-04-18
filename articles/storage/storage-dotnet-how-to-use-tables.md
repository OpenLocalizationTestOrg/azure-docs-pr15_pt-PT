<properties
    pageTitle="Introdução ao armazenamento de tabela do Azure utilizando o .NET | Microsoft Azure"
    description="Armazene dados estruturados na nuvem através do armazenamento de tabela do Azure, um arquivo de dados NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="get-started-with-azure-table-storage-using-net"></a>Introdução ao armazenamento de tabela do Azure utilizando o .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de tabela do Azure é um serviço que armazena dados estruturados de NoSQL na nuvem. Armazenamento de tabela é um arquivo de chave/atributo com um design schemaless. Porque é schemaless armazenamento de tabela, é fácil a adaptar-se os dados como as necessidades da sua evolve de aplicação. Acesso aos dados é rápido e rentável para todos os tipos de aplicações. Armazenamento de tabela é normalmente significativamente inferior na custo tradicional SQL para semelhantes volumes de dados.

Pode utilizar o armazenamento de tabela para armazenar flexíveis conjuntos de dados, tais como os dados de utilizador para aplicações web, livros de endereços, informações sobre o dispositivo e qualquer outro tipo de metadados que requer o seu serviço. Pode armazenar qualquer número de entidades numa tabela e uma conta de armazenamento pode conter qualquer número de tabelas, por excesso para o limite de capacidade da conta de armazenamento.

### <a name="about-this-tutorial"></a>Informações sobre este tutorial

Este tutorial mostra como escrever código .NET para alguns cenários comuns a utilizar o armazenamento de tabela do Azure, incluindo criar e eliminar uma tabela e inserir, atualizar, eliminar e consultar dados de tabela.

**Estimativa tempo a concluir:** 45 minutos

**Prerequisities:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Biblioteca de armazenamento Azure do cliente para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Gestor de configuração do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Uma [conta de armazenamento Azure](storage-create-storage-account.md#create-a-storage-account)

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais amostras

Para obter exemplos adicionais a utilizar o armazenamento de tabela, consulte o artigo [Introdução ao Azure armazenamento de tabela no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Pode transferir a aplicação de exemplo e executá-la ou procure o código no GitHub.


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicione o seguinte `using` declarações na parte superior da `program.cs` ficheiro:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types

### <a name="parse-the-connection-string"></a>Analisar a cadeia de ligação

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Criar o cliente do serviço de tabela

A classe de **CloudTableClient** permite-lhe obter a tabelas e entidades armazenadas no armazenamento de tabela. Eis uma forma de criar o cliente do serviço:

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

Agora, está pronto para escrever código que lê dados a partir de e escreve dados ao armazenamento de tabelas.

## <a name="create-a-table"></a>Criar uma tabela

Este exemplo mostra como criar uma tabela se não existir já:

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Retrieve a reference to the table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table if it doesn't exist.
    table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Entidades mapeiam para C\# objetos através de uma classe personalizada derivado de **TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades da sua entidade. O código seguinte define uma classe entidade que utiliza o nome do cliente primeiro como a chave de linha e o apelido como chave partição. Em conjunto, partição de uma entidade e chave de linha identificam exclusivamente a entidade na tabela. Entidades com a mesma chave partição podem ser consultadas mais rápido daqueles com as teclas de partição diferente, mas a utilização de chaves de partição diversificados permite maior escalabilidade de operações paralelas.  Para qualquer propriedade deve ficar armazenada no serviço de tabela, a propriedade tem de ser uma propriedade de um tipo de suportado expõe ambos pública `get` e `set`.
Além disso, o tipo de entidade *tem* expor um construtor de parâmetro menos.

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

Operações de tabela que envolvam entidades são executadas através do objeto **CloudTable** que criou anteriormente na secção "Criar uma tabela". A operação a ser executado é representada por um objeto de **TableOperation** .  Exemplo de código seguinte mostra a criação do objecto **CloudTable** e, em seguida, um objeto de **CustomerEntity** .  Para preparar a operação, é criado um objeto de **TableOperation** para inserir a entidade cliente na tabela.  Por fim, a operação seja executada ao contactar o suporte **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation object that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Pode inserir um lote de entidades para uma tabela uma operação de escrita. Algumas outras notas no operações batch:

-  Pode efetuar atualizações, elimina e insere na mesma operação lote único.
-  Uma operação de único lote pode incluir até 100 entidades.
-  Todas as entidades numa operação de lote único tem de ter a mesma chave partição.
-  Enquanto é possível executar uma consulta como uma operação de lote, tem de ser a operação apenas no lote de.

<!-- -->
O exemplo seguinte de código cria duas objectos da entidade e adiciona cada um deles para **TableBatchOperation** utilizando o método de **Inserir** . Em seguida, **CloudTable.Execute** chama-se para executar a operação.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

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
    table.ExecuteBatch(batchOperation);

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição

Para uma tabela para todas as entidades numa partição de consulta, utilize um objeto de **TableQuery** .
Exemplo de código seguinte especifica um filtro para entidades onde 'Santos' são a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta à consola.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Obter um intervalo de entidades de uma partição

Se não quiser todas as entidades uma partição de consulta, pode especificar um intervalo, combinando o filtro de chave partição com um filtro de chave de linha. Exemplo de código seguinte utiliza dois filtros para obter todas as entidades na partição Silva onde a chave de linha (primeiro nome) comece por uma letra anterior E do alfabeto e, em seguida, imprime os resultados da consulta.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## <a name="retrieve-a-single-entity"></a>Obter uma única entidade

Pode escrever uma consulta para obter uma entidade única e específica. O código seguinte utiliza **TableOperation** para especificar o cliente 'Carlos Santos'.
Este método devolve apenas uma entidade em vez de uma coleção de e o valor devolvido no **TableResult.Result** é um objeto de **CustomerEntity** .
Especificar teclas partição e linha numa consulta é a forma mais rápida para obter uma única entidade a partir do serviço de tabela.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="replace-an-entity"></a>Substituir uma entidade

Para atualizar uma entidade, recuperá-la a partir do serviço de tabela, modificar o objeto de entidade e, em seguida, guarde as alterações novamente para o serviço de tabela. O código seguinte altera o número de telefone de um cliente existente. Em vez de chamar a **Inserir**, este código utiliza **Substituir**. Isto faz com que a entidade totalmente ser substituído no servidor, a menos que a entidade no servidor foi alterado desde que foi obtido, caso em que a operação irá falhar.  Esta falha é impedir que a aplicação a partir de substituir inadvertidamente uma alteração feita entre a obtenção e a atualização por outro componente da sua aplicação.  É o processamento bom desta falha obter a entidade novamente, faça as suas alterações (se ainda válido) e, em seguida, executar outra operação **Substituir** .  A secção seguinte irá mostrar-lhe como substituir este comportamento.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-0105";

       // Create the Replace TableOperation.
       TableOperation updateOperation = TableOperation.Replace(updateEntity);

       // Execute the operation.
       table.Execute(updateOperation);

       Console.WriteLine("Entity updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="insert-or-replace-an-entity"></a>Inserir-ou-substituir uma entidade

**Substituir** operações falhará se a entidade foi alterada desde que foi obtida a partir do servidor.  Além disso, terá de obter a entidade do servidor pela primeira vez na ordem ser efetuada com êxito a operação **Substituir** .
Por vezes, no entanto, não sabe se a entidade existe no servidor e os atuais valores armazenados no mesmo são irrelevantes. A atualização da substituam-los a todos.  Para realizar esta tarefa, utilizaria uma operação de **InsertOrReplace** .  Esta operação insere a entidade se este não existe ou substitui-se de que faz, independentemente de quando é a última actualização efetuada.  No exemplo seguinte de código, a entidade de cliente para Bruno Santos ainda serem recuperada, mas este será guardado, em seguida, novamente para o servidor através do **InsertOrReplace**.  Serão substituídas atualizações efetuadas à entidade entre as operações de obtenção e atualização.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
    {
       // Change the phone number.
       updateEntity.PhoneNumber = "425-555-1234";

       // Create the InsertOrReplace TableOperation.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

       // Execute the operation.
       table.Execute(insertOrReplaceOperation);

       Console.WriteLine("Entity was updated.");
    }

    else
       Console.WriteLine("Entity could not be retrieved.");

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto de propriedades entidade de consulta

Uma consulta de tabela pode obter propriedades apenas alguns a partir de uma entidade em vez de todas as propriedades de entidade. Esta técnica, denominada projecções, reduz largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades muito grandes. A consulta no código que se segue devolve apenas os endereços de e-mail de entidades na tabela. Isto é feito utilizando uma consulta de **DynamicTableEntity** e também **EntityResolver**. Pode obter mais informações sobre projecções na [Introdução ao Upsert e projecções de consulta blogue publicam][]. Tenha em atenção que projecções não são suportada no emulador de armazenamento local, para que este código é executado apenas quando estiver a utilizar uma conta do serviço de tabela.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and select only the Email property.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## <a name="delete-an-entity"></a>Eliminar uma entidade

Pode eliminar uma entidade facilmente depois de ter obtidos-lo, utilizando o mesmo padrão apresentado para atualizar uma entidade.  O código seguinte obtém e elimina uma entidade de cliente.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       table.Execute(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Could not retrieve the entity.");

## <a name="delete-a-table"></a>Eliminar uma tabela

Por fim, o exemplo seguinte de código elimina uma tabela a partir de uma conta de armazenamento. Uma tabela que foi eliminada vai estar disponível para ser recriada durante um período de tempo após a eliminação.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="retrieve-entities-in-pages-asynchronously"></a>Obter entidades nas páginas de forma assíncrona

Se está a ler um grande número de entidades e pretende processo/Mostrar entidades como serem obtidos em vez de a aguardar-os devolver, pode obter entidades utilizando uma consulta segmentada. Este exemplo mostra como devolver resultados nas páginas utilizando o padrão de aguardar assíncrona para que não está bloqueada execução enquanto está à espera de um conjunto de resultados a devolver grande. Para obter mais detalhes sobre como utilizar o padrão de Await assíncrona no .NET, consulte o artigo [programação assíncrona com assíncrona e Await (c# e do Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

    // Initialize a default TableQuery to retrieve all the entities in the table.
    TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

    // Initialize the continuation token to null to start from the beginning of the table.
    TableContinuationToken continuationToken = null;

    do
    {
        // Retrieve a segment (up to 1,000 entities).
        TableQuerySegment<CustomerEntity> tableQueryResult =
            await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

        // Assign the new continuation token to tell the service where to
        // continue on the next iteration (or null if it has reached the end).
        continuationToken = tableQueryResult.ContinuationToken;

        // Print the number of rows retrieved.
        Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

    // Loop until a null continuation token is received, indicating the end of the table.
    } while(continuationToken != null);

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de tabela, siga estas ligações para saber mais sobre tarefas mais complexas de armazenamento:

- Ver mais exemplos de armazenamento de tabela em [Introdução ao Azure armazenamento de tabela no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
- Ver a documentação de referência do serviço de tabela para obter detalhes completos sobre APIs disponíveis:
    - [Biblioteca de armazenamento do cliente para referência .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referência REST API](http://msdn.microsoft.com/library/azure/dd179355)
- Saiba como simplificar o código que escrever para trabalhar com o armazenamento do Windows Azure utilizando o [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- Ver mais guias de funcionalidade para saber mais sobre as opções adicionais para armazenar dados no Azure.
    - [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
    - [Ligar à base de dados SQL utilizando o .NET (c#)](../sql-database/sql-database-develop-dotnet-simple.md) para armazenar dados relacionais.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

  [Mensagem de blogue Upsert introdução e projecções de consulta]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [How to: Programmatically access Table storage]: #tablestorage
