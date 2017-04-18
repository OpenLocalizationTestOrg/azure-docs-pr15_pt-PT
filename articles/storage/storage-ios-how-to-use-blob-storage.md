<properties
    pageTitle="Como utilizar o armazenamento de Blobs do Azure a partir do iOS | Microsoft Azure"
    description="Armazene dados não estruturados na nuvem com armazenamento de Blobs do Azure (armazenamento de objeto)."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-ios"></a>Como utilizar o armazenamento de Blobs do iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Descrição geral

Este artigo mostrar-lhe como efetuar cenários comuns a utilizar o armazenamento de Blobs do Microsoft Azure. Os exemplos são gravados no objectivo-C e utilizam a [Biblioteca de cliente do Azure armazenamento para iOS](https://github.com/Azure/azure-storage-ios). Os cenários abrangidos incluem **a carregar**, **Listar**, **Transferir**e **Eliminar** blobs. Para mais informações sobre blobs, consulte a secção [Os passos seguintes](#next-steps) . Também pode transferir a [aplicação de exemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente a utilização de armazenamento do Windows Azure numa aplicação iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importar a biblioteca de iOS de armazenamento do Windows Azure para a aplicação

Pode importar a biblioteca de iOS de armazenamento do Windows Azure para a aplicação, utilizando o [Azure armazenamento CocoaPod](https://cocoapods.org/pods/AZSClient) ou importando o ficheiro de **quadro** .

## <a name="cocoapod"></a>CocoaPod

1. Se que ainda não o tiver feito, [Instalar CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) no seu computador ao abrir uma janela de terminal e executando o seguinte comando

        sudo gem install cocoapods

2. Seguinte, no directório do projecto (o diretório que contém o `.xcodeproj` ficheiro), criar um novo ficheiro denominado `Podfile`(sem extensão de ficheiro). Adicionar o seguinte procedimento para `Podfile` e guardar

        pod 'AZSClient'

3. Na janela de terminal, navegue para o diretório de projeto e execute o seguinte comando

        pod install

4. Se o `.xcodeproj` é aberta na Xcode, fechá-lo. No diretório do seu projeto, abra o ficheiro de projeto recentemente criado que terão o `.xcworkspace` extensão. Este é o ficheiro que irá trabalhar a partir de agora no.

## <a name="framework"></a>Quadro
Para que possa utilizar a biblioteca de iOS de armazenamento do Windows Azure, terá primeiro criar o ficheiro de quadro.

1. Em primeiro lugar, transfira ou clonar o [azure-armazenamento-ios repo](https://github.com/azure/azure-storage-ios).

2. Vá para o *azure-armazenamento-ios* -> *biblioteca* -> *Biblioteca de cliente de armazenamento do Azure*e abra `AZSClient.xcodeproj` no Xcode.

3. No canto superior esquerdo da Xcode, altere o esquema ativo a partir de "Azure armazenamento cliente biblioteca" para "Framework".

4. Crie o projecto (⌘ + B). Esta opção criará uma `AZSClient.framework` ficheiro no seu ambiente de trabalho.

Em seguida, pode importar o ficheiro de quadro para a aplicação ao efetuar o seguinte:

1. Criar um novo projeto ou abra o seu projeto existente no Xcode.

2. Clique no seu projeto na navegação à esquerda e clique no separador *Geral* na parte superior do editor de projeto.

3. Na secção de *quadros ligadas e bibliotecas* , clique no botão Adicionar (+).

4. Clique em *Adicionar outros...*. Navegue para e adicionar o `AZSClient.framework` ficheiro que acabou de criar.

5. Na secção de *quadros ligadas e bibliotecas* , clique novamente no botão Adicionar (+).

6. Na lista de bibliotecas fornecido, procure `libxml2.2.dylib` e adicioná-lo ao projeto.

7. Clique no separador *Criar definições* na parte superior do editor de projeto.

8. Na secção *Caminhos de pesquisa* , faça duplo clique *Caminhos de pesquisa do quadro* e adicionar o caminho para o `AZSClient.framework` ficheiro.

## <a name="import-statement"></a>Declaração de importação
Irá precisar de incluir a seguinte instrução importar no ficheiro onde pretende invocar a API do armazenamento Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações assíncronas
> [AZURE.NOTE] Todos os métodos de executar um pedido de serviço são assíncronas operações. Os exemplos de código, encontrará que estes métodos têm um processador de conclusão. Código dentro de processador de conclusão será executado **depois do** que pedido foi concluído. Está a ser feito código após o conclusão processador será executado **enquanto** o pedido.

## <a name="create-a-container"></a>Criar um contentor
Cada blob no armazenamento Azure tem residem num contentor. O exemplo seguinte mostra como criar um contentor, denominado *newcontainer*, na sua conta de armazenamento, se ainda não existir. Quando escolher um nome para o contentor, ser cientes das regras de nomenclatura mencionadas acima.

    -(void)createContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

      // Create container in your Storage account if the container doesn't already exist
      [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
         if (error){
             NSLog(@"Error in creating container.");
         }
      }];
    }

Pode confirmar que isto funciona ao consultar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) e verificar que esse *newcontainer* está na lista de membros em contentores para a sua conta de armazenamento.

## <a name="set-container-permissions"></a>Definir permissões de contentor
Permissões de um contentor estão configuradas para acesso **privado** por predefinição. No entanto, contentores fornecem alguns diferentes opções de acesso de contentor:

- **Privado**: contentor e blob de dados podem ser lido por apenas o proprietário da conta.

- **Blob**: dados Blob dentro deste contentor podem ser lidos através do pedido anónimo, mas os dados de contentor não estão disponíveis. Os clientes não é possível enumerar blobs dentro do contentor através do pedido anónimo.

- **Contentor**: contentor e blob dados podem ser lidos através do pedido anónimo. Os clientes podem enumerar blobs dentro do contentor através do pedido anónimo, mas não é possível enumerar contentores dentro da conta de armazenamento.

O exemplo seguinte mostra como criar um contentor com permissões de acesso de **contentor** que irão permitir o acesso de público, só de leitura para todos os utilizadores na Internet:

    -(void)createContainerWithPublicAccess{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
Tal como mencionado na secção [conceitos de serviço de BLOBs](#blob-service-concepts) , armazenamento de BLOBs oferece três tipos diferentes de blobs: bloquear blobs, acrescentar blobs e blobs da página. Neste momento, a biblioteca de iOS de armazenamento do Windows Azure suporta apenas blobs bloco. Na maioria dos casos, blob bloco é o tipo recomendado para utilizar.

O exemplo seguinte mostra como carregar um blob bloco a partir de um NSString. Se já existe um blob com o mesmo nome neste contentor, os conteúdos deste blob serão substituídos.

    -(void)uploadBlobToContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
             else{
                 // Create a local blob object
                 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                 // Upload blob to Storage
                 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                     if (error){
                         NSLog(@"Error in creating blob.");
                     }
                 }];
             }
         }];
    }

Pode confirmar que isto funciona ao consultar o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com) e que o contentor, *containerpublic*, contém o blob, *sampleblob*a verificar. Neste exemplo, utilizámos um contentor público para que também pode verificar que este trabalhou ao aceder aos blobs URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Para além de carregar um blob bloco a partir de um NSString, os métodos semelhantes existem para NSData, NSInputStream ou um ficheiro local.

## <a name="list-the-blobs-in-a-container"></a>Blobs num contentor de lista
O exemplo seguinte mostra como todos os blobs num contentor de lista. Quando executar esta operação, serão cientes dos seguintes parâmetros:     

- **continuationToken** - token de continuação de notas representa onde deverá começar a operação de listagem. Não se for fornecido nenhum token, irá mostrar blobs do início. Qualquer número de blobs pode ser listado, a partir do zero, até um máximo de conjunto. Mesmo se este método devolve zero resultados, se `results.continuationToken` não é nulo, poderão existir blobs mais o serviço que não foram listados.
- **prefixo** - pode especificar o prefixo a utilizar para listagem blob. Apenas blobs que começam com este prefixo serão listados.
- **useFlatBlobListing** - tal como mencionado na secção de [nomenclatura e referência contentores e blobs](#naming-and-referencing-containers-and-blobs) , apesar do serviço de BLOBs é um esquema de armazenamento simples, pode criar uma hierarquia virtual atribuindo blobs com informações do caminho. No entanto, não plana listagem atualmente não é suportada; Isto é brevemente. Por agora, este valor deve ser`YES`
- **blobListingDetails** - pode especificar quais os itens que sejam incluídos na lista blobs
    - `AZSBlobListingDetailsNone`: Listar apenas blobs consolidadas e não devolvem blob metadados.
    - `AZSBlobListingDetailsSnapshots`: A listagem blobs consolidadas e blob instantâneos.
    - `AZSBlobListingDetailsMetadata`: Devolvido recuperar blob metadados para cada blob na listagem.
    - `AZSBlobListingDetailsUncommittedBlobs`: Blobs consolidadas e de repetição da lista.
    - `AZSBlobListingDetailsCopy`: Incluem propriedades de cópia na lista.
    - `AZSBlobListingDetailsAll`: De lista todos os blobs consolidadas disponíveis, blobs repetição instantâneos e devolver todos os metadados e copiar estado para esses blobs.
- **maxResults** - o número máximo de resultados a devolver para esta operação. Utilize -1 para não definir um limite.
- **completionHandler** - o bloco de código para executar com os resultados da operação de listagem.

Neste exemplo, um método de ajuda é utilizado para modo recursivo chamada na lista blobs método sempre que é devolvido um token de continuação de notas.

    -(void)listBlobsInContainer{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        //List all blobs in container
        [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
            if (error != nil){
                NSLog(@"Error in creating container.");
            }
        }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
        [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
            if (error)
            {
                completionHandler(error);
            }
            else
            {
                for (int i = 0; i < results.blobs.count; i++) {
                    NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
                }
                if (results.continuationToken)
                {
                    [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
                }
                else
                {
                    completionHandler(nil);
                }
            }
        }];
    }


## <a name="download-a-blob"></a>Transferir um blob

O exemplo seguinte mostra como transferir um blob para um objeto de NSString.

    -(void)downloadBlobToString{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
            else{
                NSLog(@"%@",text);
            }
        }];
    }

## <a name="delete-a-blob"></a>Eliminar um blob

O exemplo seguinte mostra como eliminar um blob.

    -(void)deleteBlob{
        NSError *accountCreationError;

        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

        if(accountCreationError){
            NSLog(@"Error in creating account.");
        }

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
            if (error) {
                NSLog(@"Error in deleting blob.");
            }
        }];
    }

## <a name="delete-a-blob-container"></a>Eliminar um contentor blob

O exemplo seguinte mostra como eliminar um contentor.

    -(void)deleteContainer{
      NSError *accountCreationError;

      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

      if(accountCreationError){
         NSLog(@"Error in creating account.");
      }

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      // Delete container
      [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
         if(error){
             NSLog(@"Error in deleting container");
         }
      }];
    }

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como utilizar o armazenamento de Blobs do iOS, siga estas ligações para mais informações sobre a biblioteca do iOS e o serviço de armazenamento.

- [Azure biblioteca de armazenamento do cliente para iOS](https://github.com/azure/azure-storage-ios)
- [Azure armazenamento iOS documentação de referência](http://azure.github.io/azure-storage-ios/)
- [Serviços de armazenamento Azure REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage)

Se tiver questões sobre esta biblioteca à vontade publicar a nossa [Fórum MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou [Pilha de conteúdo adicional](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se tiver sugestões de funcionalidade de armazenamento do Windows Azure, publique o [Azure armazenamento comentários](https://feedback.azure.com/forums/217298-storage/).
