<properties
    pageTitle="Criar e utilizar uma SA com armazenamento de BLOBs | Microsoft Azure"
    description="Este tutorial mostra-lhe como criar assinaturas acesso partilhado para utilização com o armazenamento de BLOBs e como consumi-los das suas aplicações de cliente."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Partilhadas assinaturas do Access, parte 2: Criar e utilizar uma SA com armazenamento de BLOBs

## <a name="overview"></a>Descrição geral

[Parte 1](storage-dotnet-shared-access-signature-part-1.md) deste tutorial explorado partilhados assinaturas de acesso (SA) e explicação das melhores práticas para utilizá-los. Parte 2 mostra-lhe como gerar e, em seguida, utilizar assinaturas de acesso partilhado com armazenamento de Blobs. Os exemplos estão escritos em c# e utilizam a biblioteca de cliente de armazenamento do Azure para .NET. Os cenários abrangidos incluem estes aspetos de trabalhar com assinaturas acesso partilhado:

- Gerar uma assinatura de acesso partilhado num contentor
- Gerar uma assinatura de acesso partilhado num blob
- Criar uma política de acesso armazenados para gerir as assinaturas nos recursos de um contentor
- Testar as assinaturas de acesso partilhado a partir de uma aplicação de cliente

## <a name="about-this-tutorial"></a>Informações sobre este Tutorial

Neste tutorial, iremos concentrar-em criar assinaturas de acesso partilhado para contentores e blobs através da criação de aplicações de consola de duas. A aplicação da consola primeira gera assinaturas acesso partilhado um contentor e um blob. Esta aplicação tem conhecimento das teclas de conta de armazenamento. A aplicação de consola segunda, que irá agir como uma aplicação de cliente, acede contentor e recursos de BLOBs utilizando as assinaturas de acesso partilhado criadas com a primeira aplicação. Esta aplicação utiliza as assinaturas de acesso partilhado apenas para autenticar o acesso ao contentor e recursos de blob - não haja dados de conhecimento das teclas de conta.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: Criar uma aplicação de consola para gerar acesso partilhado assinaturas

Em primeiro lugar, certifique-se de que tem a biblioteca de cliente de armazenamento do Azure para .NET instalado. Pode instalar o [pacote de NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet pacote") que contém os conjuntos mais recentes para a biblioteca de cliente; Este é o método recomendado para assegurar que tenham as correções mais recentes. Também pode transferir a biblioteca do cliente como parte da versão mais recente do [Azure SDK para .NET](https://azure.microsoft.com/downloads/).

No Visual Studio, crie uma nova aplicação de consola do Windows e o nome **GenerateSharedAccessSignatures**. Adicione referências a **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, utilizando um dos seguintes abordagens:

-   Se pretender instalar o pacote de NuGet, instale primeiro o [Cliente NuGet](https://docs.nuget.org/consume/installing-nuget). No Visual Studio, selecione **projeto | Gerir pacotes de NuGet**, procurar online **Armazenamento do Windows Azure**e siga as instruções para instalar.
-   Em alternativa, localize os conjuntos na sua instalação do SDK do Azure e adicione as referências às mesmas.

Na parte superior do ficheiro Program.cs, adicione as seguintes instruções **utilizando** :

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Edite o ficheiro de App para que contém uma definição de configuração de com uma cadeia de ligação que aponta para a sua conta de armazenamento. O ficheiro App deverá ter um aspeto semelhante a este:

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Gerar uma assinatura de acesso partilhado URI para um contentor

Para começar, vamos adicionar um método para gerar uma assinatura de acesso partilhado num contentor novo. Neste caso a assinatura não está associada uma política de acesso armazenados, para que executa no URI as informações que indica o seu tempo de expiração e as permissões que concede.

Em primeiro lugar, adicione o código para o método de **Main()** para autenticar acesso à sua conta de armazenamento e criar um novo contentor:

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

Em seguida, adicione um novo método que gera a assinatura de acesso partilhado para o contentor e devolve a assinatura URI:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Adicione as seguintes linhas na parte inferior do método **Main()** , antes das chamadas para **Console.ReadLine()**, para ligar para **GetContainerSasUri()** e escrita a assinatura URI na janela da consola:

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilar e execute a assinatura de acesso partilhado URI para o novo contentor de saída. O URI será semelhante o URI seguinte:       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Depois de ter executado o código, a assinatura de acesso partilhado que criou no contentor será válida para as horas de vinte e quatro seguintes. A assinatura concede permissão a um cliente para blobs de lista no contentor de e para escrever um novo blob para o contentor.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Gerar uma assinatura de acesso partilhado URI para um Blob

Em seguida, podemos irá escrever código semelhante para criar um novo blob dentro do contentor e gerar uma assinatura de acesso partilhado para a mesma. Esta assinatura acesso partilhado não está associada uma política de acesso armazenados, para que inclui a hora de início, hora de expiração e informações de permissão no URI.

Adicionar um novo método de que cria um novo blob e escrever algum texto à mesma, em seguida, gera uma assinatura de acesso partilhado e devolve a assinatura URI:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Na parte inferior do método **Main()** , adicione as seguintes linhas para ligar **GetBlobSasUri()**, antes das chamadas para **Console.ReadLine()**e escrita a assinatura de acesso partilhado URI na janela da consola:    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compilar e execute a assinatura de acesso partilhado URI para o novo blob de saída. O URI será semelhante o URI seguinte:

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Criar uma política de acesso armazenados no contentor

Agora vamos criar uma política de acesso armazenados no contentor, irá definir restrições para qualquer assinaturas acesso partilhado que estão associadas com o mesmo.

Nos exemplos anteriores, recomendamos especificado a hora de início (implicitamente ou explicitamente), o tempo de validade e as permissões na assinatura de acesso partilhado URI própria. Nos exemplos seguintes, podemos irá especificar estes a política de acesso armazenado e não a assinatura de acesso partilhado. Fazê-lo permite-nos alterar estas restrições sem voltar a emitir a assinatura de acesso partilhado.

É possível ter um ou mais dos constrangimentos na assinatura de acesso partilhado e o resto na política de acesso armazenado. No entanto, só pode especificar a hora de início, hora de expiração e permissões só local ou outro; Por exemplo, não é possível especificar permissões relativa à assinatura, acesso partilhado e também especificá-las na política de acesso armazenado.

Tenha em atenção que quando adiciona uma política de acesso a um contentor, deve obter permissões existentes no contentor, adicionar a nova política de acesso e, em seguida, definir permissões do contentor.

Adicione um novo método de que cria uma nova política de acesso armazenados num contentor e devolve o nome da política de:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Na parte inferior do método **Main()** , antes das chamadas para **Console.ReadLine()**, adicione as seguintes linhas para limpar primeiro quaisquer políticas de acesso existentes e, em seguida, chamar o método de **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Tenha em atenção que quando desmarca as políticas de acesso num contentor, tem primeiro obter permissões existentes no contentor, em seguida, desmarque as permissões, em seguida, defina as permissões novamente.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Gerar uma assinatura de acesso partilhado URI no contentor que utiliza uma política de acesso

Em seguida, vamos criar outra assinatura acesso partilhado no contentor que criámos anterior, mas desta vez, que podemos irá associar a assinatura com a política de acesso que criámos no exemplo anterior.

Adicione um novo método para gerar outra assinatura acesso partilhado no contentor:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Na parte inferior do método **Main()** , antes das chamadas para **Console.ReadLine()**, adicione as seguintes linhas para ligar o método de **GetContainerSasUriWithPolicy** para:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Gerar uma assinatura de acesso partilhado URI no Blob que utiliza uma política de acesso

Por fim, vamos adicionar um método semelhante para criar outra blob e gerar uma assinatura de acesso partilhado que está associada uma política de acesso.

Adicione um novo método para criar um blob e gerar uma assinatura de acesso partilhado:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Na parte inferior do método **Main()** , antes das chamadas para **Console.ReadLine()**, adicione as seguintes linhas para ligar o método de **GetBlobSasUriWithPolicy** para:    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

O método de **Main()** deverá agora o seguinte aspeto na sua totalidade. Executá-la para a assinatura de acesso partilhado URIs de escrita na janela da consola, em seguida, copiar e colá-los um ficheiro de texto para utilizar na segunda parte deste tutorial.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Quando executar a aplicação da consola de GenerateSharedAccessSignatures, verá um resultado semelhante ao seguinte na janela da consola. Estas são as assinaturas de acesso partilhado que irá utilizar na parte 2 do tutorial.

![SA-consola-saída-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: Criar uma aplicação de consola para testar as assinaturas de acesso partilhado

Para testar as assinaturas de acesso partilhado criadas nos exemplos anteriores, recomendamos vai criar uma aplicação de consola segunda que utiliza as assinaturas para efetuar operações o contentor e um blob.

> [AZURE.NOTE] Se mais do que 24 horas tiverem passado, uma vez que se tiver concluído a primeira parte do tutorial, as assinaturas que gerou deixará de estar válidas. Neste caso, deve executar o código na aplicação de consola do primeira para gerar assinaturas fresca acesso partilhado para utilização na segunda parte do tutorial.

No Visual Studio, crie uma nova aplicação de consola do Windows e o nome **ConsumeSharedAccessSignatures**. Adicione as referências às **Microsoft.WindowsAzure.Configuration.dll** e **Microsoft.WindowsAzure.Storage.dll**, como fez anteriormente.

Na parte superior do ficheiro Program.cs, adicione as seguintes instruções **utilizando** :

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

No corpo do método **Main()** , adicione as seguintes constantes e atualizar os respetivos valores e as assinaturas de acesso partilhado que gerou na parte 1 do tutorial.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Adicionar um método para experimentar o contentor operações utilizando uma assinatura de acesso partilhado

Em seguida, adicionaremos um método de que testa algumas operações de contentor representativas utilizando uma assinatura de acesso partilhado no contentor. Tenha em atenção que a assinatura de acesso partilhado é utilizada para devolver uma referência para o contentor, autenticar acesso ao contentor com base na assinatura sozinho.

Adicione o seguinte método para Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Atualize o método de **Main()** a chamar **UseContainerSAS()** com ambas as assinaturas de acesso partilhado que criou no contentor:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Adicionar um método para experimentar operações Blob utilizando uma assinatura de acesso partilhado

Por fim, vamos adicionar um método de que testa algumas operações de BLOBs representante de utilizar uma assinatura de acesso partilhado no blob. Neste caso utilizamos o construtor **CloudBlockBlob(String)**, prisma na assinatura acesso partilhado, para devolver uma referência para o blob. Outros a autenticação não é necessária; baseia-se a assinatura sozinho.

Adicione o seguinte método para Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Atualize o método de **Main()** a chamar **UseBlobSAS()** com ambas as assinaturas de acesso partilhado que criou no blob:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Executar a aplicação da consola e observe o resultado para ver quais as operações são permitidas, para que as assinaturas. O resultado na janela da consola terá uma aspeto semelhante ao seguinte:

![SA-consola-saída-2][sas-console-output-2]

## <a name="next-steps"></a>Próximos passos

[Acesso partilhado assinaturas, parte 1: Noções sobre o modelo de SA](storage-dotnet-shared-access-signature-part-1.md)

[Gerir o acesso anónimo de leitura para contentores e blobs](storage-manage-access-to-resources.md)

[Delegação de acesso com uma assinatura de acesso partilhado (REST API)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Introdução aos tabela e fila SA](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG
