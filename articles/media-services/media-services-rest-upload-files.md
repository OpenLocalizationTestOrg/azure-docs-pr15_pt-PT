<properties 
    pageTitle="Carregar ficheiros para uma conta dos serviços de multimédia com resto | Microsoft Azure" 
    description="Saiba como obter o conteúdo de multimédia dos serviços de multimédia através da criação e carregar recursos." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>


# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar ficheiros para uma conta dos serviços de multimédia com o resto

 > [AZURE.SELECTOR]
 - [.NET](media-services-dotnet-upload-files.md)
 - [RESTO](media-services-rest-upload-files.md)
 - [Portal](media-services-portal-upload-files.md)

Serviços de multimédia, carregar os ficheiros digitais para um ativo. A entidade de [elementos](https://msdn.microsoft.com/library/azure/hh974277.aspx) pode conter vídeo, áudio, imagens, coleções de miniaturas, texto tracks e legenda fechada ficheiros (e os metadados sobre estes ficheiros.)  Assim que os ficheiros são carregados no elemento, o conteúdo está armazenado segura na nuvem para subsequente transformação e transmissão. 

>[AZURE.NOTE]As seguintes considerações aplicam quando escolher um nome de ficheiro de elementos:
>
>- Dos serviços de multimédia utiliza o valor da propriedade IAssetFile.Name durante a criação de URLs para o conteúdo da transmissão (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por este motivo, codificação percentual não é permitida. O valor da propriedade **nome** não pode ter qualquer um dos seguintes [carateres percentagem codificação-reservado](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, só pode haver um '.' para a extensão de nome de ficheiro.
>
>- O comprimento do nome não deve ser maior do que 260 caracteres.

Fluxo de trabalho básico para carregar activos estiver dividido em secções que se seguem:

- Criar um ativo
- Encriptar um ativo (opcional)
- Carregar um ficheiro ao armazenamento blob

AMS também permite-lhe carregar elementos em massa. Para mais informações, consulte o artigo [nesta](media-services-rest-upload-files.md#upload_in_bulk) secção.

##<a name="upload-assets"></a>Carregar activos

###<a name="create-an-asset"></a>Criar um ativo

>[AZURE.NOTE] Quando trabalha com os serviços de multimédia REST API, aplicam-se as seguintes considerações:
>
>Ao aceder ao entidades nos serviços de multimédia, tem de definir os campos de cabeçalho específico e valores nos pedidos de HTTP. Para obter mais informações, consulte [configuração para o desenvolvimento de API de resto de serviços de multimédia](media-services-rest-how-to-use.md).

>Após com êxito estabelecer ligação ao https://media.windows.net, receberá um 301 Redireccionamento especificando outra URI de serviços de multimédia. Terá de fazer as chamadas subsequentes para o novo URI tal como descrito em [para ligar ao utilizar a REST API dos serviços de multimédia](media-services-rest-connect-programmatically.md). 
 
Um ativo é um contentor para vários tipos de ou conjuntos de objetos de serviços de multimédia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legenda fechada. No REST API, a criação de um ativo requer enviar pedido de mensagem para dos serviços de multimédia e colocar as informações de propriedade sobre os elementos no corpo do pedido.

Uma das propriedades que pode especificar durante a criação de um ativo é **Opções**. **Opções** é um valor de enumeração que descreve as opções de encriptação que pode ser criado um ativo com. Um valor válido é um dos valores a partir da lista abaixo, não uma combinação de valores. 

- **Nenhum** = **0**: sem encriptação será utilizada. Este é o valor predefinido. Tenha em atenção que ao utilizar esta opção seu conteúdo não está protegido quando estão em trânsito ou no resto no armazenamento.
    Se planear entregar uma MP4 utilizando transferência gradual, utilize esta opção. 

- **StorageEncrypted** = **1**: especificar se pretende que para os seus ficheiros encriptados com encriptação de bit AES 256 para carregar e armazenamento.

    Se a elementos armazenamento encriptado, tem de configurar políticas de entrega de elementos. Para obter mais informações consulte o artigo [política de entrega de elementos de configurar](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: Especifique se está a carregar ficheiros protegidos por um método de encriptação comum (como PlayReady). 

- **EnvelopeEncryptionProtected** = **4**: Especifique se são carregar HLS encriptados com os ficheiros AES. Note que os ficheiros tem ter sido codificados e encriptados por transformar gestor.

>[AZURE.NOTE]Se a elementos irão utilizar a encriptação, tem de criar um **ContentKey** e ligá-lo a sua ativo, tal como descrito no seguinte tópico:[como criar um ContentKey](media-services-rest-create-contentkey.md). Tenha em atenção que depois de carregar os ficheiros para o elemento, tem de atualizar as propriedades de encriptação na entidade **AssetFile** com os valores que tem durante a encriptação **ativo** . Fazê-lo utilizando o pedido HTTP **impressão em série** . 


O exemplo seguinte mostra como criar um ativo.

**Pedido de HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"BigBuckBunny.mp4"}
    

**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte procedimento:
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
###<a name="create-an-assetfile"></a>Criar um AssetFile

A entidade de [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa um ficheiro de áudio ou vídeo que está armazenado num contentor de Blobs. Um ficheiro de elementos sempre está associado um ativo e um ativo pode conter um ou vários ficheiros de elementos. A tarefa de serviços de Media Encoder falha se um objeto de ficheiro de elementos não está associado um ficheiro digital num contentor de Blobs.

Tenha em atenção que a instância **AssetFile** e no ficheiro de multimédia real estão dois objetos distintos. A instância AssetFile contém metadados sobre o ficheiro de multimédia, enquanto o ficheiro de multimédia contém os conteúdos de multimédia real.

Depois de carregar o ficheiro de multimédia para um contentor blob, irá utilizar o pedido de HTTP **impressão em série** para atualizar a AssetFile com informações sobre o seu ficheiro de multimédia (como mostrado mais tarde no tópico). 

**Pedido de HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta de HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Criar o AccessPolicy com permissão de escrita. 

Antes de carregar os ficheiros para o armazenamento de BLOBs, defina o acesso de direitos de política para escrever um ativo. Para o fazer, PUBLIQUE um pedido de HTTP para o conjunto de entidade AccessPolicies. Definir um valor de DurationInMinutes durante a criação do ou receberá uma mensagem de erro de servidor interno 500 novamente na resposta. Para mais informações sobre AccessPolicies, consulte o artigo [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

O exemplo seguinte mostra como criar um AccessPolicy:
        
**Pedido de HTTP**

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Pedido de HTTP**

    If successful, the following response is returned:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

###<a name="get-the-upload-url"></a>Obter o URL de carregamento

Para receber o URL de carregamento real, crie um localizador de SA. Locator define a hora de início e o tipo de ponto final de ligação para clientes que pretendem aceder a ficheiros de um ativo. Pode criar várias entidades de Locator para um determinado par AccessPolicy e ativos processar pedidos de cliente diferente e as suas necessidades. Cada um dos seguinte Locator utilize o valor de hora de início, assim como o valor de DurationInMinutes do AccessPolicy para determinar o comprimento do tempo, pode ser utilizado um URL. Para mais informações, consulte o artigo [Locator](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Um URL de SA tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Algumas considerações aplicam-se:

- Não pode ter mais de cinco Locator exclusivo associado a um determinado activo ao mesmo tempo. Para mais informações, consulte o artigo Locator.
- Se precisar de carregar ficheiros imediatamente, deverá definir o valor de hora de início para cinco minutos antes da hora atual. Isto acontece porque poderão existir relógio distorção entre o seu computador cliente e dos serviços de multimédia. Além disso, tem de ser o valor de hora de início no seguinte formato de data/hora: aaaa-MM-DDTHH:mm:ssZ (por exemplo, "2014-05-23T17:53:50Z").   
- Poderão existir uma segunda 30 40 atrasar depois de um localizador de é criado para quando está disponível para utilização. Este problema se aplica ao URL SA e Locator de origem.

O exemplo seguinte mostra como criar um localizador de URL SA, tal como foi definido pela propriedade tipo no corpo do pedido ("1" para um localizador de SA) e "2" para uma localizador de origem no pedido. A propriedade de **Path** devolvida contém o URL que tem de utilizar para carregar o ficheiro.
    
**Pedido de HTTP**
    
    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Resposta de HTTP**

Se tiver êxito, é devolvida a resposta seguinte:
        
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Carregar um ficheiro para um contentor de armazenamento de BLOBs
    
Assim que tiver a AccessPolicy e Locator definido, o ficheiro propriamente dito é carregado para um contentor de armazenamento de Blobs do Azure utilizando o Azure armazenamento REST APIs. Pode carregar na página ou bloquear blobs. 

>[AZURE.NOTE] Tem de adicionar o nome do ficheiro para o ficheiro que pretende carregar para o valor de Locator **caminho** recebido na secção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Para mais informações sobre como trabalhar com blobs do Azure armazenamento, consulte o artigo [Blob serviço REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx).


### <a name="update-the-assetfile"></a>Atualizar a AssetFile 

Agora que carregou o seu ficheiro, Atualize as informações de FileAsset tamanho (e outros). Por exemplo:
    
    MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta de HTTP**

Se tiver êxito, o seguinte é devolvido: HTTP/1.1 204 sem conteúdo

### <a name="delete-the-locator-and-accesspolicy"></a>Eliminar o localizador e AccessPolicy 

**Pedido de HTTP**


    DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte procedimento:

    HTTP/1.1 204 No Content 
    ...

**Pedido de HTTP**

    DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
    x-ms-version: 2.11
    Host: media.windows.net

**Resposta de HTTP**

Se tiver êxito, é devolvido o seguinte procedimento:

    HTTP/1.1 204 No Content 
    ...

##<a id="upload_in_bulk"></a>Carregar elementos em massa

###<a name="create-the-ingestmanifest"></a>Criar o IngestManifest

O IngestManifest é um contentor para um conjunto de recursos, ficheiros de elementos e informações de estatística que podem ser utilizadas para determinar o progresso em volume ingesting para o conjunto.


**Pedido de HTTP**

    POST https:// media.windows.net/API/IngestManifests HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 36
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST" }

###<a name="create-assets"></a>Criar activos

Antes de criar o IngestManifestAsset, tem de criar os elementos que vai ser concluídos utilizando ingesting em volume. Um ativo é um contentor para vários tipos de ou conjuntos de objetos de serviços de multimédia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e ficheiros de legenda fechada. No REST API, a criação de um ativo requer enviar um pedido de HTTP POST para dos serviços de multimédia do Microsoft Azure e colocar as informações de propriedade sobre os elementos no corpo do pedido. Neste exemplo, o activo é criado utilizando a opção de StorageEncrption(1) incluída com o corpo do pedido.

**Resposta de HTTP**

    POST https://media.windows.net/API/Assets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 55
    Expect: 100-continue
    
    { "Name" : "ExampleManifestREST_Asset", "Options" : 1 }

###<a name="create-the-ingestmanifestassets"></a>Criar o IngestManifestAssets

IngestManifestAssets representam elementos dentro de uma IngestManifest que são utilizados com ingesting em volume. A ligação que mostra basicamente activo para o manifesto. Azure dos serviços de multimédia está a observar internamente para o carregamento de ficheiro com base na coleção de IngestManifestFiles associada à IngestManifestAsset. Assim que estes ficheiros carregados, elemento está concluído. Pode criar um novo IngestManifestAsset com um pedido de HTTP POST. No corpo do pedido, inclua o Id do IngestManifest e o Id de elementos que o IngestManifestAsset deve ligar-se em conjunto para ingesting em volume.

**Resposta de HTTP**

    POST https://media.windows.net/API/IngestManifestAssets HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 152
    Expect: 100-continue
    { "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "Asset" : { "Id" : "nb:cid:UUID:b757929a-5a57-430b-b33e-c05c6cbef02e"}}


###<a name="create-the-ingestmanifestfiles-for-each-asset"></a>Criar o IngestManifestFiles para cada activo

Um IngestManifestFile representa um objeto de BLOBs real de vídeo ou áudio que vai ser carregado como parte de em volume ingesting de um ativo. Encriptação relacionadas com propriedades não são necessárias, a menos que o recurso está a utilizar uma opção de encriptação. O exemplo utilizado nesta secção demonstra a criação de um IngestManifestFile que utiliza StorageEncryption para o ativo criado anteriormente.


**Resposta de HTTP**

    POST https://media.windows.net/API/IngestManifestFiles HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 367
    Expect: 100-continue
    
    { "Name" : "REST_Example_File.wmv", "ParentIngestManifestId" : "nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048", "ParentIngestManifestAssetId" : "nb:maid:UUID:beed8531-9a03-9043-b1d8-6a6d1044cdda", "IsEncrypted" : "true", "EncryptionScheme" : "StorageEncryption", "EncryptionVersion" : "1.0", "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510" }
    
###<a name="upload-the-files-to-blob-storage"></a>Carregar os ficheiros ao armazenamento Blob

Pode utilizar qualquer aplicação de cliente de alta velocidade capaz de carregar os ficheiros de elementos para o contentor de armazenamento de BLOBs Uri fornecida pela propriedade BlobStorageUriForUpload a IngestManifest. Um serviço de carregamento de alta velocidade dos é [Aspera no pedido de aplicação do Azure](http://go.microsoft.com/fwlink/?LinkId=272001).

###<a name="monitor-bulk-ingest-progress"></a>Em volume do monitor ingerir esta última progresso

Pode monitorizar o progresso em volume ingesting operações para um IngestManifest pela propriedade estatísticas da IngestManifest de consulta. Que a propriedade é um tipo de complexo, [IngestManifestStatistics](https://msdn.microsoft.com/library/azure/jj853027.aspx). Para consultar a propriedade estatísticas, submeta um pedido HTTP GET prisma o ID da IngestManifest.
 

##<a name="create-contentkeys-used-for-encryption"></a>Criar ContentKeys utilizada para encriptação

Se a elementos irão utilizar a encriptação, tem de criar ContentKey a ser utilizado para encriptação antes de criar os ficheiros de elementos. Para encriptação de armazenamento, as seguintes propriedades deverão ser incluídas no corpo do pedido.
 
Propriedade do corpo de texto de pedido   | Descrição
---|---
ID | O Id do ContentKey que recomendamos gerar estabelecidos utilizando o seguinte formato, "nb:kid:UUID:<NEW GUID>".
ContentKeyType | Este é o tipo de conteúdo principais como um número inteiro para esta chave conteúdo. Vamos passar o valor 1 para encriptação de armazenamento.
EncryptedContentKey | Vamos criar um novo valor de chave conteúdo que é um valor de 256 bits (32 byte). A tecla está encriptada utilizando o certificado x. 509 encriptação de armazenamento que recomendamos obter a partir dos serviços de multimédia do Microsoft Azure através da execução de um pedido HTTP GET para os métodos de GetProtectionKey e GetProtectionKeyId.
ProtectionKeyId | Este é o id da proteção chave para o certificado x. 509 encriptação de armazenamento que foi utilizado para encriptar o nosso chave de conteúdo.
ProtectionKeyType | Este é o tipo de encriptação para a chave de proteção que foi utilizado para encriptar a chave de conteúdo. Este valor é StorageEncryption(1) para o nosso exemplo.
Soma de verificação |Soma calculada MD5 para a chave de conteúdo. É calculado ao encriptá o Id de conteúdo com a chave de conteúdo. O código de exemplo demonstra como calcular a soma de verificação.


**Resposta de HTTP**
    
    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 572
    Expect: 100-continue
    
    {"Id" : "nb:kid:UUID:316d14d4-b603-4d90-b8db-0fede8aa48f8", "ContentKeyType" : 1, "EncryptedContentKey" : "Y4NPej7heOFa2vsd8ZEOcjjpu/qOq3RJ6GRfxa8CCwtAM83d6J2mKOeQFUmMyVXUSsBCCOdufmieTKi+hOUtNAbyNM4lY4AXI537b9GaY8oSeje0NGU8+QCOuf7jGdRac5B9uIk7WwD76RAJnqyep6U/OdvQV4RLvvZ9w7nO4bY8RHaUaLxC2u4aIRRaZtLu5rm8GKBPy87OzQVXNgnLM01I8s3Z4wJ3i7jXqkknDy4VkIyLBSQvIvUzxYHeNdMVWDmS+jPN9ScVmolUwGzH1A23td8UWFHOjTjXHLjNm5Yq+7MIOoaxeMlKPYXRFKofRY8Qh5o5tqvycSAJ9KUqfg==", "ProtectionKeyId" : "7D9BB04D9D0A4A24800CADBFEF232689E048F69C", "ProtectionKeyType" : 1, "Checksum" : "TfXtjCIlq1Y=" }

### <a name="link-the-contentkey-to-the-asset"></a>Ligar a ContentKey activo

O ContentKey está associado a um ou mais elementos ao enviar um pedido de HTTP POST. O pedido seguinte é um exemplo a ligar o exemplo ContentKey activo exemplo pela ID.

**Resposta de HTTP**
    
    POST https://media.windows.net/API/Assets('nb:cid:UUID:b3023475-09b4-4647-9d6d-6fc242822e68')/$links/ContentKeys HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net
    Content-Length: 113
    Expect: 100-continue
    
    { "uri": "https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A32e6efaf-5fba-4538-b115-9d1cefe43510')"}

**Resposta de HTTP**

    GET https://media.windows.net/API/IngestManifests('nb:mid:UUID:5c77f186-414f-8b48-8231-17f9264e2048') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=070500D0-F35C-4A5A-9249-485BBF4EC70B&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1334275521&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=GxdBb%2fmEyN7iHdNxbawawHRftLhPFFqxX1JZckuv3hY%3d
    Host: media.windows.net



##<a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



 
[How to Get a Media Processor]: media-services-get-media-processor.md
 
