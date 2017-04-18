<properties 
    pageTitle="Configurar políticas de entrega de elementos com .NET SDK | Microsoft Azure" 
    description="Este tópico mostra como configurar políticas de entrega de diferentes elementos com Azure multimédia serviços .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;mingfeiy"/>

#<a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurar políticas de entrega de elementos com .NET SDK
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##<a name="overview"></a>Descrição geral

Se planeia para elementos de entrega encriptado, um destes passos no fluxo de trabalho de entrega de conteúdos dos serviços de multimédia está a configurar políticas de entrega para recursos. A política de entrega de recurso indica dos serviços de multimédia como que pretende para o seu activo a ser entregue: para o qual a transmissão de protocolo devem seu elementos dinamicamente empacotados (por exemplo, MPEG travessão, HLS, transmissão suaves ou todos), se pretende ou não que encriptar dinamicamente seu ativo e como (envelope ou encriptação comuns).

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
- Se tiver um ativo com um localizador de transmissão existente, não é possível ligar uma nova política para activo (pode desassociar uma política existente a partir do elemento, ou atualizar uma política de entrega associada activo).  Primeiro tem de remover o localizador de transmissão, ajustar as políticas que foram e, em seguida, recriar o localizador de transmissão.  Pode utilizar o mesmo locatorId quando é recriar o localizador de transmissão mas deverá garantir que não causar problemas para clientes dado que conteúdo pode ser colocada em cache pela origem ou de uma CDN descendentes.


##<a name="clear-asset-delivery-policy"></a>Política de entrega de limpar activo

Especifica o seguinte método **ConfigureClearAssetDeliveryPolicy** para não aplicar encriptação dinâmica e para entregar a sequência de em qualquer um dos seguintes protocolos: protocolos MPEG travessão, HLS e transmissão suaves. Poderá pretender aplicar esta política para os seus ativos de armazenamento encriptado.

Para obter informações sobre quais os valores pode especificar quando criar uma AssetDeliveryPolicy, consulte a secção [tipos de utilizados quando definir AssetDeliveryPolicy](#types) .

ConfigureClearAssetDeliveryPolicy void público estático (IAsset activo) {IAssetDeliveryPolicy política = _context. AssetDeliveryPolicies.Create ("Política limpar", AssetDeliveryPolicyType.NoDynamicEncryption, AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, nulo);

elementos. DeliveryPolicies.Add(policy); }

##<a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicCommonEncryption


O seguinte método **CreateAssetDeliveryPolicy** cria **AssetDeliveryPolicy** que esteja configurado para aplicar a encriptação comuns dinâmica (**DynamicCommonEncryption**) a um bom protocolo de transmissão (outros protocolos vai ser bloqueados transmissão). O método assume dois parâmetros: **activo** (elemento ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **CommonEncryption** , para obter mais informações, consulte o artigo: [criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obter informações sobre quais os valores pode especificar quando criar uma AssetDeliveryPolicy, consulte a secção [tipos de utilizados quando definir AssetDeliveryPolicy](#types) .


estático CreateAssetDeliveryPolicy de void pública (IAsset elementos, IContentKey chave) {Uri acquisitionUrl = chave. GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

Dicionário < AssetDeliveryPolicyConfigurationKey, cadeia > assetDeliveryPolicyConfiguration = novo dicionário < AssetDeliveryPolicyConfigurationKey, cadeia > {{AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()}};

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure dos serviços de multimédia também permite-lhe adicionar Widevine encriptação. O exemplo seguinte demonstra PlayReady e Widevine ser adicionado à política de entrega ativo.


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        

        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Quando encripta com Widevine, apenas seria conseguir entregar a utilizar o travessão. Certifique-se especificar TRAÇO o protocolo de entrega ativo.


##<a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicEnvelopeEncryption 

O seguinte método **CreateAssetDeliveryPolicy** cria **AssetDeliveryPolicy** que esteja configurado para aplicar a encriptação de envelope dinâmicas (**DynamicEnvelopeEncryption**) a protocolos transmissão suaves, HLS e travessão (se decidir não especificar alguns protocolos, estes serão bloqueados da transmissão). O método assume dois parâmetros: **activo** (elemento ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **EnvelopeEncryption** , para obter mais informações, consulte o artigo: [criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Para obter informações sobre quais os valores pode especificar quando criar uma AssetDeliveryPolicy, consulte a secção [tipos de utilizados quando definir AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Tipos de utilizados quando definir AssetDeliveryPolicy

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

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

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

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

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

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


