<properties 
    pageTitle="Como efetuar a transmissão direto através dos serviços de multimédia do Azure para criar fluxos de velocidade multi com .NET | Microsoft Azure" 
    description="Neste tutorial explica os passos da criação de um canal que recebe um fluxo direto de velocidade único e codifica-lo à sequência de velocidade multi utilizando .NET SDK." 
    services="media-services" 
    documentationCenter="" 
    authors="anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="juliako;anilmur"/>


#<a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-net"></a>Como efetuar a transmissão direto através dos serviços de multimédia do Azure para criar fluxos de velocidade multi com .NET

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
> Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F).

##<a name="overview"></a>Descrição geral

Neste tutorial orienta-o através dos passos de criação de um **canal** que recebe um fluxo direto de velocidade único e codifica-lo à sequência de velocidade multi.

Para mais informações conceptuais relacionados com canais que estejam ativados para codificação live, consulte o artigo [Live transmissão utilizando os serviços de multimédia do Azure ao criar sequências de velocidade multi](media-services-manage-live-encoder-enabled-channels.md).


##<a name="common-live-streaming-scenario"></a>Cenário de transmissão direto comum

Os passos seguintes descrevem tarefas envolvidas na criação de aplicações comuns de transmissão direto.

>[AZURE.NOTE] Atualmente, a duração recomendada máxima de um evento do live é 8 horas. Contacte o suportehttp amslived em Microsoft.com se de que precisa para executar um canal para períodos de tempo mais longos.

1. Liga uma câmara de vídeo num computador. Iniciação e configurar um codificador direto no local que pode exportar uma sequência de velocidade única de uma das seguintes protocolos: RTMP, transmissão suaves ou RTP (MPEG-TS). Para mais informações, consulte o artigo [suporte de RTMP de serviços de multimédia do Azure e codificadores Live](http://go.microsoft.com/fwlink/?LinkId=532824).

Este passo também pode ser realizado depois de criar o seu canal.

1. Criar e comece a um canal.

1. Obter o canal ingerir esta última URL.

O URL de ingest é utilizado pelo codificador direto para enviar a sequência de para o canal.

1. Obter o URL de pré-visualização do canal.

Utilize este URL para verificar o seu canal corretamente está a receber o fluxo direto.

2. Crie um ativo.
3. Se pretender para o activo a ser dinamicamente encriptados durante a reprodução, faça o seguinte:
1. Crie uma chave de conteúdo.
1. Configure a política de autorização a chave de conteúdo.
1. Configure a política de entrega de elementos (utilizada pela embalagem dinâmica e encriptação dinâmica).
3. Criar um programa e especificar a utilização de elemento que criou.
1. Publica activo associado ao programa através da criação de um locator pedido.

Certifique-se de ter, pelo menos, uma transmissão unidade reservada no ponto final transmissão a partir do qual pretende em sequência de conteúdo.

1. Inicie o programa quando estiver pronto para começar a transmissão e arquivo.
2. Opcionalmente, o codificador live pode serão avisados de que para iniciar um anúncio. Anúncio de é inserido na sequência de saída.
1. Pare o programa sempre que pretender parar streaming e arquivo o evento.
1. Eliminar o programa (e, opcionalmente, eliminar o activo).

## <a name="what-youll-learn"></a>O que vai aprender

Este tópico mostra-lhe como executar operações diferentes de canais e programas que utilizam .NET SDK serviços de multimédia. Uma vez que muitas operações são execução longa APIs .NET que gerir execução longa que bloqueiam operações são utilizadas.

O tópico mostra como o pode fazer o seguinte procedimento:

1. Criar e comece a um canal. APIs de execução longa são utilizadas.
1. Obter os canais ingerir esta última ponto final (entrada). Este ponto final deve ser fornecido ao codificador que pode enviar uma sequência de direto velocidade único.
1. Obter o ponto final de pré-visualização. Este ponto final é utilizado para pré-visualizar a sua sequência.
1. Crie um ativo que será utilizado para armazenar o seu conteúdo. As políticas de entrega de elementos devem ser configuradas bem, como é mostrado neste exemplo.
1. Criar um programa e especificar a utilização de elemento que foi criado anteriormente. Inicie o programa. APIs de execução longa são utilizadas.
1. Crie um localizador de elementos, para que o conteúdo é publicado e pode ser transmitido para os seus clientes.
1. Mostrar e ocultar Lousas. Iniciar e parar anúncios. APIs de execução longa são utilizadas.
1. Limpe o seu canal e todos os recursos associados.


##<a name="prerequisites"></a>Pré-requisitos

A seguinte é necessários para completar a iniciação.

- Para concluir este tutorial, é necessária uma conta Azure.

Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obter créditos que podem ser utilizados para experimentar a serviços Azure pagos. Mesmo depois de créditos são utilizados para cima, pode manter a conta e utilizar serviços Azure gratuitos e funcionalidades, como a funcionalidade de Web Apps no Azure aplicação de serviço.
- Uma conta dos serviços de multimédia. Para criar uma conta dos serviços de multimédia, consulte o artigo [Criar conta](media-services-portal-create-account.md).
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.
- Tem de utilizar serviços de multimédia .NET SDK versão 3.2.0.0 ou mais recente.
- Uma câmara Web e um codificador que pode enviar uma única velocidade live da cadeia.

##<a name="considerations"></a>Considerações sobre

- Atualmente, a duração recomendada máxima de um evento do live é 8 horas. Contacte o suportehttp amslived em Microsoft.com se de que precisa para executar um canal para períodos de tempo mais longos.
- Certifique-se de ter, pelo menos, uma transmissão unidade reservada no ponto final transmissão a partir do qual pretende em sequência de conteúdo.

##<a name="download-sample"></a>Transferir exemplo

Obter e executar um exemplo a partir do [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/).


##<a name="set-up-for-development-with-media-services-sdk-for-net"></a>Configurar para o desenvolvimento com SDK de serviços de multimédia para .NET

1. Crie uma aplicação de consola utilizando o Visual Studio.
1. Adicione o SDK de serviços de multimédia para .NET a sua aplicação de consola utilizando o pacote de NuGet de serviços de multimédia.

##<a name="connect-to-media-services"></a>Ligar a serviços de multimédia
Como prática recomendada, deve utilizar um ficheiro de App para armazenar o nome e a conta a chave dos serviços de multimédia.

>[AZURE.NOTE]Para localizar o nome e a chave de valores, aceda ao portal do Azure e selecione a sua conta. Na janela definições que é apresentado à direita. Na janela definições, selecione teclas. Clicando no ícone junto a cada caixa de texto copia o valor para a área de transferência do sistema.

Adicione a secção appSettings para o ficheiro App e defina os valores para a sua chave de nome e a conta da conta dos serviços de multimédia.


    <?xml version="1.0"?>
    <configuration>
      <appSettings>
          <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
          <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
      </appSettings>
    </configuration>
     
    
##<a name="code-example"></a>Exemplo de código

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.IO;
    using System.Linq;
    using System.Net;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
    
    namespace EncodeLiveStreamWithAmsClear
    {
        class Program
        {
            private const string ChannelName = "channel001";
            private const string AssetlName = "asset001";
            private const string ProgramlName = "program001";
    
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
    
    
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                IChannel channel = CreateAndStartChannel();
    
                // The channel's input endpoint:
                string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Intest URL: {0}", ingestUrl);
    
    
                // Use the previewEndpoint to preview and verify 
                // that the input from the encoder is actually reaching the Channel. 
                string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
    
                Console.WriteLine("Preview URL: {0}", previewEndpoint);
    
                // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
                // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
                // The thumbnail is exposed via the same end-point as the Channel Preview URL.
                string thumbnailUri = new UriBuilder
                {
                    Scheme = Uri.UriSchemeHttps,
                    Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
                    Path = "thumbnails/input.jpg"
                }.Uri.ToString();
    
                Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
    
                // Once you previewed your stream and verified that it is flowing into your Channel, 
                // you can create an event by creating an Asset, Program, and Streaming Locator. 
                IAsset asset = CreateAndConfigureAsset();
    
                IProgram program = CreateAndStartProgram(channel, asset);
    
                ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
    
                // You can use slates and ads only if the channel type is Standard.  
                StartStopAdsSlates(channel);
    
                // Once you are done streaming, clean up your resources.
                Cleanup(channel);
    
            }
    
            public static IChannel CreateAndStartChannel()
            {
                var channelInput = CreateChannelInput();
                var channePreview = CreateChannelPreview();
                var channelEncoding = CreateChannelEncoding();
    
    
                ChannelCreationOptions options = new ChannelCreationOptions
                {
                    EncodingType = ChannelEncodingType.Standard,
                    Name = ChannelName,
                    Input = channelInput,
                    Preview = channePreview,
                    Encoding = channelEncoding
                };
    
                Log("Creating channel");
                IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
                string channelId = TrackOperation(channelCreateOperation, "Channel create");
    
                IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
    
                Log("Starting channel");
                var channelStartOperation = channel.SendStartOperation();
                TrackOperation(channelStartOperation, "Channel start");
    
                return channel;
            }
    
            /// <summary>
            /// Create channel input, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelInput CreateChannelInput()
            {
                return new ChannelInput
                {
                    StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelInput001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel preview, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelPreview CreateChannelPreview()
            {
                return new ChannelPreview
                {
                    AccessControl = new ChannelAccessControl
                    {
                        IPAllowList = new List<IPRange>
                        {
                            new IPRange
                            {
                                Name = "TestChannelPreview001",
                                Address = IPAddress.Parse("0.0.0.0"),
                                SubnetPrefixLength = 0
                            }
                        }
                    }
                };
            }
    
            /// <summary>
            /// Create channel encoding, used in channel creation options. 
            /// </summary>
            /// <returns></returns>
            private static ChannelEncoding CreateChannelEncoding()
            {
                return new ChannelEncoding
                {
                    SystemPreset = "Default720p",
                    IgnoreCea708ClosedCaptions = false,
                    AdMarkerSource = AdMarkerSource.Api,
                    // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
                    AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
                };
            }
    
            /// <summary>
            /// Create an asset and configure asset delivery policies.
            /// </summary>
            /// <returns></returns>
            public static IAsset CreateAndConfigureAsset()
            {
                IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
    
                IAssetDeliveryPolicy policy =
                    _context.AssetDeliveryPolicies.Create("Clear Policy",
                    AssetDeliveryPolicyType.NoDynamicEncryption,
                    AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
    
                asset.DeliveryPolicies.Add(policy);
    
                return asset;
            }
    
            /// <summary>
            /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
            /// however each Program must have a unique name within your Media Services account.
            /// </summary>
            /// <param name="channel"></param>
            /// <param name="asset"></param>
            /// <returns></returns>
            public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
            {
                IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
                Log("Program created", program.Id);
    
                Log("Starting program");
                var programStartOperation = program.SendStartOperation();
                TrackOperation(programStartOperation, "Program start");
    
                return program;
            }
    
            /// <summary>
            /// Create locators in order to be able to publish and stream the video.
            /// </summary>
            /// <param name="asset"></param>
            /// <param name="ArchiveWindowLength"></param>
            /// <returns></returns>
            public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
            {
                // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
                var locator = _context.Locators.CreateLocator
                    (
                        LocatorType.OnDemandOrigin,
                        asset,
                        _context.AccessPolicies.Create
                            (
                                "Live Stream Policy",
                                ArchiveWindowLength,
                                AccessPermissions.Read
                            )
                    );
    
                return locator;
            }
    
            /// <summary>
            /// Perform operations on slates.
            /// </summary>
            /// <param name="channel"></param>
            public static void StartStopAdsSlates(IChannel channel)
            {
                int cueId = new Random().Next(int.MaxValue);
                var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
    
                Log("Creating asset");
                var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
                Log("Slate asset created", slateAsset.Id);
    
                Log("Uploading file");
                var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
                assetFile.Upload(path);
                assetFile.IsPrimary = true;
                assetFile.Update();
    
                Log("Showing slate");
                var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
                TrackOperation(showSlateOpeartion, "Show slate");
    
                Log("Hiding slate");
                var hideSlateOperation = channel.SendHideSlateOperation();
                TrackOperation(hideSlateOperation, "Hide slate");
    
                Log("Starting ad");
                var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
                TrackOperation(startAdOperation, "Start ad");
    
                Log("Ending ad");
                var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
                TrackOperation(endAdOperation, "End ad");
    
                Log("Deleting slate asset");
                slateAsset.Delete();
            }
    
            /// <summary>
            /// Clean up resources associated with the channel.
            /// </summary>
            /// <param name="channel"></param>
            public static void Cleanup(IChannel channel)
            {
                IAsset asset;
                if (channel != null)
                {
                    foreach (var program in channel.Programs)
                    {
                        asset = _context.Assets.Where(se => se.Id == program.AssetId)
                                                .FirstOrDefault();
    
                        Log("Stopping program");
                        var programStopOperation = program.SendStopOperation();
                        TrackOperation(programStopOperation, "Program stop");
    
                        program.Delete();
    
                        if (asset != null)
                        {
                            Log("Deleting locators");
                            foreach (var l in asset.Locators)
                                l.Delete();
    
                            Log("Deleting asset");
                            asset.Delete();
                        }
                    }
    
                    Log("Stopping channel");
                    var channelStopOperation = channel.SendStopOperation();
                    TrackOperation(channelStopOperation, "Channel stop");
    
                    Log("Deleting channel");
                    var channelDeleteOperation = channel.SendDeleteOperation();
                    TrackOperation(channelDeleteOperation, "Channel delete");
                }
            }
    
    
            /// <summary>
            /// Track long running operations.
            /// </summary>
            /// <param name="operation"></param>
            /// <param name="description"></param>
            /// <returns></returns>
            public static string TrackOperation(IOperation operation, string description)
            {
                string entityId = null;
                bool isCompleted = false;
    
                Log("starting to track ", null, operation.Id);
                while (isCompleted == false)
                {
                    operation = _context.Operations.GetOperation(operation.Id);
                    isCompleted = IsCompleted(operation, out entityId);
                    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
                }
                // If we got here, the operation succeeded.
                Log(description + " in completed", operation.TargetEntityId, operation.Id);
    
                return entityId;
            }
    
            /// <summary> 
            /// Checks if the operation has been completed. 
            /// If the operation succeeded, the created entity Id is returned in the out parameter.
            /// </summary> 
            /// <param name="operationId">The operation Id.</param> 
            /// <param name="channel">
            /// If the operation succeeded, 
            /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
            /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
            private static bool IsCompleted(IOperation operation, out string entityId)
            {
    
                bool completed = false;
    
                entityId = null;
    
                switch (operation.State)
                {
                    case OperationState.Failed:
                        // Handle the failure. 
                        // For example, throw an exception. 
                        // Use the following information in the exception: operationId, operation.ErrorMessage.
                        Log("operation failed", operation.TargetEntityId, operation.Id);
                        break;
                    case OperationState.Succeeded:
                        completed = true;
                        entityId = operation.TargetEntityId;
                        break;
                    case OperationState.InProgress:
                        completed = false;
                        Log("operation in progress", operation.TargetEntityId, operation.Id);
                        break;
                }
                return completed;
            }
    
    
            private static void Log(string action, string entityId = null, string operationId = null)
            {
                Console.WriteLine(
                    "{0,-21}{1,-51}{2,-51}{3,-51}",
                    DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
                    action,
                    entityId ?? string.Empty,
                    operationId ?? string.Empty);
            }
        }
    }   


##<a name="next-step"></a>Passo seguinte

Reveja dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Está à procura de algo?

Se este tópico não contiverem aquilo que esperava, não é apresentado algo ou numa outra forma não corresponder às suas necessidades, forneça-consigo comentários utilizando o tópico Disqus abaixo.
