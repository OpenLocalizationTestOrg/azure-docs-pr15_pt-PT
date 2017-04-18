<properties
    pageTitle="Definir e obter propriedades e os metadados para objetos no armazenamento do Windows Azure | Microsoft Azure"
    description="Armazenar metadados personalizados em objectos no armazenamento do Windows Azure e defina e recuperar as propriedades do sistema."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="set-and-retrieve-properties-and-metadata"></a>Definir e obter propriedades e metadados #

## <a name="overview"></a>Descrição geral

Objetos nas propriedades do sistema de suporte de armazenamento do Windows Azure e metadados definidos pelo utilizador, para além dos dados que contêm:

*   **Propriedades do sistema.** Propriedades do sistema de existir em cada recurso de armazenamento. Algumas páginas podem ler ou definir, enquanto outras só de leitura. Em folhas de rosto, algumas propriedades do sistema correspondem a determinadas cabeçalhos HTTP padrão. A biblioteca do cliente armazenamento Azure mantém estes por si.  

*   **Metadados definidos pelo utilizador.** Metadados definidos pelo utilizador são metadados que especificar num determinado recurso, sob a forma de um par de valor de nome. Pode utilizar metadados para armazenar valores adicionais com um recurso de armazenamento; Estes valores são a suas própria apenas para fins de e não afetam a forma como o recurso se comporta.  

Obter valores de propriedade e metadados para um recurso de armazenamento é um processo de dois passos. Antes de poder ler estes valores, explicitamente pode obtê-los ao contactar o método de **FetchAttributes** .

> [AZURE.IMPORTANT] Valores de propriedade e metadados para um recurso de armazenamento não são preenchidos a menos que ligar para um dos métodos **FetchAttributes** . 

## <a name="setting-and-retrieving-properties"></a>Definir e obter propriedades

Para obter valores de propriedade, chamar o método de **FetchAttributes** no seu blob ou contentor para preencher as propriedades, em seguida, ler os valores.

Para definir propriedades de um objeto, especifique o valor da propriedade, em seguida, chamar o método de **SetProperties** .

Exemplo de código seguinte cria um contentor e escreve alguns dos seus valores de propriedade para uma janela de consola:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    
    //Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## <a name="setting-and-retrieving-metadata"></a>Definir e obtenção de metadados

Pode especificar metadados como uma ou mais pares valor do nome de um recurso blob ou contentor. Para definir metadados, adicionar pares valor do nome para a coleção de **metadados** do recurso, em seguida, o método de **SetMetadata** guardar os valores para o serviço de chamadas.

> [AZURE.NOTE] O nome do seu metadados tem de estar em conformidade com as convenções de nomenclatura para c# identificadores.
 
O exemplo seguinte de código define metadados num contentor. Um valor definido utilizando o método de **Adicionar** a coleção. O outro valor definido utilizando sintaxe implícito chave/valor. Ambos são válidos.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Para obter os metadados, chamar o método de **FetchAttributes** no seu blob ou contentor para preencher a coleção de **metadados** , em seguida, leia os valores, conforme mostrado no exemplo abaixo.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## <a name="see-also"></a>Consulte também  

- [Biblioteca de armazenamento Azure do cliente para referência .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Azure biblioteca de armazenamento do cliente para o pacote de .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 
