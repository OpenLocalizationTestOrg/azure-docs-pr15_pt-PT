<properties 
    pageTitle="Configurar políticas de entrega de elementos multimédia serviços REST API a utilizar | Microsoft Azure" 
    description="Este tópico mostra como configurar políticas de entrega de diferentes elementos multimédia serviços REST API a utilizar." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"  
    ms.author="juliako"/>

#<a name="configuring-asset-delivery-policies"></a>Configurar políticas de entrega de elementos

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Se planeia para a prestação activos dinamicamente encriptados, um destes passos no fluxo de trabalho de entrega de conteúdos dos serviços de multimédia está a configurar políticas de entrega para recursos. A política de entrega de recurso indica dos serviços de multimédia como que pretende para o seu activo a ser entregue: para o qual a transmissão de protocolo devem seu elementos dinamicamente empacotados (por exemplo, MPEG travessão, HLS, transmissão suaves ou todos), se pretende ou não que encriptar dinamicamente seu ativo e como (envelope ou encriptação comuns).

Este tópico aborda porquê e como criar e configurar as políticas de entrega de elementos.

>[AZURE.NOTE]Para poder utilizar embalagem dinâmica e a encriptação dinâmica, terá de fazer-se de que tem pelo menos uma unidade de escala (também conhecido como transmissão unidade). Para mais informações, consulte o artigo [como dimensionar um serviço de multimédia](media-services-portal-manage-streaming-endpoints.md).
>
>Além disso, o recurso tem de conter um conjunto de velocidade ajustável MP4s ou velocidade ajustável transmissão suaves ficheiros.

Pode aplicar diferentes políticas para o mesmo activo. Por exemplo, pode aplicar PlayReady encriptação para encriptação Streaming suaves e AES Envelope para MPEG TRAÇO e HLS. Quaisquer protocolos que não são definidos numa política de entrega (por exemplo, adicionar uma única política apenas especifica HLS como o protocolo) serão bloqueados da transmissão. A exceção é, não se tem nenhuma política de entrega de elementos definida de todo. Em seguida, todos os protocolos concede em claro.

Se quiser fornecer um activo encriptada do armazenamento, tem de configurar políticas de entrega a elementos. Antes do recurso pode ser transmitido em sequência, o servidor de transmissão remove a encriptação de armazenamento e transmite em fluxo conteúdo utilizando a política de entrega especificado. Por exemplo, para entregar a sua elementos encriptados com chave de encriptação de envelope de encriptação AES Advanced Standard (), defina o tipo de política para **DynamicEnvelopeEncryption**. Para remover a encriptação de armazenamento e transmitir em fluxo elementos em claro, defina o tipo de política para **NoDynamicEncryption**. Exemplos que mostram como configurar estes tipos de política siga.

Cálculo consoante a forma como configurar a política de entrega de elementos que iria poderá dinamicamente compactar, dinamicamente encriptar e transmitir os seguintes protocolos transmissão em fluxo: fluxos de transmissão suaves, HLS, MPEG TRAÇO e HDS.

A lista seguinte mostra os formatos que utiliza para transmitir em fluxo suave, HLS, TRAÇO e HDS.

Transmissão suaves:

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG TRAVESSÃO

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

HDS

{transmissão ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Para obter instruções sobre como publicar um ativo e crie um URL de transmissão, consulte o artigo [criar um URL de transmissão](media-services-deliver-streaming-content.md).


##<a name="considerations"></a>Considerações sobre

- Não é possível eliminar uma AssetDeliveryPolicy associada de um ativo enquanto não existe um locator sob (transmissão) para esse activo. A recomendação é remover a política de elemento antes de eliminar a política.
- Não é possível criar um localizador de transmissão num activo encriptada armazenamento quando está definida como sem política de entrega ativo.  Se a elementos não armazenamento encriptado, o sistema permitirá que criar um localizador e transmitir em fluxo activo clara sem uma política de entrega ativo.
- Pode ter vários elementos entrega as políticas associadas a um único activo, mas só pode especificar uma forma de se tratar de um determinado AssetDeliveryProtocol.  O que significa que se tentar ligar duas políticas de entrega que especifique o protocolo de AssetDeliveryProtocol.SmoothStreaming irá resultar num erro porque o sistema não souber aquele que deseja-lo para aplicar quando um cliente faz com que um pedido de transmissão suaves.
- Se tiver um ativo com um localizador de transmissão existente, não pode ligar uma nova política para elemento, desassociar uma política existente a partir do elemento ou actualizar uma política de entrega associada elemento.  Primeiro tem de remover o localizador de transmissão, ajustar as políticas que foram e, em seguida, recriar o localizador de transmissão.  Pode utilizar o mesmo locatorId quando é recriar o localizador de transmissão mas deverá garantir que não causar problemas para clientes dado que conteúdo pode ser colocada em cache pela origem ou de uma CDN descendentes.

>[AZURE.NOTE] Quando trabalha com os serviços de multimédia REST API, aplicam-se as seguintes considerações:
>
>Ao aceder ao entidades nos serviços de multimédia, tem de definir os campos de cabeçalho específico e valores nos pedidos de HTTP. Para obter mais informações, consulte [configuração para o desenvolvimento de API de resto de serviços de multimédia](media-services-rest-how-to-use.md).

>Após com êxito estabelecer ligação ao https://media.windows.net, receberá um 301 Redireccionamento especificando outra URI de serviços de multimédia. Terá de fazer as chamadas subsequentes para o novo URI tal como descrito em [para ligar ao utilizar a REST API dos serviços de multimédia](media-services-rest-connect-programmatically.md).


##<a name="clear-asset-delivery-policy"></a>Política de entrega de limpar activo

###<a id="create_asset_delivery_policy"></a>Criar a política de entrega de elementos
O pedido de HTTP seguinte cria uma política de entrega de elementos que especifica para não aplicar encriptação dinâmica e para entregar a sequência de em qualquer um dos seguintes protocolos: protocolos MPEG travessão, HLS e transmissão suaves. 

Para obter informações sobre quais os valores pode especificar quando criar uma AssetDeliveryPolicy, consulte a secção [tipos de utilizados quando definir AssetDeliveryPolicy](#types) .   


Pedido:
      
    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net
    
    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Resposta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT
    
    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}
    
###<a id="link_asset_with_asset_delivery_policy"></a>Elementos de ligação com a política de entrega de elementos

O pedido de HTTP seguinte ligações do elemento especificado para a política de entrega de elementos para.

Pedido:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net
    
    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Resposta:

    HTTP/1.1 204 No Content


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicEnvelopeEncryption 

###<a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Criar conteúdo chave do tipo de EnvelopeEncryption e ligá-lo ao activo

Quando especificar DynamicEnvelopeEncryption política de entrega, tem de Certifique-se ligar os elementos a uma chave de conteúdo do tipo de EnvelopeEncryption. Para obter mais informações, consulte o artigo: [criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).


###<a id="get_delivery_url"></a>Obter o URL de entrega

Obter o URL de entrega para o método de entrega especificado da chave de conteúdo que criou no passo anterior. Um cliente utiliza o URL devolvido para solicitar uma chave de AES ou um PlayReady licença por ordem para reproduzir o conteúdo protegido.

Especifique o tipo do URL para obter no corpo do pedido de HTTP. Se estiver a proteger o seu conteúdo com PlayReady, pedir um URL de aquisição de licença PlayReady de serviços de multimédia, utilizando 1 para o keyDeliveryType: {"keyDeliveryType": 1}. Se estiver a proteger o seu conteúdo com a encriptação de envelope, solicitar um URL de aquisição chave ao especificar 2 para keyDeliveryType: {"keyDeliveryType": 2}.

Pedido:
    
    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21
    
    {"keyDeliveryType":2}

Resposta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


###<a name="create-asset-delivery-policy"></a>Criar a política de entrega de elementos

O pedido de HTTP seguinte cria **AssetDeliveryPolicy** que esteja configurado para aplicar a encriptação de envelope dinâmicas (**DynamicEnvelopeEncryption**) para o protocolo **HLS** (neste exemplo, outros protocolos serão bloqueados da transmissão). 


Para obter informações sobre quais os valores pode especificar quando criar uma AssetDeliveryPolicy, consulte a secção [tipos de utilizados quando definir AssetDeliveryPolicy](#types) .   

Pedido:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}

    
Resposta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT
    
    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


###<a name="link-asset-with-asset-delivery-policy"></a>Elementos de ligação com a política de entrega de elementos

Consulte o artigo [elementos de ligação com a política de entrega de elementos](#link_asset_with_asset_delivery_policy)

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicCommonEncryption 

###<a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Criar conteúdo chave do tipo de CommonEncryption e ligá-lo ao activo

Quando especificar DynamicCommonEncryption política de entrega, tem de Certifique-se ligar os elementos a uma chave de conteúdo do tipo de CommonEncryption. Para obter mais informações, consulte o artigo: [criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).


###<a name="get-delivery-url"></a>Obter o URL de entrega

Obter o URL de entrega para o método de entrega PlayReady da chave de conteúdo que criou no passo anterior. Um cliente utiliza o URL devolvido para pedir uma licença de PlayReady ordem para reproduzir o conteúdo protegido. Para mais informações, consulte o artigo [Obter o URL de entrega](#get_delivery_url).

###<a name="create-asset-delivery-policy"></a>Criar a política de entrega de elementos

O pedido de HTTP seguinte cria **AssetDeliveryPolicy** que esteja configurado para aplicar a encriptação comuns dinâmica (**DynamicCommonEncryption**) para o protocolo **Transmissão suaves** (neste exemplo, outros protocolos serão bloqueados da transmissão). 

Para obter informações sobre quais os valores pode especificar quando criar uma AssetDeliveryPolicy, consulte a secção [tipos de utilizados quando definir AssetDeliveryPolicy](#types) .   


Pedido:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
    x-ms-version: 2.11
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Se quiser proteger o seu conteúdo utilizando Widevine DRM, atualize os valores de AssetDeliveryConfiguration a utilizar WidevineLicenseAcquisitionUrl (que tem o valor de 7) e especifique o URL de um serviço de entrega de licença. Pode utilizar os parceiros de AMS seguintes para ajudá-lo a entregar Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

Por exemplo: 
 
    
    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

>[AZURE.NOTE]Quando encripta com Widevine, apenas seria conseguir entregar a utilizar o travessão. Certifique-se especificar o protocolo de entrega de elementos travessão (2).
  
###<a name="link-asset-with-asset-delivery-policy"></a>Elementos de ligação com a política de entrega de elementos

Consulte o artigo [elementos de ligação com a política de entrega de elementos](#link_asset_with_asset_delivery_policy)


##<a id="types"></a>Tipos de utilizados quando definir AssetDeliveryPolicy

###<a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }

###<a name="contentkeydeliverytype"></a>ContentKeyDeliveryType


    /// <summary>
    /// Delivery method of the content key to the client.
    ///
    </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }


###<a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>

    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
