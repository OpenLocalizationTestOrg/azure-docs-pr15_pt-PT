<properties
    pageTitle="Como utilizar o armazenamento de Blobs do Node.js | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com armazenamento de Blobs do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>Como utilizar o armazenamento de Blobs do Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como efetuar cenários comuns a utilizar o armazenamento de Blobs. Os exemplos são escritos através da API Node.js. Os cenários abrangidos incluem como carregar, lista, transferir e eliminar blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar uma aplicação de Node.js

Para obter instruções sobre como criar uma aplicação de Node.js, consulte o artigo [criar uma aplicação web do Node.js na aplicação de serviço de Azure], [criar e implementar uma aplicação de Node.js para um serviço em nuvem Azure] -- utilizar o Windows PowerShell ou [criar e implementar uma aplicação web do Node.js para Azure utilizando Web matriz].

## <a name="configure-your-application-to-access-storage"></a>Configurar a aplicação para aceder ao armazenamento

Para utilizar o armazenamento Azure, precisa do SDK do armazenamento Azure para Node.js, que inclui um conjunto de bibliotecas de conveniência que comunicar com os serviços de resto de armazenamento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Utilizar o Gestor de pacote nó (NPM) para obter o pacote

1.  Utilize uma interface de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix), para navegar para a pasta onde a criou a aplicação de exemplo.

2.  Na janela de comandos, escreva a **npm instalar o armazenamento do Windows azure** . Resultado do comando é semelhante ao seguinte exemplo de código.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Pode executar o comando **ls** para verificar que manualmente uma **nó\_módulos** pasta foi criada. Nessa pasta, localize o pacote de **armazenamento do Windows azure** , que contém as bibliotecas que precisa de aceder ao armazenamento.

### <a name="import-the-package"></a>Importar o pacote

Utilizar o bloco de notas ou outro editor de texto, adicione o seguinte para a parte superior do ficheiro **server.js** da aplicação onde tenciona utilizar o armazenamento:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Windows Azure

O módulo Azure vai ler as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`, ou `AZURE_STORAGE_CONNECTION_STRING`, para as informações necessárias para ligar à sua conta de armazenamento Azure. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações de conta quando chamar **createBlobService**.

Para obter um exemplo de definir as variáveis de ambiente no [Portal do Azure](https://portal.azure.com) para uma aplicação Azure web, consulte o artigo [Node.js web app utilizando o serviço de tabela do Azure].

## <a name="create-a-container"></a>Criar um contentor

O objeto **BlobService** permite-lhe trabalhar com contentores e blobs. O código seguinte cria um objeto de **BlobService** . Adicione o seguinte na parte superior do **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Pode aceder a um blob de forma anónima utilizando **createBlobServiceAnonymous** e fornecer o endereço do anfitrião. Por exemplo, utilizar `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Para criar um novo contentor, utilize **createContainerIfNotExists**. Exemplo de código seguinte cria um novo contentor denominado 'mycontainer':

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

Se o contentor recentemente for criado, `result.created` é verdadeira. Se já existir no contentor, `result.created` for falsa. `response`contém informações sobre a operação, incluindo as informações de ETag para o contentor.

### <a name="container-security"></a>Segurança do contentor

Por predefinição, os novos contentores são privados e não podem ser acedidos de forma anónima. Para tornar o contentor público para que possa aceder forma anónima, pode definir nível de acesso no contentor para **blob** ou **contentor**.

* **blob** - permita o acesso anónimo de leitura para blob conteúdos e metadados dentro deste contentor, mas não para metadados de contentor como listar todos os blobs dentro de um contentor

* **contentor** - permita o acesso anónimo de leitura para blob conteúdos e metadados, bem como contentor metadados

Exemplo de código seguinte demonstra a definir o nível de acesso para **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

Em alternativa, pode modificar o nível de acesso de um contentor utilizando **setContainerAcl** para especificar o nível de acesso. O exemplo seguinte de código altera o nível de acesso ao contentor:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

O resultado contém informações sobre a operação, incluindo o **ETag** atual para o contentor.

### <a name="filters"></a>Filtros

Pode aplicar filtragem de operações opcionais para operações realizadas utilizando **BlobService**. Operações de filtragem pode incluir registo, automaticamente a repetir, etc. Filtros são objetos que implementam um método com a assinatura:

    function handle (requestOptions, next)

Após fazer a sua pré-processamento no menu Opções de pedido, o método precisa de chamar "next", prisma uma chamada de retorno com a assinatura seguinte:

    function (returnObject, finalCallback, next)

Esta chamada de retorno e depois de processamento de returnObject (a resposta do pedido de servidor), a chamada de retorno tem invocar, em seguida, se existir para continuar a outros filtros de processamento ou simplesmente invocar finalCallback para terminar a chamada do serviço.

Dois filtros implementam lógica de repetição são incluídos com o SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. A seguinte cria um objeto de **BlobService** que utiliza o **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor

Existem três tipos de blobs: bloquear blobs, página blobs e acrescentar blobs. Bloquear blobs permitem que a mais eficientemente carregar dados grandes. Acrescentar blobs estão otimizados para acrescentar operações. Página blobs estão otimizados para operações de leitura/escrita. Para mais informações, consulte o artigo [Noções sobre bloco Blobs, acrescentar Blobs e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Bloquear blobs

Para carregar os dados para um blob bloco, utilize o seguinte:

* **createBlockBlobFromLocalFile** - cria um novo blob de bloco e carrega o conteúdo de um ficheiro

* **createBlockBlobFromStream** - cria um novo blob de bloco e os conteúdos de uma sequência dos carregamentos pendentes

* **createBlockBlobFromText** - cria um novo blob de bloco e carrega o conteúdo de uma cadeia

* **createWriteStreamToBlockBlob** - fornece uma sequência de escrita para um blob de bloco

O exemplo seguinte de código carrega o conteúdo do ficheiro de **teste. txt** para **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

O `result` devolvido por estes métodos contém informações sobre a operação, tal como o **ETag** do blob.

### <a name="append-blobs"></a>Acrescentar blobs

Para carregar os dados para um novo blob de acréscimo, utilize o seguinte:

* **createAppendBlobFromLocalFile** - cria um novo blob de acréscimo e carrega o conteúdo de um ficheiro

* **createAppendBlobFromStream** - cria um novo blob de acréscimo e os conteúdos de uma sequência dos carregamentos pendentes

* **createAppendBlobFromText** - cria um novo blob de acréscimo e carrega o conteúdo de uma cadeia

* **createWriteStreamToNewAppendBlob** - cria um novo blob de acréscimo e, em seguida, fornece uma sequência de escrita

O exemplo seguinte de código carrega o conteúdo do ficheiro de **teste. txt** para **myappendblob**.

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Para anexar um bloco para um blob acrescentar existente, utilize o seguinte:

* **appendFromLocalFile** - acrescentar os conteúdos de um ficheiro a um existente blob de acréscimo

* **appendFromStream** - acrescentar os conteúdos de uma sequência para um existente blob de acréscimo

* **appendFromText** - acrescentar os conteúdos de uma cadeia para um existente blob de acréscimo

* **appendBlockFromStream** - acrescentar os conteúdos de uma sequência para um existente blob de acréscimo

* **appendBlockFromText** - acrescentar os conteúdos de uma cadeia para um existente blob de acréscimo

> [AZURE.NOTE] appendFromXXX APIs irá fazer algumas validação do lado do cliente a falha rápido evitar unncessary chamada do servidor. não appendBlockFromXXX.

O exemplo seguinte de código carrega o conteúdo do ficheiro de **teste. txt** para **myappendblob**.

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>Página blobs

Para carregar os dados para um blob de página, utilize o seguinte:

* **createPageBlob** - cria um novo blob de página de comprimento específico

* **createPageBlobFromLocalFile** - cria um novo blob de página e os carregamentos pendentes os conteúdos de um ficheiro

* **createPageBlobFromStream** - cria um novo blob de página e os conteúdos de uma sequência dos carregamentos pendentes

* **createWriteStreamToExistingPageBlob** - fornece uma sequência de escrita para um existente blob de página

* **createWriteStreamToNewPageBlob** - cria um novo blob de página e, em seguida, fornece uma sequência de escrita

O exemplo seguinte de código carrega os conteúdos do ficheiro de **teste. txt** para **mypageblob**.

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] Página blobs é composta por 512 byte 'páginas'. Receberá um erro ao carregar dados com um tamanho de que não for um múltiplo de 512.

## <a name="list-the-blobs-in-a-container"></a>Blobs num contentor de lista

Para listar blobs num contentor, utilize o método de **listBlobsSegmented** . Se gostaria devolver blobs com um prefixo específico, utilize **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

O `result` contém um `entries` coleção, que é uma matriz de objetos que descrevem cada blob. Se não podem ser devolvidos todos os blobs, o `result` também fornece uma `continuationToken`, que pode utilizar como segundo parâmetro para obter entradas adicionais.

## <a name="download-blobs"></a>Transferir blobs

Para transferir dados a partir de um blob, utilize o seguinte:

* **getBlobToLocalFile** - escreve o conteúdo de BLOBs para o ficheiro

* **getBlobToStream** - escreve o conteúdo de BLOBs para uma sequência

* **getBlobToText** - escreve o conteúdo de BLOBs para uma cadeia

* **createReadStream** - fornece uma sequência de ler a partir do blob

O exemplo de código seguinte demonstra utilizar **getBlobToStream** para transferir o conteúdo do blob **myblob** e armazená-lo para o ficheiro de **saída** , utilizando uma sequência:

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

O `result` contém informações sobre o blob, incluindo informações de **ETag** .

## <a name="delete-a-blob"></a>Eliminar um blob

Por fim, para eliminar um blob, contacte o **deleteBlob**. O exemplo seguinte de código elimina o blob denominado **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Acesso em simultâneo

Para suportar o acesso em simultâneo para um blob a partir de vários clientes ou várias instâncias de processo, pode utilizar **ETags** ou **concessões**.

* **Etag** - fornece uma forma de detetar que o blob ou contentor terem sido modificada por outro processo

* **Locação financeira** - fornece uma forma de obter exclusivo, renováveis, escrever ou eliminar o acesso a um blob durante um período de tempo

### <a name="etag"></a>ETag

Utilizar ETags se precisar de permitir várias instâncias ou clientes escrever o bloco de página ou Blob Blob em simultâneo. O ETag permite-lhe determinar se o contentor ou blob foi modificado desde inicialmente ler ou criado, o que permite-lhe evitar a substituição alterações consolidadas por outro processo ou cliente.

Pode definir condições ETag utilizando opcional `options.accessConditions` parâmetro. O exemplo seguinte de código apenas os carregamentos pendentes o ficheiro de **teste. txt** se o blob já existe e tem o valor ETag contido por `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

Quando estiver a utilizar ETags, o padrão de geral é:

1. Obter o ETag como resultado de uma operação de obter, criar ou lista.

2. Efetue uma ação, a verificar que o valor ETag não foram modificado.

Se o valor foi modificado, isto indica que outro cliente ou instância modificado blob ou contentor desde adquiriu o valor ETag.

### <a name="lease"></a>Locação financeira

Pode adquirir uma nova locação por utilizando o método de **acquireLease** , especificando o blob ou o contentor que pretende obter uma locação no. Por exemplo, o código seguinte adquire uma locação no **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

Operações subsequentes **myblob** tem de fornecer o `options.leaseId` parâmetro. A locação ID é devolvido como `result.id` a partir do **acquireLease**.

> [AZURE.NOTE] Por predefinição, a duração de locação financeira é infinita. Pode especificar uma duração não infinito (entre 15 e 60 segundos) ao fornecer a `options.leaseDuration` parâmetro.

Para remover uma locação, utilize **releaseLease**. Para interromper uma locação, mas impedir outras pessoas de obter uma nova locação até ter expirado a duração original, utilize **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas acesso partilhado

Acesso partilhado assinaturas (SA) são uma forma segura para fornecer acesso granular a blobs e contentores sem facultar o seu nome de conta de armazenamento ou teclas. Assinaturas de acesso partilhado com frequência são utilizadas para fornecer acesso limitado para os seus dados, tais como permitir que uma aplicação móvel aceder a blobs.

> [AZURE.NOTE] Enquanto também pode permitir acesso anónimo para blobs, acesso partilhado assinaturas lhe permitem fornecer acesso mais controlado, tal como tem de gerar as associações de segurança.

Uma aplicação de confiança como um serviço baseado na nuvem gera assinaturas acesso partilhado utilizando o **generateSharedAccessSignature** do **BlobService**e fornece-o para uma aplicação ou não fidedigna ou não fidedigna semiestruturados como uma aplicação móvel. Acesso assinaturas são geradas utilizando uma política que descreve o início datas e de fim durante o qual as assinaturas de acesso partilhado são válidas partilhado, bem como o nível de acesso concedido ao titular assinaturas acesso partilhado.

Exemplo de código seguinte gera uma nova política de acesso partilhado que permite que o titular de assinaturas acesso partilhado efetuar operações de leitura do blob **myblob** e expira 100 minutos após a hora que é criada.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

Tenha em atenção que as informações de anfitrião devem ser fornecidas de além disso, conforme for necessário quando o titular de assinaturas acesso partilhado tenta aceder o contentor.

Em seguida, a aplicação de cliente utiliza assinaturas acesso partilhado com **BlobServiceWithSAS** para efetuar operações contra o blob. A seguinte obtém informações sobre **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Uma vez que as assinaturas de acesso partilhado foram geradas com o acesso só de leitura, se for feita uma tentativa para modificar o blob, será devolvido um erro.

### <a name="access-control-lists"></a>Listas de controlo de acesso

Também pode utilizar uma lista de controlo de acesso (ACL) para definir a política de acesso para SA. Isto é útil se pretender permitir que vários clientes aceder a um contentor mas fornecer políticas de acesso diferentes para cada cliente.

Uma ACL é implementada utilizando uma matriz de políticas de acesso, com um ID associado a cada política. O exemplo seguinte de código define duas políticas, uma para 'Utilizador1' e outra para 'Utilizador2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

Exemplo de código seguinte obtém a ACL atual **mycontainer**e, em seguida, adiciona as novas políticas utilizando **setBlobAcl**. Esta abordagem permite:

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Assim que a ACL estiver definida, em seguida, pode criar assinaturas acesso partilhado com base no ID de uma política. O exemplo seguinte de código cria novos assinaturas de acesso partilhado para 'Utilizador2':

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes recursos.

-   [Armazenamento Azure SDK para nó API referência][]
-   [Blogue da equipa do armazenamento Azure][]
-   Repositório de [Azure SDK de armazenamento para nó][] no GitHub
-   [Centro de programadores do node.js](/develop/nodejs/)
-   [Transferir dados com o utilitário da linha de comandos AzCopy](storage-use-azcopy.md)

[Armazenamento SDK para o nó do Azure]: https://github.com/Azure/azure-storage-node

[Criar uma aplicação web do Node.js no serviço de aplicação do Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[NODE.js web app utilizando o serviço de tabela do Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Criar e implementar uma aplicação web do Node.js para Azure utilizando Web matriz]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Criar e implementar uma aplicação de Node.js num serviço de nuvem do Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Blogue da equipa do armazenamento Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Armazenamento Azure SDK para nó API referência]: http://dl.windowsazure.com/nodestoragedocs/index.html
