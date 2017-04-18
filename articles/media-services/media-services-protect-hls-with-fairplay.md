<properties 
    pageTitle="Proteger a sua HLS conteúdo com o Apple FairPlay e/ou Microsoft PlayReady | Microsoft Azure" 
    description="Este tópico fornece uma descrição geral e mostra como utilizar dos serviços de multimédia do Azure para encriptar dinamicamente o conteúdo de HTTP Live transmissão (HLS) com o Apple FairPlay. Também mostra como utilizar o serviço de entrega de licenças dos serviços de multimédia para entregar FairPlay licenças para clientes." 
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
    ms.date="09/27/2016"
    ms.author="juliako"/>

# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Proteger a sua HLS conteúdo com o Apple FairPlay e/ou Microsoft PlayReady

Azure dos serviços de multimédia permite-lhe encriptar dinamicamente o conteúdo de HTTP Live transmissão (HLS) utilizando os seguintes formatos:  

- **Tecla de limpar AES de 128 envelope** 

    A secção inteira está encriptada utilizando o modo **AES de 128 CBC** . O desencriptar da sequência é suportado iOS e o x player vierem. Para mais informações, consulte [Este artigo](media-services-protect-with-aes128.md).

- **Apple FairPlay** 

    Os exemplos de vídeo e áudio individuais são encriptados utilizando o modo **AES de 128 CBC** . **Transmissão de FairPlay** (FPS) está integrado no sistemas operativos do dispositivo, com o suporte nativo no iOS e de TV da Apple. Safari nos X permite FPS utilizando o suporte de interface de extensões de multimédia encriptados (EME).
- **Microsoft PlayReady**

A imagem seguinte mostra o fluxo de trabalho **HLS + FairPlay e/ou PlayReady encriptação dinâmica** .

![Proteger com FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Este tópico demonstra como utilizar dos serviços de multimédia do Azure para encriptar dinamicamente o conteúdo HLS com Apple FairPlay. Também mostra como utilizar o serviço de entrega de licenças dos serviços de multimédia para entregar FairPlay licenças para clientes.

>[AZURE.NOTE] Se também pretende encriptar o seu conteúdo HLS com PlayReady, tem de criar uma chave de conteúdo comum e associá-la ao seu ativo. Também precisa de configurar a política de autorização a chave de conteúdo, tal como descrito no tópico [PlayReady utilizar encriptação comuns dinâmica](media-services-protect-with-drm.md) .

    
## <a name="requirements-and-considerations"></a>Requisitos e considerações

- É necessário o seguinte quando utilizar AMS para a prestação HLS encriptados com FairPlay e para a prestação FairPlay licenças.

    - Uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
    - Uma conta dos serviços de multimédia. Para criar uma conta dos serviços de multimédia, consulte o artigo [Criar conta](media-services-portal-create-account.md).
    - Inscreva-se com [O programa de desenvolvimento da Apple](https://developer.apple.com/).
    - Apple requer o proprietário do conteúdo obter o [pacote de implementação](https://developer.apple.com/contact/fps/). Indicar o pedido que já implementado KSM (módulo de segurança de chave) com os serviços de multimédia do Azure e que está a pedir o pacote FPS final. Será instruções no pacote FPS final para gerar certificação e obter ASK que irá utilizar para configurar FairPlay. 

    - Azure multimédia serviços .NET SDK versão **3.6.0** ou posterior.

- As seguintes coisas tem de estar definidas no lado de entrega chave AMS:
    - **Orientação do diapositivo notas de aplicação (AC)** - ficheiro. pfx que contém a chave privada. Este ficheiro é criado pelo cliente e encriptado com uma palavra-passe ao mesmo cliente. 
        
        Quando o cliente configura política de entrega chave, são tem de fornecer essa palavra-passe e PFX no formato base64.

        Os passos seguintes descrevem como gerar um certificado pfx para FairPlay.
        
        1. Instalar OpenSSL a partir de https://slproweb.com/products/Win32OpenSSL.html
        
            Vá para a pasta onde o certificado de FairPlay e outros ficheiros emitidos por Apple estão.
        
        2. Linha de comandos para converter cer pem:
        
            "C:\OpenSSL-Win32\bin\openssl.exe" x509-informar der-no fairplay.cer-saída fairplay out.pem
        
        3. Linha de comandos para converter pem pfx com a chave privada (a palavra-passe para o ficheiro pfx, em seguida, é pedida por OpenSSL).
        
            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12-exportar - saída fairplay out.pfx-inkey privatekey.pem-no fairplay out.pem - passin file:privatekey-pem-pass.txt 
        
    - **Palavra-passe de aplicação orientação do diapositivo notas** - cliente palavra-passe para criar o ficheiro. pfx.
    - **ID de palavra-passe de aplicação orientação do diapositivo notas** - o cliente tem carregar a palavra-passe semelhante a como carregar outras teclas AMS e utilizar o valor de enumeração **ContentKeyType.FairPlayPfxPassword** . Como resultado, obterão id AMS que este é o que precisam de utilize dentro a opção de política de entrega chave.
    - **iv** - valor aleatório 16 bytes, tem de corresponder iv na política de entrega de elementos. Cliente gera o IV e coloca-a em ambos os locais: elementos política e chave entrega política opção de entrega. 
    - **PERGUNTAR** - ASK (tecla de palavra-passe de aplicação) for recebida quando gerar certificação através do portal de programador da Apple. Cada equipa de desenvolvimento irão receber um ASK exclusivo. Guarde uma cópia da fazer PERGUNTAS e armazená-lo num local seguro. Terá de configurar ASK como FairPlayAsk a serviços de multimédia do Azure mais tarde. 
    -  **ID de PERGUNTAR** - é obtida quando o cliente carrega ASk para AMS. O cliente tem carregar ASk utilizando o valor de enumeração **ContentKeyType.FairPlayASk** . Como resultado, é devolvido o id de AMS e este é o que deverá ser utilizado quando definir a opção de política de entrega chave.

- As seguintes coisas tem de estar definidas por FPS do lado do cliente:
    - **Orientação do diapositivo notas de aplicação (AC)** - ficheiro de.cer/.der que contenha chave pública que SO utiliza para encriptar algumas carga útil. AMS precisa de saber acerca do mesmo, porque é necessária pelo Media player. O serviço de entrega chave desencripta-utilizar a chave privada correspondente.

- Para uma sequência de encriptada FairPlay a reprodução, tem de obter real ASK primeiro e, em seguida, gerar um certificado real. Processo cria todas as peças de 3:

    -  der, 
    -  . pfx e 
    -  a palavra-passe para o. pfx.
 
- Os clientes que suportam HLS com encriptação **AES de 128 CBC** : Safari nos X, Apple TV, iOS.

##<a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Passos para configurar FairPlay dinâmicos licença e encriptação de serviços de entrega

Seguem-se os passos gerais que precisa para executar quando proteger os seus ativos com FairPlay, utilizar o serviço de entrega de licenças dos serviços de multimédia e também utilizar encriptação dinâmica.

1. Criar um ativo e carregar ficheiros para o elemento. 
1. Codificar elemento que contém o ficheiro para a velocidade de bits ajustável que mp4 definir.
1. Criar uma chave de conteúdo e associá-lo a codificado elemento.  
1. Configure a política de autorização a chave de conteúdo. Ao criar a política de autorização de chave de conteúdo, terá de especificar o seguinte: 
    
    - método de entrega (neste caso, FairPlay), 
    - Configuração de opções de política FairPlay. Para obter detalhes sobre como configurar FairPlay, consulte o artigo ConfigureFairPlayPolicyOptions() método no exemplo abaixo.
    
        >[AZURE.NOTE] Normalmente, que seria pretende configurar as opções de política de FairPlay apenas uma vez, uma vez que apenas terá um conjunto de certificação e ASK.
-restrições (abertos ou tokens), - e as informações específicas para o tipo de entrega chave que define como a chave seja entregue ao cliente. 
    
2. Configure a política de entrega de elementos. Configuração da política de entrega inclui: 

    - protocolo de entrega (HLS) 
    - o tipo de encriptação dinâmica (comuns CBC encriptação), 
    - URL de aquisição de licença. 
    
    >[AZURE.NOTE]Se quiser fornecer uma sequência de que está encriptada com FairPlay + DRM outra, tem de configurar as políticas de entrega separada 
    >
    >- Um IAssetDeliveryPolicy para configurar o travessão com CENC (PlayReady + WideVine) e suaves com PlayReady. 
    >- Outro IAssetDeliveryPolicy para configurar FairPlay para HLS

1. Crie um locator pedido para obter um URL de transmissão.

##<a name="using-fairplay-key-delivery-by-playerclient-apps"></a>Utilizar entrega chave FairPlay por aplicações de cliente/player

Os clientes poderá desenvolver aplicações de leitor utilizar iOS SDK. Para poder reproduzir conteúdo FairPlay os clientes têm que implementar o protocolo de troca de licença. O protocolo de exchange licença não é especificado da Apple. É como enviar pedidos de entrega de chave por excesso para cada aplicação. Os serviços de entrega chave AMS FairPlay espera SPC breve como mensagem de mensagem codificado www-form-url no seguinte formato: 

    spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player não suporta a reprodução de FairPlay terminar a caixa. Os clientes necessitam obter o leitor de exemplo a partir da conta de programador da Apple para obter FairPlay reprodução no MAC os x. 
 
##<a name="streaming-urls"></a>URLs transmissão

Se o recurso foi encriptado com mais do que um DRM, deve utilizar uma tag de encriptação no URL transmissão: (formato = 'm3u8 aapl' encriptação = 'xxx').

As seguintes considerações aplicam-se:

- Pode ser especificado apenas zero ou um tipo de encriptação.
- Tipo de encriptação não tem de ser especificado no url se apenas uma encriptação foi aplicada aos elemento.
- Tipo de encriptação é maiúsculas e minúsculas.
- Podem ser especificados os seguintes tipos de encriptação:  
    - **cenc**: comuns encriptação (Playready ou Widevine)
    - **cbcs aapl**: Fairplay
    - **CBC**: encriptação AES de envelope.


##<a name="net-example"></a>Exemplo de .NET


O exemplo seguinte demonstra a funcionalidade foi introduzida numa SDK de serviços de multimédia do Azure para .net-versão 3.6.0 (a capacidade de utilizar os serviços de multimédia do Azure para fornecer o seu conteúdo encriptado com FairPlay). O seguinte comando de pacote Nuget foi utilizado para instalar o pacote:

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Crie um projeto de consola.
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
            
        
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;
        
        namespace DynamicEncryptionWithFairPlay
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
        
                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
        
                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
        
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
        
                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);
        
                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);
        
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
        
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);
        
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
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
        
                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
        
        
                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);
        
                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;
        
                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
        
                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
        
                    return tokenTemplateString;
                }
        
                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);
        
                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);
        
                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();
        
                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
        
                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);
        
                    return FairPlayConfiguration;
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
        
                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
        
                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
        
                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
        
                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
        
                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account, 
                    // the player only recognizes a Key URL that starts with skd://. 
                    // However, if you are using a customized player, 
                    // you can choose whatever protocol you want. 
                    // For example, "https". 

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };
        
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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


##<a name="next-steps-media-services-learning-paths"></a>Passos seguintes: Serviços de multimédia caminhos de aprendizagem

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
