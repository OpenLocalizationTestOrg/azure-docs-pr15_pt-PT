<properties
    pageTitle="Como utilizar o armazenamento de BLOBs (armazenamento de objeto) a partir do PHP | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com armazenamento de Blobs do Azure (armazenamento de objeto)."
    documentationCenter="php"
    services="storage"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="how-to-use-blob-storage-from-php"></a>Como utilizar o armazenamento de Blobs do PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Armazenamento de Blobs do Azure é um serviço que armazena dados não estruturados na nuvem como objetos/blobs. Armazenamento de BLOBs pode armazenar qualquer tipo de texto ou dados binários, como um documento, ficheiro de multimédia ou instalador de aplicações. Armazenamento de BLOBs é também conhecido como o armazenamento do objeto.

Este guia mostra-lhe como efetuar cenários comuns utilizando o serviço de Blobs do Azure. Os exemplos são gravados no PHP e utilizar o [SDK do Azure para PHP] [download]. Os cenários abrangidos incluem **a carregar**, **Listar**, **Transferir**e **Eliminar** blobs. Para mais informações sobre blobs, consulte a secção [os passos seguintes](#next-steps) .

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Criar uma aplicação de PHP

O requisito só para criar uma aplicação de PHP que acede ao serviço de Blobs do Azure é de referência de classes no Azure SDK para PHP a partir do seu código. Pode utilizar as ferramentas de desenvolvimento para criar a sua aplicação, incluindo o bloco de notas.

Neste guia, utilize as funcionalidades de serviço, que podem ser chamadas dentro de uma aplicação PHP localmente ou no código em execução no interior de uma função Azure web, a função de trabalho ou o Web site.

## <a name="get-the-azure-client-libraries"></a>Obter as bibliotecas do cliente Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-blob-service"></a>Configurar a aplicação para aceder ao serviço de BLOBs

Utilizar o serviço de Blobs do Azure APIs, terá de:

1. O ficheiro de Carregador automático com a instrução de [require_once] , de referência e
2. Fazer referência a qualquer classes que poderá utilizar.

O exemplo seguinte mostra como incluir o arquivo automático e a classe de **ServicesBuilder** de referência.

> [AZURE.NOTE] Este exemplo (e outros exemplos neste artigo) partem do princípio de que instalou as bibliotecas do cliente PHP para Azure através do compositor. Se tiver instalado as bibliotecas manualmente, tem de fazer referência a `WindowsAzure.php` ficheiro Carregador automático.

    require_once 'vendor/autoload.php';
    use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, o `require_once` declaração será apresentada sempre, mas só as classes necessárias para o exemplo para executar referenciadas.

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento Azure

Para criar uma instância de um cliente do serviço de Blobs do Azure, primeiro tem de ter uma cadeia de ligação válida. O formato para a cadeia de ligação de serviço de BLOBs é:

Para aceder a um serviço direto:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para aceder a emulador de armazenamento:

    UseDevelopmentStorage=true


Para criar qualquer cliente do serviço Azure, é necessário utilizar a classe de **ServicesBuilder** . Pode:

* Passar a cadeia de ligação diretamente ao mesmo ou
* Utilize o **CloudConfigurationManager (CCM)** para verificar a várias origens externas para a cadeia de ligação:
    * Por predefinição, vem com o suporte para uma origem externa - ambientais variáveis.
    * Pode adicionar novas fontes através do prolongamento a classe de **ConnectionStringSource** .

Para os exemplos descritos aqui, a cadeia de ligação será transmitida diretamente.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <a name="create-a-container"></a>Criar um contentor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Um objeto de **BlobRestProxy** permite-lhe criar um contentor de Blobs com o método de **createContainer** . Ao criar um contentor, pode definir opções no contentor, mas ao fazê-lo por isso, não é necessário. (Exemplo abaixo mostra como definir a lista de controlo de acesso do contentor (ACL) e os metadados do contentor.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
    use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPTIONAL: Set public access policy and metadata.
    // Create container options object.
    $createContainerOptions = new CreateContainerOptions();

    // Set public access policy. Possible values are
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:
    // Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
    // request, but cannot enumerate containers within the storage account.
    //
    // BLOBS_ONLY:
    // Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
    // anonymous request.
    // If this value is not specified in the request, container data is
    // private to the account owner.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Create container.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Chamar **setPublicAccess (PublicAccessType::CONTAINER\_AND\_BLOBS)** torna os dados de contentor e blob acessível através de pedidos anónimos. Chamar **setPublicAccess(PublicAccessType::BLOBS_ONLY)** torna apenas blob dados acessível através de pedidos anónimos. Para mais informações sobre o contentor ACL, consulte o artigo [Definir contentor ACL (REST API)][container-acl].

Para mais informações sobre códigos de erro do serviço de BLOBs, consulte o artigo [Códigos de erro do serviço de BLOBs][error-codes].

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Para carregar um ficheiro como um blob, utilize o método **BlobRestProxy -> createBlockBlob** . Esta operação cria o blob se este não existe ou substitui-se de que faz. Exemplo de código abaixo assume que o contentor já foram criado e utiliza [fopen] [ fopen] para abrir o ficheiro como uma sequência.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Upload blob
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tenha em atenção que a amostra anterior carrega um blob como uma sequência. No entanto, um blob pode também ser carregado como uma cadeia a utilizar, por exemplo, o [ficheiro\_obter\_conteúdo] [ file_get_contents] função. Para executar esta tarefa utilizando o exemplo anterior, altere `$content = fopen("c:\myfile.txt", "r");` para `$content = file_get_contents("c:\myfile.txt");`.

## <a name="list-the-blobs-in-a-container"></a>Blobs num contentor de lista

Para listar blobs num contentor, utilize o método **BlobRestProxy -> listBlobs** com um **foreach** ciclo para percorra o resultado. O código seguinte apresenta o nome de cada blob como saída num contentor e apresenta o respetivo URI para o browser.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // List blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();

        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }


## <a name="download-a-blob"></a>Transferir um blob

Para transferir um blob, chamar o método **BlobRestProxy -> getBlob** , em seguida, chamar o método de **getContentStream** no objeto **GetBlobResult** resultante.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Get blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Tenha em atenção que o exemplo acima obtém um blob como um recurso de sequência (o comportamento predefinido). No entanto, pode utilizar o [sequência\_obter\_conteúdo] [ stream-get-contents] função para converter a sequência de devolvida uma cadeia.

## <a name="delete-a-blob"></a>Eliminar um blob

Para eliminar um blob, passe o nome do contentor e o nome de BLOBs para **BlobRestProxy -> deleteBlob**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete blob.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="delete-a-blob-container"></a>Eliminar um contentor blob

Por fim, para eliminar um contentor blob, passe o nome do contentor para **BlobRestProxy -> deleteContainer**.

    require_once 'vendor/autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use MicrosoftAzure\Storage\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Delete container.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/library/azure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para o serviço de Blobs do Azure, siga estas ligações para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [blogue da equipa do armazenamento do Windows Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Veja o [exemplo de Blobs do PHP bloco](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Veja o [exemplo de Blobs do PHP página](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md)
 
Para mais informações, consulte o artigo também do [Centro de programadores do PHP](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents
