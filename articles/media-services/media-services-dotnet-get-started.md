<properties
    pageTitle="Introdução ao fornecer conteúdo a pedido utilizando o .NET | Azure"
    description="Neste tutorial orienta-o através dos passos de uma aplicação de entrega de conteúdos no pedido de execução com os serviços de multimédia do Azure utilizando o .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/17/2016"
    ms.author="juliako"/>


# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introdução ao fornecer conteúdo a pedido utilizando .NET SDK

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> Para concluir este tutorial, é necessária uma conta Azure. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Descrição geral 

Neste tutorial orienta-o através dos passos de implementar uma aplicação de entrega de conteúdos invés (VoD) utilizando os serviços de multimédia do Azure (MGA) SDK para .NET.


O tutorial introduz o fluxo de trabalho dos serviços de multimédia básico e os objetos de programação mais comuns e tarefas necessárias para o desenvolvimento dos serviços de multimédia. Após a conclusão do tutorial, será possível transmitir em fluxo ou gradualmente transferir um ficheiro de multimédia de exemplo que carregou, codificado e transferidos.

## <a name="what-youll-learn"></a>O que vai aprender

O tutorial mostra como realizar as seguintes tarefas:

1.  Crie uma conta dos serviços de multimédia (utilizando o portal do Azure).
2.  Configure o ponto final de transmissão (utilizando o portal do Azure).
3.  Criar e configurar um projeto do Visual Studio.
5.  Ligar à conta dos serviços de multimédia.
6.  Criar um novo imobilizado e carregue um ficheiro de vídeo.
7.  Codificar o ficheiro de origem para o conjunto de velocidade ajustável MP4 ficheiros.
8.  Publicar o elemento e obter URLs para transferência de transmissão e gradual.
9.  Teste ao reproduzir o seu conteúdo.

## <a name="prerequisites"></a>Pré-requisitos

A seguinte é necessários para completar a iniciação.

- Para concluir este tutorial, é necessária uma conta Azure. 
    
    Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Obter créditos que podem ser utilizados para experimentar a serviços Azure pagos. Mesmo depois de créditos são utilizados para cima, pode manter a conta e utilizar serviços Azure gratuitos e funcionalidades, como a funcionalidade de Web Apps no Azure aplicação de serviço.
- Sistemas operativos: Windows 8 ou posterior, Windows 2008 R2, Windows 7.
- .NET framework 4.0 ou posterior
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou versões posteriores.


##<a name="download-sample"></a>Transferir exemplo

Obter e executar um exemplo a partir do [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Criar uma conta dos serviços de multimédia do Azure utilizando o portal do Azure

Os passos nesta secção Mostrar como criar uma conta de AMS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique em **+ Novo** > **multimédia + CDN** > **dos serviços de multimédia**.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. **Criar conta de serviços de multimédia** introduza valores necessários.

    ![Criar dos serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Em **Nome da conta**, introduza o nome da nova conta de AMS. Um nome de conta dos serviços de multimédia é todos os números em minúsculas ou letras sem espaços e é 3 para 24 carateres de comprimento.
    2. Na sua subscrição, selecione entre as diferentes subscrições Azure que tiver acesso a.
    
    2. No **Grupo de recursos**, selecione o recurso novo ou existente.  Um grupo de recursos é um conjunto de recursos que partilham o ciclo de vida, permissões e políticas de. Saiba mais [aqui](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. **Localização**, selecione a região geográfica é utilizada para armazenar os registos de multimédia e de metadados para a sua conta dos serviços de multimédia. Esta região é utilizada para processar e transmitir em fluxo os ficheiros de multimédia. Apenas as regiões dos serviços de multimédia disponíveis aparecem na caixa de lista pendente. 
    
    3. Na **Conta de armazenamento**, selecione uma conta de armazenamento para fornecer o armazenamento de Blobs do conteúdo multimédia da sua conta dos serviços de multimédia. Pode selecionar uma conta de armazenamento existente na mesma região geográfica como a sua conta dos serviços de multimédia, ou pode criar uma conta de armazenamento. É criada uma nova conta de armazenamento na mesma região. As regras de nomes de conta de armazenamento são iguais para contas dos serviços de multimédia.

        Saiba mais sobre o armazenamento [aqui](storage-introduction.md).

    4. Selecione **Afixar ao dashboard** para ver o progresso de implementação de conta.
    
7. Clique em **Criar** na parte inferior do formulário.

    Assim que a conta estiver criada com êxito, o estado muda para **a executar**. 

    ![Definições de serviços de multimédia](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Para gerir a sua conta AMS (por exemplo, carregar vídeos, codificar elementos, monitorizar o progresso da tarefa) utilize a janela **Definições** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurar os pontos finais transmissão através do portal Azure

Quando estiver a trabalhar com os serviços de multimédia do Azure um dos cenários mais comuns está a entregar vídeo através de velocidade ajustável transmissão os seus clientes. Dos serviços de multimédia suporta a velocidade de bits ajustável seguinte transmissão tecnologias: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Dos serviços de multimédia fornece embalagem dinâmica, que permite-lhe entregar a sua velocidade ajustável MP4 codificado conteúdo transmissão formatos suportados dos serviços de multimédia (MPEG travessão, HLS, transmissão suaves, HDS) just-in-time, sem ter de armazenar previamente embalados versões de cada um destes transmissão formatos.

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar o seu ficheiro mezzanine (origem) para um conjunto de ficheiros de velocidade ajustável MP4 (os passos de codificação são demonstrados mais tarde neste tutorial).  
- Crie, pelo menos, uma unidade de transmissão para o *ponto final de transmissão* a partir do qual planeia entrega o conteúdo. Os passos abaixo Mostrar como alterar o número de unidades transmissão.

Com embalagem dinâmica, necessita apenas armazenar e pagar para os ficheiros no formato de armazenamento única e dos serviços de multimédia constrói e serve a resposta adequada baseada nos pedidos de um cliente.

Para criar e alterar o número de unidades reservadas de transmissão, faça o seguinte:


1. Na janela **Definições** , clique em **pontos finais de transmissão**. 

2. Clique na predefinição transmissão ponto final. 

    A janela de **Detalhes de ponto final de transmissão de predefinido** é apresentada.

3. Para especificar o número de unidades transmissão, controlo de deslize **transmissão unidades** .

    ![Transmissão de unidades](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Clique no botão **Guardar** para guardar as alterações.

    >[AZURE.NOTE]A alocação de qualquer novas unidades pode demorar até 20 minutos a concluir.

##<a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Crie uma nova aplicação de consola do c# no Visual Studio 2013, no Visual Studio 2012http ou Visual Studio 2010 SP1. Introduza o **nome**, **localização**e **nome da solução**e, em seguida, clique em **OK**.

2. Utilize o pacote de NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar o **Azure multimédia serviços .NET SDK extensões**.  Extensões SDK .NET dos serviços de multimédia é um conjunto de métodos de extensão e funções de helper que irão simplificar o seu código e simplificar a desenvolver com dos serviços de multimédia. Instalar este pacote, também instala o **.NET SDK serviços de multimédia** e adiciona todas as outras dependências.

3. Adicione uma referência a System.Configuration assemblagem. Esta assemblagem contém a classe de **System.Configuration.ConfigurationManager** que é utilizada para aceder a ficheiros de configuração, por exemplo, App.

4. Abra o ficheiro App (adicionar o ficheiro ao seu projeto se não foi adicionada por predefinição) e adicionar uma secção de *appSettings* para o ficheiro. Defina os valores para a sua dos serviços de multimédia do Azure nome e conta chave da conta, conforme mostrado no seguinte exemplo. Para obter o nome da conta e informações da chave, aceda ao [portal do Azure](https://portal.azure.com/) e selecione a sua conta AMS. Em seguida, selecione **Definições** > **teclas**. As janelas de teclas gerir mostra o nome da conta e as teclas principais e secundárias é apresentada.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Substitui as existente **utilizando** demonstrações no início do ficheiro Program.cs com o código seguinte.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Criar uma nova pasta sob o directório de projetos e copie um ficheiro. mp4 ou. wmv que pretende codificar e transmitir em fluxo ou transferir gradualmente. Neste exemplo, é utilizado o caminho de "C:\VideoFiles".

##<a name="connect-to-the-media-services-account"></a>Ligar à conta dos serviços de multimédia

Quando utilizar os serviços de multimédia com .NET, tem de utilizar a classe de **CloudMediaContext** para a maioria dos serviços de multimédia tarefas de programação: ligar à conta dos serviços de multimédia; criar, atualizar, aceder e eliminar os seguintes objectos: elementos, ficheiros de elementos, tarefas, as políticas de acesso, Locator, etc.

Substitui a classe de programa predefinida com o código seguinte. O código demonstra como ler os valores de ligação a partir do ficheiro de App e como criar o objeto **CloudMediaContext** para poder ligar aos serviços de multimédia. Para mais informações sobre como ligar aos serviços de multimédia, consulte o artigo [ligar aos serviços de multimédia com o SDK de serviços de multimédia para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

A função **principais** chama métodos que serão definidos mais nesta secção.

    class Program
    {
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
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Criar um novo imobilizado e carregar um ficheiro de vídeo

Serviços de multimédia, pode carrega (ou ingerir esta última) seus ficheiros digitais para um ativo. A entidade de **elementos** pode conter vídeo, áudio, imagens, coleções de miniaturas, texto faixas e legenda fechada ficheiros (e os metadados sobre estes ficheiros.)  Assim que os ficheiros carregados, o conteúdo está armazenado segura na nuvem para subsequente transformação e transmissão. Os ficheiros no activo são denominados **Ficheiros ativo**.

O método de **UploadFile** definido abaixo chamadas **CreateFromFile** (definido nas .NET SDK extensões). **CreateFromFile** cria um novo activo no qual o ficheiro de origem especificado é carregado.

O método de **CreateFromFile** leva-o até **AssetCreationOptions** permite-lhe especificar uma das seguintes opções de criação de elementos:

- **Nenhum** – sem encriptação é utilizada. Este é o valor predefinido. Tenha em atenção que ao utilizar esta opção, o conteúdo não está protegido quando estão em trânsito ou no resto no armazenamento.
Se planear entregar uma MP4 utilizando transferência gradual, utilize esta opção.
- **StorageEncrypted** - Utilize esta opção para encriptar o seu conteúdo limpar localmente utilizar encriptação de bit-256 de encriptação AES Advanced Standard (), em seguida, os carregamentos pendentes ao armazenamento do Azure onde foi armazenado encriptada em repouso. Elementos protegidos com encriptação de armazenamento são automaticamente não encriptados colocados num sistema de ficheiros encriptados antes de codificação e, opcionalmente, encriptar novamente antes de carregar novamente como um novo activo de saída. No caso de utilização principal para encriptação de armazenamento é quando que pretende proteger os seus ficheiros de multimédia de entrada de alta qualidade com encriptação forte em repouso no disco.
- **CommonEncryptionProtected** - Utilize esta opção se o carregamento de conteúdo que já foram encriptado e protegido com encriptação comuns ou DRM PlayReady (por exemplo, suaves transmissão protegido com PlayReady DRM).
- **EnvelopeEncryptionProtected** – Utilize esta opção se são carregar HLS encriptados com AES. Note que os ficheiros tem ter sido codificados e encriptados por transformar gestor.

O método de **CreateFromFile** também lhe permite especificar uma chamada de retorno para comunicar o progresso de carregamento do ficheiro.

No seguinte exemplo, vamos especificar **nenhum** para as opções de elementos.

Adicione o seguinte método para a classe de programa.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificar o ficheiro de origem para o conjunto de velocidade ajustável MP4 ficheiros

Após ingesting activos dos serviços de multimédia, pode ser multimédia codificado transmuxed, como a marca de água e assim sucessivamente, antes de ser entregue aos clientes. Estas actividades são agendadas e executar a em várias instâncias de função de fundo para se certificar de alto desempenho e disponibilidade. Estas actividades são chamadas tarefas e cada tarefa é composta por atómica tarefas que efetuar o trabalho real no ficheiro ativo.

Tal como foi mencionado anteriormente, quando estiver a trabalhar com os serviços de multimédia do Azure, um dos cenários mais comuns está a entregar ajustável velocidade da transmissão os seus clientes. Dos serviços de multimédia dinamicamente pode compactar um conjunto de ficheiros de velocidade ajustável MP4 para um dos seguintes formatos: HTTP Live transmissão (HLS), transmissão suaves, MPEG TRAÇO e HDS (para o licenciamento do Adobe quando for necessária/acesso apenas).

Para tirar partido da embalagem dinâmico, terá de fazer o seguinte procedimento:

- Codificar ou transcodificar seu mezzanine (origem) ficheiro para um conjunto de ficheiros de velocidade ajustável MP4 ou transmissão suaves ajustável velocidade.  
- Obter, pelo menos, uma unidade de transmissão para o ponto final de transmissão a partir do qual planeia entrega o conteúdo.

O código seguinte mostra como pretende submeter uma tarefa de codificação. A tarefa contém uma tarefa que especifica transcodificar o ficheiro mezzanine para um conjunto de velocidade ajustável MP4s utilizando **Media Encoder padrão**. O código submete a tarefa e aguarda até que seja concluída.

Assim que a tarefa está concluída, seria possível transmitir em fluxo o elementos ou transferir ficheiros MP4 que foram criados como resultado transcodificação a gradualmente.
Note que não necessita ter mais do que 0 unidades transmissão para poder para gradualmente transferir ficheiros de MP4.

Adicione o seguinte método para a classe de programa.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicar o elemento e obter URLs para transferência de transmissão e gradual

Para transmitir em fluxo ou transferir um ativo, tem primeiro de "publicá-lo" criando um localizador. Locator fornece acesso aos ficheiros contidas num elemento. Dos serviços de multimédia suporta dois tipos de Locator: Locator OnDemandOrigin, utilizado para transmitir multimédia em sequência (por exemplo, MPEG travessão, HLS ou transmissão suave) e Locator acesso assinatura (SA), utilizados para transferir ficheiros de multimédia.

Depois de criar o Locator, pode criar os URLs que são utilizados para transmitir em fluxo ou transferir os seus ficheiros.


Um URL de transmissão para transmissão suaves tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Um URL de transmissão de HLS tem o seguinte formato:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Um URL de transmissão de TRAÇO MPEG tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Um URL de SA utilizados para transferir ficheiros tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Extensões de serviços de multimédia .NET SDK fornecem métodos de helper conveniente que devolvem formatados URLs para o elemento publicado.

O código seguinte utiliza .NET SDK extensões para criar Locator e ao obter streaming e URLs de transferência gradual. O código também mostra como transferir ficheiros para uma pasta local.

Adicione o seguinte método para a classe de programa.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Testar ao reproduzir o seu conteúdo  

Depois de executar o programa definido na secção anterior, os URLs semelhantes ao seguinte serão apresentados na janela da consola.

URLs de transmissão ajustável:

Transmissão suaves

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG TRAVESSÃO

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URLs de transferência gradual (áudio e vídeo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


A sequência de vídeo, utilize o [Leitor de serviços de multimédia do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar a transferência gradual, cole um URL de um browser (por exemplo, do Internet Explorer, Chrome ou Safari).


##<a name="next-steps-media-services-learning-paths"></a>Passos seguintes: Serviços de multimédia caminhos de aprendizagem

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else"></a>Está à procura de algo?

Se este tópico não contiverem aquilo que esperava, não é apresentado algo ou numa outra forma não corresponder às suas necessidades, forneça o seu comentário utilizando o tópico Disqus abaixo.


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/
