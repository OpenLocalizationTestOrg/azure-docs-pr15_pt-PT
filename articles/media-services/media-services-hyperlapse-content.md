<properties
    pageTitle="Ficheiros de multimédia Hyperlapse com Hyperlapse de multimédia do Microsoft Azure | Microsoft Azure"
    description="Azure multimédia Hyperlapse cria suaves vídeos tempo decorrido a partir do conteúdo da primeira pessoa ou da câmara de ação. Este tópico mostra como utilizar indexador de multimédia."
    services="media-services"
    documentationCenter=""
    authors="asolanki"
    manager="johndeu"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/19/2016"  
    ms.author="adsolank"/>


# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Ficheiros de multimédia Hyperlapse com Hyperlapse de multimédia do Microsoft Azure

Azure multimédia Hyperlapse é um Media processador (programa mínimo) que cria suaves vídeos tempo decorrido do conteúdo da primeira pessoa ou da câmara de ação.  Colaterais baseado na nuvem para [Hyperlapse Pro de ambiente de trabalho Microsoft Research e baseado em telefone Hyperlapse Mobile](http://aka.ms/hyperlapse), Microsoft Hyperlapse dos serviços de multimédia do Azure utiliza a escala grandes de processamento de multimédia do Azure multimédia serviços a plataforma para horizontalmente dimensionar e parallelize em volume Hyperlapse de processamento.

>[AZURE.IMPORTANT]Microsoft Hyperlapse foi concebido para funcionar melhor no conteúdo da primeira pessoa com uma câmara mover.  Apesar de imagens da câmara ainda podem continuar a trabalhar, o desempenho e qualidade do Azure multimédia Hyperlapse multimédia processador não não garantir outros tipos de conteúdo.  Para saber mais sobre o Microsoft Hyperlapse para dos serviços de multimédia do Azure e ver alguns vídeos de exemplo, consulte o artigo de [blogue introdutórios](http://aka.ms/azurehyperlapseblog) a partir do pré-visualização do público.

Um Hyperlapse de multimédia do Azure tarefa leva como entrada de um ficheiro de elementos MP4, MOV ou WMV juntamente com um ficheiro de configuração que especifica os pacotes de vídeo que devem ser tempo decorrido e a que velocidade (por exemplo, a primeira 10.000 frames em 2 x).  O resultado é uma representação estabilizada e tempo decorrido do vídeo de entrada.

Para obter as atualizações mais recentes do Hyperlapse de multimédia do Azure, consulte [blogues dos serviços de multimédia](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse um ativo

Primeiro terá de carregar o ficheiro de entrada pretendido para dos serviços de multimédia do Azure.  Para saber mais sobre os conceitos envolvidos com carregar e gestão de conteúdo, leia o [artigo de gestão de conteúdo](media-services-portal-vod-get-started.md).

###  <a id="configuration"></a>Configuração predefinido para Hyperlapse

Assim que o seu conteúdo está na sua conta dos serviços de multimédia, terá de construir a sua configuração predefinido.  A seguinte tabela explica os campos especificado pelo utilizador:

 Campo | Descrição
-------|-------------
StartFrame|A moldura no qual o processamento de Microsoft Hyperlapse deverá começar.
NumFrames|O número de molduras para processar
Velocidade|O factor com as quais acelerar o vídeo introdução.

Segue-se um exemplo de um ficheiro de configuração de conformidade com os no XML e JSON:

**XML predefinido:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON predefinido:**

    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }

###  <a id="sample_code"></a>Microsoft Hyperlapse com o AMS .NET SDK

O seguinte método carregamentos de um ficheiro de multimédia como um activo e cria uma tarefa com o processador de multimédia do Azure multimédia Hyperlapse.

> [AZURE.NOTE] Já deve ter uma CloudMediaContext no âmbito com o nome "contexto" para que este código trabalhar.  Para saber mais sobre este procedimento, leia o [artigo de gestão de conteúdo](media-services-dotnet-get-started.md).

> [AZURE.NOTE] O argumento de cadeia "hyperConfig" se espera que seja uma configuração de conformidade com os predefinida na JSON ou XML como descrito acima.

Booleano estático RunHyperlapseJob (entrada de cadeia, saída de cadeia, cadeia hyperConfig) {/ / criar ativos com elementos do ficheiro de entrada IAsset = contexto. Elementos. CreateAssetAndUploadSingleFile (entrada, "Minha entrada Hyperlapse", AssetCreationOptions.None);

Espreite instâncias do Azure multimédia Hyperlapse MP IMediaProcessor mp = contexto. MediaProcessors. GetLatestMediaProcessorByName ("multimédia do Microsoft Azure Hyperlapse");

Criar tarefa com Hyperlapse tarefa in = contexto. Tarefas. Criar (String.Format (entrada de "Hyperlapse {0}",));

Se (String.IsNullOrEmpty(hyperConfig)) {/ / config não pode ser vazio falso devolvido;}

hyperConfig = File.ReadAllText(hyperConfig);

ITask hyperlapseTask = tarefa. Tasks.AddNew ("Hyperlapse tarefa", programa mínimo, hyperConfig, TaskOptions.None); hyperlapseTask.InputAssets.Add(asset); hyperlapseTask.OutputAssets.AddNew ("Hyperlapse saída", AssetCreationOptions.None);


tarefa. Submit();

Criar a impressão de progresso e consultar tarefas tarefa progressPrintTask = Task(() novo = > {

In jobQuery = null; Efetue {var progressContext = contexto; jobQuery = progressContext.Jobs. Onde (j = > j.Id = = tarefa. ID). First (); Console.WriteLine (cadeia. Formatar ("{0}: \t \t {1} {2}", DateTime. Now, jobQuery.State, jobQuery.Tasks[0]. Progresso)); Thread.Sleep(10000); } enquanto (jobQuery.State! = JobState.Finished & & jobQuery.State! = JobState.Error & & jobQuery.State! = JobState.Canceled); }); progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));  
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }

### <a id="file_types"></a>Tipos de ficheiro suportados

- MP4
- MOV
- WMV



##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="related-links"></a>Ligações relacionadas

[Descrição geral de análise de serviços de multimédia do Azure](media-services-analytics-overview.md)

[Azure demos de análise de multimédia](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)
