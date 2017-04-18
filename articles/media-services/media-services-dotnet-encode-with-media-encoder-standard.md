<properties 
    pageTitle="Codificar um ativo com Media Encoder padrão utilizando o .NET | Microsoft Azure" 
    description="Este tópico mostra como utilizar .NET para descodificar um ativo utilizando Strandard descodificador de multimédia." 
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
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako;anilmur"/>


# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um ativo com Media Encoder padrão utilizando o .NET

Codificação as tarefas são uma das operações de transformação mais comuns dos serviços de multimédia. Criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Quando codificar, pode utilizar o codificador dos serviços de multimédia do Media incorporados. Também pode utilizar um codificador fornecido por um parceiro dos serviços de multimédia; codificadores de terceiros estão disponíveis através do Azure Marketplace. 

Este tópico mostra como utilizar .NET para codificar seus ativos com Media Encoder padrão (EEM). Media Encoder padrão está configurado utilizando um do codificador predefinições descritas [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Recomenda-se para codificar sempre os ficheiros de mezzanine para uma velocidade ajustável MP4 definir e, em seguida, converter o conjunto para o formato pretendido através da [Embalagem dinâmicos](media-services-dynamic-packaging-overview.md). Para tirar partido da embalagem dinâmico, terá de obter, pelo menos, uma unidade de transmissão da pedido para o ponto final de transmissão a partir do qual planeia entrega o conteúdo. Para mais informações, consulte o artigo [como escala dos serviços de multimédia](media-services-portal-manage-streaming-endpoints.md).

Se a elementos de saída armazenamento encriptado, tem de configurar políticas de entrega de elementos. Para obter mais informações consulte o artigo [política de entrega de elementos de configurar](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]EEM gera um ficheiro de saída com um nome que contém os primeiros 32 carateres do nome do ficheiro de entrada. O nome se baseia-se de que está especificado no ficheiro predefinido. Por exemplo, "NomeArquivo": "_ {Index} {extensão} de {Basename}". {Basename} é substituído pelos primeiros 32 caracteres do nome do ficheiro de entrada.

###<a name="mes-formats"></a>Formatos de EEM

[Codecs e formatos](media-services-media-encoder-standard-formats.md)

###<a name="mes-presets"></a>Predefinições de EEM

Media Encoder padrão está configurado utilizando um do codificador predefinições descritas [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###<a name="input-and-output-metadata"></a>Metadados de entrada e saído

Quando codificar um teclado (ou vários activos) utilizando EEM, obterá um ativo de saída a bem sucedida na conclusão de que codificar tarefa. Elemento saída contém o vídeo, áudio, miniaturas, manifesto, etc., com base na predefinição de codificação que utiliza.

Elemento saída também contém um ficheiro com metadados sobre o elemento entrado. O nome do ficheiro XML metadados tem o seguinte formato: < asset_id > _metadata.xml (por exemplo, 41114ad3-eb5e - 4c 57-8d 92-5354e2b7d4a4_metadata.xml), onde < asset_id > é o valor de iddoactivo do elemento entrado. O esquema desta entrada metadados XML é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783120.aspx).

Elemento saída também contém um ficheiro com metadados sobre os elementos de saída. O nome do ficheiro XML metadados tem o seguinte formato: < source_file_name > _manifest.xml (por exemplo, BigBuckBunny_manifest.xml). O esquema dos metadados resultado XML é descrito [aqui](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Se pretender examinar um dos dois ficheiros de metadados, pode criar um localizador de SA e transferir o ficheiro para o seu computador local. Pode encontrar um exemplo sobre como criar um localizador de SA e transferir um ficheiro com extensões SDK .NET dos serviços de multimédia.

##<a name="download-sample"></a>Transferir exemplo

Obter e executar um exemplo a partir do [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##<a name="example"></a>Exemplo

Exemplo de código seguinte utiliza .NET SDK serviços de multimédia para efetuar as seguintes tarefas:

- Crie uma tarefa de codificação.
- Obter uma referência para o codificador Media Encoder padrão.
- Especificar a utilização de "H264 velocidade vários 720p" predefinido. Pode ver todas as predefinições [aqui](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). Também é possível analisar o esquema ao qual devem estar em conformidade destas predefinições tópico [aqui](https://msdn.microsoft.com/library/mt269962.aspx) .
- Adicione uma tarefa codificação à tarefa. 
- Especifique o elemento entrado para ser codificado.
- Crie um ativo de saída que irá conter codificado elemento.
- Adicione um processador de eventos para verificar o progresso da tarefa.
- Submeta a tarefa.
        
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
                AssetCreationOptions.None);
        
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();
        
            return job.OutputMediaAssets[0];
        }
        
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
        
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
        
                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        
        
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
        
            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
        
            return processor;
        }


##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Consulte também 

[Como gerar uma miniatura utilizar Media Encoder padrão com .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Codificação de descrição geral de serviços de multimédia](media-services-encode-asset.md)
