<properties
 pageTitle="Gerir a expiração de conteúdo de Blobs do armazenamento do Windows Azure no Azure CDN | Microsoft Azure"
 description="Saiba mais sobre as opções para controlar o time to live para blobs no Azure CDN colocação em cache."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# <a name="manage-expiration-of-azure-storage-blob-content-in-azure-cdn"></a>Gerir a expiração de conteúdo de Blobs do armazenamento do Windows Azure no Azure CDN

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Serviço de armazenamento de BLOBs Azure](cdn-manage-expiration-of-blob-content.md)

O [serviço de BLOBs](../storage/storage-introduction.md#blob-storage) no [Armazenamento do Windows Azure](../storage/storage-introduction.md) é uma das várias origens baseadas no Azure integradas com o Azure CDN.  Qualquer conteúdo acessível publicamente blob pode ser colocadas em cache no Azure CDN até decorrida respetivo time to live (TTL).  O valor TTL é determinado pela [cabeçalho *Cache-Control* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) na resposta HTTP a partir do armazenamento do Windows Azure.

>[AZURE.TIP] Pode optar por definir sem TTL num blob.  Neste caso, Azure CDN aplica automaticamente um TTL predefinido de sete dias.
>
>Para mais informações sobre como funciona o Azure CDN para acelerar o acesso a blobs e outros ficheiros, consulte o artigo [Descrição geral de CDN Azure](./cdn-overview.md).
>
>Para obter mais detalhes sobre o serviço de Blobs do Azure armazenamento, consulte o artigo [Blob conceitos de serviço](https://msdn.microsoft.com/library/dd179376.aspx). 

Neste tutorial demonstra várias formas que pode definir o valor TTL num blob de armazenamento do Windows Azure.  

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](../powershell-install-configure.md) é uma das formas mais rápidas e mais eficientes para administrar a sua Azure serviços.  Utilizar o `Get-AzureStorageBlob` cmdlet para obter uma referência a blob, em seguida, defina o `.ICloudBlob.Properties.CacheControl` propriedade. 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] Também pode utilizar o PowerShell para [Gerir perfis CDN e os pontos finais](./cdn-manage-powershell.md).

## <a name="azure-storage-client-library-for-net"></a>Biblioteca de armazenamento Azure do cliente para .NET

Para definir um blob TTL utilizando o .NET, utilize a [Biblioteca de cliente do Azure armazenamento para .NET](../storage/storage-dotnet-how-to-use-blobs.md) para definir a propriedade [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) .

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        
        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "public, max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

>[AZURE.TIP] Existem muitas mais exemplos de código .NET disponíveis nas [Amostras de armazenamento de Blobs do Azure para .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).

## <a name="other-methods"></a>Outros métodos de

- [Interface de comandos Azure](../xplat-cli-install.md)

    Ao carregar o blob, defina a propriedade do *cacheControl* utilizando o `-p` mudar.  Este exemplo define o valor TTL para uma hora (3600 segundos).

    ```text
    azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
    ```

- [Serviços de armazenamento Azure REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

    Explicitamente defina a propriedade de *x-ms-blob-cache-control de* um pedido de [Colocar Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx), [Colocar a lista de bloqueios](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)ou [Definir propriedades de BLOBs](https://msdn.microsoft.com/library/azure/ee691966.aspx) .

- Ferramentas de gestão de armazenamento de terceiros

    Algumas ferramentas de gestão de armazenamento do Windows Azure de terceiros permitem-lhe definir a propriedade *CacheControl* no blobs. 

## <a name="testing-the-cache-control-header"></a>Testar o cabeçalho *Cache-Control*

Pode verificar o TTL do seu blobs facilmente.  Utilizando de [Ferramentas de programador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), teste que o seu blob é incluindo o cabeçalho de resposta da *Cache de controlo* . o browser  Também pode utilizar uma ferramenta como **wget**, [Postman](https://www.getpostman.com/)ou [Fiddler](http://www.telerik.com/fiddler) examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximos passos

- [Leia mais sobre o cabeçalho *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Saiba como gerir expiração de conteúdo de serviço na nuvem no Azure CDN](./cdn-manage-expiration-of-cloud-service-content.md)

