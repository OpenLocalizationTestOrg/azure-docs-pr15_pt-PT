<properties
    pageTitle="Como utilizar o armazenamento de tabela a partir do Java | Microsoft Azure"
    description="Armazene dados estruturados na nuvem através do armazenamento de tabela do Azure, um arquivo de dados NoSQL."
    services="storage"
    documentationCenter="java"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-java"></a>Como utilizar o armazenamento de tabela a partir do Java

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Este guia mostrar-lhe como efetuar cenários comuns utilizando o serviço de armazenamento de tabela do Azure. Os exemplos são gravados no Java e utilizam o [Azure armazenamento SDK para Java][]. Os cenários abrangidos incluam **Criar**, **Listar**e **Eliminar** tabelas, bem como **Inserir**, **consultar**, **Modificar**e **Eliminar** entidades numa tabela. Para mais informações sobre tabelas, consulte a secção [os passos seguintes](#Next-Steps) .

Nota: Uma SDK está disponível para programadores que estão a utilizar o armazenamento do Windows Azure em dispositivos Android. Para mais informações, consulte o [SDK de armazenamento do Azure para Android][].

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação de Java

Neste guia, irá utilizar funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação Java localmente ou no dentro de uma função de web ou trabalhador no Azure a execução de código.

Para fazê-lo, terá de instalar o Java Development Kit (JDK) e criar uma conta de armazenamento Azure na sua subscrição do Azure. Assim que tiver feito, terá de confirmar se o seu sistema de desenvolvimento cumpre os requisitos mínimos e dependências que sejam encontram listadas do repositório de [Azure armazenamento SDK para Java][] no GitHub. Se o seu sistema de cumprir essas exigências, pode seguir as instruções para transferir e instalar as bibliotecas de armazenamento do Azure para Java no sistema da partir desse repositório. Assim que tiver concluído essas tarefas, pode poderão criar uma aplicação de Java que utiliza os exemplos neste artigo.

## <a name="configure-your-application-to-access-table-storage"></a>Configurar a aplicação para aceder ao armazenamento de tabela

Adicione as seguintes instruções de importar para a parte superior do ficheiro Java em que pretende utilizar o armazenamento de Microsoft Azure APIs para aceder a tabelas:

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="setup-an-azure-storage-connection-string"></a>Uma cadeia de ligação do Azure armazenamento de configuração

Um cliente de armazenamento Azure utiliza uma cadeia de ligação de armazenamento para armazenar os pontos finais e as credenciais para aceder a serviços de gestão de dados. Quando a ser executado numa aplicação cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, utilizando o nome da sua conta de armazenamento e a chave primária de acesso para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Este exemplo mostra como podem declarar um campo estático para colocar em espera a cadeia de ligação:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Numa aplicação em execução no interior de uma função no Microsoft Azure, esta cadeia pode ser armazenada no ficheiro de configuração de serviço, *ServiceConfiguration.cscfg*e pode ser acedida através de uma chamada para o método de **RoleEnvironment.getConfigurationSettings** . Eis um exemplo de obter a cadeia de ligação a partir de um elemento de **definição** chamado *StorageConnectionString* no ficheiro de configuração de serviço:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Exemplos que se seguem partem do pressuposto de que utilizou um destes dois métodos para obter a cadeia de ligação de armazenamento.

## <a name="how-to-create-a-table"></a>Como: criar uma tabela

Um objeto de **CloudTableClient** permite-lhe obter objetos de referência para tabelas e entidades. O código seguinte cria um objeto de **CloudTableClient** e utiliza-lo para criar um novo objeto **CloudTable** que representa uma tabela com o nome "utilizadores". (Nota: existem formas adicionais de criar objetos **CloudStorageAccount** ; para obter mais informações, consulte o artigo **CloudStorageAccount** na [Referência do Azure armazenamento Client SDK].)

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create the table if it doesn't exist.
       String tableName = "people";
       CloudTable cloudTable = tableClient.getTableReference(tableName);
       cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-list-the-tables"></a>Como: as tabelas de lista

Para obter uma lista de tabelas, chame o método de **CloudTableClient.listTables()** para obter uma lista iterable de nomes de tabelas.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Loop through the collection of table names.
        for (String table : tableClient.listTables())
        {
          // Output each table name.
          System.out.println(table);
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-add-an-entity-to-a-table"></a>Como: adicionar uma entidade a uma tabela

Mapa de entidades para objetos de Java através de uma classe personalizada **TableEntity**de execução. Para sua comodidade, a classe de **TableServiceEntity** implementa **TableEntity** e utiliza reflexo para mapear propriedades para obtenha e setter métodos com o nome para as propriedades. Para adicionar uma entidade a uma tabela, crie primeiro uma classe que define as propriedades da sua entidade. O código seguinte define uma classe entidade que utiliza o nome do cliente primeiro como a chave de linha e o apelido como chave partição. Em conjunto, partição de uma entidade e chave de linha identificam exclusivamente a entidade na tabela. Entidades com a mesma chave partição podem ser consultadas mais rápido daqueles com as teclas de partição diferente.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Operações de tabela que envolvam entidades necessitam de um objeto de **TableOperation** . Este objeto define a operação a ser executado numa entidade, que pode ser executada com um objecto **CloudTable** . O código seguinte cria uma nova instância da classe de **CustomerEntity** com alguns dados de cliente a ser armazenado. O código seguinte efetua **TableOperation.insertOrReplace** para criar um objecto **TableOperation** para inserir uma entidade numa tabela e associa o novo **CustomerEntity** -lo. Por fim, o código liga-lhe **Executar** método no objeto **CloudTable** , especificando a tabela "utilizadores" e a nova **TableOperation**, que, em seguida, envia um pedido para o serviço de armazenamento para inserir a nova entidade de cliente na tabela "utilizadores" ou substituir a entidade se já existir.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.setEmail("Walter@contoso.com");
        customer1.setPhoneNumber("425-555-0101");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-a-batch-of-entities"></a>Como: Inserir um lote de entidades

Pode inserir um lote de entidades para o serviço de tabela numa operação de escrita. O código seguinte cria um objeto de **TableBatchOperation** , em seguida, adiciona que três inserir operações à mesma. Cada operação de inserir é adicionada ao criar um novo objeto entidade, definir os valores e, em seguida, chamar o método de **Inserir** no objeto **TableBatchOperation** para associar a entidade de uma nova operação de inserir. Em seguida, o código de chamadas **Executar** no objeto **CloudTable** , especificando a tabela "utilizadores" e o objeto **TableBatchOperation** , que envia o lote de operações de tabela para o serviço de armazenamento num único pedido.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Define a batch operation.
        TableBatchOperation batchOperation = new TableBatchOperation();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a customer entity to add to the table.
        CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
        customer.setEmail("Jeff@contoso.com");
        customer.setPhoneNumber("425-555-0104");
        batchOperation.insertOrReplace(customer);

       // Create another customer entity to add to the table.
       CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
       customer2.setEmail("Ben@contoso.com");
       customer2.setPhoneNumber("425-555-0102");
       batchOperation.insertOrReplace(customer2);

       // Create a third customer entity to add to the table.
       CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
       customer3.setEmail("Denise@contoso.com");
       customer3.setPhoneNumber("425-555-0103");
       batchOperation.insertOrReplace(customer3);

       // Execute the batch of operations on the "people" table.
       cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Algumas coisas a nota sobre a operações batch:

- Pode executar até 100 inserir, eliminar, impressão em série, substituir, inserir ou impressão em série e inserir ou substituir operações em qualquer combinação num único lote.
- Uma operação de lote pode ter uma operação de obter, se for a única operação no lote de.
- Todas as entidades numa operação de lote único tem de ter a mesma chave partição.
- Uma operação batch está limitada a uma carga útil dados de 4MB.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Como: obter todas as entidades numa partição

Para consultar uma tabela para entidades de uma partição, pode utilizar uma **TableQuery**. Chamar **TableQuery.from** para criar uma consulta numa tabela em particular que devolve um tipo de resultado especificado. O código seguinte especifica um filtro para entidades onde 'Santos' são a chave de partição. **TableQuery.generateFilterCondition** é um método de ajuda para criar filtros para consultas. Ligar **onde** a referência devolvida pelo método **TableQuery.from** para aplicar o filtro à consulta. Quando a consulta é executada com uma chamada para **Executar** no objeto **CloudTable** , devolve um **iteração** com o tipo de resultado **CustomerEntity** especificado. Em seguida, pode utilizar o **iteração** devolvidos por um para cada loop consumir os resultados. Este código imprime os campos de cada entidade nos resultados da consulta à consola.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

       // Specify a partition query, using "Smith" as the partition key filter.
       TableQuery<CustomerEntity> partitionQuery =
           TableQuery.from(CustomerEntity.class)
           .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Como: obter um intervalo de entidades de uma partição

Se não quiser todas as entidades uma partição de consulta, pode especificar um intervalo utilizando operadores de comparação num filtro. O código seguinte combina dois filtros para obter todas as entidades na partição "Silva" onde a chave de linha (primeiro nome) começa por uma letra até 'E' do alfabeto. Em seguida, este imprime os resultados da consulta. Se utilizar as entidades adicionadas à tabela no lote de inserir secção deste guia, apenas duas entidades são devolvidas desta vez (Carlos e Diana Santos); Paula Bento não está incluído.

    try
    {
        // Define constants for filters.
        final String PARTITION_KEY = "PartitionKey";
        final String ROW_KEY = "RowKey";
        final String TIMESTAMP = "Timestamp";

        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

       // Create the table client.
       CloudTableClient tableClient = storageAccount.createCloudTableClient();

       // Create a cloud table object for the table.
       CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a filter condition where the partition key is "Smith".
        String partitionFilter = TableQuery.generateFilterCondition(
           PARTITION_KEY,
           QueryComparisons.EQUAL,
           "Smith");

        // Create a filter condition where the row key is less than the letter "E".
        String rowFilter = TableQuery.generateFilterCondition(
           ROW_KEY,
           QueryComparisons.LESS_THAN,
           "E");

        // Combine the two conditions into a filter expression.
        String combinedFilter = TableQuery.combineFilters(partitionFilter,
            Operators.AND, rowFilter);

        // Specify a range query, using "Smith" as the partition key,
        // with the row key being up to the letter "E".
        TableQuery<CustomerEntity> rangeQuery =
           TableQuery.from(CustomerEntity.class)
           .where(combinedFilter);

        // Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-retrieve-a-single-entity"></a>Como: obter uma única entidade

Pode escrever uma consulta para obter uma entidade única e específica. O código seguinte chamadas **TableOperation.retrieve** com uma chave de partição e linha parâmetros chave para especificar o cliente "Paula Silva" em vez de criar um **TableQuery** e utilizar filtros para fazer a mesma coisa. Quando é executado, a operação de obter devolve apenas uma entidade, em vez de uma coleção de. O método de **getResultAsType** converte o resultado para o tipo do destino da atribuição, um objeto de **CustomerEntity** . Se este tipo não é compatível com o tipo de especificado para a consulta, uma exceção será devolvida. Um valor nulo é devolvido se não existe uma entidade tem uma partição exata e uma chave de linha corresponder. Especificar teclas partição e linha numa consulta é a forma mais rápida para obter uma única entidade a partir do serviço de tabela.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

       // Submit the operation to the table service and get the specific entity.
       CustomerEntity specificEntity =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Output the entity.
        if (specificEntity != null)
        {
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-modify-an-entity"></a>Como: modificar uma entidade

Para modificar uma entidade, recuperá-la a partir do serviço de tabela, faça as alterações ao objeto entidade e guardar as alterações efetuadas o serviço de tabela com uma operação de substituir ou em série. O código seguinte altera o número de telefone de um cliente existente. Em vez de chamar **TableOperation.insert** como fizemos para inserir, este código chamadas **TableOperation.replace**. O método de **CloudTable.execute** liga para o serviço de tabela e, a entidade é substituída, a menos que outra aplicação foi alterado-lo na hora desde esta aplicação obtidos-lo. Quando o que acontece, uma exceção é devolvida e a entidade deve ser obtida, modificada e guardada novamente. Este padrão de repetição de simultaneidade optimista é comum num sistema de armazenamento distribuído.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
           CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff =
           TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Submit the operation to the table service and get the specific entity.
        CustomerEntity specificEntity =
          cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Specify a new phone number.
        specificEntity.setPhoneNumber("425-555-0105");

        // Create an operation to replace the entity.
        TableOperation replaceEntity = TableOperation.replace(specificEntity);

        // Submit the operation to the table service.
        cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-query-a-subset-of-entity-properties"></a>Como: um subconjunto de propriedades entidade de consulta

Uma consulta a uma tabela pode obter propriedades apenas alguns a partir de uma entidade. Esta técnica, denominada projecções, reduz largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades muito grandes. A consulta o código seguinte utiliza o método **Selecione** para devolver os endereços de e-mail de entidades na tabela. Os resultados são projectados para uma coleção de **cadeia** com a ajuda de um **EntityResolver**, que é que a conversão em entidades devolvido pelo servidor. Pode obter mais informações sobre projecções no [Azure tabelas: introdução ao Upsert e projecções de consulta][]. Tenha em atenção que projecções não são suportada no emulador de armazenamento local, para que este código é executado apenas quando uma conta a utilizar o serviço de tabela.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Define a projection query that retrieves only the Email property
        TableQuery<CustomerEntity> projectionQuery =
           TableQuery.from(CustomerEntity.class)
           .select(new String[] {"Email"});

        // Define a Entity resolver to project the entity to the Email value.
        EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-insert-or-replace-an-entity"></a>Como: inserir ou substituir uma entidade

Muitas vezes que pretende adicionar uma entidade a uma tabela sem saber se já existe na tabela. Uma operação de inserir ou substituir permite-lhe tornar um único pedido que irá inserir a entidade se não existir ou substituir um já existente, caso isso aconteça. O código seguinte edifício nos exemplos anteriores, insere ou substitui a entidade para "Walter harpa". Depois de criar uma nova entidade, este código chama o método de **TableOperation.insertOrReplace** . Este código, em seguida, liga para **Executar** no objeto **CloudTable** com a tabela e a inserir ou substituir operação de tabela como parâmetros. Para atualizar apenas uma parte de uma entidade, pode ser utilizado o método de **TableOperation.insertOrMerge** . Tenha em atenção que inserir-ou-substituir não é suportada no emulador de armazenamento local, para que este código é executado apenas quando uma conta a utilizar o serviço de tabela. Pode obter mais informações sobre como inserir ou substituir e inserir ou intercalar neste [Azure tabelas: introdução ao Upsert e projecções de consulta][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-an-entity"></a>Como: eliminar uma entidade

Pode eliminar uma entidade facilmente depois de ter obtidos-lo. Depois de serem recuperada a entidade, ligue **TableOperation.delete** com a entidade para eliminar. Em seguida, ligue para **Executar** no objeto **CloudTable** . O código seguinte obtém e elimina uma entidade de cliente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="how-to-delete-a-table"></a>Como: eliminar uma tabela

Por fim, o código seguinte elimina uma tabela a partir de uma conta de armazenamento. Uma tabela que foi eliminada vai estar disponível para ser recriadas durante um período de tempo a seguir a eliminação, normalmente menos de 40 segundos.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos de armazenamento de tabela, siga estas ligações para saber como efetuar tarefas de armazenamento mais complexas.

- [Armazenamento Azure SDK para Java][]
- [Referência SDK do cliente de armazenamento Azure][]
- [Armazenamento Azure REST API][]
- [Blogue da equipa do armazenamento Azure][]

Para obter mais informações, consulte também o [Centro de programadores do Java](/develop/java/).


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Armazenamento Azure SDK para Java]: https://github.com/azure/azure-storage-java
[Armazenamento Azure SDK para Android]: https://github.com/azure/azure-storage-android
[Referência SDK do cliente de armazenamento Azure]: http://dl.windowsazure.com/storage/javadoc/
[Armazenamento Azure REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blogue da equipa do armazenamento Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure tabelas: Introdução Upsert e projecções de consulta]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
