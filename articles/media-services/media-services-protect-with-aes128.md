<properties
    pageTitle="Utilizar encriptação AES de 128 de dinâmica e o serviço de entrega chave | Microsoft Azure"
    description="Serviços de multimédia do Microsoft Azure permite-lhe fornecer o seu conteúdo encriptado com chaves de encriptação AES de 128-bit. Serviços de multimédia também fornecem o serviço de entrega de chave que proporciona chaves de encriptação para os utilizadores autorizados. Este tópico mostra como dinamicamente encriptar com AES de 128 e utilizar o serviço de entrega chave."
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
    ms.date="10/24/2016"
    ms.author="juliako"/>

#<a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Utilizar encriptação AES de 128 de dinâmica e o serviço de entrega chave

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-aes128.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##<a name="overview"></a>Descrição geral

>[AZURE.NOTE] Consulte [Este](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) vídeo para obter uma descrição geral de como pode proteger o seu conteúdo de multimédia com encriptação AES.

Serviços de multimédia do Microsoft Azure permite-lhe entregar Http-Live-Streaming (HLS) e suaves sequências encriptadas com encriptação AES (Advanced Standard) (utilizando as chaves de encriptação de 128-bit). Serviços de multimédia também fornecem o serviço de entrega de chave que proporciona chaves de encriptação para os utilizadores autorizados. Se pretender para serviços de multimédia encriptar um ativo, tem de associar uma chave de encriptação elemento e também de configurar políticas de autorização para a chave. Quando uma sequência é pedida por um leitor de, dos serviços de multimédia utiliza a chave especificada para encriptar dinamicamente o conteúdo a utilizar encriptação AES. Para desencriptar a sequência, o leitor irá pedir a chave de serviço de entrega chave. Para decidir se ou não o utilizador está autorizado para obter a chave, o serviço avalia as políticas de autorização que especificou na chave.

Dos serviços de multimédia suporta várias formas de autenticação de utilizadores que fazem os pedidos de chaves. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição. A política de restrita token tem de ser acompanhar por um token emitido por uma seguro Token serviço (STS). Dos serviços de multimédia suporta tokens no formato de [Tokens de Web simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para mais informações, consulte o artigo [Configurar a política de autorização a chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para tirar partido de encriptação dinâmico, tem de ter um ativo que contém um conjunto de velocidade multi MP4 ficheiros ou ficheiros de origem da transmissão suaves multi velocidade. Também precisa de configurar a política de entrega para o ativo (descrita posteriormente neste tópico). Em seguida, com base no formato especificado no URL transmissão, o pedido no servidor de transmissão de irá garantir que a sequência é entregue no protocolo que escolheu. Como resultado, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e serviço dos serviços de multimédia irá criar e servir a resposta adequada baseada nos pedidos de um cliente.

Este tópico ser úteis para os programadores que funcionam nas aplicações que entregar conteúdo multimédia protegido. O tópico mostra-lhe como configurar o serviço de entrega chave com políticas de autorização para que apenas os clientes autorizados poderá receber as chaves de encriptação. Também mostra como utilizar a encriptação dinâmica.

>[AZURE.NOTE]Para começar a utilizar encriptação dinâmica, primeiro terá de obter pelo menos uma unidade de escala (também conhecido como transmissão unidade). Para mais informações, consulte o artigo [como dimensionar um serviço de multimédia](media-services-portal-manage-streaming-endpoints.md).

##<a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Encriptação AES de 128 de dinâmica e de fluxo de trabalho de serviço de entrega chave

Seguem-se os passos gerais que precisa para executar ao encriptar seus ativos com AES, utilizar o serviço de entrega chave dos serviços de multimédia e também utilizar encriptação dinâmica.

1. [Criar um ativo e carregar ficheiros para o elemento](media-services-protect-with-aes128.md#create_asset).
1. [Codificar elemento que contém o ficheiro para a velocidade de bits ajustável que mp4 definir](media-services-protect-with-aes128.md#encode_asset).
1. [Criar uma chave de conteúdo e associá-la com o activo codificado](media-services-protect-with-aes128.md#create_contentkey). Serviços de multimédia, a chave de conteúdo contém a chave de encriptação o activo.
1. [Configurar a política de autorização a chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy). A política de autorização de chave conteúdo tem de ser configurada por si e satisfeita pelo cliente para que a chave de conteúdo a ser entregues ao cliente.
1. [Configurar a política de entrega de um ativo](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Inclui a configuração da política de entrega: aquisição URL e inicialização Vector (IV) chave (AES de 128 requer o mesmo IV ser fornecido quando encriptar e desencriptar), protocolo de entrega (por exemplo, MPEG travessão, HLS, HDS, transmissão suaves ou todos), o tipo de encriptação dinâmica (por exemplo, envelope ou sem encriptação dinâmica).

Pode aplicar política diferente para cada protocolo no mesmo activo. Por exemplo, pode aplicar PlayReady encriptação suave/travessão e AES Envelope para HLS. Quaisquer protocolos que não são definidos numa política de entrega (por exemplo, adicionar uma única política apenas especifica HLS como o protocolo) serão bloqueados da transmissão. A exceção é, não se tem nenhuma política de entrega de elementos definida de todo. Em seguida, todos os protocolos concede em claro.

1. [Criar um pedido locator](media-services-protect-with-aes128.md#create_locator) para obter um URL de transmissão.

O tópico também mostra [como uma aplicação de cliente pode pedir uma chave do serviço de entrega chave](media-services-protect-with-aes128.md#client_request).

Irá encontrar um.NET completo [exemplo](media-services-protect-with-aes128.md#example) no final do tópico.

A seguinte imagem demonstra o fluxo de trabalho descrito acima. Aqui o token é utilizado para autenticação.

![Proteger com AES de 128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Os restantes este tópico fornece explicações detalhadas, exemplos de código e ligações para tópicos que lhe mostram como alcançar as tarefas descritas anteriormente.

##<a name="current-limitations"></a>Limitações atuais

Se adicionar ou atualizar a política de entrega do seu elementos, tem de eliminar um locator existente (se existir) e criar um novo localizador.

##<a id="create_asset"></a>Criar um ativo e carregar ficheiros para o elemento

Para gerir, descodificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o seu conteúdo para dos serviços de multimédia do Microsoft Azure. Depois de carregado, o conteúdo está armazenado segura na nuvem para processamento suplementar e transmissão. 

Para obter informações detalhadas, consulte o artigo [Carregar ficheiros para uma conta dos serviços de multimédia](media-services-dotnet-upload-files.md).

##<a id="encode_asset"></a>Codificar elemento que contém o ficheiro para a velocidade de bits ajustável que definir MP4

Com encriptação dinâmica tudo o que necessita é criar um ativo que contém um conjunto de velocidade multi MP4 ficheiros ou ficheiros de origem da transmissão suaves multi velocidade. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, a transmissão da pedido no servidor irá garantir que recebe a sequência de no protocolo que escolheu. Como resultado, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e serviço dos serviços de multimédia irá criar e servir a resposta adequada baseada nos pedidos de um cliente. Para mais informações, consulte o tópico [Descrição geral de embalagem dinâmica](media-services-dynamic-packaging-overview.md) .

Para obter instruções sobre como codificar, consulte o artigo [como codificar um ativo utilizando Media Encoder padrão](media-services-dotnet-encode-with-media-encoder-standard.md).

##<a id="create_contentkey"></a>Criar uma chave de conteúdo e associá-lo a codificado activo

Serviços de multimédia, a chave de conteúdo contém a chave que pretende encriptar um ativo com.

Para obter informações detalhadas, consulte o artigo [Criar chave de conteúdo](media-services-dotnet-create-contentkey.md).

##<a id="configure_key_auth_policy"></a>Configurar a política de autorização a chave de conteúdo

Dos serviços de multimédia suporta várias formas de autenticação de utilizadores que fazem os pedidos de chaves. A política de autorização de chave conteúdo deve ser configurada por si e satisfeita pelo cliente (player) para que a tecla para entregues ao cliente. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir, token restrição ou restrição de IP.

Para obter informações detalhadas, consulte o artigo [Configurar a política de autorização de chave de conteúdo](media-services-dotnet-configure-content-key-auth-policy.md).

##<a id="configure_asset_delivery_policy"></a>Configurar a política de entrega de elementos 

Configure a política de entrega para o item. Algumas coisas que a configuração de política de entrega de elementos inclui:

- O URL de aquisição chave. 
- A inicialização Vector (IV) para utilizar a encriptação do envelope. AES de 128 requer o mesmo IV ser fornecido quando encriptar e desencriptar. 
- Protocolo de entrega activo (por exemplo, MPEG travessão, HLS, HDS, transmissão suaves ou todos).
- O tipo de encriptação dinâmica (por exemplo, envelope AES) ou sem encriptação dinâmica. 

Para obter informações detalhadas, consulte o artigo [Configurar política de entrega de elementos ](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Criar um pedido de transmissão locator para obter um URL de transmissão

Terá de fornecer o utilizador com o URL de transmissão para suave, TRAÇO ou HLS.

>[AZURE.NOTE]Se adicionar ou atualizar a política de entrega do seu elementos, tem de eliminar um locator existente (se existir) e criar um novo localizador.

Para obter instruções sobre como publicar um ativo e crie um URL de transmissão, consulte o artigo [criar um URL de transmissão](media-services-deliver-streaming-content.md).

##<a name="get-a-test-token"></a>Obter um teste token

Obter um teste token com base na restrição token que foi utilizada para a política de autorização da chave.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
    
    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Pode utilizar o [Leitor de AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar a sua sequência.

##<a id="client_request"></a>Como pode o cliente pedir uma chave de serviço de entrega chave?

No passo anterior, construída o URL que aponta para um ficheiro de manifesto. O cliente tem de extrair as informações necessárias a partir dos ficheiros de manifesto transmissão encaminhá-la um pedido para o serviço de entrega chave.

###<a name="manifest-files"></a>Ficheiros de manifesto

O cliente precisa extrair o URL (que também contém conteúdo chave Id (crianças)) valor a partir do ficheiro de manifesto. O cliente, em seguida, tentar obter a chave de encriptação do serviço de entrega chave. O cliente também precisa extrair o valor de IV e utilize-desencriptar a sequência de. A seguinte mostra o fragmento de <Protection> elemento do manifesto transmissão suaves.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

No caso de HLS, o manifesto de raiz é dividido em ficheiros de segmento. 

Por exemplo, o manifesto de raiz é: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) e este contém uma lista de nomes de ficheiro do segmento.
    
    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Se abrir um dos ficheiros segmento Editor de texto (por exemplo, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels (514369)/Manifest(video,format=m3u8-aapl), deve conter #extensão X-chave que indica que o ficheiro está encriptado.
    
    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST
    
###<a name="request-the-key-from-the-key-delivery-service"></a>Pedir a chave do serviço de entrega chave

O código seguinte mostra como enviar um pedido para o serviço de entrega chave dos serviços de multimédia com uma chave entrega Uri (que foi extraída de manifesto) e um token (Este tópico não falar sobre como obter Tokens de Web simples a partir de um serviço de tokens seguro).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);
                
        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;
    
        var response = request.GetResponse();
     
        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }
    
        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();
    
        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
    
##<a id="example"></a>Exemplo

1. Crie um novo projeto de consola.
1. Utilize NuGet para instalar e adicionar Azure multimédia serviços .NET SDK extensões. Instalar este pacote, também instala o .NET SDK serviços de multimédia e adiciona todas as outras dependências.
2. Adicione o ficheiro de configuração que contém o nome da conta e informações da chave:

    
        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>
            
                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>
            
                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>

1. Substitui o código no seu ficheiro Program.cs com o código mostrado nesta secção.
    
    Certifique-se atualizar variáveis para apontarem para pastas onde se encontram os seus ficheiros de entrada.
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Net;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Newtonsoft.Json.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using System.Web;
        using System.Globalization;
        
        namespace AESDynamicEncryptionAndKeyDeliverySvc
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                // A Uri describing the issuer of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                // The Audience or Scope of the token.  
                // Must match the value in the token for the token to be considered valid.
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);
        
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
        
                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");
        
                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
        
        
                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the chached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateEnvelopeTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine();
        
                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);
        
                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();
        
                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();
        
                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
        
                        // Generate a test token based on the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
        
                        //The GenerateTestToken method returns the token without the word “Bearer” in front
                        //so you have to add it in front of the token string. 
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the bit.ly/aesplayer Flash player to test the URL 
                    // (with open authorization policy). 
                    // Paste the URL and click the Update button to play the video. 
                    //
                    string URL = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Smooth Streaming Url: {0}/manifest", URL);
                    Console.WriteLine();
                    Console.WriteLine("HLS Url: {0}/manifest(format=m3u8-aapl)", URL);
                    Console.WriteLine();
        
                    Console.ReadLine();
                }
        
                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }
        
                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
        
                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
        
                    Console.WriteLine("Created assetFile {0}", assetFile.Name);
        
                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);
        
                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
        
                    Console.WriteLine("Upload {0}", assetFile.Name);
        
                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);
        
                    locator.Delete();
                    policy.Delete();
        
                    return inputAsset;
                }
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
        
                    // Create a task with the encoding details, using a string preset.
                    // In this case "H264 Multiple Bitrate 720p" preset is used.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        TaskOptions.None);
        
                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset",
                        AssetCreationOptions.StorageEncrypted);
        
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
                    return job.OutputMediaAssets[0];
                }
        
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
                    return processor;
                }
        
                static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
                {
                    // Create envelope encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.EnvelopeEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy             
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("Open Authorization Policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                        new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "HLS Open Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null // no requirements needed for HLS
                                };
        
                    restrictions.Add(restriction);
        
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                        "policy",
                        ContentKeyDeliveryType.BaselineHttp,
                        restrictions,
                        "");
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    IContentKeyAuthorizationPolicy policy = _context.
                                            ContentKeyAuthorizationPolicies.
                                            CreateAsync("HLS token restricted authorization policy").Result;
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                            new List<ContentKeyAuthorizationPolicyRestriction>();
        
                    ContentKeyAuthorizationPolicyRestriction restriction =
                            new ContentKeyAuthorizationPolicyRestriction
                            {
                                Name = "Token Authorization Policy",
                                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                Requirements = tokenTemplateString
                            };
        
                    restrictions.Add(restriction);
        
                    //You could have multiple options 
                    IContentKeyAuthorizationPolicyOption policyOption =
                        _context.ContentKeyAuthorizationPolicyOptions.Create(
                            "Token option for HLS",
                            ContentKeyDeliveryType.BaselineHttp,
                            restrictions,
                            null  // no key delivery data is needed for HLS
                            );
        
                    policy.Options.Add(policyOption);
        
                    // Add ContentKeyAutorizationPolicy to ContentKey
                    contentKey.AuthorizationPolicyId = policy.Id;
                    IContentKey updatedKey = contentKey.UpdateAsync().Result;
                    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
        
                    return tokenTemplateString;
                }
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        
                    string envelopeEncryptionIV = Convert.ToBase64String(GetRandomBuffer(16));
            
                    // When configuring delivery policy, you can choose to associate it
                    // with a key acquisition URL that has a KID appended or
                    // or a key acquisition URL that does not have a KID appended  
                    // in which case a content key can be reused. 

                    // EnvelopeKeyAcquisitionUrl:  contains a key ID in the key URL.
                    // EnvelopeBaseKeyAcquisitionUrl:  the URL does not contains a key ID

                    // The following policy configuration specifies: 
                    // key url that will have KID=<Guid> appended to the envelope and
                    // the Initialization Vector (IV) to use for the envelope encryption.
                    
                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                    {
                                {AssetDeliveryPolicyConfigurationKey.EnvelopeKeyAcquisitionUrl, keyAcquisitionUri.ToString()}
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
                    Console.WriteLine("Adding Asset Delivery Policy: " +
                        assetDeliveryPolicy.AssetDeliveryPolicyType);
                }
        
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                EndsWith(".ism")).
                                                FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
                    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);
        
                    // Create a locator to the streaming content on an origin. 
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));
        
                    // Create a URL to the manifest file. 
                    return originLocator.Path + assetFile.Name;
                }
        
                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
        
                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
        
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
        
                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int size)
                {
                    byte[] randomBytes = new byte[size];
                    using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(randomBytes);
                    }
        
                    return randomBytes;
                }
            }
        }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
