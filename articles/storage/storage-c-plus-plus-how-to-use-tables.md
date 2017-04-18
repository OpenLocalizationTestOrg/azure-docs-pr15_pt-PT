<properties
    pageTitle="Como utilizar o armazenamento de tabela (C++) | Microsoft Azure"
    description="Armazene dados estruturados na nuvem através do armazenamento de tabela do Azure, um arquivo de dados NoSQL."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="how-to-use-table-storage-from-c"></a>Como utilizar o armazenamento de tabela a partir do C++

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral  
Este guia mostrar-lhe como efetuar cenários comuns utilizando o serviço de armazenamento de tabela do Azure. Os exemplos são gravados no C++ e utilizam a [Biblioteca de cliente do Azure armazenamento para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Os cenários abrangidos incluem **criar e eliminar uma tabela** e **trabalhar com entidades de tabela**.

>[AZURE.NOTE] Este guia destina-se a biblioteca de cliente de armazenamento do Azure para C++ versão 1.0.0 e acima. A versão recomendada é biblioteca de armazenamento do cliente 2.2.0, que se encontra disponível através de [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="create-a-c-application"></a>Criar uma aplicação de C++  
Neste guia, irá utilizar funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação C++. Para fazê-lo, terá de instalar a biblioteca de cliente de armazenamento do Azure para C++ e criar uma conta de armazenamento Azure na sua subscrição do Azure.  

Para instalar a biblioteca de cliente de armazenamento do Azure para C++, pode utilizar os seguintes métodos:

-   **Linux:** Siga as instruções indicadas na página de [Biblioteca de cliente do Azure armazenamento para C++ ficheiro Leia-me](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
-   **Windows:** No Visual Studio, clique em **Ferramentas > Gestor de pacotes NuGet > consola do Gestor de pacote**. Escreva o seguinte comando na [consola do Gestor de pacote NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima Enter.  

        Install-Package wastorage

## <a name="configure-your-application-to-access-table-storage"></a>Configurar a aplicação para aceder ao armazenamento de tabela  
Adicione que instruções para a parte superior do ficheiro C++ em que pretende utilizar o Azure APIs de armazenamento para aceder a tabelas de incluir o seguinte procedimento:  

    #include "was/storage_account.h"
    #include "was/table.h"

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação do Azure armazenamento  
Um cliente de armazenamento Azure utiliza uma cadeia de ligação de armazenamento para armazenar os pontos finais e as credenciais para aceder a serviços de gestão de dados. Quando executar uma aplicação de cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato. Utilize o nome da sua conta de armazenamento e a tecla de acesso de armazenamento para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Para obter informações sobre contas de armazenamento e as teclas de acesso, consulte o artigo [sobre Azure contas de armazenamento](storage-create-storage-account.md). Este exemplo mostra como podem declarar um campo estático para colocar em espera a cadeia de ligação:  

    // Define the connection string with your values.
    const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para testar a sua aplicação no seu computador local baseados no Windows, pode utilizar o Azure [emulador de armazenamento](storage-use-emulator.md) é instalado com o [Azure SDK](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços de Blobs do Azure, fila de espera e tabela disponíveis no seu computador de desenvolvimento local. O exemplo seguinte mostra como podem declarar um campo estático para colocar em espera a cadeia de ligação ao seu emulador armazenamento local:  

    // Define the connection string with Azure storage emulator.
    const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar o emulador armazenamento Azure, clique no botão **Iniciar** ou prima a tecla do Windows. Comece a escrever **Emulador de armazenamento do Azure**e, em seguida, selecione **Emulador de armazenamento do Microsoft Azure** a partir da lista de aplicações.  

Exemplos que se seguem partem do pressuposto de que utilizou um destes dois métodos para obter a cadeia de ligação de armazenamento.  

## <a name="retrieve-your-connection-string"></a>Obter a cadeia de ligação  
Pode utilizar a classe de **cloud_storage_account** para representar as informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método de análise.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Em seguida, obtenha uma referência a uma classe **cloud_table_client** , tal como permite-lhe obter objetos de referência para tabelas e entidades armazenadas o serviço de armazenamento de tabela. O código seguinte cria um objeto de **cloud_table_client** utilizando o objeto de conta de armazenamento que podemos obtidos acima:  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## <a name="create-a-table"></a>Criar uma tabela
Um objeto de **cloud_table_client** permite-lhe obter objetos de referência para tabelas e entidades. O código seguinte cria um objeto de **cloud_table_client** e utiliza-lo para criar uma nova tabela.

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();  

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie um novo objeto **table_entity** e passá-lo para **table_operation::insert_entity**. O código seguinte utiliza o nome do cliente primeiro como a chave de linha e o apelido como chave partição. Em conjunto, partição de uma entidade e chave de linha identificam exclusivamente a entidade na tabela. Entidades com a mesma chave partição podem ser consultadas mais rápido daqueles com as teclas de partição diferente, mas a utilização de chaves de partição diversificados permite maior escalabilidade operação paralelas. Para mais informações, consulte o artigo [desempenho de armazenamento do Microsoft Azure e lista de verificação escalabilidade](storage-performance-checklist.md).

O código seguinte cria uma nova instância do **table_entity** com alguns dados de cliente a ser armazenado. O código seguinte efetua **table_operation::insert_entity** para criar um objecto **table_operation** para inserir uma entidade numa tabela e associa a entidade de tabela nova com o mesmo. Por fim, o código liga-lhe o método execute no objeto **cloud_table** . E o novo **table_operation** envia um pedido para o serviço de tabela para inserir a nova entidade de cliente na tabela "utilizadores".  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Retrieve a reference to a table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table if it doesn't exist.
    table.create_if_not_exists();

    // Create a new customer entity.
    azure::storage::table_entity customer1(U("Harp"), U("Walter"));

    azure::storage::table_entity::properties_type& properties = customer1.properties();
    properties.reserve(2);
    properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

    properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

    // Create the table operation that inserts the customer entity.
    azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

    // Execute the insert operation.
    azure::storage::table_result insert_result = table.execute(insert_operation);

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Pode inserir um lote de entidades para o serviço de tabela numa operação de escrita. O código seguinte cria um objeto de **table_batch_operation** e, em seguida, adiciona que três inserir operações à mesma. Cada operação de inserir é adicionada ao criar um novo objeto entidade, definir os valores e, em seguida, chamar o método de inserir no objeto **table_batch_operation** para associar a entidade de uma nova operação de inserir. Em seguida, **cloud_table.execute** chama-se para executar a operação.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define a batch operation.
    azure::storage::table_batch_operation batch_operation;

    // Create a customer entity and add it to the table.
    azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

    azure::storage::table_entity::properties_type& properties1 = customer1.properties();
    properties1.reserve(2);
    properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
    properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

    // Create another customer entity and add it to the table.
    azure::storage::table_entity customer2(U("Smith"), U("Ben"));

    azure::storage::table_entity::properties_type& properties2 = customer2.properties();
    properties2.reserve(2);
    properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
    properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

    // Create a third customer entity to add to the table.
    azure::storage::table_entity customer3(U("Smith"), U("Denise"));

    azure::storage::table_entity::properties_type& properties3 = customer3.properties();
    properties3.reserve(2);
    properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
    properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

    // Add customer entities to the batch insert operation.
    batch_operation.insert_or_replace_entity(customer1);
    batch_operation.insert_or_replace_entity(customer2);
    batch_operation.insert_or_replace_entity(customer3);

    // Execute the batch operation.
    std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Algumas coisas a nota sobre a operações batch:  

-   Pode executar até 100 inserir, eliminar, impressão em série, substituir, inserir ou-em série e inserir ou substituir operações em qualquer combinação num único lote.  
-   Uma operação de lote pode ter uma operação de obter, se for a única operação no lote de.  
-   Todas as entidades numa operação de lote único tem de ter a mesma chave partição.  
-   Uma operação batch está limitada a uma carga útil dados 4 MB.  

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição
Para uma tabela para todas as entidades numa partição de consulta, utilize um objeto de **table_query** . Exemplo de código seguinte especifica um filtro para entidades onde 'Santos' são a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta à consola.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Print the fields for each customer.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

A consulta neste exemplo mostra todas as entidades que correspondem aos critérios de filtro. Se tiver tabelas grandes e precisar de transferir as entidades de tabela frequentemente, recomendamos que armazena os dados no blobs do Azure armazenamento em vez disso.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Obter um intervalo de entidades de uma partição
Se não quiser todas as entidades uma partição de consulta, pode especificar um intervalo, combinando o filtro de chave partição com um filtro de chave de linha. Exemplo de código seguinte utiliza dois filtros para obter todas as entidades na partição Silva onde a chave de linha (primeiro nome) comece por uma letra anterior E do alfabeto e, em seguida, imprime os resultados da consulta.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create the table query.
    azure::storage::table_query query;

    query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
        azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
        azure::storage::query_comparison_operator::equal, U("Smith")),
        azure::storage::query_logical_operator::op_and,
        azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Loop through the results, displaying information about the entity.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        const azure::storage::table_entity::properties_type& properties = it->properties();

        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
            << U(", Property1: ") << properties.at(U("Email")).string_value()
            << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
    }  

## <a name="retrieve-a-single-entity"></a>Obter uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O código seguinte utiliza **table_operation::retrieve_entity** para especificar o cliente 'Paula Bento'. Este método devolve apenas uma entidade, em vez de uma coleção de e o valor devolvido é no **table_result**. Especificar teclas partição e linha numa consulta é a forma mais rápida para obter uma única entidade a partir do serviço de tabela.  

    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Output the entity.
    azure::storage::table_entity entity = retrieve_result.entity();
    const azure::storage::table_entity::properties_type& properties = entity.properties();

    std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## <a name="replace-an-entity"></a>Substituir uma entidade
Para substituir uma entidade, recuperá-la a partir do serviço de tabela, modificar o objeto de entidade e, em seguida, guarde as alterações novamente para o serviço de tabela. O código seguinte altera um cliente existente telefone número e endereço de e-mail. Em vez de chamar **table_operation::insert_entity**, este código utiliza **table_operation::replace_entity**. Isto faz com que a entidade totalmente ser substituído no servidor, a menos que a entidade no servidor foi alterado desde que foi obtido, caso em que a operação irá falhar. Esta falha é impedir que a aplicação a partir de substituir inadvertidamente uma alteração feita entre a obtenção e a atualização por outro componente da sua aplicação. É o processamento inicial desta falha obter a entidade novamente, faça as suas alterações (se ainda válido) e, em seguida, executar outra **table_operation::replace_entity** operação. A secção seguinte irá mostrar-lhe como substituir este comportamento.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Replace an entity.
    azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
    properties_to_replace.reserve(2);

    // Specify a new phone number.
    properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

    // Specify a new email address.
    properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

    // Create an operation to replace the entity.
    azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result replace_result = table.execute(replace_operation);

## <a name="insert-or-replace-an-entity"></a>Inserir-ou-substituir uma entidade
operações de **table_operation::replace_entity** falhará se a entidade foi alterada desde que foi obtida a partir do servidor. Além disso, terá de obter a entidade do servidor pela primeira vez na ordem para **table_operation::replace_entity** ser efetuada com êxito. Por vezes, no entanto, não sabe se a entidade existe no servidor e os atuais valores armazenados no mesmo são irrelevantes — a atualização da substituam-los a todos. Para realizar esta tarefa, teria de utilizar uma operação de **table_operation::insert_or_replace_entity** . Esta operação insere a entidade se este não existe ou substitui-se de que faz, independentemente de quando é a última actualização efetuada. No exemplo seguinte de código, ainda é obtida a entidade de cliente para Paula Bento, mas este será guardado, em seguida, novamente para o servidor através do **table_operation::insert_or_replace_entity**. Serão substituídas atualizações efetuadas à entidade entre a obtenção e a operação de atualização.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Insert-or-replace an entity.
    azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
    azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

    properties_to_insert_or_replace.reserve(2);

    // Specify a phone number.
    properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

    // Specify an email address.
    properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

    // Create an operation to insert-or-replace the entity.
    azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

    // Submit the operation to the Table service.
    azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto de propriedades entidade de consulta  
Uma consulta a uma tabela pode obter propriedades apenas alguns a partir de uma entidade. A consulta o código seguinte utiliza o método de **table_query::set_select_columns** para devolver os endereços de e-mail de entidades na tabela.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Define the query, and select only the Email property.
    azure::storage::table_query query;
    std::vector<utility::string_t> columns;

    columns.push_back(U("Email"));
    query.set_select_columns(columns);

    // Execute the query.
    azure::storage::table_query_iterator it = table.execute_query(query);

    // Display the results.
    azure::storage::table_query_iterator end_of_results;
    for (; it != end_of_results; ++it)
    {
        std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

        const azure::storage::table_entity::properties_type& properties = it->properties();
        for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
        {
            std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
        }

        std::wcout << std::endl;
    }

>[AZURE.NOTE] Consultar alguns propriedades a partir de uma entidade é uma operação mais eficaz de que a obtenção de todas as propriedades.

## <a name="delete-an-entity"></a>Eliminar uma entidade
Pode eliminar uma entidade facilmente depois de ter obtidos-lo. Depois de serem recuperada a entidade, ligue **table_operation::delete_entity** com a entidade para eliminar. Em seguida, ligue para o método de **cloud_table.execute** . O código seguinte obtém e elimina uma entidade com uma chave de partição de "Silva" e a chave de "João" para uma linha.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);  

## <a name="delete-a-table"></a>Eliminar uma tabela
Por fim, o exemplo seguinte de código elimina uma tabela a partir de uma conta de armazenamento. Uma tabela que foi eliminada vai estar disponível para ser recriada durante um período de tempo após a eliminação.  

    // Retrieve the storage account from the connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the table client.
    azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

    // Create a cloud table object for the table.
    azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
    azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

    // Create an operation to delete the entity.
    azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

    // Submit the delete operation to the Table service.
    azure::storage::table_result delete_result = table.execute(delete_operation);

## <a name="next-steps"></a>Próximos passos
Agora que aprendeu os princípios básicos de armazenamento de tabela, siga estas ligações para mais informações sobre o armazenamento do Windows Azure:  

-   [Como utilizar o armazenamento de Blobs do C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Como utilizar o armazenamento de fila de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Lista de recursos de armazenamento Azure na C++](storage-c-plus-plus-enumeration.md)
-   [Biblioteca de armazenamento do cliente para referência C++](http://azure.github.io/azure-storage-cpp)
-   [Azure documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/)
