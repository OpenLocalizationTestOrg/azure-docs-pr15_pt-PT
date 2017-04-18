<properties
    pageTitle="Utilizar a encriptação comuns dinâmica PlayReady e/ou Widevine | Microsoft Azure"
    description="Serviços de multimédia do Microsoft Azure permite-lhe entregar sequências de TRAÇO MPEG, transmissão suaves e transmissão Http Live (HLS) protegidas com Microsoft PlayReady DRM. Também permite-lhe para entrega de TRAÇO encriptado com Widevine DRM. Este tópico mostra como dinamicamente encriptar com PlayReady e Widevine DRM."
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="09/27/2016"
    ms.author="juliako"/>


#<a name="using-playready-andor-widevine-dynamic-common-encryption"></a>Utilizar a encriptação comuns dinâmica PlayReady e/ou Widevine

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Serviços de multimédia do Microsoft Azure permite-lhe entregar sequências de TRAÇO MPEG, transmissão suaves e transmissão HTTP Live (HLS) protegidas com [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). Também permite-lhe fornecer sequências de TRAÇO encriptadas com Widevine DRM licenças. PlayReady e Widevine são encriptados pela especificação de encriptação comum (ISO/IEC 23001-7 CENC). Pode utilizar [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando com a versão 3.5.1) ou REST API para configurar o seu AssetDeliveryConfiguration para utilizar Widevine.

Dos serviços de multimédia fornece um serviço para fornecer PlayReady e Widevine DRM licenças. Serviços de multimédia também fornecem APIs que permitem-lhe configurar os direitos e restrições que pretende para o runtime PlayReady ou Widevine DRM impor quando um utilizador for reproduzido conteúdo protegido. Quando um utilizador pede uma conteúdo protegido por DRM, a aplicação de leitor irá pedir uma licença de serviço de licença AMS. O serviço de licenças AMS irá emitir uma licença para o leitor se está autorizado. Uma licença PlayReady ou Widevine contém a chave de desencriptação que pode ser utilizada pelo leitor de cliente desencriptar e transmitir em fluxo conteúdo.


Também pode utilizar os parceiros de AMS seguintes para ajudá-lo a entregar Widevine licenças: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). Para obter mais informações, consulte o artigo: integração com o [Axinom](media-services-axinom-integration.md) e [castLabs](media-services-castlabs-integration.md).

Dos serviços de multimédia suporta várias formas de autorização de utilizadores que efectuam pedidos de chaves. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição. A política de restrita token tem de ser acompanhar por um token emitido por uma seguro Token serviço (STS). Dos serviços de multimédia suporta tokens no formato de [Tokens de Web simples](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e formato [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obter mais informações, consulte o artigo configurar a política de autorização a chave de conteúdo.

Para tirar partido de encriptação dinâmico, tem de ter um ativo que contém um conjunto de velocidade multi MP4 ficheiros ou ficheiros de origem da transmissão suaves multi velocidade. Também precisa de configurar as políticas de entrega para o activo (descrita posteriormente neste tópico). Em seguida, com base no formato especificado no URL transmissão, o pedido no servidor de transmissão de irá garantir que a sequência é entregue no protocolo que escolheu. Como resultado, necessita apenas armazenar e pagar para os ficheiros num formato de armazenamento única e dos serviços de multimédia irá criar e servir a resposta HTTP adequada com base em cada pedido a partir de um cliente.

Este tópico ser úteis para os programadores que funcionam nas aplicações que entregar multimédia protegida com vários DRMs, tais como PlayReady e Widevine. O tópico mostra-lhe como configurar o serviço de entrega de licenças PlayReady com políticas de autorização para que apenas os clientes autorizados poderá receber PlayReady ou Widevine licenças. Também mostra como utilizar encriptação dinâmico encriptação com PlayReady ou Widevine DRM ao longo do TRAÇO.

>[AZURE.NOTE]Para começar a utilizar encriptação dinâmica, primeiro terá de obter pelo menos uma unidade de escala (também conhecido como transmissão unidade). Para mais informações, consulte o artigo [como dimensionar um serviço de multimédia](media-services-portal-manage-streaming-endpoints.md).


##<a name="download-sample"></a>Transferir exemplo

Pode transferir a amostra é descrita neste artigo de [aqui](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

##<a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configurar dinâmica encriptação comuns e serviços de entrega de licença DRM

Seguem-se os passos gerais que precisa para executar quando proteger os seus ativos com PlayReady, utilizar o serviço de entrega de licenças dos serviços de multimédia e também utilizar encriptação dinâmica.

1. Criar um ativo e carregar ficheiros para o elemento.
1. Codificar elemento que contém o ficheiro para a velocidade de bits ajustável que mp4 definir.
1. Criar uma chave de conteúdo e associá-lo a codificado elemento. Serviços de multimédia, a chave de conteúdo contém a chave de encriptação o activo.
1. Configure a política de autorização a chave de conteúdo. A política de autorização de chave conteúdo tem de ser configurada por si e satisfeita pelo cliente para que a chave de conteúdo a ser entregues ao cliente.

Ao criar a política de autorização de chave de conteúdo, precisa de especificar o seguinte: método (PlayReady ou Widevine), restrições de entrega (abertas ou tokens) e as informações específicas para o tipo de entrega chave que define como a chave seja entregue para o cliente (modelo de licença[PlayReady](media-services-playready-license-template-overview.md) ou [Widevine](media-services-widevine-license-template-overview.md) ).
1. Configure a política de entrega de um ativo. Inclui a configuração da política de entrega: protocolo de entrega (por exemplo, MPEG travessão, HLS, HDS, transmissão suaves ou todos), o tipo de encriptação dinâmica (por exemplo, comuns encriptação) PlayReady ou Widevine licença aquisição URL.

Pode aplicar política diferente para cada protocolo no mesmo activo. Por exemplo, pode aplicar PlayReady encriptação suave/travessão e AES Envelope para HLS. Quaisquer protocolos que não são definidos numa política de entrega (por exemplo, adicionar uma única política apenas especifica HLS como o protocolo) serão bloqueados da transmissão. A exceção é, não se tem nenhuma política de entrega de elementos definida de todo. Em seguida, todos os protocolos concede em claro.
1. Crie um pedido locator para obter um URL de transmissão.

Irá encontrar um exemplo de .NET concluído no final do tópico.

A seguinte imagem demonstra o fluxo de trabalho descrito acima. Aqui o token é utilizado para autenticação.

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Os restantes este tópico fornece explicações detalhadas, exemplos de código e ligações para tópicos que lhe mostram como alcançar as tarefas descritas anteriormente.

##<a name="current-limitations"></a>Limitações atuais

Se adicionar ou atualizar uma política de entrega de elementos, tem de eliminar o localizador de associado (se existir) e criar um novo localizador.

Limitação quando encriptá com Widevine com os serviços de multimédia do Azure: atualmente, não são suportadas múltiplas teclas conteúdas.

##<a name="create-an-asset-and-upload-files-into-the-asset"></a>Criar um ativo e carregar ficheiros para o elemento

Para gerir, descodificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o seu conteúdo para dos serviços de multimédia do Microsoft Azure. Depois de carregado, o conteúdo está armazenado segura na nuvem para processamento suplementar e transmissão.

Para obter informações detalhadas, consulte o artigo [Carregar ficheiros para uma conta dos serviços de multimédia](media-services-dotnet-upload-files.md).

##<a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codificar elemento que contém o ficheiro para a velocidade de bits ajustável que definir MP4

Com encriptação dinâmica tudo o que necessita é criar um ativo que contém um conjunto de velocidade multi MP4 ficheiros ou ficheiros de origem da transmissão suaves multi velocidade. Em seguida, com base no formato especificado no pedido de manifesto e fragmento, o servidor de transmissão no pedido irá garantir que recebe a sequência de no protocolo que escolheu. Como resultado, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e serviço dos serviços de multimédia irá criar e servir a resposta adequada baseada nos pedidos de um cliente. Para mais informações, consulte o tópico [Descrição geral de embalagem dinâmica](media-services-dynamic-packaging-overview.md) .

Para obter instruções sobre como codificar, consulte o artigo [como codificar um ativo utilizando Media Encoder padrão](media-services-dotnet-encode-with-media-encoder-standard.md).


##<a id="create_contentkey"></a>Criar uma chave de conteúdo e associá-lo a codificado activo

Serviços de multimédia, a chave de conteúdo contém a chave que pretende encriptar um ativo com.

Para obter informações detalhadas, consulte o artigo [Criar chave de conteúdo](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configurar a política de autorização a chave de conteúdo

Dos serviços de multimédia suporta várias formas de autenticação de utilizadores que fazem os pedidos de chaves. A política de autorização de chave conteúdo deve ser configurada por si e satisfeita pelo cliente (player) para que a tecla para entregues ao cliente. A política de autorização de chave conteúdo poderia ter uma ou mais restrições de autorização: abrir ou token restrição.

Para obter informações detalhadas, consulte o artigo [Configurar a política de autorização de chave de conteúdo](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configurar a política de entrega de elementos 

Configure a política de entrega para o item. Algumas coisas que a configuração de política de entrega de elementos inclui:

- O URL de aquisição do DRM licença. 
- Protocolo de entrega activo (por exemplo, MPEG travessão, HLS, HDS, transmissão suaves ou todos). 
- O tipo de encriptação dinâmica (neste caso, a encriptação comuns). 

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

##<a id="example"></a>Exemplo


O exemplo seguinte demonstra a funcionalidade foi introduzida numa SDK de serviços de multimédia do Azure para .net-versão 3.5.2 (especificamente, a capacidade de definir um modelo de licença Widevine e pedir uma licença de Widevine a partir dos serviços de multimédia do Azure). O seguinte comando de pacote Nuget foi utilizado para instalar o pacote:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Crie um novo projeto de consola.
1. Utilize NuGet para instalar e adicionar Azure multimédia serviços .NET SDK.
2. Adicionar referências adicionais: System.Configuration.
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

1. Obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para obter mais informações, consulte o artigo: [Configurar pontos finais de transmissão](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Substitui o código no seu ficheiro Program.cs com o código mostrado nesta secção.
    
    Certifique-se atualizar variáveis para apontarem para pastas onde se encontram os seus ficheiros de entrada.
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;
        
        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
        
                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
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
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
        
                    bool tokenRestriction = false;
                    string tokenTemplateString = null;
        
                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);
        
                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
        
                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
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
        
                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified 
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }
        
                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
                     
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
        
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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
        
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
        
                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";
            
                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));
            
                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
            
                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
            
                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),    AssetCreationOptions.StorageEncrypted);
            
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();
            
                    return job.OutputMediaAssets[0];
                }
        
        
                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {
                    
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);
        
                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);
        
                    return key;
                }
        
                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {
        
                    // Create ContentKeyAuthorizationPolicy with Open restrictions 
                    // and create authorization policy          
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("", 
                            ContentKeyDeliveryType.Widevine, 
                            restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;
        
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }
        
                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();
        
                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };
        
                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
        
                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
        
                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);
        
                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
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
        
                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.
        
                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
                    //It contains a field for a custom data string between the license server and the application 
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
        
                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users. 
                    //It contains the data on the content key in the license and any rights or restrictions to be 
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client) 
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
        
                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;
        
                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
                    // It grants the user the ability to playback the content subject to the zero or more restrictions 
                    // configured in the license and on the PlayRight itself (for playback specific policy). 
                    // Much of the policy on the PlayRight has to do with output restrictions 
                    // which control the types of outputs that the content can be played over and 
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
                    //then the DRM runtime will only allow the video to be displayed over digital outputs 
                    //(analog video outputs won’t be allowed to pass the content).
        
                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
                    // If the output protections are configured too restrictive, 
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
        
                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
        
                    responseTemplate.LicenseTemplates.Add(licenseTemplate);
        
                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }
        
        
                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };
        
                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }
        
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
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
        
                        };
        
                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);
        
        
                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
        
                }
        
                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {
        
                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset. 
        
                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();
        
                    // Create a 30-day readonly access policy. 
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
        
                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }
        
                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];
        
                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }
        
                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="see-also"></a>Consulte também

[CENC com Multi DRM e controlo de acesso](media-services-cenc-with-multidrm-access-control.md)

[Configurar Widevine embalagem com AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Anunciar o Google Widevine serviços de entrega de licença nos serviços de multimédia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
