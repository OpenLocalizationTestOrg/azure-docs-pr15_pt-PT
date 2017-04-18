<properties
    pageTitle="Como utilizar o armazenamento de tabela a partir do PHP | Microsoft Azure"
    description="Saiba como utilizar o serviço de tabela do PHP para criar e eliminar uma tabela e inserir, eliminar e consultar a tabela."
    services="storage"
    documentationCenter="php"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-table-storage-from-php"></a>Como utilizar o armazenamento de tabela a partir do PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Este guia mostra-lhe como efetuar cenários comuns utilizando o serviço de tabela do Azure. Os exemplos são gravados no PHP e utilizar o [SDK do Azure para PHP][download]. Os cenários abrangidos incluem **criar e eliminar uma tabela e inserir, eliminar e consultar entidades numa tabela**. Para mais informações sobre o serviço de tabela do Azure, consulte a secção [os passos seguintes](#next-steps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação de PHP

O requisito só para criar uma aplicação de PHP acede ao serviço de tabela do Azure é de referência de classes no Azure SDK para PHP a partir do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a sua aplicação, incluindo o bloco de notas.

Neste guia, utilizar funcionalidades de serviço de tabela que podem ser chamadas de dentro de uma aplicação PHP localmente ou no código em execução no interior de uma função Azure web, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas do cliente Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Configurar a aplicação para aceder ao serviço de tabela

Utilizar o serviço de tabela do Azure APIs, terá de:

1. Referenciar o ficheiro de Carregador automático utilizando o [require_once] [ require_once] instrução, e
2. Fazer referência a qualquer classes que poderá utilizar.

O exemplo seguinte mostra como incluir o arquivo automático e a classe de **ServicesBuilder** de referência.

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) partem do princípio de que instalou as bibliotecas do cliente PHP para Azure através do compositor. Se tiver instalado as bibliotecas manualmente, tem de fazer referência a <code>WindowsAzure.php</code> ficheiro Carregador automático.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, o `require_once` declaração sempre é apresentada, mas só as classes necessárias para o exemplo para executar referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento Azure

Para criar uma instância do cliente do serviço de uma tabela do Azure, primeiro tem de ter uma cadeia de ligação válida. O formato para a cadeia de ligação de serviço de tabela é:

Para aceder a um serviço direto:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para aceder ao armazenamento de emulador:

    UseDevelopmentStorage=true


Para criar qualquer cliente do serviço Azure, é necessário utilizar a classe de **ServicesBuilder** . Pode:

* passar a cadeia de ligação diretamente ao mesmo ou
* Utilize o **CloudConfigurationManager (CCM)** para verificar a várias origens externas para a cadeia de ligação:
    * Por predefinição, vem com o suporte para uma origem externa - variáveis de ambiente
    * Pode adicionar novas fontes através do prolongamento a classe de **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de ligação será transmitida diretamente.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## <a name="create-a-table"></a>Criar uma tabela

Um objeto de **TableRestProxy** permite-lhe criar uma tabela com o método de **createTable** . Ao criar uma tabela, pode definir o tempo de serviço de limite da tabela. (Para mais informações sobre o limite de tempo de serviço de tabela, consulte o artigo [Definir tempos limite das operações de serviço de tabela][table-service-timeouts].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
    }

Para informações sobre as restrições sobre nomes de tabelas, consulte o artigo [Compreender o modelo de dados do serviço de tabela][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, criar um novo objeto **entidade** e transmiti-lo para **TableRestProxy -> insertEntity**. Tenha em atenção que quando cria uma entidade, tem de especificar uma `PartitionKey` e `RowKey`. Estes são os identificadores exclusivos para uma entidade e são valores que podem ser consultados muito for mais rápido outras propriedades de entidade. O sistema utiliza `PartitionKey` para distribuir automaticamente entidades a tabela ao longo do demasiados nós de armazenamento. Entidades com o mesmo `PartitionKey` são armazenadas no mesmo nó. (Executar operações em várias entidades armazenadas no mesmo nó melhor do que em entidades armazenadas em nós diferentes.) O `RowKey` é o ID exclusivo de uma entidade dentro de uma partição.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $entity = new Entity();
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate",
                         EdmType::DATETIME,
                         new DateTime("2012-11-05T08:15:00-08:00"));
    $entity->addProperty("Location", EdmType::STRING, "Home");

    try{
        $tableRestProxy->insertEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Para obter informações sobre as propriedades da tabela e os tipos, consulte o artigo [Compreender o modelo de dados do serviço de tabela][table-data-model].

A classe de **TableRestProxy** oferece dois métodos alternativos para inserir entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para utilizar estes métodos, criar uma nova **entidade** e transmiti-lo como um parâmetro para ambos os métodos. Cada método irá inserir a entidade se não existir. Se a entidade já existir, **insertOrMergeEntity** valores de propriedade é atualizado se as propriedades já existem e adiciona novas propriedades, se não existirem, enquanto **insertOrReplaceEntity** substitui completamente uma entidade existente. O exemplo seguinte mostra como utilizar **insertOrMergeEntity**. Se a entidade com `PartitionKey` "tasksSeattle" e `RowKey` "1" já não existir, será inserida. No entanto, se tiver sido inserido anteriormente (conforme mostrado no exemplo acima), o `DueDate` propriedade será atualizada bem como a `Status` propriedade será adicionada. O `Description` e `Location` propriedades também são atualizadas, mas com valores que eficazmente deixá-los inalterado. Se estas duas propriedades último não foram adicionadas conforme mostrado no exemplo, mas existia na entidade de destino, os respetivos valores existentes seriam que permaneça inalterados.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    //Create new entity.
    $entity = new Entity();

    // PartitionKey and RowKey are required.
    $entity->setPartitionKey("tasksSeattle");
    $entity->setRowKey("1");

    // If entity exists, existing properties are updated with new values and
    // new properties are added. Missing properties are unchanged.
    $entity->addProperty("Description", null, "Take out the trash.");
    $entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
    $entity->addProperty("Location", EdmType::STRING, "Home");
    $entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="retrieve-a-single-entity"></a>Obter uma única entidade

O método **TableRestProxy -> getEntity** permite-lhe obter uma única entidade consultando relativamente a sua `PartitionKey` e `RowKey`. No exemplo abaixo, a tecla partição `tasksSeattle` e chave de linha `1` são transmitidos para o método de **getEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <a name="retrieve-all-entities-in-a-partition"></a>Obter todas as entidades numa partição

Consultas de entidade são construídas através dos filtros de (para obter mais informações, consulte o artigo [consultar tabelas e entidades][filters]). Para obter todas as entidades na partição, utilize o filtro "PartitionKey eq *nome_partição*". O exemplo seguinte mostra como obter todas as entidades na `tasksSeattle` partição, passando um filtro para o método de **queryEntities** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Obter um subconjunto de entidades de uma partição

O mesmo padrão utilizado no exemplo anterior pode ser utilizado para obter qualquer subconjunto de entidades de uma partição. O subconjunto de entidades obtiver são determinadas pelo filtro que utiliza (para obter mais informações, consulte o artigo [consultar tabelas e entidades][filters]). O exemplo seguinte mostra como utilizar um filtro para obter todas as entidades com um específico `Location` e um `DueDate` inferiores a uma data especificada.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <a name="retrieve-a-subset-of-entity-properties"></a>Obter um subconjunto de propriedades entidade

Uma consulta pode obter um subconjunto de propriedades entidade. Esta técnica, denominada *projecções*, reduz largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades muito grandes. Para especificar uma propriedade a serem obtidos, passe o nome da propriedade para o método **addSelectField -> a consulta** . Pode ligar a este método várias vezes para adicionar mais propriedades. Depois de executar **TableRestProxy -> queryEntities**, as entidades devolvidas só terá as propriedades selecionadas. (Se pretender que seja devolvido um subconjunto de entidades de tabela, utilize um filtro conforme apresentado nas consultas acima.)

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    // All entities in the table are returned, regardless of whether
    // they have the Description field.
    // To limit the results returned, use a filter.
    $entities = $result->getEntities();

    foreach($entities as $entity){
        $description = $entity->getProperty("Description")->getValue();
        echo $description."<br />";
    }

## <a name="update-an-entity"></a>Atualizar uma entidade

Pode ser atualizada uma entidade existente utilizando os métodos de **entidade -> DefinirPropriedade** e **entidade -> addProperty** na entidade e, em seguida, chamar **TableRestProxy -> updateEntity**. O exemplo seguinte obtém uma entidade, modifica uma propriedade, remove outra propriedade e adiciona uma nova propriedade. Tenha em atenção que pode remover uma propriedade ao definir o valor **Nulo**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

    $entity = $result->getEntity();

    $entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

    $entity->setPropertyValue("Location", null); //Removed Location.

    $entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-an-entity"></a>Eliminar uma entidade

Para eliminar uma entidade, passar o nome da tabela e a entidade `PartitionKey` e `RowKey` para o método **TableRestProxy -> deleteEntity** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tenha em atenção para os controlos de simultaneidade, pode definir a Etag para uma entidade a ser eliminada utilizando o método **DeleteEntityOptions -> setEtag** e passar o objeto **DeleteEntityOptions** para **deleteEntity** como uma quarta parâmetro.

## <a name="batch-table-operations"></a>Operações de tabela batch

O método **TableRestProxy -> lote** permite-lhe executar várias operações num único pedido. O padrão de aqui envolve adicionar operações a **BatchRequest** objeto e, em seguida, passando o objeto **BatchRequest** para o método **TableRestProxy -> lote** . Para adicionar uma operação a um objeto de **BatchRequest** , pode ligar várias vezes a qualquer um dos seguintes métodos:

* **addInsertEntity** (adiciona uma operação de insertEntity)
* **addUpdateEntity** (adiciona uma operação de updateEntity)
* **addMergeEntity** (adiciona uma operação de mergeEntity)
* **addInsertOrReplaceEntity** (adiciona uma operação de insertOrReplaceEntity)
* **addInsertOrMergeEntity** (adiciona uma operação de insertOrMergeEntity)
* **addDeleteEntity** (adiciona uma operação de deleteEntity)

O exemplo seguinte mostra como executar operações de **insertEntity** e **deleteEntity** num único pedido:

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;
    use MicrosoftAzure\Storage\Table\Models\Entity;
    use MicrosoftAzure\Storage\Table\Models\EdmType;
    use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    // Create list of batch operation.
    $operations = new BatchOperations();

    $entity1 = new Entity();
    $entity1->setPartitionKey("tasksSeattle");
    $entity1->setRowKey("2");
    $entity1->addProperty("Description", null, "Clean roof gutters.");
    $entity1->addProperty("DueDate",
                          EdmType::DATETIME,
                          new DateTime("2012-11-05T08:15:00-08:00"));
    $entity1->addProperty("Location", EdmType::STRING, "Home");

    // Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

    // Add operation to list of batch operations.
    $operations->addDeleteEntity("mytable", "tasksSeattle", "1");

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Para mais informações sobre lotes operações de tabela, consulte o artigo [Efetuar operações de grupo entidade][entity-group-transactions].

## <a name="delete-a-table"></a>Eliminar uma tabela

Por fim, para eliminar uma tabela, passe o nome da tabela para o método **TableRestProxy -> deleteTable** .

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para o serviço de tabela do Azure, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [blogue da equipa do armazenamento do Windows Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para obter mais informações, consulte também o [Centro de programadores do PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
